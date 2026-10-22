---
title: "演習 2 - GitHub Copilot cloud agent"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [前のレッスン: カスタム命令][previous-lesson] |
|:--|

技術的負債に悩んでいない組織はほとんどないでしょう。未解決のセキュリティ問題、更新が必要なレガシー コード、実装する時間がなくバックログに残った機能要望などが考えられます。GitHub Copilot cloud agent は、コードの更新や機能の追加などを自律的に実行できます。エージェントが作業を終えると、開発者がレビューできるドラフト PR が作成されます。これにより、時間のかかる作業を任せて開発を加速し、開発者はより大きな課題に集中できます。

Copilot cloud agent を使って、次のことを学びます。

- コード生成用の環境をカスタマイズする。
- 操作が安全に行われるようにする。
- Issue の範囲を明確にする重要性を理解する。
- Issue を Copilot に割り当てる。

## シナリオ

Tailspin Toys には対応したい技術的負債があります。サイトの最初のバージョンを開発した業者は、ドキュメントを十分に残していません。最初の作業として、アプリケーション内のエクスポートされたすべての関数に TSDoc のドキュメント コメントを追加します。

また、デザイン チームはゲームを見つけやすくしたいと考えています。各ゲームの詳細ページに、同じカテゴリに属するほかのタイトルを関連ゲームとして表示し、閲覧を続けられるようにします。現時点では、受け入れテストで UX を試せる簡易的な実装で十分です。この作業は現在ブロッカーですが、より優先度の高い Issue があります。

このような作業は優先度が下がりやすいため、Copilot cloud agent に割り当てるのに適しています。エージェントに非同期で作業を任せれば、開発者は別の作業に集中し、後から結果をレビューできます。

## GitHub Copilot cloud agent について

[GitHub Copilot cloud agent](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent) は、人間の開発者と同じようにバックグラウンドでタスクを実行できます。人間の開発者に作業を依頼するときと同様に、[GitHub Issue を Copilot に割り当てる][assign-issue]など、さまざまな方法で依頼できます。割り当てられると、Copilot は進行状況を追跡するドラフト プルリクエストを作成し、環境を準備して、タスクに取り組みます。実行中でも完了後でも、Copilot のセッションを確認できます。提案された解決策のレビュー準備ができると、プルリクエストで通知されます。

## 範囲を明確にした指示の重要性

GitHub Copilot に魔法はありません。考える作業を省けるわけではありません。一見簡単な作業にも、詳しく見ると複雑な点があります。[Copilot cloud agent に依頼するタスクの範囲は慎重に決めてください][cloud-agent-best-practices]。AI ペア プログラマーのように段階を踏んで作業し、学び、試し、必要に応じて調整します。生成 AI を追加しても、ソフトウェア開発の基本は変わりません。

## このリポジトリのカスタム命令

前の演習ではカスタム命令と、その命令が Copilot を導く仕組みを確認しました。カスタム命令の演習に取り組んでいれば、`.github/copilot-instructions.md` の動作をすでに見ています。まだ確認していない場合は、この機会に見ておきましょう。Copilot cloud agent に作業を割り当てる前に、このリポジトリに含まれる命令ファイルを読み取り専用で確認し、後から影響を見分けられるようにします。

リポジトリの GitHub Web UI または起動中の Codespace で、次のファイルを確認します。

- `.github/copilot-instructions.md` — **Code standards** セクション、特に TypeScript の規則と TSDoc のドキュメント コメントに関する要件を確認します。
- `.github/instructions/unit-tests.instructions.md` — frontmatter の `applyTo` フィールドを確認します。この命令は `**/*.test.ts` ファイルに適用されます。

次のセクションで `Code lacks documentation` Issue を cloud agent に割り当てたら、作成されるプルリクエストに TSDoc コメント、TypeScript の規則、コメント ヘッダーが含まれているかを確認します。これらの内容は命令ファイルに基づいています。後の演習で PR をレビューする際にも確認します。

## Copilot cloud agent 用の開発環境を設定する

誰がコードを書く場合でも、通常は適切な環境と、正常な状態を保つセットアップ スクリプトが必要です。開発者に似た方法でタスクを実行する Copilot に作業を割り当てる場合も同様です。

cloud agent は作業環境として [GitHub Actions][github-actions] を使います。`.github/workflows/copilot-setup-steps.yml` に設定する[専用のセットアップ ワークフロー][setup-workflow]を作成すると、作業開始前に必要な開発ツールや依存関係を利用できるよう環境をカスタマイズできます。この学習機会と演習の流れを支援するため、テンプレートではあらかじめ設定されています。Node.js、プロジェクトの依存関係、エンドツーエンド テスト用のブラウザー、単一の Astro アプリ用にマイグレーションとシードを行った SQLite データベースを Copilot が利用できるようにします。

```yaml
name: "Copilot Setup Steps"

# Allows you to test the setup steps from your repository's "Actions" tab
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # Set the permissions to the lowest permissions possible needed for *your steps*. Copilot will be given its own token for its operations.
    permissions:
      # If you want to clone the repository as part of your setup steps, for example to install dependencies, you'll need the `contents: read` permission. If you don't clone the repository in your setup steps, Copilot will do this for you automatically after the steps complete.
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # Frontend / app setup - Node.js (the whole app is now Astro + Drizzle/libSQL)
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

      # Migrate + seed the local SQLite database so builds and tests have data.
      - name: Set up the database
        run: npm run db:setup
```

