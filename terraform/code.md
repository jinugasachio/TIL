# 書き方

- `dynamic` の中で `for_each` を使う
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
