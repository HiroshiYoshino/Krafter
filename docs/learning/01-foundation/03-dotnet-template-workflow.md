# dotnet new テンプレートの使い方

## 何を学ぶか

Krafter は clone して直接アプリを作るためのリポジトリではなく、`dotnet new` で新しいアプリを生成するテンプレートパックです。利用者は `dotnet new install AditiKraft.Krafter.Templates` を一度実行し、その後 `dotnet new krafter -n MyApp` または `dotnet new krafter-single -n MyApp` でプロジェクトを作ります。

## Krafterでの実装

- 利用手順: [README.md](../../../README.md)
- Split Host template config: [.template.config/template.json](../../../.template.config/template.json)
- Single Host template config: [.template.config-single/template.json](../../../.template.config-single/template.json)
- Template package project: [AditiKraft.Krafter.Templates.csproj](../../../AditiKraft.Krafter.Templates.csproj)
- Single Host overlay source: [src-single/](../../../src-single/)
- Single Host Aspire overlay: [aspire-single/](../../../aspire-single/)

`template.json` の `shortName` が CLI で指定する名前です。Split Host は `krafter`、Single Host は `krafter-single` です。`sourceName` は `AditiKraft.Krafter` で、`-n MyApp` のように指定した名前で namespace、folder、project file が置換されます。

## 実務で必要な知識

テンプレートを使う側と、テンプレートを開発する側では作業が違います。使う側は生成されたアプリだけを変更します。テンプレート開発者はこのリポジトリを変更し、`dotnet pack` で template package を作り、`dotnet new install` でローカル検証します。

`template.json` の `sources` は、どのファイルを出力に含めるかを決めます。Single Host では root source から一部ファイルを除外し、`src-single/` と `aspire-single/` の overlay を同じ出力先にコピーします。この仕組みにより、共有コードは `src/` に置きつつ、起動方式だけを差し替えています。

実務では `-n` を必ず指定する習慣が重要です。名前を後から手作業で変更すると namespace、project reference、launchSettings、設定値がずれやすくなります。

## 確認課題

- `.template.config/template.json` と `.template.config-single/template.json` の `sources` を比較する。
- Single Host で除外される Split Host 用ファイルを 3 つ挙げる。
- `AditiKraft.Krafter.Templates.csproj` の `PackageId` と `PackageVersion` を確認する。

## 出典リンク

- [dotnet new command](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-new)
- [Custom templates for dotnet new](https://learn.microsoft.com/en-us/dotnet/core/tools/custom-templates)
- [dotnet pack command](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-pack)
- [Create and publish a NuGet package with the dotnet CLI](https://learn.microsoft.com/en-us/nuget/quickstart/create-and-publish-a-package-using-the-dotnet-cli)
