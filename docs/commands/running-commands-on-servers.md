---
title: 在服务器上运行命令
---

# 在服务器上运行命令

常用命令可使用[别名](../../configuration/aliases)。

## [在所有服务器上运行命令](#run-command-on-all-servers)

```bash
$ kamal app exec 'ruby -v'
App Host: 192.168.0.1
ruby 3.1.3p185 (2022-11-24 revision 1a6b16756e) [x86_64-linux]

App Host: 192.168.0.2
ruby 3.1.3p185 (2022-11-24 revision 1a6b16756e) [x86_64-linux]
```

## [在主服务器上运行命令](#run-command-on-primary-server)

```bash
$ kamal app exec --primary 'cat .ruby-version'
App Host: 192.168.0.1
3.1.3
```

## [在所有服务器上运行 Rails 命令](#run-rails-command-on-all-servers)

```bash
$ kamal app exec 'bin/rails about'
App Host: 192.168.0.1
About your application's environment
Rails version             7.1.0.alpha
Ruby version              ruby 3.1.3p185 (2022-11-24 revision 1a6b16756e) [x86_64-linux]
RubyGems version          3.3.26
Rack version              2.2.5
Middleware                ActionDispatch::HostAuthorization, Rack::Sendfile, ActionDispatch::Static, ActionDispatch::Executor, Rack::Runtime, Rack::MethodOverride, ActionDispatch::RequestId, ActionDispatch::RemoteIp, Rails::Rack::Logger, ActionDispatch::ShowExceptions, ActionDispatch::DebugExceptions, ActionDispatch::Callbacks, ActionDispatch::Cookies, ActionDispatch::Session::CookieStore, ActionDispatch::Flash, ActionDispatch::ContentSecurityPolicy::Middleware, ActionDispatch::PermissionsPolicy::Middleware, Rack::Head, Rack::ConditionalGet, Rack::ETag, Rack::TempfileReaper
Application root          /rails
Environment               production
Database adapter          sqlite3
Database schema version   20221231233303

App Host: 192.168.0.2
About your application's environment
Rails version             7.1.0.alpha
Ruby version              ruby 3.1.3p185 (2022-11-24 revision 1a6b16756e) [x86_64-linux]
RubyGems version          3.3.26
Rack version              2.2.5
Middleware                ActionDispatch::HostAuthorization, Rack::Sendfile, ActionDispatch::Static, ActionDispatch::Executor, Rack::Runtime, Rack::MethodOverride, ActionDispatch::RequestId, ActionDispatch::RemoteIp, Rails::Rack::Logger, ActionDispatch::ShowExceptions, ActionDispatch::DebugExceptions, ActionDispatch::Callbacks, ActionDispatch::Cookies, ActionDispatch::Session::CookieStore, ActionDispatch::Flash, ActionDispatch::ContentSecurityPolicy::Middleware, ActionDispatch::PermissionsPolicy::Middleware, Rack::Head, Rack::ConditionalGet, Rack::ETag, Rack::TempfileReaper
Application root          /rails
Environment               production
Database adapter          sqlite3
Database schema version   20221231233303
```

## [在主服务器上运行 Rails runner](#run-rails-runner-on-primary-server)

```bash
$ kamal app exec -p 'bin/rails runner "puts Rails.application.config.time_zone"'
UTC
```

## [通过 SSH 运行交互式命令](#run-interactive-commands-over-ssh)

你可以在服务器上运行交互式命令，例如 Rails 控制台或 bash 会话（默认在主服务器上，使用 `--hosts` 可连接其他服务器）。

在由最新应用镜像创建的新容器中启动 bash 会话：

```bash
kamal app exec -i bash
```

在当前正在运行的应用容器中启动 bash 会话：

```bash
kamal app exec -i --reuse bash
```

在由最新应用镜像创建的新容器中启动 Rails 控制台：

```bash
kamal app exec -i 'bin/rails console'
```

## [获取未修改的输出](#get-unmodified-output)

默认情况下，`exec` 会通过 SSHKit 的 capture 处理命令输出，会去掉首尾空白——包括尾部换行和 NUL 字节。这会破坏 `tar` 流等二进制输出。传入 `--raw` 可原样输出 stdout。它还会降低日志级别，使只写出命令本身的输出。

```bash
kamal app exec --raw 'tar c -C /rails/storage .' > storage.tar
```

`--raw` 不能与 `--interactive` 或 `--detach` 同时使用。
