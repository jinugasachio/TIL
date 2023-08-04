# ReviewApp on ECS

```yaml
name: Check /deploy comment for ReviewApp

on:
  issue_comment:
    types: [created]

jobs:
  deploy:
    if: ${{ github.event.issue.pull_request && github.event.comment.body == '/deploy' }}
    name: Check /deploy comment for ReviewApp
    runs-on: ubuntu-20.04
    steps:
      - name: Generate token
        id: generate_token
        uses: tibdex/github-app-token@v1
        with:
          app_id: ${{ secrets.APP_ID }}
          private_key: ${{ secrets.APP_PRIVATE_KEY }}

      - uses: actions/checkout@v3
        with:
          repository: ${{ github.event.pull_request.head.repo.full_name }}
          ref: refs/pull/${{ github.event.issue.number }}/merge
          token: ${{ steps.generate_token.outputs.token }}

      - name: Dispatch Workflow
        run: |
          echo '{ "pr_number": "${{ github.event.issue.number }}", "author": "${{ github.event.issue.user.login }}" }' | gh workflow run reviewapp-deployment.yml --json
        env:
          GITHUB_TOKEN: ${{ steps.generate_token.outputs.token }}

      - name: Post Comment (START)
        uses: actions/github-script@v6.4.0
        env:
          MESSAGE: |
            @${{ github.event.sender.login}}

            ReviewAppのデプロイを開始します :rocket:
        with:
          github-token: ${{ steps.generate_token.outputs.token }}
          script: |
            github.rest.issues.createComment({
              issue_number: ${{ github.event.issue.number }},
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: process.env.MESSAGE
            })
```

