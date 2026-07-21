---
# This file has been generated from the Kamal source, do not edit directly.
# Find the source of this file at lib/kamal/configuration/docs/accessory.yml in the Kamal repository.
title: 附属服务
---

# 附属服务

附属服务（accessories）可以在单台主机、一组主机或特定角色上启动。
这些主机不必出现在 Kamal 的 servers 配置中。

附属服务与主服务分开管理——部署时不会更新它们，
也没有零停机部署。

运行 `kamal accessory boot <accessory>` 可启动附属服务。
更多信息见 `kamal accessory --help`。

## [配置附属服务](#configuring-accessories)

首先在 `accessories` 中定义附属服务：

```yaml
accessories:
  mysql:
```

## [服务名称](#service-name)

用于服务标签，默认为 `<service>-<accessory>`，
其中 `<service>` 是根配置中的主服务名：

```yaml
    service: mysql
```

## [镜像](#image)

要使用的 Docker 镜像。
当根级镜像仓库不是 Docker Hub 时，请加上服务器前缀。
当与根级镜像仓库不同时，可直接定义 registry，或通过锚点定义。

```yaml
    image: mysql:8.0
```

## [镜像仓库](#registry)

默认情况下，附属服务使用 Docker Hub。
可通过此选项为每个附属服务指定不同的镜像仓库。
不要在 image 前再加该仓库服务器前缀。
若多个附属服务需要同一特定仓库，可使用锚点。

```yml
registry:
  <<: *specific-registry
```

更多信息见 [Docker 镜像仓库](../docker-registry)：

```yaml
    registry:
      ...
```

## [附属服务主机](#accessory-hosts)

指定 `host`、`hosts`、`role`、`roles`、`tag` 或 `tags` 之一：

```yaml
    host: mysql-db1
    hosts:
      - mysql-db1
      - mysql-db2
    role: mysql
    roles:
      - mysql
    tag: writer
    tags:
      - writer
      - reader
```

## [自定义命令](#custom-command)

若不使用默认命令，可设置在容器中运行的自定义命令：

```yaml
    cmd: "bin/mysqld"
```

## [端口映射](#port-mappings)

见 [https://docs.docker.com/network/](https://docs.docker.com/network/)，
并特别注意关于公开暴露端口的安全风险警告。

```yaml
    port: "127.0.0.1:3306:3306"
```

## [标签](#labels)

```yaml
    labels:
      app: myapp
```

## [选项](#options)

会以 `--<name> <value>` 的形式传给 Docker run 命令：

```yaml
    options:
      restart: always
      cpus: 2
```

## [环境变量](#environment-variables)

更多信息见[环境变量](../environment-variables)：

```yaml
    env:
      ...
```

## [复制文件](#copying-files)

你可以指定要挂载进容器的文件。

它们会从本地仓库上传到主机，再挂载进容器。
ERB 文件在复制前会先求值。

可使用字符串格式：`local:remote` 或 `local:remote:options`，
其中 options 可以是 `ro`（只读）或 `z`/`Z`（SELinux 标签）。

```yaml
    files:
      - config/my.cnf.erb:/etc/mysql/my.cnf
      - config/myoptions.cnf:/etc/mysql/myoptions.cnf:ro
      - config/certs:/etc/mysql/certs:ro,Z
```


也可使用哈希格式设置自定义 mode 与 ownership。

注意：设置 `owner` 需要 root 权限：

```yaml
    files:
      - local: config/secret.key
        remote: /etc/mysql/secret.key
        mode: "0600"
        owner: "mysql:mysql"
      - local: config/ca-cert.pem
        remote: /etc/mysql/certs/ca-cert.pem
        mode: "0644"
        owner: "1000:1000"
        options: "Z"
```

## [目录](#directories)

你可以指定要挂载进容器的目录。挂载前会在主机上创建这些目录。

可使用字符串格式：`local:remote` 或 `local:remote:options`，
其中 options 可以是 `ro`（只读）或 `z`/`Z`（SELinux 标签）。

```yaml
    directories:
      - mysql-logs:/var/log/mysql
      - mysql-data:/var/lib/mysql:z
```


也可使用哈希格式设置自定义 mode 与 ownership。

注意：设置 `owner` 需要 root 权限：

```yaml
    directories:
      - local: mysql-data
        remote: /var/lib/mysql
        mode: "0750"
        owner: "mysql:mysql"
      - local: mysql-logs
        remote: /var/log/mysql
        mode: "0755"
        options: "z"
```

## [卷](#volumes)

除文件和目录外，其他要挂载的卷。
挂载前不会创建或复制它们：

```yaml
    volumes:
      - /path/to/mysql-logs:/var/log/mysql
```

## [网络](#network)

附属服务将加入的网络。

默认为 kamal：

```yaml
    network: custom
```

## [代理](#proxy)

可以在 Kamal 代理后运行附属服务。更多信息见[代理](../proxy)。

```yaml
    proxy:
      ...
```
