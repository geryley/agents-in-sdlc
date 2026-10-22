---
title: "演習 1 - カスタム命令 (VS Code)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[前のレッスン: 前提条件][previous-lesson] · [次のレッスン: エージェント モード →][next-lesson]

生成 AI を使うときは、コンテキストが重要です。タスクを特定の方法で実行したい場合や、Copilot に背景情報を伝える必要がある場合は、その情報を参照できるようにします。Copilot に特定のコンテキストを与える方法はいくつかあります。特に重要なのが[命令ファイル][instruction-files]で、コード生成のガイダンスを提供します。

この演習では、次のことを行います。

- リポジトリのカスタム命令とパスに応じた命令ファイルを通じて、プロジェクト固有のコンテキスト、コーディング ガイドライン、ドキュメント基準が Copilot に伝わる仕組みを確認します。
- 現在の命令を使って、フィルタリング用の最初のデータ スライス (発行元ヘルパー) を生成します。
- リポジトリ全体に適用する新しい基準を `.github/copilot-instructions.md` に追加します。
- 同じプロンプトを再実行し、生成コードに新しい基準が反映される様子を確認します。
- 命令の更新とフィルタリングの一部を `main` にコミットし、次の演習で Copilot が新しいガイダンスを利用できるようにします。

> [!CAUTION]
> 生成コードが設定した一部の基準に沿わない場合があります。Copilot の結果は確定的ではありません。この演習では、出力が文字単位で一致するかではなく、命令を更新した後に動作がどのように変わるかを確認します。

## 命令ファイル

### シナリオ

Tailspin Toys には、開発プラクティスに関するガイドラインがあります。

- データレイヤーには必ずユニット テストを追加します。
- UI はダーク モードにし、現代的なデザインにします。
- コードには TSDoc のドキュメント コメントを追加します。
- 各ファイルの冒頭に、そのファイルの役割を説明するコメント ブロックを追加します。

命令ファイルを使い、Copilot がこれらのプラクティスに沿って作業するために必要な情報を提供します。

### カスタム命令

カスタム命令を使うと、コーディング スタイルや要件に関するコンテキストと好みを Copilot に伝えられます。コーディング規則、ライブラリ、コメントの記述方法などを指定できるため、より適切な提案やコード スニペットを得るのに役立ちます。リポジトリ全体、または特定のファイルやタスクに対して命令を作成できます。

命令ファイルには、次の 2 種類があります。

- `.github/copilot-instructions.md` は、リポジトリに対する**すべての**リクエストで Copilot に送られる単一の命令ファイルです。Copilot に送る多くのチャットや CLI のリクエストに関係する、プロジェクト全体の情報を記載します。技術スタック、開発内容の概要、ベスト プラクティス、全体に適用するガイダンスなどが該当します。
- `.github/instructions/*.instructions.md` ファイルを作成すると、特定のタスクやファイルの種類にガイドラインを適用できます。TypeScript や Astro など特定の言語、UI コンポーネントやユニット テストの作成などのタスクに対するガイダンスを記載できます。

> [!NOTE]
> IDE の命令ファイルは、Copilot Chat でのコード生成にのみ使用されます。コード補完や次の編集候補には使用されません。
>
> Copilot Chat、Copilot CLI、Copilot cloud agent は、リポジトリ単位の命令と、`applyTo` frontmatter で適用範囲を指定した `*.instructions.md` ファイルの両方を使ってコードを生成します。
>
> Copilot は AGENTS.md や CLAUDE.md など、ほかの標準形式の命令ファイルにも対応しています。[命令ファイルのサポート情報][custom-instructions-support]を参照してください。

### 命令ファイルを管理するためのベスト プラクティス

命令ファイルの作成について詳しく扱うのはこの演習の範囲外ですが、サンプル プロジェクトの例は代表的なアプローチを示しています。概要は次のとおりです。

- `copilot-instructions.md` には、開発内容、プロジェクトの構成、全体的なコーディング基準など、プロジェクト全体のガイダンスを記載します。
- `*.instructions.md` には、ファイルの種類 (ユニット テスト、Astro コンポーネント、データレイヤー) や特定のタスクに対する具体的な指示を記載します。
- 自然な言葉で、分かりやすく簡潔なガイダンスを書きます。適切な例と不適切な例も示してください。

命令ファイルや AI の使い方に、唯一の方法はありません。試しながら、自分のプロジェクトに適した方法を見つけてください。

> [!TIP]
> GitHub Copilot を利用するプロジェクトには、充実した命令ファイルを用意することをお勧めします。このプロジェクトにも、[UI 更新][ui-instructions]や [Astro][astro-instructions] など、さまざまなタスクに対応するファイルがあります。
>
> Copilot に命令ファイルを作成してもらうこともできます。操作方法は環境によって異なります。たとえば VS Code では **Configure Chat → Generate Agent Instructions**、Copilot CLI では `/init` を使います。各環境のレッスンで、必要な箇所を説明します。
>
> テンプレートや出発点を探すには、命令ファイル、カスタム エージェント、その他のリソースが集まった [awesome-copilot][awesome-copilot] を参照してください。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## このプロジェクトのカスタム命令ファイルを確認する

