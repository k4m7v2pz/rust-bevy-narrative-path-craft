# AGENTS.md — Agent 协作规约

本文件是本仓库的 Agent 协作约定。**Agent 在与人类的协作方式发生变动时，必须自动编辑本文件以反映现状。** 人类也可随时手动修订。

---

## 一、脱敏与公开仓库政策

> **大部分仓库都是公开开源仓库，提交到 git 的内容默认会被互联网可见。**

### 1. 提交内容脱敏

凡进入 git 的内容（文档、commit message、注释、配置）必须满足：

- **不得包含** 个人邮箱、真实姓名、私钥、token、密码、私人服务器地址、代理端口、内部 IP。
- **不得包含** 未公开的私人仓库地址。公开开源仓库地址可保留。
- **commit message** 里不要嵌入远端 URL、不要嵌入用户私人邮箱；trailer 的 `Co-Authored-By` **如实标注实际执行任务的 AI Agent 名字**（是哪个 Agent 就写哪个，不固定某一家；例如豆包写 `Doubao`、AtomCode 写 `AtomCode (GLM-5.2)`）。
- **文档里** 若要举例远端、邮箱、端口，用占位符（`<example@example.com>`、`<proxy-port>`、`<your-remote>`）。

### 2. 用 .gitignore 忽略不该进库的本地数据

Agent 在提交前必须核对暂存区，下列内容**不得入库**，应写入 `.gitignore`：

| 类别 | 示例 | 理由 |
|---|---|---|
| 本地缓存 / 系统文件 | `.DS_Store`、`*.log` | 体积大、机器相关 |
| 私人笔记 / 草稿 | `notes/private.md`、`scratch/` | 个人用，非项目交付 |
| IDE 本地配置 | `.idea/`、`.vscode/`（除非团队共享） | 机器相关 |

### 3. 提交前核对流程

Agent 在执行 `git commit` 前必须：

1. `git status --short` + `git diff --cached --name-only` 列暂存区
2. 肉眼扫一遍：有无 `.DS_Store`、私人邮箱、token、本地绝对路径泄漏
3. 若有误网，`git restore --staged <file>` 摘出，必要时加进 `.gitignore`
4. 确认无泄漏再 commit

---

## 二、Agent 自动提交与推送

### 1. 何时自动提交

当人类明确要求"提交并推送"、"你来处理提交"等时，Agent 可直接执行 `git add` → `git commit` → `git push`，无需每步停下问人。

### 2. commit message 规范

- 首行：`<type>: <概要>`，type 用 `feat` / `fix` / `docs` / `refactor` / `chore` / `test`
- 空行后正文：要点列表，说明做了什么、为什么
- 末尾 trailer（空行隔开），`Co-Authored-By` 填写**实际执行本次提交的 AI Agent 名**：
  ```
  Co-Authored-By: <实际执行任务的AI Agent名> <noreply@<对应域名>>
  ```
  例：豆包 → `Co-Authored-By: Doubao <noreply@doubao.com>`；AtomCode → `Co-Authored-By: AtomCode (GLM-5.2) <noreply@atomgit.com>`
- 用 `git commit -m "$(cat <<'EOF' ... EOF)"` heredoc 保空行；`--amend` / `revert` 不加 trailer

### 3. 推送前确认

- 推送前 `git log -1 --format='%B'` 校验 message 完整（trailer 不应裸成首行）
- 推送目标分支默认当前分支（`git push origin <current>`），不擅自改远端或新建分支
- 推送失败不重试同一命令，先读错误（权限 / 非快进 / 拒接）再修

---

## 三、协作方式自维护

**触发条件**：Agent 与人类的协作方式发生变动时，例如：

- 人类指定了新的代理或网络配置 → 不要写进 git，但要在本文件"附录"里记协作约束
- 人类偏好变更 → 在"附录"里记设计原则
- 新的自动行为约定 → 在本文件里记成规则

**执行方式**：Agent 在执行完变动后，编辑本文件追加/修订对应条目，下次会话 Agent 读到本文件即继承约定。

---

## 四、附录：本项目当前约定

> 本节是 Agent 维护的动态部分，记录与本项目具体协作约定。

### A. 项目形态

- **本仓库是纯文档骨架**：Narrative Path Craft 是引擎，endless-corridor 是载体示例。**当前不创建 Cargo.toml / src 等 Rust 项目组件**，等引擎真正从 endless-corridor 抽离后再建 crate。
- 核心思想：引擎从可玩示例中分离——先有能试玩、能排错的示例，再抽离出引擎。

### B. 授权与署名

- 本仓库按 **Mulan PSL v2 + Unlicense 双许可** 授权，见 `LICENSE` / `UNLICENSE`；署名 `k4m7v2pz`，见 `CREDITS`。
- 详细策略见 `README.md` 与主代码仓库 `docs/license_strategy.md`。

### C. 网络与代理

- github.com 走代理（端口值不入 git，见脱敏政策）；克隆仓库时按远端域名判断，不把代理端口写进任何提交内容。
