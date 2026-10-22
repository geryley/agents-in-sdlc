---
title: "练习 2 - GitHub Copilot 云智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：自定义指令][previous-lesson] |
|:--|

几乎所有组织都面临技术债务问题。这可能是尚未解决的安全问题、需要更新的旧代码，或因缺少实施时间而长期搁置的功能请求。GitHub Copilot 云智能体可以自主执行更新代码、添加功能等任务。完成工作后，它会创建草稿 PR，供开发人员审查。这样可以委派繁琐任务、加快开发流程，并让开发人员专注于更重要的工作。

在本课中，你将使用 Copilot 云智能体：

- 自定义代码生成环境。
- 确保操作安全执行。
- 了解清晰定义议题范围的重要性。
- 将议题分配给 Copilot。

## 场景

Tailspin Toys 想要处理一些技术债务。最初受聘开发网站的承包商没有留下完善的文档——事实上，你会发现代码完全没有文档。团队希望先为应用程序中所有导出的函数添加 TSDoc 文档注释。

此外，设计团队已准备好改进游戏发现功能。他们希望每个游戏详情页都显示相关游戏，即同一类别中的其他游戏，以便用户继续浏览。目前不需要完善的实现，只要能用于用户体验验收测试即可。该功能目前是一个阻碍，不过当前还有优先级更高的议题。

这类任务很容易被推迟，因此非常适合分配给 Copilot 云智能体。云智能体可以异步处理任务，让开发人员先专注其他工作，稍后再回来审查 Copilot 的工作并确认结果符合预期。

## 认识 GitHub Copilot 云智能体

[GitHub Copilot 云智能体](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent)可以像人类开发人员一样在后台执行任务。与人类开发人员协作类似，你可以通过多种方式分配工作，包括[将 GitHub 议题分配给 Copilot](https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions)。分配后，Copilot 会创建草稿拉取请求来跟踪进度，设置环境并开始处理任务。你可以在会话执行期间或完成后查看详情。建议解决方案准备好供你审查时，Copilot 会在拉取请求中提及你。

## 明确指令范围的重要性

GitHub Copilot 并没有魔法——使用它并不意味着可以跳过思考。即使是看似简单的操作，深入分析后也会发现复杂之处，因此请[谨慎界定分配给 Copilot 云智能体的任务范围][cloud-agent-best-practices]。将它视为 AI 编程伙伴：分阶段开展工作，不断学习、试验并调整。加入生成式 AI 并不会改变软件开发的基本原则。

## 本存储库中的自定义指令

前面的练习介绍了自定义指令及其引导 Copilot 的方式。如果你已完成自定义指令实操，就会见过 `.github/copilot-instructions.md` 的作用；如果尚未学习，现在可以快速查看。在将工作分配给 Copilot 云智能体之前，先只读查看存储库中现有的指令文件，稍后便能观察到它们的影响。

在存储库的 GitHub 网页界面中，或在已启动的 codespace 中打开以下文件：

- `.github/copilot-instructions.md`：查看 **Code standards** 部分，尤其是 TypeScript 约定和 TSDoc 文档注释要求。
- `.github/instructions/unit-tests.instructions.md`：留意 `applyTo` frontmatter，它将指令范围限定为 `**/*.test.ts` 文件。

下一节将 `Code lacks documentation` 议题分配给云智能体时，留意生成的拉取请求中是否包含 TSDoc 文档注释、TypeScript 约定和注释标题——这些内容来自上述指令文件。稍后的练习中审查 PR 时还会再次提到。

## 为 Copilot 云智能体设置开发环境

无论由谁编写代码，通常都需要专门的环境并运行一些设置脚本，确保一切准备就绪。将任务分配给 Copilot 时也是如此，因为它会以类似软件工程师（SWE）的方式执行任务。

云智能体使用 [GitHub Actions][github-actions] 环境执行工作。你可以通过创建专门的设置工作流来自定义该环境，并将文件配置为 `.github/workflows/copilot-setup-steps.yml`。该工作流会在云智能体开始工作前运行，以便提供所需开发工具和依赖项。本实验室已预先配置好此环境，帮助顺利完成练习：为 Copilot 提供 Node.js、项目依赖项、端到端测试所需的浏览器二进制文件，以及单个 Astro 应用所需的迁移并填充数据的 SQLite 数据库：

```yaml
name: "Copilot Setup Steps"

# 允许你从存储库的“Actions”选项卡测试设置步骤
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # 为*你的步骤*设置尽可能低的权限。Copilot 会获得自己的令牌来执行操作。
    permissions:
      # 如果希望在设置步骤中克隆存储库（例如安装依赖项），需要 `contents: read` 权限。如果不在设置步骤中克隆存储库，Copilot 会在步骤完成后自动为你克隆。
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # 前端 / 应用设置 - Node.js（整个应用现在基于 Astro + Drizzle/libSQL）
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"

      - name: Install JavaScript dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      # 迁移并填充本地 SQLite 数据库，为构建和测试准备数据。
      - name: Set up the database
        run: npm run db:setup
```

