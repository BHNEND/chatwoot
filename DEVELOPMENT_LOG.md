# 开发日志

## 2026-08-28

- 阅读 Chatwoot 官方开发文档和自托管 Docker 部署文档。
- 确认推荐的生产部署由 Rails Web、Sidekiq、PostgreSQL（pgvector）和 Redis 组成，前置需要 Nginx/HTTPS 反向代理。
- 确认首次启动前执行 `docker compose run --rm rails bundle exec rails db:chatwoot_prepare`，升级镜像后也需要执行数据库准备命令。
- 使用已登录的 GitHub 账号 `BHNEND` 创建上游 `chatwoot/chatwoot` 的派生仓库：<https://github.com/BHNEND/chatwoot>。
- 将派生仓库的 `develop` 分支克隆到本地工作目录；尚未配置服务器参数，也未触发部署流程。

