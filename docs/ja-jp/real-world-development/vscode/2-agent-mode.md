---
title: "演習 2 - Copilot Agent Mode で新機能を追加する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

アプリケーションに簡単な変更を加えるだけでも、通常は複数のファイルを更新し、テストなどを実行する必要があります。開発者は、必要なファイルを探し、変更し、テストを実行し、問題を修正して、変更漏れのファイルを見つけてさらに更新する、といった作業を繰り返します。

こうした作業には Copilot Agent Mode が役立ちます。

Copilot Agent Mode は IDE 内でより自律的に作業します。開発者と同様に、まず既存のプロジェクト構造を調査し、必要な変更を行い、テストなどのタスクを実行し、問題が見つかると自動で修正します。Agent Mode を使ってサイトに機能を追加する方法を見てみましょう。

> [!NOTE]
> 名前は似ていますが、エージェント モードと cloud agent は異なる種類の作業に対応します。エージェント モードは IDE 内で動作し、すばやく結果を確認しながら対話できます。cloud agent はチームの一員のように非同期で作業し、Issue とプルリクエストを通じてやり取りします。

この演習では、次のことを学びます。

- Copilot Agent Mode がプロジェクトを調査し、関連するファイルを特定して、複数の変更を調整する方法。
- GitHub Copilot Agent Mode で UI とデータレイヤーにまたがる新機能を実装する方法。
- コードベースにマージする前に、Copilot Agent Mode が生成した変更とテストをレビューする方法。

## シナリオ

ゲーム一覧が増えているため、カテゴリや発行元で絞り込めるようにします。前の演習では発行元ヘルパーを追加しました。次は Copilot Agent Mode を使い、残りのデータレイヤー、UI、テストを完成させます。

## Tailspin Toys の Web サイトを起動する

変更を加える前に、Tailspin Toys の現在の機能を確認します。

この Web サイトは、開発者をテーマにしたボードゲームのクラウドファンディング プラットフォームです。利用者はゲーム一覧と詳細を表示できます。サイトは Astro の単一アプリで、ビルド時にページを静的 HTML として生成します。ページの frontmatter から Drizzle ORM を使ってローカル SQLite データベースを直接参照します。独立したバックエンド API やクライアント側の UI フレームワークはありません。再利用可能なデータアクセス ヘルパーは `src/lib/` に置き、標準の DOM API を使った小規模で範囲を限定した Astro の `<script>` でインタラクティブな機能を追加します。

### Web サイトを起動する

Web サイトを実行しやすいように、Astro 開発サーバーを起動する `npm run dev` タスクが用意されています。GitHub Codespace で次の手順を実行します。

1. Codespace に戻ります。前の演習の後、`main` ブランチにいるはずです。
2. <kbd>Control</kbd>+<kbd>\`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>\`</kbd> (Windows/Linux) を押して、Codespace 内に新しいターミナルを開きます。
3. `main` を更新し、フィルタリング用の新しいブランチを作成します。

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. 次のコマンドで Web サイトを起動します。

   ```bash
   npm run dev
   ```

   Astro 開発サーバーが起動すると、次のような URL を示すバナーが表示されます。

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> `http://localhost:4321` を開くよう促すダイアログが表示された場合は、**x** を選択して閉じます。

5. <kbd>Command</kbd> (Mac) または <kbd>Ctrl</kbd> (Windows/Linux) を押しながら、ターミナルに表示された Astro サーバーのアドレス `http://localhost:4321` を選択してサイトを開きます。

> [!NOTE]
> Codespace のターミナルで localhost のリンクを選択すると、自動的に `https://<your-codespace-name>-4321.app.github.dev/` に転送されます。これは Codespace にアクセスするためのプライベート トンネルです。Codespace が Web サーバーをホストしています。

### Web サイトを確認する

サイトが起動したら、機能を確認できます。主な機能は次のとおりです。

- **ホーム ページ**: タイトル、画像、説明とともに、ボードゲームの一覧を表示します。
- **ゲーム詳細ページ**: ゲームを選択すると、タイトル、説明、発行元、カテゴリなど、詳しい情報を表示します。

## Copilot でバックログを確認する

> [!TIP]
> **Copilot Chat を開く**
>
> 次の演習を始める前に Codespace に戻り、Copilot Chat パネルを開いて **New Chat** を選択し、新しい会話を開始します。モードとモデルの選択は演習ごとに異なります。必要な箇所で説明します。

Web サイトは動作しますが、新しい機能を追加して改善できます。まずバックログを確認しましょう。テンプレートからプロジェクトを設定すると Issue のバックログも作成されます。GitHub Copilot に一覧を表示してもらいます。

1. Chat ビューのエージェント ドロップダウンから **Agent** を選択します。**Agent** エージェントは自律的に計画を立て、複数のファイルを変更し、ターミナル コマンドを実行してツールを呼び出します。

   ![Chat ビューのエージェント選択メニュー](../../../_images/shared-chat-mode-selector.png)

2. 利用可能なモデルの一覧から **Claude Sonnet 4.5** を選択します。

> [!CAUTION]
> このワークショップの作成者は、特定のモデルを推奨しているわけではありません。ワークショップの作成時に Claude Sonnet 4.5 を使用したため、指示に含めています。提案の一貫性を保ち、演習を進めやすくするためですが、大規模言語モデル (LLM) は確率的に動作するため、提案内容が記載例と異なる場合があります。これは正常であり、想定されています。

