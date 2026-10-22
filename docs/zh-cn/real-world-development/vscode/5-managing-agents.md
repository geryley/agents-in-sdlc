---
title: "练习 5 - 监控和管理智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：自定义智能体][previous-lesson] |
|:--|

使用智能体模式时，GitHub Copilot 会自主探索代码库、提出更改、编辑文件并运行命令。由于这些工作会在本地实时进行，VS Code 中的 Copilot Chat 会显示每次工具调用和文件编辑。你可以在聊天中审查每个差异、接受或拒绝单项更改，并通过后续提示调整或扩展工作，而无需离开编辑器。

在本练习中，你将：

- 实时监控无障碍智能体的工作。
- 审查并接受建议的文件编辑。
- 在会话进行期间指导智能体，在高对比度模式旁添加浅色模式切换。

## 场景

你在[练习 4][previous-lesson]中使用的无障碍自定义智能体正在实现高对比度模式。工作进行期间，设计团队又提出一项需求：他们也希望为觉得默认主题过暗的用户提供浅色模式切换。无需从头开始新对话，你将指导现有会话扩展工作范围。

## 监控 Copilot 的进度

Copilot Chat 视图会显示智能体的每个步骤，包括探索的文件、运行的命令和提出的编辑。每次文件编辑都会以内嵌差异的形式显示在聊天中，并带有 **Keep** / **Undo** 控件。

1. 返回练习 4 中使用的 Copilot Chat 视图。
2. 智能体工作时，滚动聊天内容，查看每次工具调用。你会看到文件读取、文件编辑以及智能体请求运行的终端命令。
3. 智能体提出文件编辑后，差异会以内嵌方式显示：
   - 选择 **Keep** 接受更改。
   - 选择 **Undo** 撤销更改，让智能体重新尝试。
4. 也可以打开聊天顶部的 **Files changed** 指示器，查看智能体在此会话中修改的全部文件。

> [!NOTE]
> 如果工具调用需要权限（例如运行终端命令），智能体会暂停并显示 **Allow** 提示。只批准你认可的命令。

5. 等待智能体完成高对比度模式。完成后应会显示总结更改的消息。

## 指导智能体添加浅色模式

高对比度模式准备就绪后，在同一对话中继续要求添加浅色模式切换。智能体会保留先前工作的上下文，包括修改的组件、采用的样式方法和本地存储模式。

1. 在 Copilot Chat 输入框中发送以下后续提示：

    ```plaintext
    Nice work! Now add a light-mode toggle alongside the high-contrast toggle. Light mode should follow the same pattern — persist via local storage and be accessible from the same area of the UI. The user should be able to enable high-contrast and light mode independently.
    ```

2. 智能体会检查先前的更改，并提出添加浅色模式的方案。
3. 与高对比度模式一样，逐项审查新编辑，并使用 **Keep** 或 **Undo**。
4. 如果智能体的方向不符合预期，例如以你不喜欢的方式重构切换 UI，可以选择聊天输入框旁的 **Stop** 按钮停止处理，再发送说明清楚的后续消息。

> [!TIP]
> 与从头开始相比，指导正在进行的会话更快。可以使用后续提示要求调整（“简化 CSS，使用 CSS 变量而非重复选择器”）、纠正方向（“不要添加第三个切换控件，将浅色模式集成到现有设置面板”），或扩展范围（“同时为两个切换功能添加键盘快捷键”）。

## 审查工作文件集

提交工作前，快速检查智能体修改的所有内容。

1. 在 VS Code 中打开 **Source Control** 视图。
2. 审查更改文件列表。应能看到 Astro 组件、样式和相关测试的更新。
3. 打开部分更改文件并查看差异。确认自定义智能体采用了无障碍模式，例如 ARIA 属性、键盘导航、语义化 HTML 和通过本地存储保存设置。
4. 确认满意后，在 Source Control 面板中暂存并提交更改。下一课会发布这些更改。

## 总结和后续步骤

你使用 Copilot Chat 智能体模式监控并指导了正在进行的会话：逐项审查文件编辑、接受或拒绝更改，并在保留上下文的同时发送后续提示扩展工作。

本课中你了解了：

- 实时监控智能体的工作。
- 接受或拒绝单项文件编辑。
- 使用后续提示指导正在进行的会话。

在[下一课][next-lesson]中，你将把无障碍更改发布为拉取请求，并审查本实验中本地自定义智能体生成的更改。

## 资源

- [VS Code 中的 Copilot Chat][copilot-chat-vscode]
- [使用智能体模式][agent-mode]

---

| [← 上一课：自定义智能体][previous-lesson] | [下一课：迭代改进 Copilot 的工作 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../4-custom-agents/
[next-lesson]: ../6-iterating/
[copilot-chat-vscode]: https://code.visualstudio.com/docs/copilot/chat/copilot-chat
[agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
