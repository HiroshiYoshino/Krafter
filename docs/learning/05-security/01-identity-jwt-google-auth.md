# Identity、JWT、Google Auth

## 何を学ぶか

Krafter の認証は ASP.NET Core Identity、JWT bearer authentication、refresh token、Google external auth を組み合わせています。Backend が token を発行し、UI は Blazor の authentication state と storage/cookie を使って sign-in 状態を扱います。

## Krafterでの実装

- Auth DI: [src/AditiKraft.Krafter.Backend/Web/DependencyInjection.cs](../../../src/AditiKraft.Krafter.Backend/Web/DependencyInjection.cs)
- Login endpoint: [src/AditiKraft.Krafter.Backend/Features/Auth/Login.cs](../../../src/AditiKraft.Krafter.Backend/Features/Auth/Login.cs)
- Refresh token endpoint: [src/AditiKraft.Krafter.Backend/Features/Auth/RefreshToken.cs](../../../src/AditiKraft.Krafter.Backend/Features/Auth/RefreshToken.cs)
- Google auth endpoint: [src/AditiKraft.Krafter.Backend/Features/Auth/ExternalLogin.cs](../../../src/AditiKraft.Krafter.Backend/Features/Auth/ExternalLogin.cs)
- Token service: [src/AditiKraft.Krafter.Backend/Features/Auth/Common/TokenService.cs](../../../src/AditiKraft.Krafter.Backend/Features/Auth/Common/TokenService.cs)
- UI auth service: [src/UI/AditiKraft.Krafter.UI.Web.Client/Features/Auth/Common/AuthenticationService.cs](../../../src/UI/AditiKraft.Krafter.UI.Web.Client/Features/Auth/Common/AuthenticationService.cs)
- Server cookie middleware: [src/UI/AditiKraft.Krafter.UI.Web/Services/AuthCookieMiddleware.cs](../../../src/UI/AditiKraft.Krafter.UI.Web/Services/AuthCookieMiddleware.cs)

Backend の `AddAuthServices` は Identity user/role、permission、token service、Google auth client、JWT bearer を登録します。

## 実務で必要な知識

Authentication は「誰か」を確認する処理で、Authorization は「何ができるか」を判断する処理です。Krafter では Identity が user、role、claim、password token を管理し、JWT が API request の認証情報として使われます。

JWT は bearer token なので、漏れるとその token を持つ人が API を呼べます。Krafter の Blazor host では server-side cookie/cache を使って token を扱う部分があり、WebAssembly 側の storage と役割が分かれます。実務では access token の保存場所、有効期限、refresh token の扱いを慎重に設計します。

Google auth は外部 provider から得た情報を Krafter の user と token に変換する流れです。local development では client id / secret の設定が必要で、本番では secret を repository に commit せず user-secrets や environment variables を使います。

## 確認課題

- `ConfigureJwtBearerOptions` の token validation parameters を読み、何を検証しているか確認する。
- `AuthenticationService.RefreshAsync` が token 期限切れ時に何をするか追う。
- `AuthCookieMiddleware` が intercept する endpoint path を確認する。

## 出典リンク

- [Introduction to Identity on ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/security/authentication/identity?view=aspnetcore-10.0)
- [Overview of ASP.NET Core authentication](https://learn.microsoft.com/en-us/aspnet/core/security/authentication/?view=aspnetcore-10.0)
- [Configure JWT bearer authentication in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/security/authentication/configure-jwt-bearer-authentication?view=aspnetcore-10.0)
- [ASP.NET Core Blazor authentication and authorization](https://learn.microsoft.com/en-us/aspnet/core/blazor/security/?view=aspnetcore-10.0)
- [Google external login setup in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/security/authentication/social/google-logins?view=aspnetcore-10.0)
