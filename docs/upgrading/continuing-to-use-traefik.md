---
title: "Kamal 2：继续使用 Traefik"
---

# Kamal 2：继续使用 Traefik

Kamal 2 需要 kamal-proxy，但如有需要仍可继续使用 Traefik。

可以将其作为 Kamal 附属服务运行，请求先经 Traefik，再转发到 kamal-proxy。

## 设置 kamal-proxy 启动配置

需要修改 kamal-proxy 的默认启动配置，使：

1. 不在主机上发布端口。
2. 添加 Traefik 将其路由到自身所需的标签。

添加一个 [pre-deploy 钩子](../../hooks/pre-deploy) 供 Traefik 使用：

```shell
#!/bin/sh
kamal proxy boot_config set \
  --publish false \
  --docker_options label=traefik.http.services.kamal_proxy.loadbalancer.server.scheme=http \
                   label=traefik.http.routers.kamal_proxy.rule=PathPrefix\(\`/\`\)
```

可将 `kamal proxy boot_config set` 命令加入 [pre-deploy 钩子](../../hooks/pre-deploy)。这样首次部署到某台主机时配置会正确设置。

## 创建附属服务

将 Traefik 作为附属服务加入 `config/deploy.yml`，并绑定到主机端口。

```yaml
accessories:
  traefik:
    service: traefik
    image: traefik:v2.10
    port: 80
    cmd: "--providers.docker"
    options:
      volume:
        - "/var/run/docker.sock:/var/run/docker.sock"
    roles:
      - web
```

## 与 Traefik 一起运行

调用 `kamal setup` 时会启动 Traefik 附属服务，调用 pre-deploy 钩子更新 kamal-proxy 的启动配置，再启动 kamal-proxy 和应用。

请求路径为：Traefik → kamal-proxy → 你的应用。

```
$ docker ps
CONTAINER ID   IMAGE                                                                     COMMAND                  CREATED              STATUS              PORTS                               NAMES
3729c50d9d94   registry:4443/app_with_traefik:30482914d55f9ca5e4302dd2d050e424d29d8f74   "/docker-entrypoint.…"   11 seconds ago       Up 10 seconds       80/tcp                              app_with_traefik-web-30482914d55f9ca5e4302dd2d050e424d29d8f74
3c87e1c649e3   basecamp/kamal-proxy:v0.4.0                                               "kamal-proxy run"        12 seconds ago       Up 11 seconds       80/tcp, 443/tcp                     kamal-proxy
609a18d8ecd7   traefik:v2.10                                                             "/entrypoint.sh --pr…"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, :::80->80/tcp   traefik
```

## 在已运行 kamal-proxy 的主机上切换

若已经在运行 kamal-proxy，需要：

1. 手动运行部署钩子中的 `kamal proxy boot_config set` 命令。
2. 运行 `kamal proxy reboot` 以应用这些启动配置更改。
3. 运行 `kamal accessory boot traefik` 启动 Traefik。
