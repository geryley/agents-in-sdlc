---
title: "演習 4 - エージェントの監視と管理"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [前のレッスン: カスタム エージェント][previous-lesson] |
|:--|

ここまでの演習では、ユーザー エクスペリエンスの改善や機能追加に関する 3 つのタスクを Copilot cloud agent に依頼しました。cloud agent は非同期で自律的に動作しますが、タスクの進行を監視することも重要です。

GitHub.com の[エージェント ページ][agents-page]など、cloud agent に割り当てたタスクを管理するツールが複数あります。このミッション コントロールでは、プルリクエスト (PR) が開いているすべてのエージェント タスクを確認できます。実行内容を調べたり、進行中のセッションを誘導したりすることもできます。

このレッスンでは、次のことを行います。

- エージェント ページを使って cloud agent のタスクを監視します。
- 実行中のセッションを誘導し、機能を追加するよう依頼します。

## シナリオ

エージェントにハイ コントラスト モードの作成を依頼した後、チームはライト モードも追加するとよいと気付きました。サイトのスタイルと切り替え機能をすでに変更中なので、ライト モードも含めるのが自然です。エージェントの作業を誘導して、ハイ コントラストに加えてライト モードも実装してもらいます。

## Copilot cloud agent のタスクを確認する

Copilot cloud agent に割り当てたすべてのタスクの状況を確認します。

1. [Copilot agents ページ](https://github.com/copilot/agents)を開きます。
2. メイン ペインと左側のペインに表示されるタスクの一覧を確認します。たとえば、次のタスクが割り当てられています。
   - コードベースのドキュメントを更新する。
   - ゲームを変更する API を生成する。
   - Web サイトにハイ コントラスト モードを追加する。
3. 実行中のタスクを選択し、Copilot が実行した作業を確認します。次のような作業が含まれる場合があります。
   - リポジトリをチェックアウトする。
   - Copilot の作業環境を作成する。
   - MCP サーバーを設定する。
   - 割り当てられたタスクを完了するための各種作業を実行する。

> [!NOTE]
> 表示される手順は、Copilot の作業状況や採用した方法によって異なります。

4. 右側に表示されるプルリクエスト (PR) ペインも確認します。ここから PR と変更されたファイルを確認し、さらに作業を監視できます。

## cloud agent を誘導する

実行中のタスクを確認したら、エージェントがハイ コントラスト モードを作成している間に、ライト モードの切り替えも追加するよう依頼します。

1. ハイ コントラスト モードの追加に関するセッションを選択します。正確なタイトルは、Copilot が付けた名前と現在の作業状況によって異なります。

    ![ミッション コントロールでのアクセシビリティ セッション](../../../_images/ex6-accessibility-session.png)

2. セットアップが終わって作業を始めたことが表示されるまで、数分間セッションを確認します。次のようなメッセージが表示されます。
3. **Steer active session while Copilot is working** ダイアログに、次のプロンプトを入力します。

    ```plaintext
    While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
    ```

    ![エージェント ページで Copilot の作業中にセッションを誘導するダイアログが選択された画面](../../../_images/ex6-steer-cloud-agent-task.png)

4. <kbd>Enter</kbd> を押してプロンプトを送信します。
5. Copilot がプロンプトを受け取り、作業計画に含めることを確認します。

## Copilot に作業を任せる

Copilot は更新されたタスクに取り組みます。メッセージを送った時点で実行中の手順が終わると、新しい依頼を作業に加えます。

前と同様に完了まで数分かかります。その間に、これまで学んだことを振り返ってみましょう。

## まとめと次のステップ

このレッスンでは、GitHub Copilot cloud agent の監視と案内に使う中心的な場所である Copilot agents ページを確認しました。ミッション コントロールでは、進行中および完了済みのタスクを追跡し、実行中の作業をレビューし、進行中のタスクの範囲を変更したり、ガイダンスを追加したりできます。

次の概念を学びました。

- ミッション コントロールとエージェント ページで cloud agent のタスクを監視する。
- 進行中のセッションを誘導し、機能を追加するよう依頼する。

Copilot がアクセシビリティ機能を作成している間に、次は Copilot が作成したプルリクエストを[反復して改善します][next-lesson]。

## リソース

- [Copilot agents ページ][agents-page]
- [カスタム エージェントについて][custom-agents]

---

| [前のレッスン: カスタム エージェント][previous-lesson] | [次のレッスン: Copilot の作業を反復して改善する →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
