# Vibe Kanban 项目完整指南

## 项目概述

Vibe Kanban 是一个强大的任务管理和 AI 编码助手协调工具，让你能够充分发挥 Claude Code、Gemini CLI、Codex、Amp 等编码助手的潜力，提升 10 倍的工作效率。

### 核心功能

1. **多 AI 助手管理**：轻松切换不同的编码助手
2. **并行/串行执行**：协调多个编码助手的执行
3. **任务跟踪**：可视化看板管理编码任务
4. **代码审查**：快速审查 AI 生成的代码
5. **开发服务器管理**：一键启动和管理开发服务器
6. **MCP 配置集中化**：统一管理各种编码助手的 MCP 配置

## 系统架构

```
vibe-kanban/
├── backend/          # Rust 后端服务
│   ├── src/
│   │   ├── executors/    # AI 助手执行器
│   │   ├── models/       # 数据模型
│   │   ├── routes/       # API 路由
│   │   └── services/     # 业务服务
│   └── Cargo.toml
├── frontend/         # React 前端应用
│   ├── src/
│   │   ├── components/   # UI 组件
│   │   ├── pages/        # 页面组件
│   │   └── lib/          # 工具库
│   └── package.json
└── npx-cli/          # NPX 命令行接口
```

## 安装与配置

### 前置要求

1. **系统要求**：
   - Node.js >= 18
   - pnpm >= 8
   - Rust (最新稳定版)
   - Git

2. **AI 助手认证**：
   确保你已经认证了至少一个 AI 编码助手（如 Claude Code）

### 快速开始

#### 方法一：使用 NPX（推荐）

```bash
npx vibe-kanban
```

这将自动下载并启动 Vibe Kanban。

#### 方法二：从源码构建

1. **克隆仓库**：
```bash
git clone https://github.com/BloopAI/vibe-kanban.git
cd vibe-kanban
```

2. **安装依赖**：
```bash
pnpm install
```

3. **运行开发服务器**：
```bash
pnpm run dev
```

这将启动前端和后端服务，并启用实时重载。

4. **构建生产版本**：
```bash
./build-npm-package.sh
cd npx-cli
npm pack
npx [生成的.tgz文件]
```

### 配置文件

#### 1. Claude Code 配置 (`.claude.json`)

在你的主目录创建 `.claude.json` 文件：

```json
{
  "claudeCodePath": "/usr/local/bin/claude-code",  // 可选：本地 claude-code 路径
  "mcpServers": {
    "filesystem": {
      "command": "node",
      "args": ["/path/to/mcp-server-filesystem/index.js"],
      "env": {
        "ALLOWED_DIRECTORIES": "/home/user/projects"
      }
    }
  }
}
```

#### 2. 其他 AI 助手配置

- **Amp**: `~/.config/amp/settings.json`
- **Gemini**: `~/.gemini/settings.json`
- **Charm Opencode**: `~/.opencode.json`
- **SST Opencode**: `~/.config/opencode/opencode.json`

### 环境变量

#### 构建时变量
```bash
# GitHub OAuth 应用 ID（可选，有默认值）
GITHUB_CLIENT_ID=your_github_client_id

# PostHog 分析（可选）
POSTHOG_API_KEY=your_posthog_key
POSTHOG_API_ENDPOINT=https://app.posthog.com
```

#### 运行时变量
```bash
# 后端端口（默认：自动分配）
BACKEND_PORT=8080

# 前端端口（默认：3000）
FRONTEND_PORT=3000

# 主机地址（默认：127.0.0.1）
HOST=127.0.0.1

# 禁用 worktree 清理（调试用）
DISABLE_WORKTREE_ORPHAN_CLEANUP=1
```

## 使用指南

### 1. 创建项目

1. 启动 Vibe Kanban
2. 点击 "New Project"
3. 填写项目信息：
   - 项目名称
   - Git 仓库路径
   - 设置脚本（可选）
   - 开发脚本（可选）

### 2. 创建任务

1. 在项目页面点击 "New Task"
2. 填写任务信息：
   - 任务标题
   - 任务描述（可选）
   - 选择执行器（Claude、Gemini 等）

### 3. 执行任务

1. 点击任务卡片上的执行按钮
2. 选择要使用的 AI 助手
3. 等待执行完成
4. 查看生成的代码和日志

### 4. 代码审查

1. 在任务详情中查看差异
2. 审查 AI 生成的更改
3. 创建 Pull Request 或继续迭代

### 5. 任务状态管理

任务有以下状态：
- **Todo**: 待处理
- **In Progress**: 进行中
- **In Review**: 审查中
- **Done**: 已完成
- **Cancelled**: 已取消

## 高级功能

### 1. 本地 Claude Code 支持

Vibe Kanban 现在支持使用本地安装的 Claude Code，提供更好的性能和离线支持。

**优先级顺序**：
1. 配置文件中的 `claudeCodePath`
2. 系统 PATH 中的 `claude-code`
3. NPX 远程版本（回退）

### 2. MCP (Model Context Protocol) 集成

通过 MCP 服务器扩展 AI 助手的能力：

```json
{
  "mcpServers": {
    "database": {
      "command": "mcp-server-sqlite",
      "args": ["--db", "/path/to/database.db"]
    },
    "github": {
      "command": "mcp-server-github",
      "env": {
        "GITHUB_TOKEN": "your_token"
      }
    }
  }
}
```

### 3. 任务模板

创建可重用的任务模板以提高效率：
1. 在设置中管理任务模板
2. 基于模板快速创建新任务

### 4. Git 集成

- 自动创建工作树
- 分支管理
- PR 创建和跟踪

## 故障排除

### 常见问题

1. **端口冲突**：
```bash
BACKEND_PORT=8081 FRONTEND_PORT=3001 pnpm run dev
```

2. **权限问题**：
确保对 Git 仓库和配置文件有适当的读写权限

3. **AI 助手连接失败**：
- 检查认证状态
- 验证配置文件路径
- 查看日志获取详细错误信息

### 日志位置

- 后端日志：控制台输出
- 前端日志：浏览器开发者工具
- AI 助手日志：任务执行详情页

## 开发指南

### 添加新的执行器

1. 在 `backend/src/executors/` 创建新文件
2. 实现 `Executor` trait
3. 在 `mod.rs` 中注册
4. 更新 `ExecutorConfig` 枚举

### 前端开发

```bash
cd frontend
pnpm dev
```

### 后端开发

```bash
cd backend
cargo run
```

## 贡献指南

1. 在提交 PR 前，请先通过 GitHub Issues 讨论
2. 遵循现有的代码风格
3. 添加适当的测试
4. 更新相关文档

## 支持

- GitHub Issues: https://github.com/BloopAI/vibe-kanban/issues
- 文档: https://vibekanban.com

## 许可证

请查看项目根目录的 LICENSE 文件。