```yaml
name: Deploy to ECS on QA (ReviewApp)
on:
  workflow_dispatch:
    inputs:
      pr_number:
        description: 'Pull Request Number'
        required: true
      author:
        description: 'Pull Request Author'
        required: true

permissions:
  id-token: write
  contents: read

jobs:
  build_and_push_rails_image:
    runs-on: ubuntu-20.04
    name: Rails image build and push
    outputs:
      image_tag: ${{ steps.push-ecr.outputs.image_tag }}
    steps:
      - name: Checkout
        uses: actions/checkout@v3
        with:
          ref: refs/pull/${{ github.event.inputs.pr_number }}/merge
          fetch-depth: 0
      - name: Docker image build and push in QA
        id: push-ecr
        uses: ./.github/actions/build_and_push_image
        with:
          environment: qa
          region: ap-northeast-1
          role-to-assume: ${{ secrets.QA_AWS_ROLE_ARN }}
          build_context: .
          docker_file_path: docker/ecs/app/production.Dockerfile
          ecr-repository-name: freee-sign-rails
          build-args: |
            "GITHUB_TOKEN_FOR_GITHUB_PACKAGES=${{ secrets.SUSHI_GITHUB_TOKEN_FOR_GITHUB_PACKAGES }}"
            "BUNDLE_GEMS__CONTRIBSYS__COM=${{ secrets.BUNDLE_GEMS__CONTRIBSYS__COM }}"

  build_and_push_nginx_image:
    runs-on: ubuntu-20.04
    name: nginx image build and push
    outputs:
      image_tag: ${{ steps.push-ecr.outputs.image_tag }}
    steps:
      - name: Checkout
        uses: actions/checkout@v3
        with:
          ref: refs/pull/${{ github.event.inputs.pr_number }}/merge
          fetch-depth: 0
      - name: Docker image build and push in ${{ inputs.environment }}
        id: push-ecr
        uses: ./.github/actions/build_and_push_image
        with:
          environment: qa
          region: ap-northeast-1
          role-to-assume: ${{ secrets.QA_AWS_ROLE_ARN }}
          build_context: docker/ecs/nginx
          docker_file_path: docker/ecs/nginx/Dockerfile
          ecr-repository-name: freee-sign-nginx
          build-args: |
            "BUNDLE_GEMS__CONTRIBSYS__COM=${{ secrets.bundle_gems_contribsys_com }}"

  build_and_push_fluentbit_image:
    runs-on: ubuntu-20.04
    name: fluentbit image build and push
    outputs:
      image_tag: ${{ steps.push-ecr.outputs.image_tag }}
    steps:
      - name: Checkout
        uses: actions/checkout@v3
        with:
          ref: refs/pull/${{ github.event.inputs.pr_number }}/merge
          fetch-depth: 0
      - name: Docker image build and push in ${{ inputs.environment }}
        id: push-ecr
        uses: ./.github/actions/build_and_push_image
        with:
          environment: qa
          region: ap-northeast-1
          role-to-assume: ${{ secrets.QA_AWS_ROLE_ARN }}
          build_context: docker/ecs/fluentbit
          docker_file_path: docker/ecs/fluentbit/Dockerfile
          ecr-repository-name: freee-sign-fluentbit

  # ###########################################################
  # # ECS サービスまでの経路に必要なAWSリソースを作成
  # # すでに存在する場合は、checkout だけ実施して成功ステータスにします。
  # ###########################################################
  create-aws-resources:
    name: Create AWS Resources
    runs-on: ubuntu-20.04
    steps:
      - name: Checkout
        uses: actions/checkout@v3
        with:
          ref: refs/pull/${{ github.event.inputs.pr_number }}/merge
          fetch-depth: 0
      - name: Configure ${{ inputs.environment }} AWS credentials
        uses: aws-actions/configure-aws-credentials@v2.0.0
        with:
          aws-region: ap-northeast-1
          role-to-assume: ${{ secrets.QA_AWS_ROLE_ARN }}
      - name: Get Cloudformation Stack
        id: check-stack
        run: |
          sh ./.github/scripts/descript-cloudformation-stack.sh
        env:
          PR_NUMBER: ${{ github.event.inputs.pr_number }}
      - name: Set Environment Variables
        if: steps.check-stack.outputs.needs_stack == 'true'
        run: |
          bash ./.github/scripts/set-aws-recource-arns.sh
        env:
          QA_ALB_LISTENER_ARN: ${{ secrets.QA_ALB_LISTENER_ARN }}
      - name: Deploy to AWS CloudFormation
        id: cloudformation
        if: steps.check-stack.outputs.needs_stack == 'true'
        uses: aws-actions/aws-cloudformation-github-deploy@v1
        with:
          name: ReviewappPR${{ github.event.inputs.pr_number }}
          template: ./.github/cloudformation/stack.yml
          parameter-overrides: >-
            AlbLIistenerArn=${{ secrets.QA_ALB_LISTENER_ARN }},
            PullRequestNumber=${{ github.event.inputs.pr_number }},
            AlbDnsName=${{ env.alb_dns_name }},
            VpcID=${{ env.vpc_id }},
            ListenerPriorityNumber=${{ env.listener_priority }},
            HostedZoneId=${{ env.route53_hosted_zone_id }}
  # ################################################################
  # # PRごとにECSサービスを構築します。
  # # ECSサービスがなければ作成し、すでに存在する場合はデプロイのみ実行します。
  # ################################################################
  deploy_ecs:
    needs:
      - build_and_push_rails_image
      - build_and_push_fluentbit_image
      - build_and_push_nginx_image
      - create-aws-resources
    name: Deploy ECS Services
    runs-on: ubuntu-20.04
    steps:
      - name: Checkout
        uses: actions/checkout@v3
        with:
          ref: refs/pull/${{ github.event.inputs.pr_number }}/merge
          fetch-depth: 0
      - name: Configure ${{ inputs.environment }} AWS credentials
        uses: aws-actions/configure-aws-credentials@v2.0.0
        with:
          aws-region: ap-northeast-1
          role-to-assume: ${{ secrets.QA_AWS_ROLE_ARN }}
      - name: Login to ${{ inputs.environment }} Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v1
      - name: Get Target Group
        id: target-group
        run: |
          sh ./.github/scripts/describe-target-group.sh
        env:
          PR_NUMBER: ${{ github.event.inputs.pr_number }}
      - name: ecspresso deploy
        uses: ./.github/actions/ecspresso_deploy_reviewapp
        env:
          AWS_REGION: ap-northeast-1
          APP_HOST: pr-${{ github.event.inputs.pr_number }}.reviewapp-fs.com
          APP_NAME: pr-${{ github.event.inputs.pr_number }}
          TARGET_GROUP_ARN: ${{ steps.target-group.outputs.target_group_arn }}
          ECS_SERVICE_NAME: freee-sign-reviewapp-${{ github.event.inputs.pr_number }}
          ECS_CLUSTER_NAME: freee-sign
          TASK_DEF_FAMILY: freee-sign-reviewapp-${{ github.event.inputs.pr_number }}
          FLUENTBIT_IMAGE_URL: ${{ steps.login-ecr.outputs.registry }}/${{ needs.build_and_push_fluentbit_image.outputs.image_tag }}
          NGINX_IMAGE_URL: ${{ steps.login-ecr.outputs.registry }}/${{ needs.build_and_push_nginx_image.outputs.image_tag }}
          RAILS_IMAGE_URL: ${{ steps.login-ecr.outputs.registry }}/${{ needs.build_and_push_rails_image.outputs.image_tag }}
          POSTGRES_IMAGE_URL: ${{ steps.login-ecr.outputs.registry }}/freee-sign-postgres:12.13
          REDIS_IMAGE_URL: ${{ steps.login-ecr.outputs.registry }}/freee-sign-redis:6.2.6
          RELEASE_COMMIT_HASH: ${{ github.sha }}
        with:
          working-directory: ./.github/ecspresso/qa/reviewapp

  notify_deploy_completed:
    needs:
      - create-aws-resources
      - deploy_ecs
    runs-on: ubuntu-20.04
    name: Notify Deploy Completed
    steps:
      - name: Checkout
        uses: actions/checkout@v3
        with:
          ref: refs/pull/${{ github.event.inputs.pr_number }}/merge
          fetch-depth: 0
      - name: Generate github token
        id: generate_token
        uses: tibdex/github-app-token@v1
        with:
          app_id: ${{ secrets.APP_ID }}
          private_key: ${{ secrets.APP_PRIVATE_KEY }}
      - name: Post Comment (END)
        uses: actions/github-script@v6.4.0
        env:
          MESSAGE: |
            @${{ github.event.inputs.author }}

            ReviewAppのデプロイが完了しました。:tada: :tada:

            https://pr-${{ github.event.inputs.pr_number }}.reviewapp-fs.com
        with:
          github-token: ${{ steps.generate_token.outputs.token }}
          script: |
            github.rest.issues.createComment({
              issue_number: ${{ github.event.inputs.pr_number }},
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: process.env.MESSAGE
            })

  notify_deploy_failed:
    needs:
      - deploy_ecs
    runs-on: ubuntu-latest
    if: failure()
    steps:
      - name: Checkout
        uses: actions/checkout@v3
        with:
          ref: refs/pull/${{ github.event.inputs.pr_number }}/merge
          fetch-depth: 0
      - name: Generate github token
        id: generate_token
        uses: tibdex/github-app-token@v1
        with:
          app_id: ${{ secrets.APP_ID }}
          private_key: ${{ secrets.APP_PRIVATE_KEY }}
      - name: Notify PR when CI fails
        uses: actions/github-script@v6.4.0
        env:
          MESSAGE: |
            @${{ github.event.inputs.author }}

            :exclamation: ReviewAppのデプロイに失敗しました。

            ${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}
        with:
          github-token: ${{ steps.generate_token.outputs.token }}
          script: |
            github.rest.issues.createComment({
              issue_number: ${{ github.event.inputs.pr_number }},
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: process.env.MESSAGE
            })
```

