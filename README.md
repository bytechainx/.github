# .github — 组织级配置中心

> bytechainx 组织下所有仓库的全局规则、CI/CD 模板和 AI Agent 标准。

---

## 快速开始

```bash
# 一键配置全局规则到本机
bash scripts/setup-global-rules.sh
```

详见 [新仓库接入流程](docs/onboarding.md)。

---

## 目录结构

```
.github/
├── CLAUDE.md                          # AI Agent 全局规则入口
├── AGENTS.md                          # 项目 Agent 说明
├── CONTRIBUTING.md                    # 贡献指南（自动继承）
├── SECURITY.md                        # 安全策略（自动继承）
│
├── rulesets/                          # 📋 全局规则中心
│   ├── agent-discipline.md            #   Agent 执行纪律（跨语言）
│   ├── agent-workflow.md              #   Agent 工作流编排（跨语言）
│   ├── agent-safety.md                #   Agent 安全护栏（跨语言）
│   ├── agent-context.md               #   Agent 上下文管理（跨语言）
│   ├── rust/                          #   Rust 编码规范（10 篇 / 859 行）
│   │   ├── RULES.md                   #     核心规范（入口）
│   │   ├── security.md                #     安全基线
│   │   ├── async-runtime.md           #     异步/并发
│   │   ├── testing.md                 #     测试策略
│   │   ├── observability.md           #     可观测性
│   │   ├── api-design.md              #     API 设计
│   │   ├── release.md                 #     发布策略
│   │   ├── clippy.md                  #     Clippy 配置
│   │   ├── ci.md                      #     CI 门禁
│   │   └── cheatsheet.md              #     速查卡
│   ├── python/                        #   Python 编码规范（2 篇）
│   │   ├── RULES.md                   #     核心规范
│   │   └── ci.md                      #     CI 门禁
│   ├── main-protection.json           #   GitHub Ruleset：分支保护
│   └── release-tag-protection.json    #   GitHub Ruleset：Tag 保护
│
├── workflows/                         # ⚙️ GitHub Actions（41 个）
│   ├── reusable-rust-ci.yml           #   Rust CI Reusable Workflow
│   ├── reusable-python-ci.yml         #   Python CI Reusable Workflow
│   ├── reusable-node-ci.yml           #   Node.js CI Reusable Workflow
│   └── ...
│
├── docs/                              # 📖 文档（6 篇）
│   ├── architecture.md                #   四层治理架构
│   ├── onboarding.md                  #   新仓库接入流程
│   ├── global-rules-distribution.md   #   规则分发机制
│   └── reusable-workflows-guide.md    #   Workflow 使用指南
│
├── profile/README.md                  # 🏠 组织首页
├── scripts/                           # 🔧 工具脚本
├── prompts/                           # 🤖 Agent Prompt 模板（10 个）
├── skills/                            # 🧠 Agent 技能（10 个）
└── ISSUE_TEMPLATE/                    # 📝 Issue 模板
```

## 四层治理架构

```
⚡ 强制层 ─ Organization Rulesets（代码级强制，不可绕过）
📜 宪法层 ─ 本仓库（全局规则 + CI 模板 + Agent 标准）
📋 法律层 ─ 各 repo .claude/ .agent/（项目特定规则）
🔧 执行层 ─ 各 repo .agent/roles/（角色级指令）
```

详见 [architecture.md](docs/architecture.md)。

## 子仓库接入

**Rust 项目**只需 5 行 YAML：

```yaml
name: CI
on: { push: { branches: [main] }, pull_request: { branches: [main] } }
jobs:
  ci:
    uses: bytechainx/.github/.github/workflows/reusable-rust-ci.yml@main
```

**Python / Node.js** 类似，替换 workflow 名称即可。

## 规则分发

```bash
# symlink 到用户级 Claude Code 规则（一次配置，所有项目生效）
ln -sf ~/org-config/rulesets/rust/RULES.md ~/.claude/rules/rust.md
ln -sf ~/org-config/rulesets/python/RULES.md ~/.claude/rules/python.md
ln -sf ~/org-config/rulesets/agent-discipline.md ~/.claude/rules/agent-discipline.md
ln -sf ~/org-config/rulesets/agent-workflow.md ~/.claude/rules/agent-workflow.md
ln -sf ~/org-config/rulesets/agent-safety.md ~/.claude/rules/agent-safety.md
ln -sf ~/org-config/rulesets/agent-context.md ~/.claude/rules/agent-context.md
```

详见 [global-rules-distribution.md](docs/global-rules-distribution.md)。
