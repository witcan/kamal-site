---
# This file has been generated from the Kamal source, do not edit directly.
# Find the source of this file at lib/kamal/configuration/docs/ssh.yml in the Kamal repository.
title: SSH 配置
---

# SSH 配置

Kamal 通过 SSH 连接主机并在其上执行命令。
默认会尝试以 root 用户连接 22 端口。

如果使用非 root 用户，可能需要在搭配 Kamal 使用前手动初始化服务器。在 Ubuntu 上可以这样做：

```shell
sudo apt update
sudo apt upgrade -y
sudo apt install -y docker.io curl git
sudo usermod -a -G docker app
```

## [SSH 选项](#ssh-options)

这些选项写在配置文件的 `ssh` 键下。

```yaml
ssh:
```

## [SSH 用户](#the-ssh-user)

默认为 `root`：

```yaml
  user: app
```

## [SSH 端口](#the-ssh-port)

默认为 22：

```yaml
  port: "2222"
```

## [代理主机](#proxy-host)

格式为 &lt;host&gt; 或 &lt;user&gt;@&lt;host&gt;：

```yaml
  proxy: root@proxy-host
```

## [代理命令](#proxy-command)

自定义代理命令，旧版 SSH 可能需要：

```yaml
  proxy_command: "ssh -W %h:%p user@proxy"
```

## [日志级别](#log-level)

默认为 `fatal`。若遇到 SSH 连接问题，可设为 `debug`。

```yaml
  log_level: debug
```

## [仅使用密钥](#keys-only)

设为 `true` 时，只使用 `keys` 和 `key_data` 参数中的私钥，
即使 ssh-agent 提供了更多身份。该选项适用于
ssh-agent 提供了多种身份，或你需要覆盖所有身份并强制使用某一个的场景。

```yaml
  keys_only: false
```

## [密钥文件](#keys)

用于公钥与基于主机认证的私钥文件名数组：

```yaml
  keys: [ "~/.ssh/id.pem" ]
```

## [密钥数据](#key-data)

字符串数组，每个元素为密钥名称。

```yaml
  key_data:
    - SSH_PRIVATE_KEY
```

也可以直接提供 PEM 格式的原始私钥，但该方式已弃用。

```yaml
  key_data:
    - "-----BEGIN OPENSSH PRIVATE KEY----- ..."
```

## [配置](#config)

设为 true 时加载默认 OpenSSH 配置文件（~/.ssh/config、
/etc/ssh_config）；设为 false 则忽略配置文件；也可设为文件路径
（或路径数组）以加载指定配置。默认为 true。

```yaml
  config: [ "~/.ssh/myconfig" ]
```

## [转发 Agent](#forward-agent)

是否将本地 SSH agent 转发到远程主机。默认为
true（sshkit 的默认值）。通过不支持 agent 转发的跳板机或隧道
连接时（例如 Cloudflare Access for Infrastructure 的 SSH），
请设为 false。

```yaml
  forward_agent: false
```
