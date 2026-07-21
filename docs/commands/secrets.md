---
title: Secrets
---

# kamal secrets

```bash
$ kamal secrets
Commands:
  kamal secrets extract                                                     # 从 fetch 调用的结果中提取单个密钥
  kamal secrets fetch [SECRETS...] --account=ACCOUNT -a, --adapter=ADAPTER  # 从保管库获取密钥
  kamal secrets help [COMMAND]                                              # 说明子命令或某个具体子命令
  kamal secrets print                                                       # 打印密钥（用于调试）
```

用这些命令从常见密码管理器读取密钥（目前支持 1Password、LastPass 和 Bitwarden）。

辅助命令会处理登录、索要密码，并高效获取密钥：

设计上配合 `.kamal/secrets` 中的[命令替换](https://github.com/bkeepers/dotenv?tab=readme-ov-file#command-substitution)使用：

```shell
# .kamal/secrets

SECRETS=$(kamal secrets fetch ...)

REGISTRY_PASSWORD=$(kamal secrets extract REGISTRY_PASSWORD $SECRETS)
DB_PASSWORD=$(kamal secrets extract DB_PASSWORD $SECRETS)
```

## 1Password

首先安装并配置 [1Password CLI](https://developer.1password.com/docs/cli/get-started/)。

使用适配器 `1password`：

```bash
# 从保管库 MyVault 的条目 MyItem 获取
kamal secrets fetch --adapter 1password --account myaccount --from MyVault/MyItem REGISTRY_PASSWORD DB_PASSWORD

# 从保管库 MyVault 中条目 MyItem 的分区获取
kamal secrets fetch --adapter 1password --account myaccount --from MyVault/MyItem common/REGISTRY_PASSWORD production/DB_PASSWORD

# 从独立条目 MyItem、MyItem2 获取
kamal secrets fetch --adapter 1password --account myaccount --from MyVault MyItem/REGISTRY_PASSWORD MyItem2/DB_PASSWORD

# 从多个保管库获取
kamal secrets fetch --adapter 1password --account myaccount MyVault/MyItem/REGISTRY_PASSWORD MyVault2/MyItem2/DB_PASSWORD

# 以下三种方式均可提取密钥
kamal secrets extract REGISTRY_PASSWORD <SECRETS-FETCH-OUTPUT>
kamal secrets extract MyItem/REGISTRY_PASSWORD <SECRETS-FETCH-OUTPUT>
kamal secrets extract MyVault/MyItem/REGISTRY_PASSWORD <SECRETS-FETCH-OUTPUT>
```

## LastPass

首先安装并配置 [LastPass CLI](https://github.com/lastpass/lastpass-cli)。

使用适配器 `lastpass`：

```bash
# 获取密码
kamal secrets fetch --adapter lastpass --account email@example.com REGISTRY_PASSWORD DB_PASSWORD

# 从文件夹获取密码
kamal secrets fetch --adapter lastpass --account email@example.com --from MyFolder REGISTRY_PASSWORD DB_PASSWORD

# 从多个文件夹获取密码
kamal secrets fetch --adapter lastpass --account email@example.com MyFolder/REGISTRY_PASSWORD MyFolder2/DB_PASSWORD

# 提取密钥
kamal secrets extract REGISTRY_PASSWORD <SECRETS-FETCH-OUTPUT>
kamal secrets extract MyFolder/REGISTRY_PASSWORD <SECRETS-FETCH-OUTPUT>
```

## Bitwarden

首先安装并配置 [Bitwarden CLI](https://bitwarden.com/help/cli/)。

使用适配器 `bitwarden`：

```bash
# 获取密码
kamal secrets fetch --adapter bitwarden --account email@example.com REGISTRY_PASSWORD DB_PASSWORD

# 从条目获取密码
kamal secrets fetch --adapter bitwarden --account email@example.com --from MyItem REGISTRY_PASSWORD DB_PASSWORD

# 从多个条目获取密码
kamal secrets fetch --adapter bitwarden --account email@example.com MyItem/REGISTRY_PASSWORD MyItem2/DB_PASSWORD

# 提取密钥
kamal secrets extract REGISTRY_PASSWORD <SECRETS-FETCH-OUTPUT>
kamal secrets extract MyItem/REGISTRY_PASSWORD <SECRETS-FETCH-OUTPUT>
```

## Bitwarden Secrets Manager

首先安装并配置 [Bitwarden Secrets Manager CLI](https://bitwarden.com/help/secrets-manager-cli/#download-and-install)。

使用适配器 `bitwarden-sm`：

```bash
# 获取机器账号有权访问的全部密钥
kamal secrets fetch --adapter bitwarden-sm all

# 从项目获取密钥
kamal secrets fetch --adapter bitwarden-sm MyProjectID/all

# 提取密钥
kamal secrets extract REGISTRY_PASSWORD <SECRETS-FETCH-OUTPUT>
```

## AWS Secrets Manager

首先安装并配置 [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)。

使用适配器 `aws_secrets_manager`：

```bash
# 从包含 "REGISTRY_PASSWORD" 的 "myapp" 获取密钥字符串
kamal secrets fetch --adapter aws_secrets_manager --account default myapp

# 以下两种方式都会从包含 "REGISTRY_PASSWORD" 的 "myapp/staging" 获取密钥字符串
kamal secrets fetch --adapter aws_secrets_manager --account default myapp/staging
kamal secrets fetch --adapter aws_secrets_manager --account default --from myapp staging

# 以下三种方式均可提取密钥
kamal secrets extract REGISTRY_PASSWORD <SECRETS-FETCH-OUTPUT>
kamal secrets extract myapp/REGISTRY_PASSWORD <SECRETS-FETCH-OUTPUT>
kamal secrets extract myapp/staging/REGISTRY_PASSWORD <SECRETS-FETCH-OUTPUT>
```

**注意：** `--account` 选项应设为你的 AWS CLI 配置文件名，通常为 `default`。请确保 AWS CLI 已配置访问 AWS Secrets Manager 所需的权限。

## Doppler

首先安装并配置 [Doppler CLI](https://docs.doppler.com/docs/install-cli)。

使用适配器 `doppler`：

```bash
# 获取密码
kamal secrets fetch --adapter doppler --from my-project/prd REGISTRY_PASSWORD DB_PASSWORD

# 也支持 project/config 这种写法
kamal secrets fetch --adapter doppler my-project/prd/REGISTRY_PASSWORD my-project/prd/DB_PASSWORD

# 提取密钥
kamal secrets extract REGISTRY_PASSWORD <SECRETS-FETCH-OUTPUT>
kamal secrets extract DB_PASSWORD <SECRETS-FETCH-OUTPUT>
```

Doppler 将密钥组织在 "projects"（如 `my-awesome-project`）和 "configs"（如 `prod`、`stg` 等）中，定义 `--from` 时使用 `project/config` 模式。

doppler 适配器不使用 `--account` 选项；若提供会被忽略。

## GCP Secret Manager

首先安装并配置 [gcloud CLI](https://cloud.google.com/sdk/gcloud/reference/secrets)。

`--account` 标志选择在 `gcloud` 中配置的账号，`--from` 标志指定要使用的 **GCP 项目 ID**。可在 `--account` 和 `--from` 中使用字符串 `default`，分别表示使用 `gcloud` 的默认凭证和默认项目。

使用适配器 `gcp`：

```bash
# 用显式项目名、凭证和密钥版本获取密钥：
kamal secrets fetch --adapter=gcp --account=default --from=default my-secret/latest

# 项目名可作为密钥名的前缀，而不使用 --from：
kamal secrets fetch --adapter=gcp --account=default default/my-secret/latest

# 默认使用 'latest' 版本，因此也可以省略：
kamal secrets fetch --adapter=gcp --account=default default/my-secret

# 若使用默认项目，前缀也可以完全省略，这是用默认项目与凭证、
# 以及密钥最新版本获取密钥的最简方式：
kamal secrets fetch --adapter=gcp --account=default my-secret

# 可同时获取多个密钥。
# 从项目 my-project 获取 my-secret 和 another-secret：
kamal secrets fetch --adapter=gcp \
  --account=default \
  --from=my-project \
  my-secret another-secret

# 可同时从多个项目获取密钥。
# 从多个项目获取，用 default 表示默认项目：
kamal secrets fetch --adapter=gcp \
  --account=default \
  default/my-secret my-project/another-secret

# 可获取特定密钥版本。
# 获取默认项目中密钥 my-secret 的版本 123（默认行为是获取 latest）
kamal secrets fetch --adapter=gcp \
  --account=default \
  default/my-secret/123

# 也可使用非默认凭证。
# 使用 user@example.com 凭证获取密钥：
kamal secrets fetch --adapter=gcp \
  --account=user@example.com \
  my-secret

# 支持服务账号模拟与委托链。
# 以 user@example.com 身份、通过 delegate@example.com 委托、
# 模拟 service-account@example.com 获取密钥
kamal secrets fetch --adapter=gcp \
  --account="user@example.com|delegate@example.com,service-account@example.com" \
  my-secret
```

## Passbolt

首先安装并配置 [Passbolt CLI](https://github.com/passbolt/go-passbolt-cli)。

Passbolt 将密钥组织在文件夹中（如 `coolfolder`），文件夹可以嵌套（如 `coolfolder/prod`、`coolfolder/stg` 等）。访问这些文件夹中的密钥有两种方式：

1. 使用 `--from` 选项指定文件夹路径 `--from coolfolder`
2. 在密钥名前加上文件夹路径前缀 `coolfolder/REGISTRY_PASSWORD`

使用适配器 `passbolt`：

```bash
# 从根目录（无文件夹）获取密码
kamal secrets fetch --adapter passbolt REGISTRY_PASSWORD DB_PASSWORD

# 使用 --from 从文件夹获取密码
kamal secrets fetch --adapter passbolt --from coolfolder REGISTRY_PASSWORD DB_PASSWORD

# 使用 --from 从嵌套文件夹获取密码
kamal secrets fetch --adapter passbolt --from coolfolder/subfolder REGISTRY_PASSWORD DB_PASSWORD

# 通过在密钥名前加文件夹路径前缀获取密码
kamal secrets fetch --adapter passbolt coolfolder/REGISTRY_PASSWORD coolfolder/DB_PASSWORD

# 从多个文件夹获取密码
kamal secrets fetch --adapter passbolt coolfolder/REGISTRY_PASSWORD otherfolder/DB_PASSWORD

# 提取密钥值
kamal secrets extract REGISTRY_PASSWORD <SECRETS-FETCH-OUTPUT>
kamal secrets extract DB_PASSWORD <SECRETS-FETCH-OUTPUT>
```

passbolt 适配器不使用 `--account` 选项；若提供会被忽略。
