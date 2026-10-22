---
title: "第 3 课 - 智能体模式：Plan 和 Autopilot"
description: "使用 Plan 商定实施方案，使用 Autopilot 根据议题构建筛选功能，并在 Interactive 模式中审查结果。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

我们刚才为项目添加了一项小功能，但较大的更改需要更完善的流程。GitHub Copilot CLI 可融入组织现有的工作流，帮助我们以正确的方式构建正确的内容。本课是多个课程中的第一课，你将遵循典型的智能体驱动开发流程：从议题创建新功能，确保代码有效、功能符合预期，最后将其成功合并到项目中。

> [!NOTE]
> 后续功能工作会继续使用同一个对话和分支。通常会针对不同文件类型使用不同分支或 PR，但为了聚焦核心概念，本研讨会会简化流程。

在本课中，你将：

- 从 GitHub 议题开始新的 Copilot 对话。
- 在 Plan 模式中定义需求。
- 使用 Autopilot 模式实现新功能。
- 审查代码。
- 在转发的浏览器中手动验证功能。

继续处理此功能时，你还会更新存储库指令、自定义现有的 `quality-checks` 技能、添加 MCP 验证、创建 QA 智能体并打开功能 PR。

## 场景

Tailspin Toys 的游戏目录不断增长，访客需要按类别和 publisher 缩小范围。待办议题描述了该功能，但诸如类别组合筛选等细节需要在编码前商定。你将使用 Plan 模式确定这些决策，然后授权一个范围明确的 Autopilot 实现。

## 背景

在开发流程中引入 AI 编程智能体并不会改变基本原则，反而会让这些原则更重要！大多数开发人员遵循类似以下的流程：

1. 从描述需求的已创建议题开始。
2. 制定实现计划。
3. 编写并审查代码。
4. 运行测试验证代码。
5. 手动验证新功能。
6. 创建拉取请求（PR）。
7. 代码通过审查且持续集成流程成功后，合并代码。

> [!NOTE]
> 具体流程会根据团队和组织而有所不同，但大多数流程都与上述过程类似。

遵循这种标准方法，可以确保 AI 生成的代码符合既定要求，并像手动编写的代码一样经过审查。

## 对话模式

**对话模式**决定智能体具有多少自主权。按 <kbd>Shift</kbd>+<kbd>Tab</kbd> 可在模式间切换：

- **Interactive**：你与智能体协作。智能体提出更改并等待你的输入后再继续。
- **Plan**：智能体先制定计划，但不能编辑项目文件。
- **Autopilot**：智能体自主工作，编写代码、运行测试并迭代，直到完成任务。

先使用 Plan 模式，审查计划后再用 Autopilot 实现。

## 从议题开始

开始筛选工作前，返回 Codespace 并确认存储库和终端已准备就绪。

1. 返回 Codespace。如果它已停止，请先重新启动。
2. 确认星级评分 PR 已合并。
3. 如果终端尚未打开，按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）。
4. 更新 `main` 并创建筛选工作分支：

   ```bash
   git checkout main
   git pull --ff-only
   git checkout -b game-filters-cli
   ```

5. 启动 Copilot CLI：

   ```bash
   copilot --yolo
   ```

6. 按两次 <kbd>Tab</kbd> 打开 **Issues** 选项卡。
7. 按 <kbd>A</kbd> 显示所有议题。
8. 使用方向键突出显示标题为 **Allow users to filter games by category and publisher** 的议题。
9. 按 <kbd>C</kbd> 将议题添加到提示中，并返回 **Session** 选项卡。

留意提示现在以 `#7`（或类似编号）开头。`#` 可将 GitHub 上的议题或拉取请求（PR）加入上下文。

## 规划筛选功能

规划让你有机会在交由 Copilot 执行之前，先确定功能实现或任务处理方式。复杂任务最好先花时间规划。现在切换到 Plan 模式并要求 Copilot 制定计划。

1. 按 <kbd>Shift</kbd>+<kbd>Tab</kbd> 切换到 Plan 模式。确认提示下方的模式指示器显示 **Plan**。
2. 在上一步添加的议题引用后输入以下提示：

   ```plaintext
   Create a plan for implementing this feature.
   ```

Copilot 会开始制定计划，先探索项目，再确定最佳方案。

3. Copilot 可能会询问筛选功能的具体行为。根据你的偏好回答，没有错误答案。
4. 计划完成后，按 <kbd>Control</kbd>+<kbd>E</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>E</kbd>（Windows/Linux）展开计划。
5. 滚动查看计划。
6. 如果计划的任何部分与已作出的决定不符，请要求 Copilot 修改。

## 批准 Autopilot

计划编写并审查完毕后，就可以开始实现。现在使用 Autopilot 让 Copilot 执行工作。

Autopilot 会让 Copilot 反复处理问题，直到它认为任务已完成。

1. 选择 **Accept plan and build on autopilot (recommended)**，或当前安装版本中含义相近的选项。
2. 确认提示下方的模式指示器显示 **Autopilot**。
3. 观察 Copilot 如何按照计划迭代、生成代码并运行测试。

> [!NOTE]
> 批准计划后可能会立即开始实现，因此务必先审查计划。如果 Copilot 报告缺少依赖项或端口冲突，请先解决环境问题，再将检查视为已完成。

## 审查并验证实现

代码生成后，必须像其他代码一样审查，然后才能合并。现在同时审查代码并运行网站，确认一切正常。

1. 按 <kbd>Shift</kbd>+<kbd>Tab</kbd> 切换到 Interactive 模式。确认模式指示器不再显示 **Plan** 或 **Autopilot**。
2. 输入 `/diff`，检查筛选实现和测试。
3. 将结果与议题及规划时作出的决定进行比较。
4. 审查代码后，按 <kbd>Esc</kbd> 退出差异视图。
5. 查看项目检查输出，并要求 Copilot 解决任何失败项。

## 体验新功能

代码看起来没问题，但是否能运行？与之前一样启动应用，并通过 Codespaces 转发端口打开网站。

1. 要求 Copilot 启动应用：

   ```plaintext
   Start the app so I can try the filtering feature in my browser. Tell me the URL and leave the server running.
   ```

2. Codespaces 报告端口 `4321` 可用时，选择 **Open in Browser**。
3. 测试类别筛选、publisher 筛选，以及计划中商定的组合方式。
4. 确认重置和无结果行为符合议题要求及已作出的决定。
5. 返回 Codespace，要求 Copilot 停止它启动的开发服务器。

## 总结和后续步骤

你使用不同对话模式构建并审查了功能。本课中你：

- 从 GitHub 议题开始新的 Copilot 对话。
- 在 Plan 模式中定义需求。
- 使用 Autopilot 模式实现新功能。
- 审查代码。
- 在转发的浏览器中手动验证功能。

接下来，[使用自定义指令][next-lesson]深入了解代码生成，确保它遵循已记录的实践。

## 资源

- [GitHub Copilot CLI 中的 Autopilot][autopilot]
- [Copilot CLI 命令参考][cli-reference]

[autopilot]: https://docs.github.com/copilot/concepts/agents/copilot-cli/autopilot
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[previous-lesson]: ../2-add-star-rating/
[next-lesson]: ../4-custom-instructions/
