---
title: "第 4 课 - 使用自定义指令引导 Copilot"
description: "浏览存储库指令，添加文档标准，并将其应用到筛选代码。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

使用生成式 AI 时，上下文至关重要。如果任务必须以特定方式完成，就应确保 Copilot 可以访问相关指导。[指令文件][instruction-files]不仅说明你希望生成什么代码，也说明代码应如何组织。你已构建筛选功能，现在将了解 Copilot 使用的指令、添加文档标准并将其应用于代码。

在本课中，你将：

- 了解存储库指令和路径范围指令文件如何传递给智能体。
- 更新指令文件，确保遵循编码标准。
- 观察指令文件对代码的影响。

## 场景

Tailspin Toys 有一套开发实践准则和要求：

- 注释应说明意图和不明显的决策，而不是复述代码。
- `db/` 和 `src/lib/` 中的导出函数应使用 TSDoc/JSDoc 记录用途、参数和返回值，并说明注入的 `db` 参数（如果存在）。
- 可复用 Astro 组件应记录其 `Props` 契约；相关代码更改时，注释也应保持最新。
- 现有格式和 lint 指导应予以保留。

通过指令文件，可以确保 Copilot 获得正确的信息，并按照上述实践完成任务。

## 指令文件

自定义指令可为 Copilot 提供上下文和偏好设置，帮助它理解编码风格和要求。你可以指定编码约定、库和注释要求，并为整个存储库或特定文件类型创建指令。

指令文件有两种类型：

- `.github/copilot-instructions.md`：针对存储库的**每个**请求发送的单个指令文件，其中包含大多数请求都适用的项目级信息。
- `.github/instructions/*.instructions.md`：针对特定语言、文件类型或任务提供指导。

> [!NOTE]
> 不同环境对其他指令格式的支持情况各不相同。依赖特定格式前，请查阅[自定义指令支持参考][custom-instructions-support]。

## 浏览项目中的自定义指令文件

项目模板已附带一组指令文件。先浏览现有内容，再进行更改以观察其影响。

1. 返回 Codespace。
2. 在 Codespaces 编辑器（而非终端）中打开 `.github/copilot-instructions.md`。
3. 浏览项目简介和编码指导。这些指令适用于在此存储库中与 Copilot 的每次交互。
4. 打开 `.github/instructions` 文件夹并浏览文件。留意针对 Astro 文件、Drizzle 数据层、测试等内容的指令。
5. 打开 `.github/instructions/unit-tests.instructions.md`。留意顶部的 `applyTo` 字段，它设置一个 glob，用于确定指令适用的文件。
6. 打开 `.github/instructions/drizzle.instructions.md`，留意它指向其他指令文件和项目现有文件的链接。这样可以将大型指令集拆分为可复用的小文件，并为 Copilot 提供示例。

## 根据团队指导更新指令文件

现有文件是不错的起点，但仍有不足。修改核心 `copilot-instructions.md` 文件，确保新生成的 TypeScript 代码包含 TSDoc 注释。

1. 在 `.github/copilot-instructions.md` 中找到 **Code formatting guidance** 部分，位置应在第 35 行附近。
2. 在该部分的最后添加以下内容：

   ```markdown
   - All new TypeScript should contain TSDocs comments for documentation purposes.
   ```

文件会自动保存。

## 使用更新后的指导

Copilot CLI 会在对话开始时加载存储库指令。保存更新后，退出并恢复筛选对话，让 Copilot 在不丢失功能上下文的情况下加载新指导。

1. 如果筛选对话仍处于打开状态，输入 `/exit` 关闭它。Copilot CLI 会自动保存对话。
2. 在存储库根目录的 `game-filters-cli` 分支上，恢复已保存对话：

   ```bash
   copilot --resume
   ```

   如果系统提示，请选择筛选对话，并确认检出的是 `game-filters-cli` 分支。
3. 要求 Copilot 应用更新后的指导：

   ```plaintext
   We just updated our instructions and code guidance. Can you please update the code you generated to match that guidance?
   ```

4. 输入 `/diff` 并检查更改的 TypeScript 文件。留意新生成的 TSDoc 注释，并确认注释准确说明代码。

## 总结和后续步骤

你了解了 Copilot CLI 如何获取指令文件上下文，并将新标准应用于功能。具体来说，你：

- 了解存储库指令和路径范围指令文件如何传递给智能体。
- 更新指令文件，确保遵循编码标准。
- 观察了指令文件对代码的影响。

接下来，[自定义并运行可复用的 quality-checks 技能][next-lesson]，确保一致地运行 lint 和测试。

## 资源

- [为 Copilot CLI 添加自定义指令][instruction-files]
- [自定义指令支持][custom-instructions-support]
- [创建自定义指令的最佳实践][instructions-best-practices]
- [Awesome Copilot — 指令文件和其他资源集][awesome-copilot]

[instruction-files]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions
[instructions-best-practices]: https://docs.github.com/copilot/concepts/prompting/response-customization#writing-effective-custom-instructions
[awesome-copilot]: https://awesome-copilot.github.com/
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support
[previous-lesson]: ../3-agent-modes/
[next-lesson]: ../5-agent-skills/
