---
title: "演習 1 - カスタム命令 (Cloud agent)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[前のレッスン: 前提条件][previous-lesson] · [次のレッスン: Copilot cloud agent →][next-lesson]

生成 AI を使うときは、コンテキストが重要です。タスクを特定の方法で実行したい場合や、Copilot に背景情報を伝える必要がある場合は、その情報を参照できるようにします。[命令ファイル][instruction-files]を使うと、実行してほしい内容だけでなく、どのように実行するかも Copilot に伝えられます。

この演習では、次のことを行います。

- リポジトリのカスタム命令とパスに応じた命令ファイルを通じて、プロジェクト固有のコンテキスト、コーディング ガイドライン、ドキュメント基準が Copilot に伝わる仕組みを確認します。
- リポジトリ全体に適用する新しい基準を `.github/copilot-instructions.md` に追加します。

> [!NOTE]
> VS Code や CLI の環境とは異なり、この演習では変更前後のプロンプトを実行しません。Copilot cloud agent は GitHub Issue に対して非同期に作業するため、リアルタイムに並べて効果を示すのは困難です。この環境の後半で、cloud agent が作成したプルリクエストをレビューするときに、命令ファイルの影響を確認します。

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
4. `.github/instructions/unit-tests.instructions.md` を開き、先頭の `applyTo` フィールドを確認します。このフィールドはリポジトリのルートからの glob で適用対象を定義します。たとえば、`**/*.test.ts` に一致する TypeScript テスト ファイルが対象です。
5. このプロジェクトでユニット テストを作成するための命令を確認します。
6. `.github/instructions/drizzle.instructions.md` を開き、末尾までスクロールします。`unit-tests.instructions.md` などの命令ファイルや、プロジェクト内の既存ファイルへのリンクがあります。こうしたリンクを使うと、大きな命令を再利用可能な小さなファイルに分け、コード生成の参考例を Copilot に示せます (リンク先はリポジトリのルートではなく、命令ファイルからの相対パスです)。

> [!NOTE]
> `copilot-instructions.md` の **Code formatting requirements** セクションにはコーディング基準が記載されていますが、コード内ドキュメントの要件はまだありません。次の手順で TSDoc のドキュメント コメントとファイル ヘッダー用の規則を追加します。

## リポジトリ全体に適用する基準を追加する

この演習で行う編集は、TSDoc のドキュメント コメントとファイル単位のコメント ヘッダーをコードに追加する、プロジェクト全体の規則です。以降の演習で Issue を割り当てると、cloud agent がこの規則を読み取ります。

編集する前に作業ブランチを作成します。cloud agent は Issue の対象ブランチにある命令を読み取りますが、ここでは変更を整理してコミットします。

1. Codespace のターミナルで、新しいブランチを作成して切り替えます。

   ```bash
   git checkout -b custom-instructions-cloud
   ```

前の手順で確認したように、`.github/copilot-instructions.md` はプロジェクト全体の情報を Copilot に伝えます。コードの提案を改善するため、リポジトリのコーディング基準を明記します。

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

## 命令の更新をコミットしてプッシュする

cloud agent は、Issue の対象ブランチにある命令ファイルを読み取ります。次の演習で Issue を Copilot に割り当てると、Copilot は `main` からブランチを作成します。そのため、この命令を cloud agent が使うには `main` にマージしておく必要があります。

1. ステージしてコミットします。

   ```bash
   git add .github/copilot-instructions.md
   git commit -m "Add doc comment and file-header standards to copilot instructions"
   ```

2. ブランチをプッシュします。

   ```bash
   git push -u origin custom-instructions-cloud
   ```

3. github.com で `custom-instructions-cloud` から `main` へのプルリクエストを作成してマージします。次の演習で Issue を割り当てると、cloud agent がこれらの命令を読み取ります。

> [!TIP]
> このワークショップで `main` に直接作業する場合は、ブランチの作成を省略し、`main` に直接コミットしてもかまいません。ブランチの手順は、実際のプロジェクトでの進め方に沿うために含めています。

## まとめと次のステップ

このプロジェクトで Copilot が命令ファイルからコンテキストを取得する方法を確認し、リポジトリ全体に適用する新しい基準を `.github/copilot-instructions.md` に追加しました。残りの演習で cloud agent が作成するプルリクエストに、この基準が適用される様子を確認できます。

次は、[最初の Issue を Copilot cloud agent に割り当てます][next-lesson]。

## リソース

- [GitHub Copilot のカスタマイズに使う命令ファイル][instruction-files]
- [カスタム命令を作成するためのベスト プラクティス][instructions-best-practices]
- [Copilot のカスタム命令を改善する 5 つのヒント][copilot-instructions-five-tips]
- [Awesome Copilot — 命令ファイルなどのリソース集][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-cloud-agent/
[instruction-files]: https://docs.github.com/copilot/customizing-copilot/about-customizing-github-copilot-chat-responses
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
