---
title: "演習 5: GitHub Copilot の作業を反復して改善する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [前のレッスン: エージェントの管理][previous-lesson] |
|:--|

## 作業をレビューする

この演習では、ユーザー エクスペリエンスの改善と機能追加を目的に、GitHub Copilot に複数のタスクを依頼しました。コードへのドキュメント追加、デザイン チームが改善を進めるための関連ゲーム機能の作成、ハイ コントラストとライト モードの切り替えを含むアクセシビリティ機能の実装を依頼しました。作成されたコード変更を確認し、必要に応じて Copilot にフィードバックを送って、結果を改善しましょう。

### シナリオ

これまで何度も確認したように、生成 AI を導入しても、ソフトウェア設計と DevOps の基本は変わりません。生成されたコードをレビューし、通常の DevOps プロセスを実行します。そこで、チームにコードレビューを有効にする前に、ドキュメント、関連ゲーム機能、アクセシビリティ機能について GitHub Copilot の提案を確認します。

## セキュリティと GitHub Copilot cloud agent

Copilot cloud agent は、監督なしで非同期にタスクを実行するため、安全を保つためのセキュリティ制約が設けられています。

- Copilot はリポジトリへの読み取りアクセス権を持ち、書き込みアクセス権は作業に使うブランチ**のみ**に限定されます。
- cloud agent は GitHub Actions 内で実行され、作業用に独立した一時環境が作成されます。
- GitHub Actions のワークフローを実行するには、人間による承認が必要です。
- MCP サーバーを含め、既定では[外部リソースへのアクセスが制限されています](https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall)。

## 生成されたドキュメントをレビューする

まず、GitHub Copilot cloud agent が作成した最初のプルリクエスト (PR) である、コードへのドキュメント追加を確認します。GitHub.com の標準的な PR 画面を使います。

> [!NOTE]
> PR を確認すると、GitHub Copilot がファイアウォールでブロックされたという警告が表示される場合があります。既定では Copilot の外部リソースへのアクセス (外部 MCP サーバーへの接続など) が制限されているため、これは**想定された動作**です。必要に応じて、[Copilot cloud agent のファイアウォールをカスタマイズまたは無効化][agent-firewall]できます。

1. github.com でリポジトリを開きます。
2. **Pull Requests** を選択して、PR の一覧を開きます。
3. **Add missing documentation** などのタイトルが付いた PR を開きます。

> [!NOTE]
> Copilot がまだ作業中の場合、PR のタイトルに **[WIP]** が表示されます。その場合は作業が終わるまで待ちます。数分かかることがあるので、休憩したり、これまでに学んだ内容を振り返ったりしてください。

4. PR の準備ができたら、**Files changed** タブを選択して変更を確認します。

    ![Files changed タブ](../../../_images/shared-pr-files-changed.png)

5. TSDoc のドキュメント コメントなど、新しく追加されたドキュメントを確認します。実際の変更内容は毎回異なります。

    変更を確認しながら、TSDoc のドキュメント コメント、TypeScript の規則、コメント ヘッダーを探します。これらは演習 2 の最初に確認したカスタム命令ファイルに基づいています。

6. 更新内容をレビューし、問題がなければ **Conversation** タブに戻って下にスクロールします。
7. ワークフローの実行承認を待っていることを示す表示を確認します。
8. 承認待ちのワークフローがある場合は、**Approve and run workflows** を選択します。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

9. PR のチェック欄でワークフローがキューに入ることを確認します。問題がなければ、単一の Astro アプリのプロジェクト チェックが成功します。完了まで数分かかる場合があります。

## GitHub Copilot に変更を依頼する

Copilot とのプルリクエスト作業は一方通行ではありません。チーム メンバーと同じように PR やコードのインライン コメントで Copilot にメンションできます。Copilot はコメントを確認し、対応する新しいセッションを開始します。結果は確定的ではないため、依頼する内容を厳密には指定できませんが、次のような変更を依頼できます。

- 各コード ファイルの先頭に、ファイルの役割を説明するコメント ヘッダーを追加する。
- TypeScript と Astro のファイルに TSDoc のドキュメント コメントを追加する。
- Astro アプリの構成を説明する README を作成する。

1. **@copilot** を通常のユーザーと同じようにメンションし、生成されたドキュメントの変更を依頼します。上記の例や、コードベースに追加したいドキュメントについて別の依頼を使います。
2. **View Session** を選択し、Copilot の作業を確認します。Copilot が更新用の新しいセッションを開始します。
3. **Back to pull request** を選択すると、PR に戻れます。

    ![プルリクエストに戻る](../../../_images/ex7-back-to-pr.png)

4. Copilot が変更を終えると、PR に新しいコミットが追加されます。
5. **Files changed** タブを選択して変更を確認します。

納得できるまで反復して改善できます。準備ができたら、ドラフト PR をレビュー可能な状態に変更して `main` ブランチにマージします。

![PR をレビュー可能な状態に変更する](../../../_images/ex7-ready-for-review.png)

## 関連ゲーム機能をレビューする

次に、ゲーム詳細ページに関連ゲームを表示する Issue を解決するために Copilot が作成した PR を確認します。

1. GitHub.com のリポジトリに戻ります。
2. **Pull Requests** タブを選択します。
3. **Show related games on the game details page** などのタイトルが付いた PR を選択します。
4. **Files changed** タブを選択して、生成されたコードをレビューします。
5. 更新内容を確認し、問題がなければ **Conversation** タブに戻って下にスクロールします。
6. ワークフローの実行承認を待っていることを示す表示を確認します。
7. 承認待ちのワークフローがある場合は、**Approve and run workflows** を選択します。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

