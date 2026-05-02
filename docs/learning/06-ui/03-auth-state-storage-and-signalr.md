# Auth State、Storage、SignalR

## 何を学ぶか

Krafter の UI は認証状態、token storage、server-side cookie/cache、SignalR real-time connection を扱います。Blazor では `AuthenticationStateProvider` が UI に sign-in 状態を伝え、SignalR は通知などのリアルタイム機能に使われます。

## Krafterでの実装

- Client auth state provider: [src/UI/AditiKraft.Krafter.UI.Web.Client/Infrastructure/Auth/UIAuthenticationStateProvider.cs](../../../src/UI/AditiKraft.Krafter.UI.Web.Client/Infrastructure/Auth/UIAuthenticationStateProvider.cs)
- Server auth state provider: [src/UI/AditiKraft.Krafter.UI.Web/Services/PersistingServerAuthenticationStateProvider.cs](../../../src/UI/AditiKraft.Krafter.UI.Web/Services/PersistingServerAuthenticationStateProvider.cs)
- Client storage service: [src/UI/AditiKraft.Krafter.UI.Web.Client/Infrastructure/Storage/AuthStorageService.cs](../../../src/UI/AditiKraft.Krafter.UI.Web.Client/Infrastructure/Storage/AuthStorageService.cs)
- Server storage service: [src/UI/AditiKraft.Krafter.UI.Web/Services/AuthStorageServiceServer.cs](../../../src/UI/AditiKraft.Krafter.UI.Web/Services/AuthStorageServiceServer.cs)
- Auth cookie middleware: [src/UI/AditiKraft.Krafter.UI.Web/Services/AuthCookieMiddleware.cs](../../../src/UI/AditiKraft.Krafter.UI.Web/Services/AuthCookieMiddleware.cs)
- SignalR hub: [src/AditiKraft.Krafter.Backend/Infrastructure/Realtime/RealtimeHub.cs](../../../src/AditiKraft.Krafter.Backend/Infrastructure/Realtime/RealtimeHub.cs)
- SignalR client service: [src/UI/AditiKraft.Krafter.UI.Web.Client/Infrastructure/SignalR/SignalRService.cs](../../../src/UI/AditiKraft.Krafter.UI.Web.Client/Infrastructure/SignalR/SignalRService.cs)

`SignalRService` は WebAssembly 実行時に認証済み user だけ HubConnection を作り、access token provider で token を渡します。

## 実務で必要な知識

Blazor の `AuthenticationStateProvider` は UI の表示判断に使われます。ただし、client-side の認証状態だけでは API は守れません。API endpoint は Backend の JWT bearer authentication と authorization policy で守ります。

Storage は hosting model によって変わります。WebAssembly では browser storage を使いますが、server-side prerender や cookie 管理では server 側 service が必要です。Krafter では `IAuthStorageService` を client/server で差し替え、`AuthCookieMiddleware` が login/refresh response を intercept して HttpOnly cookie と cache に保存します。

SignalR は long-lived connection です。token が期限切れの場合は refresh が必要です。Krafter の `SignalRService` は token 期限を見て refresh を試み、失敗したら logout に進みます。実務では reconnect、tenant group、authorization、server resource 使用量を考慮します。

## 確認課題

- `Routes.razor` の `AuthorizeRouteView` と `Login` 表示の流れを確認する。
- `SignalRService.InitializeAsync` が WebAssembly 以外では早期 return する理由を説明する。
- `RealtimeHub.OnConnectedAsync` が tenant group に connection を追加する流れを読む。

## 出典リンク

- [ASP.NET Core Blazor authentication and authorization](https://learn.microsoft.com/en-us/aspnet/core/blazor/security/?view=aspnetcore-10.0)
- [ASP.NET Core Blazor authentication state](https://learn.microsoft.com/en-us/aspnet/core/blazor/security/authentication-state?view=aspnetcore-10.0)
- [Overview of ASP.NET Core SignalR](https://learn.microsoft.com/en-us/aspnet/core/signalr/introduction?view=aspnetcore-10.0)
- [Use hubs in ASP.NET Core SignalR](https://learn.microsoft.com/en-us/aspnet/core/signalr/hubs?view=aspnetcore-10.0)
- [ASP.NET Core SignalR clients](https://learn.microsoft.com/en-us/aspnet/core/signalr/client-features?view=aspnetcore-10.0)