このリポジトリには、中心となる `copilot-instructions.md` と、さまざまなタスク用の `*.instructions.md` ファイルが含まれています。エディターまたは GitHub の Web UI で内容を確認します。

1. `.github/copilot-instructions.md` を開きます。
2. プロジェクトの概要と、**Agent notes**、**Code standards**、**Scripts**、**Repository Structure** などのセクションを確認します。**Code standards** にある **GitHub Actions Workflows** のガイダンスも確認してください。これらは Copilot とのあらゆるやり取りに適用されます。
3. `.github/instructions` フォルダーを開いて確認します。Astro ファイル、Drizzle データレイヤー、テストなどの命令があります。
4. `.github/instructions/unit-tests.instructions.md` を開き、先頭の `applyTo` フィールドを確認します。このフィールドは適用対象をリポジトリのルートからの glob で定義します。たとえば、`**/*.test.ts` に一致する TypeScript テスト ファイルが対象です。
5. このプロジェクトでユニット テストを作成するための命令を確認します。
6. `.github/instructions/drizzle.instructions.md` を開き、末尾までスクロールします。`unit-tests.instructions.md` などの命令ファイルや、プロジェクト内の既存ファイルへのリンクがあります。こうしたリンクを使うと、大きな命令を再利用可能な小さなファイルに分け、コード生成の参考例を Copilot に示せます (リンク先はリポジトリのルートではなく、命令ファイルからの相対パスです)。

> [!NOTE]
> `copilot-instructions.md` の **Code formatting requirements** セクションにはコーディング基準が記載されていますが、コード内ドキュメントの要件はまだありません。次の手順で TSDoc のドキュメント コメントとファイル ヘッダー用の規則を追加します。

## 変更用のブランチを作成する

ここでは、推奨される方法に従って変更用のブランチを作成します。

1. 前の演習で使った Codespace に戻ります。
2. <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して新しいターミナルを開きます。
3. 新しいブランチを作成して切り替えます。

   ```bash
   git checkout -b custom-instructions
   ```

## 命令を更新する前に Copilot Chat を使う

> [!TIP]
> **Copilot Chat を開く**
>
> 次の演習を始める前に、Codespace に戻り、Copilot Chat パネルを開いて **New Chat** を選択し、新しい会話を開始します。モードとモデルの選択は演習ごとに異なります。必要な箇所で説明します。

カスタム命令の効果を確認するため、まず現在の命令ファイルを使ってプロンプトを送信します。後で命令を更新し、同じプロンプトを再送信して違いを確認します。

1. Copilot が必要なコンテキストだけを参照できるよう、前の演習で開いたエディター タブを閉じます。
2. `src/lib/publishers.ts` を開き、ヘルパーの配置先を Copilot に伝えます。
3. Chat ビューのエージェント ドロップダウンから **Agent** を選択し、Copilot がファイルを変更できるようにします。

   ![Chat ビューのエージェント選択メニュー](../../../_images/shared-chat-mode-selector.png)

4. 次のプロンプトを送信します。

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

5. Copilot はプロジェクトを調査し、変更を適用します。ヘルパー ファイルとテストなど、複数のファイルが変更される場合があります。
6. 提案されたヘルパーは、最初の引数に `db` クライアントを取る型付き関数で、発行元の型付き配列を返すことに注目します。これは `src/lib/*.ts` に適用される `.github/instructions/drizzle.instructions.md` のデータレイヤー規則に基づいています。
7. 提案されたコードに TSDoc のドキュメント コメントとファイル単位のコメント ヘッダーが**ない**ことを確認します。

> [!CAUTION]
> Copilot の結果は確定的ではないため、命令がなくてもドキュメント コメントが追加される場合があります。その場合も問題ありません。命令を更新した後に一貫性が改善することが、この演習の要点です。

## リポジトリ全体に適用する基準を追加する

前の説明のとおり、`.github/copilot-instructions.md` はプロジェクト全体の情報を Copilot に伝えます。コードの提案を改善するため、リポジトリのコーディング基準を明記します。

1. `.github/copilot-instructions.md` を開き直します。
2. **Code formatting requirements** セクションを探します。通常は 27 行前後です。コーディング基準は説明されていますが、コード内ドキュメントの規則がないため、生成されたヘルパーにドキュメント コメントがありませんでした。
3. 既存の基準の直後に、次の Markdown 行を追加します。これにより、Copilot はファイル ヘッダーと TSDoc のドキュメント コメントを追加します。

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. `copilot-instructions.md` を保存します。

> [!TIP]
> 前のレッスンで確認したように、リポジトリ全体の命令には `.github/copilot-instructions.md` を、特定の言語、ファイルの種類、タスクの命令には `*.instructions.md` を使えます。今追加したドキュメント コメントの規則のようなプロジェクト全体の基準は、リポジトリ単位のファイルに記載します。

## プロンプトを再実行して変化を確認する

1. Copilot Chat に戻り、**New Chat** を選択して会話を新しくします。
2. `src/lib/publishers.ts` を選択し、Copilot が対象ファイルに集中できるようにします。
3. 前と**同じプロンプト**を送信します。

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