```yaml
name: Terminate ReviewApp

on:
  pull_request:
    types: [closed]

permissions:
  id-token: write
  contents: read

jobs:
  destroy_ecs_service:
    runs-on: ubuntu-20.04
    name: Destroy ECS Service
    steps:
      - name: Checkout
        uses: actions/checkout@v3
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2.0.0
        with:
          aws-region: ap-northeast-1
          role-to-assume: ${{ secrets.QA_AWS_ROLE_ARN }}
      - name: Get ECS Service
        shell: bash
        id: deployment
        run: |
          sh ./.github/scripts/describe-ecs-service.sh
        env:
          ECS_SERVICE_NAME: freee-sign-reviewapp-${{ github.event.pull_request.number }}
          ECS_CLUSTER_NAME: freee-sign
      - name: ecspresso destroy
        if: steps.deployment.outputs.service-exist == 'true'
        uses: ./.github/actions/ecspresso_destroy_reviewapp
        env:
          ECS_SERVICE_NAME: freee-sign-reviewapp-${{ github.event.pull_request.number }}
        with:
          working-directory: ./.github/ecspresso/qa/reviewapp
          cluster-name: freee-sign
          service-name: freee-sign-reviewapp-${{ github.event.pull_request.number }}
      - name: delete task definitions
        if: steps.deployment.outputs.service-exist == 'true'
        run: |
          aws ecs list-task-definitions --family-prefix freee-sign-reviewapp-${{ github.event.pull_request.number }} \
            | jq '.taskDefinitionArns[]' \
            | xargs -I{} sh -c "aws ecs deregister-task-definition --task-definition {} && aws ecs delete-task-definitions --task-definitions {}"

  terminate:
    needs:
      - destroy_ecs_service
    runs-on: ubuntu-20.04
    name: Destroy Cloudformation Stack
    steps:
      - name: Checkout
        uses: actions/checkout@v3
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2.0.0
        with:
          aws-region: ap-northeast-1
          role-to-assume: ${{ secrets.QA_AWS_ROLE_ARN }}
      - name: Get Cloudformation Stack
        id: check-stack
        run: |
          sh ./.github/scripts/descript-cloudformation-stack.sh
        env:
          PR_NUMBER: ${{ github.event.pull_request.number }}
      - name: Destroy Stack
        if: steps.check-stack.outputs.needs_stack == 'false'
        run: |
          aws cloudformation delete-stack --stack-name ReviewappPR${{ github.event.pull_request.number }}
```

