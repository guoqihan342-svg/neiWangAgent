[![MseeP.ai Security Assessment Badge](https://mseep.net/pr/guoqihan342-svg-neiwangagent-badge.png)](https://mseep.ai/app/guoqihan342-svg-neiwangagent)

# neiWangAgent v0.1.2

> 本地无服务器 MCP Agent — 自动改代码 → commit → push → 创建 MR
> 支持多语言项目：Java / Python / Go / TypeScript / Vue
> 此仓库为 **Windows版** | Linux版见 [neiWangAgent-linux](https://github.com/guoqihan342-svg/neiWangAgent-linux)

![Version](https://img.shields.io/badge/version-0.1.2-blue)
![Python](https://img.shields.io/badge/python-3.10+-green)

---

## 这是什么

neiWangAgent 是一个运行在你本地的 AI 编程助手。给它一个需求描述，它会自动完成：

1. **理解需求** — LLM 分析需求，提取目标模块/数据模型/接口变更
2. **检索上下文** — 三层知识库（Summary/Hotspot/Deep）提供代码背景
3. **生成代码** — 支持6种LLM输出格式自动检测（不再捆死单一格式）
4. **安全检查** — deny_paths 拦截 + 分支regex校验 + 命令黑名单
5. **Git操作** — 自动创建 `agent/` 分支 → commit → push → 创建 Pull Request

**全部在本地运行，不依赖服务器。**

---

## 架构

```
CLI (Click) → Orchestrator (16步状态机 + 错误恢复)
                ├── LLM Client     (DeepSeek/OpenAI兼容, 带代理)
                ├── Code Parser    (6种格式自动检测)
                ├── Tracer         (JSON Lines 结构化日志)
                ├── Config Loader  (多语言项目类型自动适配)
                └── 6个 MCP Server (继承 BaseMCPServer)
```

---

## 快速开始

```bash
# 安装
pip install -e .

# 配置
export DEEPSEEK_API_KEY="sk-xxx"
export GITHUB_TOKEN="ghp_xxx"

# 使用
neiWangAgent init
neiWangAgent warmup
neiWangAgent run --task task.md
neiWangAgent resume 20260511-123456
```

---

## 版本历史

| 版本 | 日期 | 关键变更 |
|------|------|---------|
| v0.1.2 | 2026-05-11 | code_parser 6格式检测 + 错误恢复 + 状态级重试 |
| v0.1.1 | 2026-05-11 | BaseMCPServer基类 + 空handler修复 + 多语言支持 |
| v0.1.0 | 2026-05-10 | 初始版本 |

---

## 安全红线

| 操作 | 状态 |
|------|------|
| 读写工作目录（受 deny_paths 限制） | ✅ |
| git push agent/* | ✅ |
| push master/main/release/hotfix | ❌ |
| git merge / force push | ❌ |
| 执行 INSERT/UPDATE/DELETE/DROP | ❌ |
| 操作 .env / .ssh / *.pem | ❌ |

---

## 环境变量

- `DEEPSEEK_API_KEY` — LLM API Key（必填）
- `GITHUB_TOKEN` / `GITHUB_PAT` — GitHub Token（创建MR需要）
- `https_proxy` — 代理地址（WSL环境自动读取）
