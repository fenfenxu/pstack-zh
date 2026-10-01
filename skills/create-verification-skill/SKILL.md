---
name: create-verification-skill
description: "生成项目本地的 verification skill，像用户一样驱动应用——任意语言、框架或平台。用于 /create-verification-skill、「给这个仓库做个 control skill」，或项目没有脚本化方式证明 UI/CLI/服务行为时。"
disable-model-invocation: true
---

# 创建 verification skill

每个严肃项目都需要脚本化方式驱动真实应用并证明行为：启动、像用户一样演练功能、采集证据。本 skill 在仓库中生成项目本地 skill（`.cursor/skills/verify-<app>/`）。你写的是给下一个 agent 看的，不是给人：它会在任务中途、从未见过该应用的情况下冷读。

## 1. 访谈仓库，而非用户

从代码库回答下列问题；只有观察不到时才问用户：

- **界面：** 用户实际接触什么？Web UI、CLI/TUI、桌面应用、API、移动应用、库？仓库可有多种，选主要一种并注明其余。
- **运行：** 本地如何启动？优先仓库自文档化的 dev 命令（package scripts、Makefile、README 快速上手）。记下端口、环境变量、种子数据、鉴权。
- **驱动：** agent 如何程序化交互？先看现有 harness——Playwright/Cypress spec、expect 脚本、PTY 辅助、可 curl 的端点、调试端口。再选通用方案：浏览器/CDP 用于 Web 与 Electron，tmux/PTY harness 用于 CLI/TUI，纯 HTTP 用于服务。
- **观察：** 能采集什么证据？截图、终端 transcript、响应体、日志、退出码、DB 状态。
- **隔离：** 能否并排跑两个实例（端口、数据目录、profile）？若不能，在生成的 skill 中说明：拒绝双开共享实例，好过搞坏用户会话。

若 checkout 本身无法构建或启动，先生成前修复（或精确报告）；基于坏底座的 skill 会教错步骤。无关缺失资产阻塞启动时（API 从不服务的静态目录、示例配置），生成的 skill 可创建它，明确标为 verification 脚手架，并在 cleanup 中删除。

## 2. 生成 skill

写 `.cursor/skills/verify-<app>/SKILL.md`，含 YAML frontmatter（`name: verify-<app>` 与 `description` 写明应用、界面与何时使用——无 frontmatter 则 skill 不会注册），以及下列各节，均基于访谈实际发现（不留占位符）：

- **Launch：** 验证用的确切启动命令，以及如何判定就绪（日志行、端口响应、提示符）。含 teardown。短生命周期 CLI/TUI 无常驻 server：launch 指先构建二进制（或装依赖），每次 drive 在独立 PTY 或 tmux 会话中启动。
- **Doctor：** 一次只读检查，回答「这个实例值得驱动吗？」——进程在跑、版本/构建正确、端口归我们、鉴权有效。任何异常时 agent 先跑这个。
- **Drive：** 本仓库真实 selector/命令 的 harness 配方，不是示例。优先稳定句柄（ARIA label、data 属性、提示字符串、路由路径），而非坐标与 tab 顺序。
- **Evidence：** 证明要采什么、放哪里。写明证明标准：走真实用户路径，不用内部 setter 或仅测试端点；采集动作与结果状态，不只最终画面；与可见结果一起验证副作用（写文件、插行、发消息）；mock 仅用于生产边界已隔离外部系统处。安全路径是 dry-run 或 test mode 时，用观察验证它实际跳过了什么（文件、网络、git ref），不信名字：有些 dry-run 仍会触网或开浏览器。
- **Cleanup：** 如何拆掉本 run 创建的实例。不要按进程名杀；只杀你启动的。cleanup 移除实例与 scratch 状态，从不删证据：证明产物在 teardown 后仍留在 skill 指名的位置。
- **Helpers：** skill 自带的脚本须可执行，且 skill 正文写出调用方式。读者还要反推的 helper 不算 helper。

## 3. 播种 feature map

创建 `.cursor/skills/verify-<app>/features/README.md`，以及每个可识别的面向用户功能各一个文件（先从路由、命令、菜单或文档找 top 3–5）。形态见 [`references/feature-map-example/`](references/feature-map-example/)：README 索引加每功能一文件。每文件从用户视角回答：功能是什么、如何到达、如何用 harness 驱动、何种可观察终态证明有效。四个 H2 为 `Sub-features`、`How to get to it (user POV)`、`Driving it with <harness>`、`Gotchas`。map 是仓库维护的验证源；map 列了其他入口却只驱动一个方便入口的证明是不完整的。

## 4. 交付前证明生成的 skill

按自身说明端到端跑一遍：launch、doctor、驱动 ONE 个已映射功能（一个就够；map 供后续覆盖其余）、采证据、cleanup。cleanup 后确认证据仍在指名位置——吃掉证明的 cleanup 算本步失败。修失败项，每次失败迭代后也跑生成的 cleanup，避免坏尝试遗留进程与端口。从未执行过的生成 skill 是草稿，不是交付物。

## 5. 提供维护循环

指向 `/maintain-verification-skill` 以保持 map 随应用变化而诚实。仅当用户问时才建议 cadence。
