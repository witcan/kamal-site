---
title: "Kamal 2：升级指南"
---

# Kamal 2：升级指南

Kamal 1 与 Kamal 2 之间有一些重要差异。

- Traefik 代理已被 [kamal-proxy 取代](../proxy-changes)。
- Kamal 会在[自定义 Docker 网络](../network-changes)中运行所有容器。
- 有一些不向后兼容的[配置变更](../configuration-changes)。
- 向容器传递密钥的方式[已改变](../secrets-changes)。

若仍想使用 Traefik，可将其作为附属服务运行；详见[这里](../continuing-to-use-traefik)。

## [升级步骤](#upgrade-steps)

### 升级到 Kamal 1.9.x

若计划对服务器做原地升级，应先升级到 Kamal 1.9，因为它支持降级。

若直接使用 gem，可运行：

```bash
gem install kamal --version 1.9.0
```

确认可以用 Kamal 1.9 部署你的应用。

### 升级到 Kamal 2

若直接使用 gem，运行：

```bash
gem install kamal
```

### 修改配置

需要将配置[转换为](../configuration-changes)与 Kamal 2 兼容。

可通过运行以下命令验证新配置是否有效：

```bash
$ kamal config
```

若有多个目标环境，应分别测试每个环境的配置：

```bash
$ kamal config -d staging
$ kamal config -d beta
```

### 从 .env 迁移到 .kamal/secrets

按[这里](../secrets-changes)的步骤操作。

### 确认容器端口

默认应用端口[已从 3000 改为 80](https://kamal-deploy.org/docs/upgrading/configuration-changes/#traefik-to-proxy)；若未使用 80 端口，需要指定 `app_port` 或更新 `EXPOSE` 端口。

## [原地升级](#in-place-upgrades)

**警告：如有可能，请先在非生产环境测试。**

### 升级

然后可这样升级：

```
$ kamal upgrade [-d <DESTINATION>]
```

每个目标环境需分别执行。

`kamal upgrade` 命令会：

1. 停止并移除 Traefik 代理。
2. 若不存在则创建 `kamal` Docker 网络。
3. 在新网络中启动 `kamal-proxy` 容器。
4. 在新网络中重启当前已部署版本的应用容器。
5. 让 `kamal-proxy` 将流量发往该容器。
6. 在新网络中重启全部附属服务。

### 避免停机

若应用跑在多台服务器上且希望避免停机，可做滚动升级：

```bash
$ kamal upgrade --rolling [-d <DESTINATION>]
```

步骤与上面相同，但按主机逐台进行。

也可以按主机运行该命令：

```bash
$ kamal upgrade -h 127.0.0.1[,127.0.0.2]
```

还可配合 [pre-proxy-reboot](../hooks/pre-proxy-reboot.md) 和 [post-proxy-reboot](../hooks/post-proxy-reboot.md) 钩子，手动从上游负载均衡器移除服务器，确保升级过程中不丢请求。

### 降级

若想撤销更改并回到 Kamal 1.9：

1. 卸载 Kamal 2.0。
2. 运行 `kamal version` 确认当前为 Kamal 1.9。
3. 运行 `kamal downgrade` 命令。选项与 `kamal upgrade` 相同，流程相反。

升级与降级命令可对已升级或已降级的服务器重复运行。
