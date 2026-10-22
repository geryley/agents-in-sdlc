---
title: "练习 4 - 自定义智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

## 什么是自定义智能体？

GitHub Copilot 中的[自定义智能体][custom-agents-concept]是针对开发工作流中特定任务或领域定制的专用 AI 助手。通过在存储库的 `.github/agents` 文件夹中使用 Markdown 文件定义智能体，可以为 Copilot 提供专门的指令、最佳实践、编码模式和领域知识，引导它更有效地完成特定类型的工作。团队可以将专业知识编写成可复用的智能体，例如检查 [WCAG][wcag] 合规性的无障碍智能体、遵循安全编码实践的安全智能体，或保持测试模式一致的测试智能体。

自定义智能体通过项目 `.github/agents` 文件夹中的 Markdown 文件定义，也可以全局定义在 `~/.copilot/agents` 中。每个文件都包含 YAML frontmatter，至少要有 `name` 和 `description`，后面跟着用于定义智能体行为、专业知识和指令的 Markdown 提示。

### 自定义智能体与智能体技能的区别

自定义智能体和[智能体技能][agent-skills-concept]在逻辑上有些重叠。两者主要通过 Markdown 文件定义，用来指导 AI 执行操作。最简单的区分方式是：**自定义智能体是执行工作的角色，技能是工具。**

自定义智能体拥有自己的上下文窗口，并且会协调技能（甚至其他智能体）来完成工作。在本实验中，无障碍自定义智能体会根据无障碍指南审查并更新网站；在处理过程中，它可以调用拉取请求工作流技能，或运行和管理测试的技能。

> [!NOTE]
> 自定义智能体没有唯一的“正确”编写方式。与 AI 的其他用法一样，需要进行测试和迭代，找到最适合你的环境和场景的方式。

[custom-agents-concept]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[agent-skills-concept]: https://docs.github.com/copilot/concepts/agents/about-agent-skills
[wcag]: https://www.w3.org/WAI/standards-guidelines/wcag/

## 场景

Tailspin Toys 致力于让所有用户都能使用其众筹平台，不受视觉能力或偏好的限制。最近的用户反馈指出，一些用户觉得当前深色主题难以阅读，因为文本和背景颜色之间的对比度不足。为解决这一无障碍问题，设计团队要求实现一种可由用户切换的高对比度模式。

无障碍功能至关重要，因此需要尽快实现。你将使用自定义智能体生成此功能。

在本练习中，你将：

- 检查现有的无障碍自定义智能体。
- 在 Copilot Chat 中使用无障碍智能体实现高对比度模式。

## 检查无障碍自定义智能体

我们已为你创建了无障碍自定义智能体。先检查其内容，了解它将如何引导 Copilot。

返回 codespace 并从 `main` 创建一个新的无障碍工作分支，这样可以将筛选 PR 与练习 3 分开。

1. 打开终端并运行以下命令：

   ```bash
   git checkout main
   git pull
   git checkout -b accessibility-vscode
   ```

2. 打开 `.github/agents/accessibility.md`。
3. 留意包含 `name` 和 `description` 字段的 YAML frontmatter。

> [!CAUTION]
> 自定义智能体必须包含 `name` 和 `description` frontmatter。

4. 浏览后续部分，查看其中列出的内容：
   - 生成无障碍网站代码时的核心职责。
   - 无障碍最佳实践。
   - HTML、CSS 和 JavaScript 代码示例。
   - 常见问题和错误列表。

## 在 Copilot Chat 中使用自定义智能体

VS Code 会在 Copilot Chat 视图底部的智能体下拉菜单中显示 `.github/agents` 目录里定义的所有自定义智能体。可以选择自定义智能体，让聊天会话使用它的指令和工具。

> [!TIP]
> **打开 Copilot Chat**
>
> 开始下面的练习前，返回 codespace，打开 Copilot Chat 面板并选择 **New Chat**，开始新的对话。不同练习使用的模式和模型可能不同；需要时会在步骤中说明。

1. 如果 Chat 视图的智能体下拉菜单中尚未选中 **Agent**，请先选择它。

   ![显示 Chat 视图中智能体选择器的截图。](../../../_images/shared-chat-mode-selector.png)

2. 选择 Chat 视图底部的智能体下拉菜单（其中显示当前活动智能体，默认为 **default**）。
3. 从可用智能体列表中选择 **Accessibility agent**。
4. 向无障碍智能体发送以下提示：

    ```plaintext
    Add a high-contrast mode to the site. There should be a toggle for high contrast which the user can set, and the setting should persist across page reloads using local storage on the browser.
    ```

5. Copilot Chat 会开始工作：探索代码库、提出更改并编辑项目文件。每次编辑都会在聊天中以内嵌形式显示文件路径和差异，供你审查。
6. 智能体修改文件时，**Files changed** 指示器会更新，显示本次修改的文件集合。

> [!NOTE]
> 此过程可能需要几分钟。Copilot 正在对存储库进行实际更改，可能会编辑 Astro 组件、CSS 和相关测试等现有文件。

下一练习中，你将检查并指导正在执行的工作。

## 总结和后续步骤

本课介绍了 GitHub Copilot 中的[自定义智能体][custom-agents]，即针对特定任务和领域定制的专用 AI 助手。使用自定义智能体可以将团队的专业知识和标准编写成可复用的智能体，从而更有效地引导 Copilot 完成特定类型的工作。

你了解了以下概念：

- 自定义智能体的定义方式。
- 在 Copilot Chat 智能体模式中使用自定义智能体。

接下来，[监控并指导智能体的工作][next-lesson]，在审查更改的同时，为同一会话追加浅色模式切换。

## 资源

- [关于自定义智能体][custom-agents]
- [在 IDE 中创建自定义智能体][creating-custom-agents-ide]
- [VS Code 中的自定义智能体][custom-agents-vscode]
- [自定义智能体配置][custom-agents-config]
- [awesome-copilot 中的自定义智能体][awesome-copilot-agents]

---

| [← 上一课：使用 Playwright MCP 服务器测试功能][previous-lesson] | [下一课：监控和管理智能体 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-mcp/
[next-lesson]: ../5-managing-agents/
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
[creating-custom-agents-ide]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/create-custom-agents-in-your-ide
[custom-agents-vscode]: https://code.visualstudio.com/docs/copilot/customization/custom-agents
[custom-agents-config]: https://docs.github.com/copilot/reference/custom-agents-configuration
[awesome-copilot-agents]: https://github.com/github/awesome-copilot/tree/main/agents
