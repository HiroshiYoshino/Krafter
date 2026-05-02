# ソリューション構成

## 何を学ぶか

Krafter を読む第一歩は、技術名よりもフォルダの責務を理解することです。Krafter は Contracts、Backend、Backend.Migrator、UI.Web、UI.Web.Client、Aspire AppHost、ServiceDefaults に分かれています。各プロジェクトが何を担当するかを知ると、機能追加時にどこを変更すべきか迷いにくくなります。

## Krafterでの実装

- 全体構成: [README.md](../../../README.md)
- AI agent 向け構成説明: [Agents.md](../../../Agents.md)
- Shared contracts: [src/AditiKraft.Krafter.Contracts/](../../../src/AditiKraft.Krafter.Contracts/)
- Backend API: [src/AditiKraft.Krafter.Backend/](../../../src/AditiKraft.Krafter.Backend/)
- Database migrator: [src/AditiKraft.Krafter.Backend.Migrator/](../../../src/AditiKraft.Krafter.Backend.Migrator/)
- Blazor Server host: [src/UI/AditiKraft.Krafter.UI.Web/](../../../src/UI/AditiKraft.Krafter.UI.Web/)
- Blazor WebAssembly client: [src/UI/AditiKraft.Krafter.UI.Web.Client/](../../../src/UI/AditiKraft.Krafter.UI.Web.Client/)
- Aspire orchestration: [aspire/](../../../aspire/)

Contracts は Backend と UI の両方から参照される DTO、route、permission、共通 model を持ちます。Backend は API、Identity、EF Core、jobs、SignalR を持ちます。UI.Web は Blazor Web App の server 側 host で、UI.Web.Client は WebAssembly 側の画面と client service を持ちます。

## 実務で必要な知識

Krafter で新機能を作る場合、変更は一箇所で完結しないことが多いです。たとえば新しい「Projects」機能なら、Contracts に DTO と route、Backend に VSA operation、DbContext に DbSet、UI に Refit interface と page、MenuService に menu、PermissionCatalog に permission を追加します。

一方で、何でも共有化すればよいわけではありません。Contracts は「Backend と UI の境界で共有するもの」に限定します。Backend の business logic や EF entity を Contracts に置くと、UI が知るべきではない実装詳細が漏れます。

Migrator は通常の API とは別プロセスです。AppHost が PostgreSQL を起動し、Migrator が migration を適用し、それから API/UI を起動します。ローカル開発ではこの順序を意識すると、起動時のエラーを切り分けやすくなります。

## 確認課題

- `AditiKraft.Krafter.Dev.slnx` を読み、各 project がどの folder に分類されているか確認する。
- `src/AditiKraft.Krafter.Contracts/Common/ApiRoutes.cs` が Backend と UI のどちらで使われるか `rg "ApiRoutes.Users"` で調べる。
- `src/AditiKraft.Krafter.Backend.Migrator/Program.cs` と Backend の `Program.cs` の違いを説明する。

## 出典リンク

- [.NET project SDK overview](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview)
- [Dependency injection in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-10.0)
- [What is Aspire?](https://learn.microsoft.com/en-us/dotnet/aspire/get-started/aspire-overview)
- [DbContext Lifetime, Configuration, and Initialization](https://learn.microsoft.com/en-us/ef/core/dbcontext-configuration/)
