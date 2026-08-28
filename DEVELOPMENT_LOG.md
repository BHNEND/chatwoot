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