ほかの GitHub Actions ワークフローと同様に見えますが、いくつか重要な点があります。

- `copilot-setup-steps` という単一のジョブが含まれています。Copilot がプルリクエストの作業を始める前に、GitHub Actions でこのジョブを実行します。
- `workflow_dispatch` トリガーを指定しています。これにより、リポジトリの **Actions** タブからワークフローを手動で実行できます。Copilot に実行させるのを待たずに、正常に動作するかをテストできます。

## ドキュメントを追加する

ドキュメントの重要性は広く認識されていますが、古い情報のままだったり、そもそも存在しなかったりするプロジェクトが多くあります。このような技術的負債は対応されないままになりやすく、生産性を下げ、コードベースの保守や新しい開発者の参加を難しくします。Copilot はドキュメントの作成が得意なため、cloud agent に割り当てるのに適した Issue です。バックグラウンドで必要なドキュメントを作成し、後の演習で結果を確認します。

1. 新しいブラウザー タブで github.com のリポジトリを開きます。
2. **Issues** タブを選択します。
3. **New issue** を選択して、新しい Issue のダイアログを開きます。
4. **Blank issue** を選択して新しい Issue を作成します。
5. **Title** に `Code lacks documentation` と入力します。
6. **Description** に次の内容を入力します。

    ```plaintext
    Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
    ```

7. **Create** を選択して Issue を作成します。
8. 右側の **Assign to Copilot** を選択して、割り当てのダイアログを開きます。

    ![Issue に Copilot を割り当てる画面](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。

    ![Copilot の割り当て詳細](../../../_images/ex4-assign-copilot-details.png)

10. **Pull Requests** タブを選択します。
11. 新しく作成されたプルリクエスト (PR) を開きます。タイトルは `[WIP]: Code lacks documentation` などです。PR が一覧にない場合は、少し待ってからブラウザーを更新します。
12. 数分後、Copilot が todo リストを作成したことを確認できます。

> [!NOTE]
> Copilot の todo リストが PR に表示されるまで数分かかる場合があります。Copilot は、先ほどのワークフローを実行して環境を作成し、プロジェクトを分析して、問題を解決する方法を検討しています。

13. リストと、Copilot が実行するタスクを確認します。
14. プルリクエストのタイムラインを下にスクロールし、Copilot が Issue の作業を始めたことを示す更新を確認します。
15. **View session** ボタンを選択します。

    ![Copilot セッションの表示](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 表示が更新されていない場合は、ブラウザーを更新してください。

16. ライブ セッションをスクロールして、Copilot が問題を解決する様子を確認します。コードの調査、状態の把握、計画を検討するための一時停止、コードの作成などを行います。

作業には数分かかる場合があります。Copilot cloud agent の主な利点は、タスクを非同期で実行し、その間に別の作業を進められることです。この利点を活用するため、もう 1 つのタスクも cloud agent に割り当ててから、アプリケーションの機能追加に取り組みます。

## ゲーム詳細ページに関連ゲームを追加する

GitHub Copilot cloud agent の大きな利点の 1 つは、作業を分担できることです。デザイン チーム向けの関連ゲーム セクションの追加に時間がかからないとしても、その間に別の作業を進められます。cloud agent に割り当てましょう。

1. github.com のリポジトリに戻ります。
2. **Issues** タブを選択します。
3. **New issue** を選択して、新しい Issue のダイアログを開きます。
4. **Blank issue** を選択して、空のテンプレートを使います。
5. **Title** に `Show related games on the game details page` と入力します。
6. **Description** に次の内容を入力します。

    ```markdown
    We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
    ```

7. **Create** を選択して Issue を作成します。
8. 右側の **Assign to Copilot** を選択して、割り当てのダイアログを開きます。

    ![Issue に Copilot を割り当てる画面](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。

まもなく、Copilot が作業中であることを示す 👀 が Issue の最初のコメントに表示されます。

![Copilot が Issue の作業中であることを目の絵文字で示す画面](../../../_images/ex4-issue-eyes-emoji.png)

Copilot は 2 つ目の依頼に取り組んでいます。Copilot cloud agent は開発者と同様に作業するため、積極的に監視する必要はありません。完了後にレビューすればよいので、次はカスタム エージェントの作成と使用に進みます。

## まとめと次のステップ

このレッスンでは、[GitHub Copilot cloud agent][copilot-agents] を確認しました。cloud agent に Issue を割り当てると、非同期に作業を進められます。技術的負債への対応、新機能の追加、フレームワーク間のコード移行などに Copilot を活用できます。

次の概念を学びました。

- コード生成用の環境をカスタマイズする。
- 操作が安全に行われるようにする。
- Issue の範囲を明確にする重要性を理解する。
- Issue を Copilot に割り当てる。

cloud agent がバックグラウンドで作業する間に、カスタム エージェントの作成と使用に進みます。[Copilot cloud agent は MCP サーバーも利用できます][cloud-agent-mcp]。前のモジュールで確認したカスタム命令も使用できます。

## リソース

- [Copilot cloud agent について][copilot-agents]
- [GitHub Issue を Copilot に割り当てる][assign-issue]
- [Copilot cloud agent セットアップ ワークフローのベスト プラクティス][cloud-agent-best-practices]

| [次のレッスン: カスタム エージェント →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