8. PR のチェック欄でワークフローがキューに入ることを確認します。問題がなければ、単一の Astro アプリのプロジェクト チェックが成功します。完了まで数分かかる場合があります。
9. **任意:** Codespace でこのブランチに切り替え、関連ゲーム機能を手動でテストできます。Codespace に移動してターミナルを開き、Copilot が作成したブランチ名 (例: **copilot/fix-8**) に `<branch-name>` を置き換えて、次のコマンドを実行します。

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

Copilot が関連ゲーム機能を作成しました。前と同じように cloud agent に更新を依頼し、反復して改善できます。たとえば、表示する関連ゲームの数を調整したり、コメント ヘッダーや TSDoc のドキュメント コメントを追加したりできます (この Issue を割り当てた時点では、カスタム命令を更新する前だったことを思い出してください)。前と同じく、**Conversation** タブにコメントを追加すると、Copilot が内容を確認して新しいセッションを開始します。

## アクセシビリティ機能をレビューする

最後に、アクセシビリティ カスタム エージェントで実装したアクセシビリティ機能をレビューします。この PR には、演習 3 で依頼したハイ コントラスト モードと、演習 4 でミッション コントロールから追加を依頼したライト モードの両方が含まれているはずです。

1. GitHub.com のリポジトリに戻ります。
2. **Pull Requests** タブを選択します。
3. **Add high contrast mode to website** などのタイトルが付いた PR を選択します。

> [!NOTE]
> Copilot がまだ作業中の場合、PR のタイトルに **[WIP]** が表示されます。その場合は作業が完了するまで数分待ちます。

4. **Files changed** タブを選択して、生成されたコードを確認します。
5. 特に次の項目に注意して実装をレビューします。
   - モード切り替え用の UI コンポーネント。
   - ユーザー設定を保存するローカル ストレージの使用方法。
   - ハイ コントラスト モードとライト モードの CSS またはスタイルの変更。
   - アクセシビリティ属性 (ARIA ラベル、キーボード操作など)。
   - モード切り替えを管理する JavaScript/TypeScript コード。

6. 更新内容を確認し、問題がなければ **Conversation** タブに戻って下にスクロールします。
7. ワークフローの実行承認を待っていることを示す表示を確認します。
8. 承認待ちのワークフローがある場合は、**Approve and run workflows** を選択します。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

9. PR のチェック欄でワークフローがキューに入ることを確認します。問題がなければ、単一の Astro アプリのプロジェクト チェックが成功します。完了まで数分かかる場合があります。
10. **任意:** Codespace でこのブランチに切り替えて、アクセシビリティ機能を手動でテストできます。Codespace に移動してターミナルを開き、Copilot が作成したブランチ名に `<branch-name>` を置き換えて、次のコマンドを実行します。

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

    その後、アプリケーションを起動し、ブラウザーでハイ コントラストとライト モードの切り替えを試します。ページを再読み込みしても設定が保持されることを確認してください。

アクセシビリティ カスタム エージェントが、アクセシビリティのベスト プラクティスに沿って機能を実装するよう Copilot を導いたことを確認してください。アクセシビリティ上の懸念や改善点がある場合は、前の PR と同様に、コメントで **@copilot** に修正を依頼できます。

## 任意の演習 — さらに作業を委任する

cloud agent に実際のバックログ項目を任せて、その間に別の作業を進める方法を身につけましょう。リポジトリにいくつか Issue を登録し、Copilot に割り当てます。たとえば次のような作業があります。

- ゲーム詳細ページに支援者向けの関心フォームを作成する。
- ゲーム一覧ページにページネーションを実装する。
- データアクセス ヘルパーに入力検証とエラー処理を追加する。

## まとめ

cloud agent の環境で一連の演習を完了しました。各レッスンを通じて、次の作業を行いました。

- **このリポジトリに含まれるカスタム命令ファイルを確認し、後から cloud agent の出力にどう影響するかを確認しました。**
- **Issue を Copilot cloud agent に割り当て、環境の準備、計画、非同期の実行を確認しました。**
- **アクセシビリティ用のカスタム エージェントを作成して使い、ハイ コントラストとライト モードの切り替えを追加しました。**
- **Copilot agents ページをミッション コントロールとして使い、アクセシビリティ セッションを監視して途中で誘導しました。**
- **cloud agent のプルリクエストをレビューして改善し、`@copilot` に変更を依頼し、ワークフローを承認しました。**

## レビューと次のステップ

cloud agent の演習を完了しました。さらに学びたい場合は、ここで学んだ内容を補完する別の環境の演習に進みます。

- 🖥️ **[VS Code 環境](../../vscode/)** — IDE から離れずに Copilot Chat のエージェント モードと MCP 統合を体験します。
- 💻 **[CLI 環境](../../cli/)** — Plan と Autopilot、エージェント スキル、QA カスタム エージェント、Playwright MCP、Agent Merge、cloud agent に戻る任意の `/delegate` 機能を使って、ターミナルから 2 つの変更をレビューしてリリースします。

自分のリポジトリでは、次の作業も試してみてください。

- リファクタリングやテスト範囲の改善を cloud agent に Issue として割り当てる。
- 別の分野用のカスタム エージェントを作成する。
- 異なる技術スタック向けに `copilot-setup-steps.yml` を設定する。

## リソース

- [GitHub Copilot][github-copilot]
- [Copilot agents について][copilot-agents]
- [GitHub Issue を Copilot に割り当てる][assign-issue]
- [Copilot cloud agent セットアップ ワークフローのベスト プラクティス][cloud-agent-best-practices]
- [Copilot cloud agent ファイアウォールの設定][agent-firewall]

---

| [前のレッスン: エージェントの管理][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall

[previous-lesson]: ../4-managing-agents/
