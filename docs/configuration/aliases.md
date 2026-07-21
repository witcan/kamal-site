---
# This file has been generated from the Kamal source, do not edit directly.
# Find the source of this file at lib/kamal/configuration/docs/alias.yml in the Kamal repository.
title: 别名
---

# 别名

别名是 Kamal 命令的快捷方式。

例如，对 Rails 应用，你可能会这样打开控制台：

```shell
kamal app exec -i --reuse "bin/rails console"
```

定义别名后：

```yaml
aliases:
  console: app exec -i --reuse "bin/rails console"
```

就可以这样打开控制台：

```shell
kamal console
```

## [配置别名](#configuring-aliases)

别名定义在根配置的 `aliases` 键下。

每个别名都有名称，且只能包含小写字母、数字、连字符和下划线：

```yaml
aliases:
  uname: app exec -p -q -r web "uname -a"
```


别名可以通过 `-d` 标志指定目标环境：

```yaml
  staging_deploy: deploy -d staging
```