> [!NOTE]
> LLM の確率的な特性により、Copilot が別の MCP コマンドを使う場合がありますが、タスクは完了できるはずです。

3. 次のプロンプトを Copilot に送信し、Issue のバックログを確認します。

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. **Continue** を選択し、すべての Issue を一覧表示するコマンドを実行します。
5. 生成された Issue の一覧を確認します。

Copilot が利用者にとって役立つと思われる順に、Issue を優先順位付けしていることを確認してください。

## 命令ファイルを確認する

エージェントにコードを生成させる前に、作業のコンテキストを伝えるために使う命令ファイルを確認します。Web サイトの機能を追加する方法が記載された[ユーザー インターフェイス (UI) 命令][ui-instructions]ファイルを使います。

1. Codespace で `.github/instructions/ui.instructions.md` を開きます。
2. 機能追加に関する全体的なガイダンスを確認します。次の内容があります。
   - アーキテクチャの概要。
   - コンポーネント設計、テスト容易性、アクセシビリティの原則。
   - 次のようなファイル固有の命令へのリンク。
     - Astro
     - Tailwind CSS
     - Drizzle

> [!TIP]
> 命令ファイルから、ほかの命令ファイルやプロジェクト内のファイルを参照できます。パスは命令ファイルからの相対パスです。これにより、命令を再利用し、複雑な内容を管理しやすい単位に分割して、例やテンプレートを提示できます。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md

## フィルタリング機能を実装する

フィルタリングを完成させるには、アプリケーションに少なくとも次の 3 つの変更を行います。

- データレイヤー (`src/lib/`) の残りのフィルタリング ロジックを追加または改善します。
- フィルタリングの動作を確認するテストを追加または更新します。
- ゲーム一覧ページにフィルタリング UI を追加します。

さらに、すべてのテストに合格してから変更をコードベースにマージする必要があります。Copilot Agent Mode にこれらのタスクを依頼します。

1. Copilot との現在の会話を続けるか、**New Chat** を選択して新しい会話を開始します。
2. **Add Context**、**Instructions**、**ui** の順に選択して命令ファイルを追加します。

   ![UI 命令ファイルを選択する例](../../../_images/ex3-select-instructions-file.png)

3. Chat ビューのエージェント ドロップダウンで **Agent** が選択されていることを確認します。

   ![Chat ビューのエージェント選択メニュー](../../../_images/shared-chat-mode-selector.png)

4. モデルに **Claude Sonnet 4.5** が選択されていることを確認します。
5. バックログの関連 Issue に基づいて機能を実装するよう、次のプロンプトで Copilot に依頼します。

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. Copilot がプロジェクトを調査し、機能に関連するファイルを見つけます。データレイヤーのヘルパー、UI、テストを確認した後、ファイルの変更とテストを始めます。

   ![Copilot がプロジェクト ファイルを調査する画面](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot はプロジェクトの調査、ファイルの変更、テストの実行などを行います。タスクやコードベースの複雑さによっては数分かかります。その間、コード エディターに **Keep** と **Undo** が表示される場合があります。Copilot の作業中に選択する必要はありません。完了すると、すべての変更に **Keep** または **Undo** が表示されます。

7. Copilot に促されたら **Continue** を選択し、テストを実行します。

   ![テストの実行を確認するダイアログ](../../../_images/ex3-agent-mode-run-tests.png)

8. 作業中に一時停止したり、テストが失敗したりする場合があります。問題ありません。Copilot はタスクを完了してエラーが見つからなくなるまで、コード生成とテストを繰り返します。

   ![Copilot Agent Mode の完了した Chat セッション](../../../_images/ex3-agent-mode-proposed-changes.png)

9. 生成されたコードに問題がないかを確認します。

> [!CAUTION]
> Copilot などの AI ツールが生成したコードは、必ずレビューしてください。

10. Web サイトを表示しているブラウザーに戻り、新しい機能を試します。
11. すべてが動作し、コードをレビューできたら、Copilot Chat ウィンドウで **Keep** を選択します。

## まとめと次のステップ

この演習では、GitHub Copilot Agent Mode を使って Tailspin Toys の Web サイトに機能を追加する方法を学びました。次のことを確認しました。

- GitHub Copilot Agent Mode で UI とデータレイヤーにまたがる機能を実装できる。
- Copilot Agent Mode がプロジェクトを調査し、関連ファイルを特定して、複数の変更を調整できる。
- コードベースにマージする前に、Copilot Agent Mode が生成した変更とテストをレビューする必要がある。

次は [Playwright MCP サーバーで機能をテストし][next-lesson]、そのプルリクエストを作成します。

### 追加の演習 — ページングを実装する

ゲーム一覧が増えると、ページングが必要になります。この演習で学んだことを活用し、Copilot にページングの実装を依頼します。次の点を検討してください。

- 既存の命令ファイルを使うなど、現在のベスト プラクティスに従う。
- ページ サイズを利用者に選択させるか、固定にするかなど、ページングの方式を決める。
- 希望する実装に必要なガイダンスを、プロンプトに含める。
- Copilot に変更を依頼し、コンテキストを追加しながら反復する。これは Copilot と作業するときの通常の流れです。

## リソース

- [Copilot の ask、edit、agent モード: 機能と使い分け][choose-mode]
- [VS Code のエージェント モード][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
