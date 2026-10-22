---
title: "练习 5：迭代改进 GitHub Copilot 的工作"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：管理智能体][previous-lesson] |
|:--|

## 审查工作

在本实验室中，你让 GitHub Copilot 执行了多项任务，以改善用户体验和增加功能：为代码添加文档、为设计团队构建相关游戏功能，以及实现高对比度和浅色模式切换等无障碍功能。现在来检查代码更改；必要时，可以向 Copilot 提供反馈来改进工作。

### 场景

如前所述，采用生成式 AI 并不会改变软件设计和 DevOps 的基本原则。我们始终需要审查生成的代码，并遵循正常的 DevOps 流程。因此，在为团队启用审查之前，先审查 GitHub Copilot 为文档、相关游戏功能和无障碍功能提供的建议。

## 安全与 GitHub Copilot 云智能体

Copilot 云智能体异步且无人监督地执行任务，因此设有安全限制：

- Copilot 对存储库只有读取权限，对其代码分支只有写入权限。
- 云智能体在 GitHub Actions 中运行，并在单独的临时环境中工作。
- 任何 GitHub Actions 工作流都必须由人工批准后才能运行。
- [默认限制对外部资源的访问](https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall)，其中也包括 MCP 服务器。

## 审查生成的文档

先检查 GitHub Copilot 云智能体创建的第一个拉取请求（PR），即为代码添加文档的 PR。你将使用 GitHub.com 中的标准 PR 界面完成此任务。

> [!NOTE]
> 查看 PR 时，可能会看到 GitHub Copilot 被防火墙阻止的警告。这是**预期行为**，因为默认情况下 Copilot 对外部资源（包括外部 MCP 服务器）的访问受到限制。如有需要，可以[自定义或禁用 Copilot 云智能体防火墙][agent-firewall]。

1. 返回 github.com 上的存储库。
2. 选择 **Pull Requests** 打开拉取请求列表。
3. 打开标题类似 **Add missing documentation** 或更完整标题的拉取请求。

> [!NOTE]
> 如果 Copilot 仍在处理任务，拉取请求会带有 **[WIP]** 标记。此时请等待 Copilot 完成工作。可能需要几分钟，可以休息一下或回顾之前学到的内容。

4. PR 准备就绪后，选择 **Files changed** 选项卡并审查更改。

    ![Files changed 选项卡](../../../_images/shared-pr-files-changed.png)

5. 查看更新后的代码，其中包括新生成的 TSDoc 文档注释及其他文档。具体更改可能有所不同。

   浏览更改时，留意 TSDoc 文档注释、TypeScript 约定和注释标题。这些内容来自练习 2 开始时查看的自定义指令文件。

6. 审查更新并确认没有问题后，返回 **Conversation** 选项卡并向下滚动。
7. 查看是否有工作流等待批准的提示。
8. 如果工作流正在等待批准，选择 **Approve and run workflows**。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

9. 工作流应会在拉取请求的检查部分排队。顺利的话，单个 Astro 应用的项目检查会通过。此过程可能需要几分钟。

## 请求 GitHub Copilot 修改

与 Copilot 合作并不是单向的。你可以像提及其他团队成员一样，在拉取请求评论或代码行内评论中提及 Copilot。Copilot 会看到这些评论，并启动新会话来处理请求。由于结果并非确定，无法提供规定所有情况下都适用的提示文本。可以要求 Copilot：

- 为每个代码文件顶部添加简要说明其用途的注释标题。
- 为 TypeScript 和 Astro 文件添加 TSDoc 文档注释。
- 创建一份介绍 Astro 应用结构的 README。

1. 添加一条评论，请求修改生成的文档，并像提及其他用户一样提及 **@copilot**。可以使用上面的想法，也可以提出其他文档改进建议。
2. 选择 **View Session**，观察 Copilot 执行工作。留意它会启动新会话来进行更新。
3. 选择 **Back to pull request** 返回拉取请求。

    ![Back to pull request](../../../_images/ex7-back-to-pr.png)

4. Copilot 完成更改后，PR 中应会出现新的提交。
5. 选择 **Files changed** 选项卡并审查更改。

可以继续迭代，直到对结果满意。完成后，可以将 PR 从草稿转换为待审查状态，并合并到 main 分支。

![将 PR 转为待审查状态](../../../_images/ex7-ready-for-review.png)

## 审查相关游戏功能

接下来，检查 Copilot 为解决“在游戏详情页显示相关游戏”议题而创建的 PR。

1. 返回 GitHub.com 上的存储库。
2. 选择 **Pull Requests** 选项卡。
3. 选择标题类似 **Show related games on the game details page** 或更完整标题的 PR。
4. 选择 **Files changed** 选项卡，审查生成的代码。
5. 审查更新并确认没有问题后，返回 **Conversation** 选项卡并向下滚动。
6. 查看是否有工作流等待批准的提示。
7. 如果工作流正在等待批准，选择 **Approve and run workflows**。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

