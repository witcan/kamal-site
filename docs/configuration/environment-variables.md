---
# This file has been generated from the Kamal source, do not edit directly.
# Find the source of this file at lib/kamal/configuration/docs/env.yml in the Kamal repository.
title: 环境变量
---

# 环境变量

环境变量可以直接写在 Kamal 配置中，也可以从 `.kamal/secrets` 读取。

## [从配置中读取环境变量](#reading-environment-variables-from-the-configuration)

环境变量可以直接写在配置文件中。

部署时会传给 `docker run` 命令。

```yaml
env:
  DATABASE_HOST: mysql-db1
  DATABASE_PORT: 3306
```

## [密钥（Secrets）](#secrets)

Kamal 使用 dotenv 自动从配置的密钥文件加载环境变量。

所有目标环境共用的密钥可写在 `.kamal/secrets-common` 中。Kamal 会先查找
`.kamal/secrets-common`，再查找 `.kamal/secrets`，后者会覆盖前者中的同名值。

若使用了 destination，Kamal 会先查找 `.kamal/secrets-common`，再查找
`.kamal/secrets.<destination>`。选定 destination 时不会读取无 destination 的 `.kamal/secrets` 文件。

该文件可用于设置 `KAMAL_REGISTRY_PASSWORD` 或数据库密码等变量。
密钥文件中支持变量替换和命令替换。

```shell
KAMAL_REGISTRY_PASSWORD=$KAMAL_REGISTRY_PASSWORD
RAILS_MASTER_KEY=$(cat config/master.key)
```

也可以使用[密钥辅助命令](../../commands/secrets)对接常见密码管理器。

```shell
SECRETS=$(kamal secrets fetch ...)

REGISTRY_PASSWORD=$(kamal secrets extract REGISTRY_PASSWORD $SECRETS)
DB_PASSWORD=$(kamal secrets extract DB_PASSWORD $SECRETS)
```

若直接把密钥写在 `.kamal/secrets` 中，请确保该文件不会提交到版本控制。

要传入密钥，应把它们列在 `secret` 键下。这样做时，
其他变量需要移到 `clear` 键下。

与明文值不同，密钥不会直接传给容器，
而是保存在主机上的 env 文件中：

```yaml
env:
  clear:
    DB_USER: app
  secret:
    - DB_PASSWORD
```

## [别名密钥](#aliased-secrets)

也可以用 `:` 分隔符把密钥别名到其他密钥。

当环境变量名与密钥名不同时很有用。例如，你需要在两处定义环境变量 `DB_PASSWORD`，
但不同上下文中的值不同。

```shell
SECRETS=$(kamal secrets fetch ...)

MAIN_DB_PASSWORD=$(kamal secrets extract MAIN_DB_PASSWORD $SECRETS)
SECONDARY_DB_PASSWORD=$(kamal secrets extract SECONDARY_DB_PASSWORD $SECRETS)
```

```yaml
env:
  secret:
    - DB_PASSWORD:MAIN_DB_PASSWORD
  tags:
    secondary_db:
      secret:
        - DB_PASSWORD:SECONDARY_DB_PASSWORD
accessories:
  main_db_accessory:
    env:
      secret:
        - DB_PASSWORD:MAIN_DB_PASSWORD
  secondary_db_accessory:
    env:
      secret:
        - DB_PASSWORD:SECONDARY_DB_PASSWORD
```

## [标签](#tags)

标签用于为特定主机添加额外环境变量。
如何给主机打标签见[服务器](../servers)。

标签只允许出现在顶层 env 配置中（即不能写在角色专属的 env 下）。

环境变量可以像上文一样用 secret 和 clear 分别指定。

```yaml
env:
  tags:
    <tag1>:
      MYSQL_USER: monitoring
    <tag2>:
      clear:
        MYSQL_USER: readonly
      secret:
        - MYSQL_PASSWORD
```

## [配置示例](#example-configuration)

```yaml
env:
  clear:
    MYSQL_USER: app
  secret:
    - MYSQL_PASSWORD
  tags:
    monitoring:
      MYSQL_USER: monitoring
    replica:
      clear:
        MYSQL_USER: readonly
      secret:
        - READONLY_PASSWORD
```
