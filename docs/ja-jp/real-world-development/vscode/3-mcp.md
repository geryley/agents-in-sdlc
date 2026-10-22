---
title: "演習 3 - Playwright MCP サーバーで機能をテストする"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[前のレッスン: エージェント モード][previous-lesson] · [次のレッスン: カスタム エージェント →][next-lesson]

エージェント モードを使ってフィルタリング機能を実装しました。プルリクエストを作成する前に、ブラウザーで実際に動作することを確認します。自分でアプリを操作する代わりに、**Playwright MCP サーバー**を接続して、Copilot に実際のブラウザーで機能をテストしてもらいます。その後、ブランチを公開して PR を作成します。

この演習では、次のことを行います。

- Model Context Protocol (MCP) と、MCP サーバーが Copilot にツールを追加する仕組みを学びます。
- Playwright MCP サーバーをワークスペースに追加します。
- Copilot にサーバーを使ってブラウザーでフィルタリング機能を手動テストするよう依頼します。
- ブランチを公開し、フィルタリング作業のプルリクエストを作成します。

## Model Context Protocol (MCP) とは

エージェント モードは、エディターの外部に接続できるとさらに強力になります。Copilot は Model Context Protocol (MCP) を使って外部のツールやサービスと通信します。これはエージェントが外部のツールやサービスを利用するための標準的な方法です。

![エージェント モードとコンテキスト、LLM、ツールのやり取り、および MCP サーバーと VS Code 拡張機能が追加するツールを示す図](../../../_images/ex1-mcp-diagram.png)

[Model Context Protocol (MCP)](https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/) を使うと、AI エージェントは外部のツールやサービスと通信できます。MCP によって、エージェントはリソースから最新情報を得たり、ツールを使って代わりに操作を実行したりできます。

これらのツールやリソースには、AI エージェントと外部のツールやサービスをつなぐ MCP サーバーを通じてアクセスします。MCP サーバーは、既存の API や NPM パッケージなどのローカル ツールを含む、AI エージェントと外部ツール間の通信を管理します。各 MCP サーバーが、エージェントから利用できるツールとリソースを提供します。

広く使われている MCP サーバーの例を次に示します。

