# ASP.NET Core Minimal APIs

## 何を学ぶか

Krafter の Backend は Controller ではなく Minimal APIs を中心に構成されています。`MapGet`、`MapPost`、`MapPut`、`MapDelete` で endpoint を登録し、`RouteGroupBuilder` で feature ごとの route group を作ります。OpenAPI と Scalar によって API 仕様も確認できます。

## Krafterでの実装

- Backend entry point: [src/AditiKraft.Krafter.Backend/Program.cs](../../../src/AditiKraft.Krafter.Backend/Program.cs)
- Endpoint mapping extension: [src/AditiKraft.Krafter.Backend/Web/HostingExtensions.cs](../../../src/AditiKraft.Krafter.Backend/Web/HostingExtensions.cs)
- Route discovery: [src/AditiKraft.Krafter.Backend/Web/RouteConfiguration.cs](../../../src/AditiKraft.Krafter.Backend/Web/Configuration/RouteConfiguration.cs)
- Swagger/Scalar configuration: [src/AditiKraft.Krafter.Backend/Web/Configuration/SwaggerConfiguration.cs](../../../src/AditiKraft.Krafter.Backend/Web/Configuration/SwaggerConfiguration.cs)
- Example endpoint: [src/AditiKraft.Krafter.Backend/Features/Users/GetUsers.cs](../../../src/AditiKraft.Krafter.Backend/Features/Users/GetUsers.cs)

各 operation の `Route` class は `IRouteRegistrar` を実装し、`MapRoute` で endpoint を追加します。`MapBackendEndpoints()` が route discovery 経由でそれらをまとめて登録します。

## 実務で必要な知識

Minimal APIs では endpoint handler の引数に `[FromServices]`、`[FromBody]`、`[AsParameters]` などを使って、DI service、request body、query parameter を受け取ります。Krafter の一覧系 endpoint は `GetRequestInput` を `[AsParameters]` で受け取り、pagination、filter、sort に使います。

`.Produces<Response<T>>()` は OpenAPI document の response schema を正しくするために重要です。UI client や API reference に影響するため、新しい endpoint を追加したら response type も忘れずに記述します。

`AddFluentValidationFilter()` は endpoint filter として validation を差し込みます。Minimal APIs では MVC の model validation と違うため、Krafter の既存 filter を使うことが大切です。

## 確認課題

- `GetUsers.cs` の `MapGet` がどの route に対応するか、`ApiRoutes.Users` と合わせて確認する。
- `CreateTenant.cs` の `MapPost` で body、service、cancellation token がどう渡されるか読む。
- Scalar API Reference の URL が README にどう記載されているか確認する。

## 出典リンク

- [Minimal APIs quick reference](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/minimal-apis?view=aspnetcore-9.0)
- [Filters in Minimal API apps](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/minimal-apis/min-api-filters?view=aspnetcore-10.0)
- [OpenAPI support in ASP.NET Core API apps](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/openapi/overview?view=aspnetcore-10.0)
- [Use the generated OpenAPI documents](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/openapi/using-openapi-documents?view=aspnetcore-9.0)
- [Scalar ASP.NET Core integration](https://guides.scalar.com/products/api-references/integrations/aspnetcore/integration)
