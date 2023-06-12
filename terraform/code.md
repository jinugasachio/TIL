# 書き方

### `dynamic` の中で `for_each` を使う
```hcl
locals {
  tags = {
    staging = {
      hoge = "hoge"
    }
    production = {
      hoge = "hoge"
    }
  }
}

resource "example" "example" {
  dynamic "tag" {
    for_each = local.tags
		
    content {
      env  = tag.key # each ではなくブロック名（tag）で参照できる
      hoge = tag.value.hoge
    }
  }
}

# iterator 使う場合
resource "example" "example" {
  dynamic "tag" {
    for_each = local.tags
    iterator = item
		
    content {
      env  = item.key
      hoge = item.value.hoge
    }
  }
}
```

### 特定の属性の値をもつオブジェクトのみ `for_each` で回す
```hcl
locals {
  slos = {
    slo_1 = {
      type = "metric"
      name = "slo_1"
    }
    slo_2 = {
      type = "monitor"
      name = "slo_2"
    }
    slo_3 = {
      type = "metric"
      name = "slo_3"
    }
  }
}

resource "datadog_service_level_objective" "default" {
  # slo_2 がフィルタリングされる
  for_each = { for key, attributes in local.slos : key => attributes if attributes.type == "monitor" }
  
  name = each.value.name
}
```

### `for_each` に `map` の `list` を渡して回す（[参考](https://qiita.com/minamijoyo/items/3785cad0283e4eb5a188#%E8%A7%A3%E7%AD%94)）
```hcl
locals {
  images = [
    { name = "foo", image = "alpine" },
    { name = "bar", image = "debian" },
  ]
}

resource "docker_container" "this" {
  for_each = { for i in local.images : i.name => i }
 
  name     = each.value.name
  image    = each.value.image
}
```

### ブロックの生成を `dynamic` と `for_each` で制御する
```hcl
  # can を使うパターン
  dynamic "example" {
    # each.value.example が何かしらの値を返せば、trueに評価され [1] が返り exmple ブロックが生成される
    for_each = can(each.value.example) ? [1] : []

    content {
      action {
        type  = each.value.example.action.type
        class = each.value.example.action.class
      }
    }
  }
  
  # == で評価するパターン
  dynamic "example" {
    for_each = each.value.example_create == true ? [1] : []

    content {
      action {
        type  = each.value.example.action.type
        class = each.value.example.action.class
      }
    }
  }
```

# TIPS
- `lookup` は古いので `try` を使うようにする（[参考](https://discuss.hashicorp.com/t/try-vs-lookup-preference/47939/2)）

