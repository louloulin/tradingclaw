# TradingAgents-AShare 部署基础设施

> 🚀 A股智能投研多智能体系统的完整部署解决方案

这是 [TradingAgents-AShare](https://github.com/KylinMountain/TradingAgents-AShare) 的部署基础设施仓库，包含 Docker 配置、生产环境部署脚本、系统服务配置和监控工具。

## ✨ 系统特性

- 🤖 **12个AI智能体协作**: 基本面、情绪、新闻、技术四大维度分析师同步作业
- 🎯 **结构化辩论**: 多头与空头研究员红蓝对抗，挖掘潜在收益与风险
- 🌐 **现代化Web界面**: 实时任务进度追踪、响应式布局与研报管理
- 🐳 **Docker容器化**: 一键部署，环境隔离，易于维护
- 🔄 **生产就绪**: 完整的监控、日志、健康检查和自动重启机制
- 📊 **API接口**: 标准REST API，方便集成第三方系统

## 🎯 快速开始

### 前置要求

- Docker & Docker Compose
- 或 Node.js 18+ 和 Python 3.13+

### 方式一：Docker 部署（推荐）

1. **配置环境变量**
```bash
cp .env.example .env
# 编辑 .env 文件，填入你的 API 密钥
```

2. **启动服务**
```bash
docker-compose up -d
```

3. **访问系统**
- 主界面: http://localhost:8000
- API文档: http://localhost:8000/docs
- 健康检查: http://localhost:8000/health

### 方式二：生产环境部署

1. **构建前端**
```bash
cd TradingAgents-AShare/frontend
npm install
npm run build
```

2. **启动生产服务**
```bash
./start-production.sh start
```

3. **检查状态**
```bash
./status-production.sh
```

## 📁 项目结构

```
.
├── docker-compose.yaml              # 开发环境配置
├── docker-compose.prod.yml          # 生产环境配置
├── .env.example                     # 环境变量模板
├── start-production.sh              # 生产启动脚本
├── status-production.sh             # 状态检查脚本
├── build-production.sh              # 构建脚本
├── tradingagents-*.service          # Systemd 服务配置
├── TradingAgents-AShare/            # 核心应用代码
│   ├── api/                         # FastAPI 后端
│   ├── frontend/                    # Vue.js 前端
│   └── tradingagents/               # 智能体逻辑
└── docs/                            # 部署文档
```

## 🔧 环境配置

### 核心配置

创建 `.env` 文件并配置以下参数：

```bash
# 智谱AI配置
TA_API_KEY=your_api_key_here
TA_BASE_URL=https://open.bigmodel.cn/api/coding/paas/v4

# LLM模型配置
TA_LLM_QUICK=glm-4.5-air
TA_LLM_DEEP=glm-5

# 数据库配置
DATABASE_URL=sqlite:///./tradingagents.db
```

### CORS 配置

如需配置跨域访问，可使用提供的脚本：

```bash
./add-cors-domain.sh http://your-frontend-domain.com
```

## 🛠 管理命令

### Docker 管理

```bash
# 启动服务
docker-compose up -d

# 停止服务
docker-compose down

# 查看日志
docker-compose logs -f

# 重启服务
docker-compose restart
```

### 生产环境管理

```bash
# 启动/停止/重启服务
./start-production.sh start
./start-production.sh stop
./start-production.sh restart

# 查看服务状态
./status-production.sh

# 查看实时日志
./start-production.sh logs
./start-production.sh follow
```

### 系统服务管理

如果配置了 systemd 服务：

```bash
# 启用服务
sudo systemctl enable tradingagents-backend
sudo systemctl enable tradingagents-frontend

# 管理服务
sudo systemctl start tradingagents-backend
sudo systemctl stop tradingagents-backend
sudo systemctl status tradingagents-backend
```

## 📊 监控与诊断

### 健康检查

```bash
# 检查服务健康状态
curl http://localhost:8000/health

# 完整状态报告
./status-report.sh
```

### 日志查看

```bash
# Docker 日志
docker-compose logs -f tradingagents

# 生产环境日志
tail -f /tmp/backend-production.log

# 服务状态报告
./service-status-report.sh
```

### 性能监控

```bash
# Docker 资源使用
docker stats

# 系统资源
top -p $(cat /tmp/tradingagents-production.pid)

# 网络连接
netstat -tlnp | grep 8000
```

## 🚨 故障排查

### 常见问题

1. **端口占用**
```bash
# 检查端口占用
lsof -i :8000
netstat -tlnp | grep 8000

# 停止占用进程
kill -9 $(lsof -t -i:8000)
```

2. **API 调用失败**
```bash
# 检查 API 密钥配置
cat .env | grep API_KEY

# 测试 API 连接
curl -X POST http://localhost:8000/v1/analyze \
  -H "Content-Type: application/json" \
  -d '{"symbol": "600519.SH"}'
```

3. **前端页面 404**
```bash
# 检查前端构建产物
ls -la TradingAgents-AShare/frontend/dist/

# 重新构建前端
cd TradingAgents-AShare/frontend && npm run build
```

### CORS 问题

如果遇到跨域问题，可以使用提供的修复工具：

```bash
# 检查 CORS 状态
./test-cors-fix.sh

# 添加允许的域名
./add-cors-domain.sh http://localhost:5173
```

## 📦 备份与恢复

### 数据备份

```bash
# 备份数据库
cp tradingagents.db tradingagents.db.backup.$(date +%Y%m%d)

# 备份配置文件
cp .env .env.backup.$(date +%Y%m%d)
```

### 日志归档

```bash
# 归档日志文件
mv /tmp/backend-production.log /tmp/backend-production.log.$(date +%Y%m%d)
```

## 🔐 安全建议

1. **API 密钥管理**
   - 使用环境变量存储敏感信息
   - 定期轮换 API 密钥
   - 不要在代码中硬编码密钥

2. **网络安全**
   - 在生产环境启用 HTTPS
   - 配置防火墙规则
   - 使用强密码和认证机制

3. **访问控制**
   - 配置 CORS 策略
   - 实施速率限制
   - 定期审计访问日志

## 📚 相关文档

- [生产环境部署指南](PRODUCTION-DEPLOYMENT.md)
- [CORS 配置说明](CORS-COMPLETE-FIX.md)
- [邮件设置指南](setup-email-guide.md)
- [核心项目 README](TradingAgents-AShare/README.md)

## 🌟 核心功能

### 智能体团队

系统模拟真实交易机构的部门协作：

1. **分析师团队**: 基本面、情绪、新闻、技术四大维度
2. **研究员团队**: 多头与空头红蓝对抗
3. **决策团队**: 交易员、风控、组合经理

### API 接口

```bash
# 触发分析
POST /v1/analyze

# 查询状态
GET /v1/jobs/{job_id}

# 获取结果
GET /v1/jobs/{job_id}/result

# 历史记录
GET /v1/reports
```

## 🤝 贡献

欢迎贡献代码、报告问题或提出建议！

## 📄 许可证

- 新增组件采用 `PolyForm Noncommercial 1.0.0` 协议
- 仅限非商业用途

## 🔗 相关链接

- [核心项目仓库](https://github.com/KylinMountain/TradingAgents-AShare)
- [在线 Demo](https://app.510168.xyz)
- [API 文档](http://localhost:8000/docs)

---

**维护者**: TradingAgents Team
**最后更新**: 2026-03-14
**部署状态**: ✅ 生产环境就绪