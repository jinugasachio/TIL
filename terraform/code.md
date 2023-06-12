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
  # slo_2 のみをフィルタリングして回す
  for_each = { for slo_name, attributes in var.slos : slo_name => attributes if attributes.type == "monitor" }
  
  name = each.value.name
}
```
