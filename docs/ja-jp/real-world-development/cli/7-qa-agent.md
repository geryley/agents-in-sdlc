---
title: "レッスン 7 - QA エージェントを作成して使う"
description: "Issue の要件、quality-checks スキル、Playwright MCP を組み合わせる QA カスタム エージェントを作成します。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

`quality-checks` スキルで自動チェックを実行し、Playwright MCP でブラウザーのフィルタリング動作を確認しました。次は、明確に定義した QA プロセスを備えるカスタム エージェントで、これらの機能を組み合わせます。

このレッスンでは、次のことを行います。

- カスタム エージェントが命令、スキル、MCP ツールと連携する仕組みを確認します。
- 再利用可能な quality assurance プロファイルを作成して確認します。
- QA エージェントを選択し、フィルタリングの Issue に対する評価結果を確認します。

## シナリオ

Tailspin Toys では、プルリクエスト (PR) を開く前に、要件、コード品質、自動チェック、テスト範囲、ブラウザー上の動作を一貫して確認したいと考えています。カスタム エージェントを使うと、この QA プロセスを調整して再利用可能なレポートを作成できます。

## カスタム エージェントとは

カスタム エージェントは、Markdown プロファイルで定義する Copilot の専門的な設定です。プロファイルにはエージェントの目的、命令、利用できるツールを記述します。このワークショップでは、`.github/agents/qa.agent.md` で QA の役割を定義して Copilot CLI で選択します。

これまで使ったカスタマイズにはそれぞれ異なる役割があります。リポジトリの命令はチームの基準を記述し、`quality-checks` スキルは繰り返し実行できるチェックをまとめ、Playwright MCP はブラウザー ツールを提供します。QA プロファイルはこれらを使って要件を評価し、結果を報告する方法を Copilot に指示します。既存のカスタマイズを置き換えたり、別の会話を始めたりする必要はありません。

## QA プロファイルを作成する

機能 PR を開く前に、再利用できる QA プロファイルを Copilot に作成してもらいます。プロファイルには、QA が実行するチェックと守るべき境界の両方を定義します。

1. Codespace に戻り、フィルタリングの会話が Interactive モードであることを確認します。
2. 既定のエージェントに、次のカスタム エージェントを作成するよう依頼します。

   ```plaintext
   Create a custom agent named QA in .github/agents/qa.agent.md. It should check features against their issues and agreed requirements, follow the repository instructions, run the quality-checks skill, use Playwright MCP to verify behavior, and add tests when coverage is missing.

   Have it report each requirement as pass, fail, or blocked with supporting evidence. It must ask before changing implementation code, and it must not commit changes or open pull requests.

   Just create the profile for now so I can review it.
   ```

## プロファイルを確認する

新しいエージェントを使う前に、Copilot が意図した QA ワークフローと制約を正しく記述したことを確認します。これにより、検証だけを求めているのに、エージェントが不完全な設定や広すぎる権限でコードを変更するのを防ぎます。

1. `/diff` を入力し、`.github/agents/qa.agent.md` を開きます。
2. frontmatter を読みます。`description` は必須です。`name` は省略できますが、指定すると分かりやすい表示名になります。
3. プロファイルの命令を読み、QA が要件から始め、リポジトリの命令に従い、`quality-checks` スキルを実行して Playwright MCP を利用することを確認します。
4. QA が証拠とともに結果を報告し、実装コードを変更する前に許可を求め、変更をコミットしたり PR を作成したりしないことを確認します。
5. これらの責任や制約が抜けている場合は、続ける前に既定のエージェントへ修正を依頼します。

## Issue に対して QA を実行する

Copilot CLI は会話を開始したときにプロジェクトのエージェントを読み込みます。プロファイルを作成した後、同じフィルタリングの会話を再開し、QA を選択して Issue と計画時の決定事項を使えるようにします。

1. 次のプロンプトを使ってエージェントを有効にします。

   ```plaintext
   /agent QA
   ```

> [!NOTE]
> エージェントを作成した直後は、選択肢に表示されない場合があります。次のコマンドで有効にできます。

2. 次のプロンプトで、QA エージェントに機能をレビューするよう依頼します。

   ```plaintext
   Review the filtering feature against the issue and the decisions in our plan. Is it ready for a PR?
   ```

3. QA エージェントの作業を待ちます。
4. 完了したら、レポートを確認します。

## まとめと次のステップ

再利用可能な専門的な役割を追加し、その作業をレビューしました。このレッスンでは、次のことを行いました。

- カスタム エージェントが命令、スキル、MCP ツールと連携する仕組みを確認しました。
- 再利用可能な quality assurance プロファイルを作成して確認しました。
- QA エージェントを選択し、フィルタリングの Issue に対する評価結果を確認しました。

機能の実装、スキルの更新、QA プロファイル、テスト、検証レポートの準備ができました。次は[機能 PR でこれらをまとめ、Agent Merge を使います][next-lesson]。

## リソース

- [Copilot CLI 用のカスタム エージェントを作成する][create-agents]
- [カスタム エージェントの構成][agent-config]

[previous-lesson]: ../6-mcp-playwright/
[next-lesson]: ../8-create-pull-request/
[create-agents]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/create-custom-agents-for-cli
[agent-config]: https://docs.github.com/copilot/reference/custom-agents-configuration