8. 工作流应会在拉取请求的检查部分排队。顺利的话，单个 Astro 应用的项目检查会通过。此过程可能需要几分钟。
9. **可选：**可以在 Codespace 中切换到此分支，手动测试相关游戏功能。打开 Codespace 和终端，运行以下命令（将 `<branch-name>` 替换为 Copilot 创建的分支名称，例如 **copilot/fix-8**）：

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

Copilot 已构建相关游戏功能！与之前一样，你可以通过迭代向 Copilot 请求更新。例如，可以要求调整显示的相关游戏数量，或确保添加注释标题和 TSDoc 文档注释（请记住，分配此任务时，你还没有更新自定义指令！）。与之前一样，可以在 **Conversation** 选项卡中添加评论来提出请求，Copilot 会看到评论并启动新会话。

## 审查无障碍功能

最后，审查使用无障碍自定义智能体实现的功能。此 PR 应包含练习 3 中分配的高对比度模式，以及练习 4 中在任务控制中心追加要求的浅色模式。

1. 返回 GitHub.com 上的存储库。
2. 选择 **Pull Requests** 选项卡。
3. 选择标题类似 **Add high contrast mode to website** 或更完整标题的 PR。

> [!NOTE]
> 如果 Copilot 仍在处理任务，拉取请求会带有 **[WIP]** 标记。此时请等待 Copilot 完成工作，可能需要几分钟。

4. 选择 **Files changed** 选项卡，审查生成的代码。
5. 检查实现，尤其留意：
   - 用于切换模式的切换 UI 组件。
   - 使用本地存储持久保存用户偏好设置。
   - 高对比度和浅色模式的 CSS 或样式更改。
   - 无障碍属性（ARIA 标签、键盘导航等）。
   - 管理模式切换的 JavaScript/TypeScript 代码。
6. 审查更新并确认没有问题后，返回 **Conversation** 选项卡并向下滚动。
7. 查看是否有工作流等待批准的提示。
8. 如果工作流正在等待批准，选择 **Approve and run workflows**。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

9. 工作流应会在拉取请求的检查部分排队。顺利的话，单个 Astro 应用的项目检查会通过。此过程可能需要几分钟。
10. **可选：**可以在 Codespace 中切换到此分支，手动测试无障碍功能。打开 Codespace 和终端，运行以下命令（将 `<branch-name>` 替换为 Copilot 创建的分支名称）：

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

    然后启动应用，在浏览器中测试高对比度和浅色模式切换，确认其按预期工作，并在重新加载页面后保留设置。

请注意，无障碍自定义智能体如何帮助 Copilot 遵循无障碍最佳实践实现这些功能。如果发现无障碍问题或改进空间，可以像处理前面几个 PR 一样，在评论中提及 **@copilot** 请求更新。

## 可选练习 — 继续委派任务

云智能体最适合处理真实待办事项，让你可以先专注其他工作。可以尝试在存储库中再创建一些议题并分配给 Copilot。以下是一些想法：

- 在游戏详情页创建支持者兴趣表单。
- 为游戏列表页实现分页。
- 为数据访问辅助函数添加输入验证和错误处理。

## 总结

你已完成云智能体工作流。在这些课程中，你：

- **检查了此存储库自带的自定义指令文件**，以便之后观察这些指令对云智能体输出的影响。
- **将议题分配给 Copilot 云智能体**，并观察它设置环境、规划任务和异步执行工作的过程。
- **创建并使用了自定义智能体**处理无障碍问题，添加高对比度和浅色模式切换。
- **使用 Copilot agents 页面作为任务控制中心**，监控并指导正在执行的无障碍会话。
- **审查并迭代改进了云智能体创建的拉取请求**，通过提及 `@copilot` 请求更改并批准工作流。

## 回顾和后续步骤

你已完成云智能体工作流。其他工作流可以补充你在此处练习的内容：

- 🖥️ **[VS Code 工作流](../../vscode/)** — 在 IDE 中直接体验 Copilot Chat 智能体模式和 MCP 集成。
- 💻 **[CLI 工作流](../../cli/)** — 在终端中交付两项经过审查的更改，使用 Plan 和 Autopilot 模式、智能体技能、自定义 QA 智能体、Playwright MCP、Agent Merge，以及可选的 `/delegate` 云智能体桥接。

在自己的存储库中，可以尝试以下后续练习：

- 将重构或测试覆盖率议题分配给云智能体。
- 为其他领域创建自定义智能体。
- 为另一种技术栈设置 `copilot-setup-steps.yml`。

## 资源

- [GitHub Copilot][github-copilot]
- [关于 Copilot 智能体][copilot-agents]
- [将 GitHub 议题分配给 Copilot][assign-issue]
- [Copilot 云智能体设置工作流最佳实践][cloud-agent-best-practices]
- [配置 Copilot 云智能体防火墙][agent-firewall]

---

| [← 上一课：管理智能体][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall
[previous-lesson]: ../4-managing-agents/
