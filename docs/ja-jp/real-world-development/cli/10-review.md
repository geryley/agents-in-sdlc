---
title: "レッスン 10 - 振り返りと次のステップ"
description: "Copilot CLI のワークフロー、2 つのプルリクエスト、再利用可能なカスタマイズ、参考資料を振り返ります。"
authors:
  - geektrainer
lastUpdated: 2026-09-29
---

GitHub Copilot CLI を使い、Tailspin Toys の一連のワークフローを進めました。次のことを行いました。

- Codespace を準備し、Copilot CLI をインストールしてプロジェクトを確認し、登録済みのフィルタリング Issue を見つけました。
- 星評価を追加し、転送されたブラウザーで結果を確認し、最初のプルリクエスト (PR) を手動でマージしました。
- フィルタリングの Issue から始め、Plan モードで方法を決め、Autopilot モードで実装し、Interactive モードでレビューしました。
- カスタム命令でエージェントをガイドし、既存の `quality-checks` スキルをカスタマイズして、ユニット テスト、lint、型チェックを実行しました。
- Playwright Model Context Protocol (MCP) サーバーを追加し、実際のブラウザーでフィルタリングを確認しました。
- 要件、テスト範囲、スキルの結果、ブラウザーでの証拠を確認するために、quality assurance (QA) カスタム エージェントを作成して選択しました。
- フィルタリングの変更全体をレビューし、フィルタリング PR で Agent Merge を有効にしました。
- コンテキスト、モデル、共有、cloud agent への任意の委任に使えるスラッシュ コマンドを確認しました。

## リリースした内容

このワークショップでは、更新した `main` からそれぞれのブランチを作成し、2 つの PR を作成します。

1. **星評価:** 既存の `starRating` と、未評価の場合の明示的な表示をゲームカードに追加します。
2. **フィルタリングと品質確認:** フィルタリングを実装し、命令を更新して機能に適用し、`quality-checks` のレポートをカスタマイズし、QA プロファイルと関連テストを追加します。

フィルタリングの計画から PR の作成まで、同じ会話とブランチを使いました。手順を簡略化するため、この作業を 1 つの PR にまとめました。

## さまざまな検証方法

自動テスト、自分でのブラウザー確認、MCP を使った Copilot のブラウザー確認など、複数の方法でコードを検証しました。`quality-checks` スキルはユニット テスト、lint、型チェックを実行して、設定した形式でレポートを作成しました。QA は PR を作成する前に、それらの結果と要件およびテスト範囲を確認しました。

追加するテストは、実際の不足を補うものである必要があります。QA の結果、新しいテストが不要と判断される場合もあります。マージを許可する前にコードと検証結果を確認し、変更があった場合は関係する検証を再実行してください。

## ベスト プラクティス

Copilot に与えるコンテキストとツールは、その作業に影響します。このワークショップでは、命令を更新し、スキルをカスタマイズし、QA プロファイルを作成して、MCP サーバーを設定しました。これらのカスタマイズを会話間で再利用し、チームのニーズに応じて調整できます。命令は基準を定め、スキルは繰り返し可能な作業を記述し、カスタム エージェントは専門的な役割を定義し、MCP サーバーは外部ツールに接続します。エージェントの要約だけでなく、実際の変更とツールの結果を確認してください。

タスクに応じて**モードとモデル**を選びます。**Plan** は実装前に方法を検討する場合、**Interactive** は小さな変更に関与し続ける場合、**Autopilot** は範囲が明確なタスクに適しています。定型的な編集には高速なモデル、複雑な作業には高性能なモデルを選びます。

インフラストラクチャと同じく、コンテキストも重要です。何を、なぜ、どのように構築するのかを明確に伝えると、結果が大きく変わります。

## さらに試す

基本的なワークフローを学びました。次に試せる CLI 機能をいくつか紹介します。

- `/review` を使って、コードレビュー エージェントに変更を分析してもらいます。
- `/rubber-duck` を使って、問題について相談し、別の視点を得ます。
- `/fleet` を使って、独立したサブタスクを並列に調整します。
- `/worktree` を使って、別のタスクを分離します。
- `/delegate` を使って、Copilot cloud agent にタスクを送信します。

## 次のステップ

どのツールでも、使い続けることが上達への近道です。製品コード、趣味のコード、何年も作りたいと思っていた小さなアプリなどに活用してください。学んだことをチームと共有し、お互いの知識を深めましょう。ドキュメントも引き続き確認してください。

ターミナルから Tailspin Toys を拡張する場合は、任意の [Foundry Backer Concierge シリーズ][foundry]に進んでください。別の環境での作業については、[VS Code ワークショップ][vscode]、[GitHub Copilot app ワークショップ][app]、または [Copilot cloud agent ワークショップ][cloud]を確認できます。

## リソース

- [GitHub Copilot CLI について][about-cli]
- [Copilot CLI コマンド リファレンス][cli-reference]
- [Copilot CLI をカスタマイズする][customize-cli]
- [Copilot CLI でプルリクエストを管理する][manage-prs]

[previous-lesson]: ../9-cli-power-tools/
[foundry]: ../8-foundry-agent/
[vscode]: ../../vscode/
[app]: ../../app/
[cloud]: ../../cloud/
[about-cli]: https://docs.github.com/copilot/concepts/agents/about-copilot-cli
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[customize-cli]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot
[manage-prs]: https://docs.github.com/copilot/how-tos/copilot-cli/use-copilot-cli/manage-pull-requests
