# 开发日志

## 2026-08-28

- 阅读 Chatwoot 官方开发文档和自托管 Docker 部署文档。
- 确认推荐的生产部署由 Rails Web、Sidekiq、PostgreSQL（pgvector）和 Redis 组成，前置需要 Nginx/HTTPS 反向代理。
- 确认首次启动前执行 `docker compose run --rm rails bundle exec rails db:chatwoot_prepare`，升级镜像后也需要执行数据库准备命令。
- 使用已登录的 GitHub 账号 `BHNEND` 创建上游 `chatwoot/chatwoot` 的派生仓库：<https://github.com/BHNEND/chatwoot>。
- 将派生仓库的 `develop` 分支克隆到本地工作目录；尚未配置服务器参数，也未触发部署流程。
- 将生产 Compose 的 PostgreSQL 密码改为从服务器 `.env` 读取。
- 新增手动触发的 GitHub Actions 部署流程：同步源码、校验 `.env` 和 Compose、执行数据库准备、启动服务并检查本机 API。
- 首次触发部署时发现面板预先创建的部署目录不是 Git 仓库；调整流程为保留现有 `.env` 后在该目录初始化并同步源码。
- 第二次触发时发现面板目录所有者与部署用户不同导致 Git 安全检查失败；在部署流程中显式登记该部署目录并补充远程地址初始化。
- 第三次触发 GitHub Actions 成功部署（运行编号 `33185570259`）：完成源码同步、镜像拉取、`chatwoot_production` 数据库创建与准备、容器启动及 API 检查。
- 复核服务器容器时发现上一次数据库准备命令占用了远程脚本输入，导致 Rails/Sidekiq 启动步骤未执行；修复 Compose 命令的标准输入处理，并增加两个应用容器的运行状态检查。
- 新一轮部署发现服务器 `127.0.0.1:3000` 被同一 Compose 项目的残留容器占用；在启动前增加仅针对当前 Compose 项目的容器清理，避免影响其他项目。
- 清理后端口仍被外部进程占用；增加 3000 端口占用者诊断，确认来源后再决定释放端口或调整 Chatwoot 本机监听端口。
- 确认 3000 端口由 `lklkai-docker-frontend-1` 使用；将 Chatwoot Rails 映射到本机 `3001`，反向代理目标调整为 `127.0.0.1:3001`，不影响现有项目。
- 发现 3001 端口也被占用；继续诊断端口来源，确认后选择未占用端口。
- 确认 3001 端口由 `bottalk-feishu` 使用；将 Chatwoot Rails 映射到本机 `3100`，反向代理目标调整为 `127.0.0.1:3100`。
- 确认 3100 端口也被占用；将 Chatwoot Rails 改用本机高位端口 `13100`，反向代理目标调整为 `127.0.0.1:13100`。
- 端口切换后 Rails/Sidekiq 已成功启动；健康检查因应用启动需要时间而过早失败，改为最多等待 60 秒重试。
- 使用本机 `13100` 端口后部署成功（运行编号 `33190497332`）；Rails、Sidekiq、PostgreSQL、Redis 均启动，API 健康检查通过。
- 发现 Compose 锚点 `base` 被错误实例化为已停止容器；改为 `x-base` 扩展字段，避免生成多余的 `chatwoot-base-1` 服务。
- 复核发现 `x-base` 仍放在 `services` 内并被实例化为 `chatwoot-x-base-1`；将扩展字段移到 Compose 顶层，彻底移除多余容器。
- 将网页聊天组件的 `disableBranding` 固定为开启，仅移除访客聊天窗口的品牌区，不修改管理后台和邮件品牌。
- 将生产 Compose 改为从仓库内 `docker/Dockerfile` 构建 `chatwoot-custom:develop` 镜像；部署流程改为构建自有镜像后启动。

## 2026-08-29

- 针对远程构建期间 SSH 连接断开的问题，为 GitHub Actions 部署连接增加连接超时、服务端保活和 TCP 保活参数，避免长时间构建时连接被中途回收。
- 使用 GitHub Actions 运行 `33196169490` 完成自建镜像部署：`chatwoot-custom:develop` 构建成功，Rails、Sidekiq、PostgreSQL、Redis 均正常运行，API 健康检查通过。
- 扩展 Captain 编辑器模型白名单，加入 GPT-5.3 Chat、GPT-5.4 系列、GPT-5.5 系列及可通过 OpenAI 兼容接口调用的 `gpt-5.6-luna`，用于客服消息润色；新模型不改变默认模型。
- 使用 GitHub Actions 运行 `33202175285` 完成模型配置部署，镜像构建和四个 Chatwoot 服务健康检查均成功。