4. 提案されたファイルの冒頭に、次のようなコメント ブロックが追加されていることを確認します。

   ```typescript
   /**
    * Publisher data-access helpers for the Tailspin Toys Crowd Funding platform.
    * Provides functions to retrieve publisher information from the database.
    */
   ```

5. 関数に、次のような TSDoc コメントが含まれていることを確認します。

   ```typescript
   /**
    * Returns a list of all publishers with their id and name.
    *
    * @param db - The Drizzle database client.
    * @returns A promise that resolves to an array of publisher objects.
    */
   ```

新しいプロジェクト基準に従うよう Copilot を誘導し、次の演習でも利用する実際のコードに適用できました。

## 命令とブランチをコミットしてプッシュする

命令ファイルも、リポジトリ内のほかのファイルと同様にソース管理します。変更をコミットしてブランチをプッシュします。

1. <kbd>Control</kbd>+<kbd>`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>`</kbd> (Windows/Linux) を押して、新しいターミナルを開きます。
2. ターミナルで次のコマンドを実行し、命令とヘルパーの変更があることを確認します。

   ```bash
   git status
   ```

3. ターミナルで命令の更新と、ヘルパーの基盤として Copilot が変更したファイルをステージしてコミットします。

   ```bash
   git add .github/copilot-instructions.md src/lib/publishers.ts
   # If git status shows additional supporting updates (for example tests), add those files too.
   git commit -m "Add doc comment standards and publishers helper foundation"
   ```

4. ターミナルからブランチをリポジトリにプッシュします。

   ```bash
   git push -u origin custom-instructions
   ```

## プルリクエストを作成してマージする

ブランチをプッシュしたら、バックログにあるドキュメント基準の Issue に関連付けたプルリクエストを作成します。手動でも作成できますが、プロジェクトに接続済みの GitHub ツールを使って Copilot に任せます。Issue を見つけ、Issue を解決する PR を作成し、マージを依頼します。

> [!NOTE]
> ここでは初めて Copilot に GitHub 上の操作を依頼します。プロジェクト テンプレートでは GitHub のツールがワークスペースに接続されているため、セットアップは不要です。Copilot が初めてツールを使うとき、VS Code から GitHub へのサインインを求められる場合があります。画面の指示に従って許可してください。この接続の仕組み (Model Context Protocol) は後の演習で学びます。

1. Codespace 内で Copilot Chat を開きます。
2. <kbd>Control</kbd>+<kbd>Command</kbd>+<kbd>I</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd> (Windows/Linux) を押して Copilot Chat ビューを開き、エージェント ピッカーで **Agent** が選択されていることを確認します。
3. 命令ファイルの更新に関連する Issue を探し、命令の更新と発行元ヘルパーの基盤の両方を説明する現在のブランチからの PR を作成するよう Copilot に依頼します。

   ```plaintext
   Find the issue related to updating the instructions file. Create a new PR from the current branch, highlight that the PR closes that issue, and include that we also added the publishers helper foundation for the upcoming filtering work.
   ```

4. Copilot が Issue を検索し、PR を作成します。
5. Copilot が GitHub の操作を許可するよう求めたら、実行する内容を確認して、適切な場合に **Allow** を選択します。
6. PR が作成されたら、次のプロンプトで PR をマージし、ローカルのブランチを `main` に戻すよう Copilot に依頼します。

   ```plaintext
   Merge the PR into main. Then return to main locally, and pull the latest code so we are up to date.
   ```

7. Copilot が GitHub 操作とシェル コマンドを実行する許可を求めたら、実行する内容を確認して **Allow** を選択します。

GitHub Copilot の支援を受けて、プルリクエストを作成してマージできました。

## まとめと次のステップ

このプロジェクトで Copilot が命令ファイルからコンテキストを取得する方法を確認し、VS Code の Copilot Chat を使って、次の作業を行いました。

- 既存の命令を使ってコード生成プロンプトを送信し、Copilot の出力を確認しました。
- リポジトリ全体に適用する新しい基準を `.github/copilot-instructions.md` に追加しました。
- 同じプロンプトを再実行し、提案されたコードに新しい基準が反映されることを確認しました。
- 次の演習に備えて、命令の更新とヘルパーの基盤を `main` にコミットしました。

次は[エージェント モード][next-lesson]で命令を使い、コードベース全体に新しい機能を追加します。

## リソース

- [GitHub Copilot のカスタマイズに使う命令ファイル][instruction-files]
- [カスタム命令を作成するためのベスト プラクティス][instructions-best-practices]
- [Copilot のカスタム命令を改善する 5 つのヒント][copilot-instructions-five-tips]
- [GitHub Copilot の個人用カスタム命令][personal-instructions]
- [Awesome Copilot — 命令ファイルなどのリソース集][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-agent-mode/
[instruction-files]: https://code.visualstudio.com/docs/copilot/copilot-customization
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[personal-instructions]: https://docs.github.com/copilot/customizing-copilot/adding-personal-custom-instructions-for-github-copilot
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
