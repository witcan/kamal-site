---
# This file has been generated from the Kamal source, do not edit directly.
# Find the source of this file at lib/kamal/configuration/docs/configuration.yml in the Kamal repository.
title: Kamal 配置
---

# Kamal 配置

配置从 `config/deploy.yml` 读取。

## [目标环境（Destinations）](#destinations)

运行命令时，可以用 `-d` 标志指定目标环境，
例如 `kamal deploy -d staging`。

此时还会从 `config/deploy.staging.yml` 读取配置，
并与基础配置合并。

## [扩展](#extensions)

Kamal 不会接受配置文件中无法识别的键。

不过，你可能希望用 YAML 锚点与别名声明配置块，
以避免重复。

可以用 `x-` 前缀标记配置段，表示这是扩展。
Kamal 会忽略扩展，不会报错。

## [服务名称](#the-service-name)

这是必填项。用作容器名称前缀。

```yaml
service: myapp
```

## [Docker 镜像名称](#the-docker-image-name)

镜像会推送到已配置的镜像仓库。

```yaml
image: my-image
```

## [标签](#labels)

添加到容器的额外标签：

```yaml
labels:
  my-label: my-value
```

## [卷](#volumes)

挂载到容器的额外卷：

```yaml
volumes:
  - /path/on/host:/path/in/container:ro
```

## [镜像仓库](#registry)

Docker 镜像仓库配置，见 [Docker 镜像仓库](../docker-registry)：

```yaml
registry:
  ...
```

## [服务器](#servers)

要部署到的服务器，可选择自定义角色，见[服务器](../servers)：

```yaml
servers:
  ...
```

## [环境变量](#environment-variables)

见[环境变量](../environment-variables)：

```yaml
env:
  ...
```

## [静态资源路径](#asset-path)

用于跨部署的资源桥接，默认为 `nil`。

若 CSS 或 JS 有变更，新容器上可能仍收到对旧版本资源的请求，
反之亦然。

为避免 404，可指定资源路径。
Kamal 会用包含两套文件的映射卷替换容器中的该路径。
这要求文件名在内容变化时也变化
（例如在名称中包含内容哈希）。

配置时设置资源所在路径即可。

也可以在冒号后指定挂载选项，例如 `ro` 表示只读，
或 `z`/`Z` 用于 SELinux 标签。

```yaml
asset_path: /path/to/assets
```

## [钩子路径](#hooks-path)

钩子路径，默认为 `.kamal/hooks`。
更多信息见[钩子](/docs/hooks)：

```yaml
hooks_path: /user_home/kamal/hooks
```

## [钩子输出](#hook-output)

钩子输出的可见性。可全局设置，也可按钩子设置。
CLI 标志（`-v`、`-q`）会覆盖这些设置。

- `:quiet` - 隐藏钩子输出
- `:verbose` - 显示钩子输出

未设置时，钩子输出跟随 CLI 的详细程度标志。

注意：无论设置如何，失败的钩子总会在错误信息中显示输出。

全局设置（适用于所有钩子）：

```yaml
hooks_output: :verbose
```

或按钩子设置：

```yaml
hooks_output:
  pre-deploy: :verbose
  pre-build: :quiet
```

## [密钥路径](#secrets-path)

密钥路径，默认为 `.kamal/secrets`。
Kamal 会先查找 `<secrets_path>-common`，再查找 `<secrets_path>`。
使用 destination 时，则先查找 `<secrets_path>-common`，再查找
`<secrets_path>.<destination>`。后者会覆盖前者。

```yaml
secrets_path: /user_home/kamal/secrets
```

## [错误页面](#error-pages)

相对于应用根目录的目录，供代理提供错误页面。
每个页面以对应的 HTTP 状态码命名，例如 404.html、500.html、
502.html、503.html、504.html。

```yaml
error_pages_path: public
```

## [要求指定目标环境](#require-destinations)

部署是否必须指定 destination，默认为 `false`：

```yaml
require_destination: true
```

## [主角色](#primary-role)

默认为 `web`；若没有 web 角色，可修改：

```yaml
primary_role: workers
```

## [允许空角色](#allowing-empty-roles)

是否允许没有服务器的角色。默认为 `false`：

```yaml
allow_empty_roles: false
```

## [保留容器数](#retain-containers)

保留多少个旧容器和镜像，默认为 5：

```yaml
retain_containers: 3
```

## [最低版本](#minimum-version)

部署此配置所需的最低 Kamal 版本，默认为 `nil`：

```yaml
minimum_version: 1.3.0
```

## [就绪延迟](#readiness-delay)

容器运行后等待其就绪的秒数，默认 7。

仅适用于不使用代理、也未指定健康检查的容器：

```yaml
readiness_delay: 4
```

## [部署超时](#deploy-timeout)

等待容器就绪的最长时间，默认 30：

```yaml
deploy_timeout: 10
```

## [排空超时](#drain-timeout)

等待容器排空的最长时间，默认 30：

```yaml
drain_timeout: 10
```

## [停止超时](#stop-timeout)

发送 SIGTERM 后等待容器停止的最长时间；默认值对非代理角色为
drain_timeout，对使用代理的角色为 10 秒（Docker 默认）。
可按角色覆盖：

```yaml
stop_timeout: 30
```

## [运行目录](#run-directory)

主机上存放 kamal 运行时文件的目录，默认为 `.kamal`：

```yaml
run_directory: /etc/kamal
```

## [SSH 选项](#ssh-options)

见 [SSH](../ssh)：

```yaml
ssh:
  ...
```

## [构建器选项](#builder-options)

见[构建器](../builders)：

```yaml
builder:
  ...
```

## [附属服务](#accessories)

在 Docker 中运行的附加服务，见[附属服务](../accessories)：

```yaml
accessories:
  ...
```

## [代理](#proxy)

kamal-proxy 的配置，见[代理](../proxy)：

```yaml
proxy:
  ...
```

## [SSHKit](#sshkit)

见 [SSHKit](../sshkit)：

```yaml
sshkit:
  ...
```

## [启动选项](#boot-options)

见[启动](../booting)：

```yaml
boot:
  ...
```

## [日志](#logging)

Docker 日志配置，见[日志](../logging)：

```yaml
logging:
  ...
```

## [输出](#output)

配置输出记录器（OTel、文件），见[输出](../output)：

```yaml
output:
  ...
```

## [别名](#aliases)

别名配置，见[别名](../aliases)：

```yaml
aliases:
  ...
```
