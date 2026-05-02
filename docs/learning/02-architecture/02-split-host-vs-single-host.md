# Split Host と Single Host

## 何を学ぶか

Krafter には 2 つの hosting model があります。Split Host は Backend API と Blazor UI を別プロセスで動かします。Single Host は API と UI を同じ ASP.NET Core process にまとめます。どちらも同じ業務コードを使いますが、起動構成、CORS、Refit の宛先、BFF 的な cookie 処理が変わります。

## Krafterでの実装

- Split Host template: [.template.config/template.json](../../../.template.config/template.json)
- Single Host template: [.template.config-single/template.json](../../../.template.config-single/template.json)
- Split Host AppHost: [aspire/AditiKraft.Krafter.Aspire.AppHost/Program.cs](../../../aspire/AditiKraft.Krafter.Aspire.AppHost/Program.cs)
- Single Host AppHost overlay: [aspire-single/AditiKraft.Krafter.Aspire.AppHost/Program.cs](../../../aspire-single/AditiKraft.Krafter.Aspire.AppHost/Program.cs)
- Split Host UI.Web: [src/UI/AditiKraft.Krafter.UI.Web/Program.cs](../../../src/UI/AditiKraft.Krafter.UI.Web/Program.cs)
- Single Host UI.Web overlay: [src-single/UI/AditiKraft.Krafter.UI.Web/Program.cs](../../../src-single/UI/AditiKraft.Krafter.UI.Web/Program.cs)
- Shared backend registration: [src/AditiKraft.Krafter.Backend/Web/HostingExtensions.cs](../../../src/AditiKraft.Krafter.Backend/Web/HostingExtensions.cs)

Split Host の AppHost は `api` と `web` の 2 つの project resource を登録します。Single Host の AppHost は `krafter-app` だけを登録し、`RemoteHostUrl` に自分自身の HTTPS endpoint を注入します。

## 実務で必要な知識

Split Host は API と UI の境界が明確です。将来 mobile app や外部 client を追加する場合、API を独立して扱いやすくなります。ただし、CORS、token transfer、service discovery の理解が必要です。

Single Host は構成が単純です。API と UI が同じ process にあるため、deployment unit が少なくなります。ただし、Backend の middleware と Blazor の middleware が同じ pipeline に入るため、順序が重要になります。`UseBackendMiddleware`、`AuthCookieMiddleware`、`MapBackendEndpoints`、`MapRazorComponents` の並びを理解してください。

テンプレートとしては、Single Host は overlay 方式です。共有コードを duplicate せず、起動構成だけを `src-single/` と `aspire-single/` で差し替えます。実務でテンプレートを修正するときは、Split Host と Single Host の両方で必要な修正か、片方だけの修正かを見極めます。

## 確認課題

- Split Host AppHost の `WithReference(backend)` と Single Host AppHost の `RemoteHostUrl` 注入の違いを説明する。
- `.template.config-single/template.json` で overlay されるファイルを確認する。
- `src-single/UI/.../Program.cs` にだけ存在する backend service registration を探す。

## 出典リンク

- [What is the AppHost?](https://learn.microsoft.com/en-us/dotnet/aspire/fundamentals/app-host-overview)
- [Service discovery in Aspire](https://learn.microsoft.com/en-us/dotnet/aspire/service-discovery/overview)
- [ASP.NET Core Middleware](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/middleware/?view=aspnetcore-10.0)
- [ASP.NET Core Blazor render modes](https://learn.microsoft.com/en-us/aspnet/core/blazor/components/render-modes?view=aspnetcore-10.0)