该文件与其他 GitHub 工作流文件类似，但有几个要点：

- 其中只有一个名为 `copilot-setup-steps` 的作业。Copilot 开始处理拉取请求前，GitHub Actions 会运行此作业。
- 留意 `workflow_dispatch` 触发器，它允许你在存储库的 **Actions** 选项卡中手动运行工作流。这样可以测试工作流是否能成功运行，而不用等到 Copilot 触发它。

## 添加文档

大家都知道文档很重要，但大多数项目中的文档不是过时就是缺失。这类技术债务经常无人处理，降低工作效率，也使代码库难以维护，或难以让新开发人员加入团队。Copilot 擅长生成文档，因此很适合将此任务分配给 Copilot 云智能体。它会在后台生成所需文档，之后的练习中你将回来审查结果。

1. 在新的浏览器选项卡中打开 github.com 上的存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue** 打开新议题对话框。
4. 选择 **Blank issue** 创建议题。
5. 将 **Title** 设为 `Code lacks documentation`。
6. 将 **Description** 设为：

    ```plaintext
    Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
    ```

7. 选择 **Create** 创建议题。
8. 在右侧选择 **Assign to Copilot** 打开分配对话框。

   ![将议题分配给 Copilot](../../../_images/shared-assign-copilot.png)

9. 选择 **Assign**。
10. 选择 **Pull Requests** 选项卡。
11. 打开新创建的拉取请求，标题类似 `[WIP]: Code lacks documentation`。如果列表中尚未出现新的 PR，稍等片刻并刷新浏览器窗口。
12. 几分钟后，Copilot 应会创建待办事项列表。

> [!NOTE]
> Copilot 可能需要几分钟才会在 PR 中显示待办事项列表。Copilot 正在创建环境（运行前面介绍的工作流）、分析项目并确定最佳处理方案。

13. 审查待办事项列表以及 Copilot 准备完成的任务。
14. 向下滚动拉取请求时间线，应能看到 Copilot 已开始处理议题的更新。
15. 选择 **View session** 按钮。

   ![Copilot 会话视图](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 可能需要刷新窗口才能看到更新后的状态标记。

16. 浏览实时会话，观察 Copilot 如何解决问题，包括探索代码、了解当前状态、暂停以思考并确定合适方案，以及创建代码。

这可能需要几分钟。Copilot 云智能体的主要目标之一是异步执行任务，让我们能够处理其他工作。接下来，我们将利用这一功能：再分配一项任务给 Copilot 云智能体，然后着手编写代码，为应用添加功能。

## 为游戏详情页添加相关游戏部分

如前所述，GitHub Copilot 云智能体的一大优势是可以分担工作，让你专注于一项任务，同时让它处理另一项任务。为设计团队添加相关游戏部分也许并不需要太多时间，但这些时间仍可以用于其他工作。现在就将此任务分配给 Copilot 云智能体。

1. 返回 github.com 上的存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue** 打开新议题对话框。
4. 选择 **Blank issue** 使用空白模板。
5. 将 **Title** 设为 `Show related games on the game details page`。
6. 将 **Description** 设为：

    ```markdown
    We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
    ```

7. 选择 **Create** 创建议题。
8. 在右侧选择 **Assign to Copilot** 打开分配对话框。

   ![将议题分配给 Copilot](../../../_images/shared-assign-copilot.png)

9. 选择 **Assign**。

不久后，你会在议题的首条评论中看到 👀，表示 Copilot 正在处理任务。

![Copilot 使用眼睛表情符号表示正在处理议题](../../../_images/ex4-issue-eyes-emoji.png)

Copilot 现在正在努力完成第二项请求！Copilot 云智能体的工作方式类似软件工程师，因此你不必主动监控它，可以等任务完成后再审查。接下来，我们来了解如何创建和使用自定义智能体。

## 总结和后续步骤

本课介绍了 [GitHub Copilot 云智能体][copilot-agents]。使用云智能体可以将议题分配给 Copilot，由它异步处理。你可以用 Copilot 处理技术债务、开发新功能，或协助将代码从一种框架迁移到另一种框架。

你了解了以下概念：

- 自定义代码生成环境。
- 确保操作安全执行。
- 明确界定议题范围的重要性。
- 将议题分配给 Copilot。

云智能体正在后台处理任务，现在可以开始创建和使用自定义智能体。[Copilot 云智能体也可以使用 MCP 服务器][cloud-agent-mcp]，并且能够使用之前模块中介绍的自定义指令。

## 资源

- [关于 Copilot 云智能体][copilot-agents]
- [将 GitHub 议题分配给 Copilot][assign-issue]
- [Copilot 云智能体设置工作流最佳实践][cloud-agent-best-practices]

| [下一课：自定义智能体 →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
