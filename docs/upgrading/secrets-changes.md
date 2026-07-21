---
title: 密钥变更
---

# Kamal 2：密钥变更

密钥已从 `.env`/`.env.rb` 迁移到 `.kamal/secrets`。

若使用 destination，密钥会先从 `.kamal/secrets.<DESTINATION>` 读取；找不到则使用 `.kamal/secrets-common`。

## [插值密钥](#interpolating-secrets)

`kamal envify` 和 `kamal env` 命令已移除，密钥不再有独立的生命周期。

若曾用 `kamal envify` 生成密钥，可改用 dotenv 的[命令](https://github.com/bkeepers/dotenv?tab=readme-ov-file#command-substitution)和[变量](https://github.com/bkeepers/dotenv?tab=readme-ov-file#variable-substitution)替换。

替换会在运行需要密钥的 Kamal 命令时按需执行。

```
# .kamal/secrets

SECRET_FROM_ENV=$SECRET_FROM_ENV
SECRET_FROM_COMMAND=$(op read ...)
```

更多细节见[这里](../../configuration/environment-variables#using-kamal-secrets)。

## [deploy.yml 中的环境变量](#environment-variables-in-deployyml)

在 Kamal 1 中，`.env` 会加载到环境里，因此可在 `deploy.yml` 中通过 ERB 引用其中的值。Kamal 2 不再如此。`.kamal/secrets` 中的值也不会被加载。

Kamal 1：

```
# .env
SERVER_IP=127.0.0.1

# config/deploy.yml
servers
  - <%= ENV["SERVER_IP"] %>
```

要在 Kamal 2 中实现同样效果，可手动加载 `.env`。

Kamal 2：

```
# .env
SERVER_IP=127.0.0.1

# config/deploy.yml

<% require "dotenv"; Dotenv.load(".env") %>

servers
  - <%= ENV["SERVER_IP"] %>
```
