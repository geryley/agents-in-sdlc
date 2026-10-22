---
title: "レッスン 4 - カスタム命令で Copilot をガイドする"
description: "リポジトリの命令を確認し、ドキュメント作成の基準を追加して、フィルタリングのコードに適用します。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

生成 AI を使う際は、コンテキストが重要です。作業に特定の方法が必要な場合、そのガイダンスを Copilot が利用できるようにします。[命令ファイル][instruction-files]には、望むコードの内容だけでなく、構成方法も記述できます。フィルタリング機能を実装したので、Copilot が参照した命令を確認し、ドキュメント作成の基準を追加して、コードに適用します。

このレッスンでは、次のことを行います。

- リポジトリの命令とパスに応じた命令ファイルがエージェントに使われる仕組みを確認します。
- コーディング基準を守るため、命令ファイルを更新します。
- 命令ファイルがコードに与える影響を確認します。

## シナリオ

Tailspin Toys には、開発プラクティスに関するガイドラインがあります。

- コメントにはコードの繰り返しではなく、意図や分かりにくい判断を記述します。
- `db/` と `src/lib/` のエクスポートされた関数には、目的、パラメーター、戻り値、および存在する場合は注入可能な `db` 引数を説明する TSDoc/JSDoc を記述します。
- 再利用可能な Astro コンポーネントには `Props` の仕様を記載し、関連コードが変わったらコメントも更新します。
- 既存の書式設定と lint のガイダンスを維持します。

命令ファイルを使って、Copilot がこれらのプラクティスに沿って作業できる情報を提供します。

## 命令ファイル

カスタム命令を使うと、コーディング スタイルや要件を Copilot に伝えられます。好みのコーディング規則、ライブラリ、コメントの記述方法などを指定できるため、より適切な提案やコード スニペットを得るのに役立ちます。リポジトリ全体、または特定のファイルやタスクに対して命令を作成できます。

命令ファイルには、次の 2 種類があります。

- `.github/copilot-instructions.md` は、リポジトリに対する**すべての**リクエストで Copilot に送られる単一の命令ファイルです。チャットや CLI の多くのリクエストに関連するプロジェクト情報を記載します。
- `.github/instructions/*.instructions.md` は、特定の言語、ファイルの種類、タスクにガイドラインを適用します。

> [!NOTE]
> 他の命令ファイル形式への対応は環境によって異なります。特定の形式を使用する前に、[カスタム命令のサポート リファレンス][custom-instructions-support]を確認してください。

## このプロジェクトのカスタム命令ファイルを確認する

変更による影響を確認するため、スターター プロジェクトに含まれている命令ファイルを見てみましょう。

1. Codespace に戻ります。
2. Codespaces エディターで (ターミナルではなく) `.github/copilot-instructions.md` を開きます。
3. プロジェクトの概要とコーディング ガイダンスを確認します。この命令は、このリポジトリでの Copilot とのすべてのやり取りに適用されます。
4. `.github/instructions` フォルダーを開きます。Astro ファイル、Drizzle データレイヤー、テストなどの命令を確認してください。
5. `.github/instructions/unit-tests.instructions.md` を開き、先頭の `applyTo` フィールドを確認します。このフィールドは命令を適用するファイルを決める glob を指定します。
6. `.github/instructions/drizzle.instructions.md` を開き、他の命令ファイルやプロジェクト内のファイルへのリンクを確認します。これにより、より大きな命令を再利用できる小さなファイルに分割し、Copilot が参考にできる例を示せます。

## チームのガイダンスに合わせて命令ファイルを更新する

既存のファイルは十分な出発点ですが、まだ改善できます。コアの `copilot-instructions.md` を変更して、新しく生成する TypeScript に TSDoc コメントを追加するよう指示します。

1. `.github/copilot-instructions.md` で、**Code formatting guidance** というセクションを探します。通常は 35 行前後にあります。
2. 最後の箇条書きとして、次を追加します。

   ```markdown
   - All new TypeScript should contain TSDocs comments for documentation purposes.
   ```

ファイルは自動的に保存されます。

## 更新したガイダンスを使う

Copilot CLI は会話の開始時にリポジトリの命令を読み込みます。機能のコンテキストを失わずに新しい命令を適用するため、保存した命令を反映してフィルタリングの会話を再開します。

1. フィルタリングの会話が開いている場合は、`/exit` を入力して閉じます。Copilot CLI は会話を自動的に保存します。
2. `game-filters-cli` ブランチ上でリポジトリのルートに移動し、保存した会話を再開します。

   ```bash
   copilot --resume
   ```

   プロンプトが表示されたら、フィルタリングの会話を選択し、`game-filters-cli` ブランチがチェックアウトされていることを確認します。
3. 次のプロンプトで、更新したガイダンスを適用するよう Copilot に依頼します。

   ```plaintext
   We just updated our instructions and code guidance. Can you please update the code you generated to match that guidance?
   ```

4. `/diff` を入力し、変更された TypeScript ファイルを確認します。新しい TSDoc コメントが生成され、内容を正確に説明していることを確認してください。

## まとめと次のステップ

Copilot CLI が命令ファイルを読み込む仕組みを確認し、新しい基準を機能に適用しました。次のことを行いました。

- リポジトリの命令とパスに応じた命令ファイルがエージェントに使われる仕組みを確認しました。
- コーディング基準を守るため、命令ファイルを更新しました。
- 命令ファイルがコードに与える影響を確認しました。

次は[再利用可能な quality-checks スキルをカスタマイズして実行し][next-lesson]、lint とテストを一貫して実行します。

## リソース

- [Copilot CLI にカスタム命令を追加する][instruction-files]
- [カスタム命令のサポート][custom-instructions-support]
- [カスタム命令を作成するためのベスト プラクティス][instructions-best-practices]
- [Awesome Copilot — 命令ファイルなどのリソース集][awesome-copilot]

[instruction-files]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions
[instructions-best-practices]: https://docs.github.com/copilot/concepts/prompting/response-customization#writing-effective-custom-instructions
[awesome-copilot]: https://awesome-copilot.github.com/
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support
[previous-lesson]: ../3-agent-modes/
[next-lesson]: ../5-agent-skills/