stack.yaml
```yaml
AWSTemplateFormatVersion: '2010-09-09'
Parameters:
  PullRequestNumber:
    Type : String
  AlbDnsName:
    Type: String
  VpcID:
    Type : String
  AlbLIistenerArn:
    Type: String
  ListenerPriorityNumber:
    Type: Number
  HostedZoneId:
    Type: String

Resources:
  DNSRecord:
    Type: AWS::Route53::RecordSetGroup
    Properties:
      HostedZoneName: reviewapp-fs.com.
      Comment: Zone apex alias targeted to DNSRecord LoadBalancer.
      RecordSets:
        - Name: !Sub pr-${PullRequestNumber}.reviewapp-fs.com
          Type: A
          AliasTarget:
            HostedZoneId: !Sub ${HostedZoneId}
            DNSName: !Sub ${AlbDnsName}

  TargetGroup:
    Type: AWS::ElasticLoadBalancingV2::TargetGroup
    Properties:
      Name: !Sub freee-sign-reviewapp-pr-${PullRequestNumber}-tg
      HealthCheckEnabled: true
      HealthCheckIntervalSeconds: '5'
      HealthCheckPath: '/health_check'
      HealthCheckPort: 8080
      HealthCheckProtocol: HTTP
      HealthCheckTimeoutSeconds: '2'
      HealthyThresholdCount: '5'
      Matcher:
        HttpCode: 200,301
      Port: 80
      Protocol: HTTP
      TargetType: ip
      UnhealthyThresholdCount: '3'
      VpcId: !Sub ${VpcID}
      TargetGroupAttributes:
      - Key: deregistration_delay.timeout_seconds
        Value: 5
  ListenerRule:
    Type: AWS::ElasticLoadBalancingV2::ListenerRule
    Properties:
      Actions:
        - Type: forward
          ForwardConfig:
              TargetGroups:
                - TargetGroupArn: !Ref TargetGroup
                  Weight: 100
      Conditions:
        - Field: host-header
          HostHeaderConfig:
              Values:
                - !Sub pr-${PullRequestNumber}.reviewapp-fs.com
      ListenerArn: !Sub ${AlbLIistenerArn}
      Priority: !Sub ${ListenerPriorityNumber}

Outputs:
  TargetGroupArn:
    Description: Target Group Arn
    Value: !Ref TargetGroup

```

