# .NET CLI と SDK

## 何を学ぶか

Krafter を使う最初の入口は Visual Studio の画面ではなく、`dotnet` CLI です。`dotnet new install` でテンプレートを入れ、`dotnet new krafter -n MyApp` でアプリを生成し、`dotnet run --project ...AppHost.csproj` で Aspire AppHost から起動します。

ここで学ぶべき中心は、.NET SDK が「プロジェクト作成」「依存関係の復元」「ビルド」「実行」「パッケージ作成」をまとめて扱うという考え方です。Krafter では `net10.0`、SDK-style project、`PackageReference`、`ProjectReference` が全体の土台です。

## Krafterでの実装

- テンプレートの利用手順: [README.md](../../../README.md)
- Split Host solution: [AditiKraft.Krafter.slnx](../../../AditiKraft.Krafter.slnx)
- Single Host solution: [AditiKraft.Krafter.Single.slnx](../../../AditiKraft.Krafter.Single.slnx)
- 開発用 solution: [AditiKraft.Krafter.Dev.slnx](../../../AditiKraft.Krafter.Dev.slnx)
- Template package project: [AditiKraft.Krafter.Templates.csproj](../../../AditiKraft.Krafter.Templates.csproj)
- Backend project: [src/AditiKraft.Krafter.Backend/AditiKraft.Krafter.Backend.csproj](../../../src/AditiKraft.Krafter.Backend/AditiKraft.Krafter.Backend.csproj)
- Blazor client project: [src/UI/AditiKraft.Krafter.UI.Web.Client/AditiKraft.Krafter.UI.Web.Client.csproj](../../../src/UI/AditiKraft.Krafter.UI.Web.Client/AditiKraft.Krafter.UI.Web.Client.csproj)

`*.csproj` では `<TargetFramework>net10.0</TargetFramework>` が対象ランタイムを示し、`PackageReference` が NuGet package、`ProjectReference` が同一 solution 内の依存プロジェクトを示します。

## 実務で必要な知識

`dotnet restore` は NuGet package を復元します。`dotnet build` はコンパイルします。`dotnet run --project <path>` は指定プロジェクトを実行します。`dotnet test` はテストプロジェクトを実行します。`dotnet pack` は NuGet package を作ります。Krafter のテンプレート開発では、このうち `build`、`run`、`pack`、`new install` を頻繁に使います。

実務では solution と project の違いも重要です。solution は複数 project の一覧で、project は実際のビルド単位です。Krafter には Split Host、Single Host、template 開発用の複数 solution があるため、どれをビルドしているかを意識してください。

また、SDK-style project は既定値が多い形式です。すべてのソースファイルを `csproj` に列挙しなくてもコンパイル対象になります。反対に、`PackageReference` や `ProjectReference` の追加は依存関係を変える操作なので、実装の影響範囲が広がります。

## 確認課題

- `dotnet --info` を実行し、インストール済み SDK のバージョンを確認する。
- `rg -n "TargetFramework|PackageReference|ProjectReference" -g "*.csproj"` を実行し、Krafter がどの package を使っているか見る。
- `AditiKraft.Krafter.Dev.slnx` と `AditiKraft.Krafter.slnx` の違いを説明する。

## 出典リンク

- [.NET CLI overview](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet)
- [dotnet build command](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-build)
- [dotnet run command](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-run)
- [.NET project SDK overview](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview)
- [PackageReference in project files](https://learn.microsoft.com/en-us/nuget/consume-packages/package-references-in-project-files)