- **[GitHub MCP Server](https://github.com/github/github-mcp-server)**: GitHub リポジトリを管理する API を提供します。AI エージェントは、新しいリポジトリの作成、既存リポジトリの更新、Issue やプルリクエストの管理などを行えます。
- **[Playwright MCP Server](https://github.com/microsoft/playwright-mcp)**: Playwright を使ったブラウザー自動化機能を提供します。AI エージェントは Web ページへの移動、フォームへの入力、ボタンの選択などを行えます。

ほかにもさまざまなツールやリソースにアクセスできる MCP サーバーがあります。GitHub では、見つけやすさとエコシステムへの貢献を促すために [MCP レジストリ](https://github.com/mcp)を提供しています。

> [!CAUTION]
> セキュリティの面では、MCP サーバーを他の依存関係と同様に扱ってください。使用する前にソースコードを注意深く確認し、発行元を検証して、セキュリティ上の影響を検討します。信頼できる MCP サーバーだけを使用し、機密リソースや操作へのアクセス許可には注意してください。

## MCP の構成を確認する

`.vscode/mcp.json` ファイルで、この VS Code ワークスペースで利用できる MCP サーバーを設定します。

1. Codespace で `.vscode/mcp.json` を開きます。
2. `github` サーバーがあらかじめ設定されていることを確認します。

    ```json
    {
      "servers": {
        "github": {
          "type": "http",
          "url": "https://api.githubcopilot.com/mcp/"
        }
      }
    }
    ```

この `github` エントリはプロジェクト テンプレートに含まれているため、Copilot は前の演習で Issue のバックログを読み取れました。[リモート GitHub MCP サーバー][remote-github-mcp-server]を使っており、ローカルにインストールする必要はありません。VS Code は HTTP 経由で接続し、Copilot が初めてツールを使うときに GitHub で認証します。

## Playwright MCP サーバーを追加する

次に 2 つ目のサーバーを追加します。[Playwright MCP サーバー][playwright-mcp-server]を使うと Copilot がブラウザーを操作できるため、機能をテストできます。

1. `.vscode/mcp.json` の `github` と同じ階層に `playwright` エントリを追加し、次のようにします。

    ```json
    {
      "servers": {
        "github": {
          "type": "http",
          "url": "https://api.githubcopilot.com/mcp/"
        },
        "playwright": {
          "command": "npx",
          "args": ["@playwright/mcp@latest", "--headless"]
        }
      }
    }
    ```

2. ファイルを保存します。

リモートの `github` サーバーとは異なり、`playwright` は**ローカル**で動作します。VS Code が `npx @playwright/mcp@latest` を実行してサーバーを起動します。`--headless` フラグを指定すると、Playwright はブラウザー ウィンドウを表示せずに実行されます。デスクトップ環境がない Codespace では、これが必要です。

> [!NOTE]
> Tailspin Toys ではエンドツーエンド テストに Playwright を使っているため、必要なブラウザーも通常はインストール済みです。Copilot がブラウザーの不足を報告したら、`npx playwright install chromium` を実行するよう依頼して、もう一度試します。

## サーバーを起動して信頼する

VS Code は必要に応じて MCP サーバーを起動します。Copilot が初めてサーバーのツールを使うとき、VS Code はサーバーを起動し、信頼するか確認します。

1. `.vscode/mcp.json` の `playwright` エントリの上に **Start** アクションが表示されます。選択してサーバーを起動し、設定が正しいことを確認します。
2. VS Code からサーバーを信頼するか尋ねられたら、設定を確認して信頼することを選択し、起動します。

手動で起動する必要はありません。省略すると、次の手順で Copilot がツールを必要としたときに VS Code がサーバーを起動し、信頼を確認します。

## フィルタリング機能をテストする

[Playwright MCP サーバー][playwright-mcp-server]を使うと、Copilot が実際のブラウザーを操作できます。自分でアプリを操作する代わりに、エージェントがページを開き、移動し、フィルターを適用して結果を読み取り、確認内容をまとめて報告します。会話を離れずに、機能が期待どおりに動くかをすばやく確認できます。

Playwright MCP サーバーはスクリーンショットではなく、ページの[アクセシビリティ ツリー][playwright-mcp-server]を使って動作します。エージェントは、ボタン、リンク、リスト項目などの構造化されラベル付けされた要素を、支援技術と同様に読み取ります。そのため、簡単な機能確認をアクセシビリティの基本確認にも活用できます。

サーバーが接続され、アプリが起動している状態で、次のプロンプトを Copilot に送信してフィルタリング機能を試します。

```text
Using the Playwright MCP server, open a browser to the running app at http://localhost:4321 and verify the new game filtering feature:

1. Go to the games page and note how many games are listed.
2. Apply a category filter and confirm the list updates to only show games in that category.
3. Clear it, then apply a publisher filter and confirm the list updates to that publisher.
4. Combine a category and a publisher filter and confirm the results respect both.

Report what you observe at each step, and call out anything that does not behave as expected.
```

Copilot は Playwright MCP サーバーでブラウザーを起動し、各手順を実行して結果を報告します。報告を Issue の受け入れ条件と比較してください。想定と異なる点があれば、プルリクエストを作成する前に質問するか、コードの修正を依頼します。

> [!NOTE]
> テストの前に、アプリが `http://localhost:4321` で起動している必要があります。開発サーバーを停止した場合は、プロンプトを送信する前に再起動してください。Copilot が Playwright MCP サーバーを初めて使うときは、ブラウザーのダウンロードが必要になる場合があります。不足が報告されたら、`npx playwright install chromium` を実行するよう依頼して再試行します。

[playwright-mcp-server]: https://github.com/microsoft/playwright-mcp

## ブランチを公開してプルリクエストを作成する

機能が動作することを確認したら、チームがレビューできるようプルリクエスト (PR) を作成します。最初に `filtering-vscode` ブランチを公開します。

1. Codespace の **Source Control** パネルを開き、Copilot が変更したファイルを確認します。
2. **+** アイコンを選択して変更をステージします。
3. **Sparkle** ボタンを使ってコミット メッセージを生成します。

   ![Copilot が変更したファイルを表示する Source Control パネル](../../../_images/ex3-source-control-changes.png)

4. **Publish** を選択してブランチをリポジトリにプッシュします。

## プルリクエストを作成する

プルリクエストの作成方法はいくつかあります。github.com や GitHub コマンド ライン インターフェイス (CLI) を使う方法などです。ここでは GitHub Copilot を使って PR を作成します。Copilot は関連する Issue を探し、それに紐づく PR を作成できます。

1. Copilot Chat パネルを開き、**New Chat** を選択して新しいセッションを始めます。
2. エージェント ドロップダウンから **Agent** を選び、Copilot が GitHub ツールを使えるようにします。
3. 次のプロンプトで PR を作成するよう依頼します。

   ```plaintext
   Find the issue in the repo related to filtering by category and publisher. Create a new pull request for the current branch, and associate it with the correct issue.
   ```

4. Copilot が情報を取得して操作を実行するための確認を求めたら、必要に応じて **Continue** を選択します。GitHub ツールを初めて使うとき、GitHub への認証を求められる場合があります。画面の指示に従って許可します。
5. Copilot が Issue を検索して適切なものを見つけ、PR を作成する様子を確認します。
6. Copilot が生成したリンクを選択してプルリクエストをレビューします。ただし、**まだマージしないでください**。

## まとめと次のステップ

この演習では、次のことを行いました。

- Model Context Protocol (MCP) と、MCP サーバーが Copilot にツールを追加する仕組みを学びました。
- Playwright MCP サーバーをワークスペースに追加しました。
- Copilot にサーバーを使ってブラウザーでフィルタリング機能を手動テストするよう依頼しました。
- ブランチを公開し、フィルタリング作業のプルリクエストを作成しました。

MCP サーバーで機能を**テスト**しましたが、MCP は Copilot を拡張する方法の 1 つです。次は[カスタム エージェント][next-lesson]を作成し、アクセシビリティ レビューなど、対象を絞ったタスクを効率化します。

## リソース

- [MCP とは何か、なぜ注目されているのか][mcp-blog-post]
- [Microsoft Playwright MCP Server][playwright-mcp-server]
- [GitHub MCP Server][github-mcp-server]
- [GitHub MCP Registry][mcp-registry]
- [VS Code の MCP サーバー][vscode-mcp-config]

[previous-lesson]: ../2-agent-mode/
[next-lesson]: ../4-custom-agents/
[mcp-blog-post]: https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/
[github-mcp-server]: https://github.com/github/github-mcp-server
[mcp-registry]: https://github.com/mcp
[remote-github-mcp-server]: https://github.blog/changelog/2025-06-12-remote-github-mcp-server-is-now-available-in-public-preview/
[vscode-mcp-config]: https://code.visualstudio.com/docs/agents/reference/mcp-configuration