describe-deployments.sh
```shell
#!/bin/bash -eux

# DEPLOYMENT_ID を取得します
# CodeDeploy は 1 DeployGroup に 1 Deployment しか実行できない(並列不可)
# 進行中のデプロイが存在した場合は、デプロイを中止します
# 進行中のデプロイが終了後に該当JobをRe Runするとデプロイが再度開始されます。
#
# CODEDEPLOY_APP: CodeDeploy Application Name
# CODEDEPLOY_GROUP: CodeDeploy Deployment Group Name
#   see https://docs.aws.amazon.com/cli/latest/reference/deploy/list-deployments.html

DEPLOYMENT_ID=$(aws deploy list-deployments \
  --application-name ${CODEDEPLOY_APP} \
  --deployment-group-name ${CODEDEPLOY_GROUP} \
  --include-only-statuses InProgress \
  --query deployments \
  --output text)

if [ -z "${DEPLOYMENT_ID}" ];then
  echo "Inprogress deployment does not exist"
else
  echo "Warning: Inprogress deployment exists! Deployment will be terminated. Please Re-Run After Inprogress Deployment will be done."
  echo "deployment-inprogress=true" >> $GITHUB_OUTPUT
fi
```

describe-ecs-service.sh
```shell
#!/bin/bash -eux

# PRごとにECSサービスを作成する
# ecspresso deploy は事前にサービスが作成されている必要があるため、サービスを取得して
# ecspress create or deploy を分けて実行する。

SERVICE=$(aws ecs describe-services --cluster ${ECS_CLUSTER_NAME} --services ${ECS_SERVICE_NAME} | jq -r --arg ECS_SERVICE_NAME ${ECS_SERVICE_NAME} '.services[] | select(.serviceName == $ECS_SERVICE_NAME  and .status == "ACTIVE") | .serviceArn')

if [ -z "${SERVICE}" ];then
  echo "ECS Service isn't found."
  echo "service-exist=false" >> $GITHUB_OUTPUT
else
  echo "ECS Service already exists."
  echo $SERVICE
  echo "service-exist=true" >> $GITHUB_OUTPUT
fi
```

describe-target-group.sh
```shell
#!/bin/bash -eux

aws elbv2 describe-target-groups \
  --names freee-sign-reviewapp-pr-${PR_NUMBER}-tg \
  | jq '.TargetGroups[].TargetGroupArn'  \
  | xargs -I{} echo target_group_arn={} >> $GITHUB_OUTPUT
```

descript-cloudformation-stack.sh
```shell
#!/bin/bash -eux

# ReviewApp向けのAWSリソースの作成が必要かどうかをチェックします
# すでに構築がなされている場合は環境変数に TargetGroupのARNを出力します
STACK_NAME="ReviewappPR${PR_NUMBER}"
STACK=$(aws cloudformation describe-stacks | jq -r --arg STACK_NAME "${STACK_NAME}" '.Stacks[] | select(.StackName == $STACK_NAME)')

if [ -z "${STACK}" ];then
  echo "Stack isn't found. It will be created."
  echo "needs_stack=true" >> $GITHUB_OUTPUT
else
  echo "Stack already exists."
  echo "needs_stack=false" >> $GITHUB_OUTPUT
fi
```

set-aws-recource-arns.sh
```shell
#!/bin/bash -eux

names=(alb_dns_name route53_hosted_zone_id vpc_id alb_https_listener_arn)

for name in ${names[@]}
do
  aws ssm get-parameters --name "/ReviewApp/${name}" --with-decryption | jq '.Parameters[].Value' | xargs -I{} echo ${name}={} >> $GITHUB_ENV
done

PRIORITY_NUMBER=`aws elbv2 describe-rules --listener-arn $(aws ssm get-parameters --name "/ReviewApp/alb_https_listener_arn" --with-decryption \
                | jq -r '.Parameters[].Value')  | jq '.Rules[] | select(.IsDefault != true) | .Priority?|tonumber' | jq -s max`
echo listener_priority=`expr ${PRIORITY_NUMBER} + 1` >> $GITHUB_ENV
```