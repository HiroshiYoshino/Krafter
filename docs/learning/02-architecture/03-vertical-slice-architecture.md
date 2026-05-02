# Vertical Slice Architecture

## 何を学ぶか

Vertical Slice Architecture は、技術レイヤーごとではなく、ユースケースや機能単位でコードをまとめる考え方です。Krafter の Backend では、`Features/Users/GetUsers.cs` のように、1 つの operation file に handler と route mapping を近づけています。

## Krafterでの実装

- Backend agent rules: [src/AditiKraft.Krafter.Backend/Agents.md](../../../src/AditiKraft.Krafter.Backend/Agents.md)
- Users list operation: [src/AditiKraft.Krafter.Backend/Features/Users/GetUsers.cs](../../../src/AditiKraft.Krafter.Backend/Features/Users/GetUsers.cs)
- Tenant create operation: [src/AditiKraft.Krafter.Backend/Features/Tenants/CreateTenant.cs](../../../src/AditiKraft.Krafter.Backend/Features/Tenants/CreateTenant.cs)
- Handler marker: [src/AditiKraft.Krafter.Backend/Features/IScopedHandler.cs](../../../src/AditiKraft.Krafter.Backend/Features/IScopedHandler.cs)
- Route registrar: [src/AditiKraft.Krafter.Backend/Web/IRouteRegistrar.cs](../../../src/AditiKraft.Krafter.Backend/Web/IRouteRegistrar.cs)
- Handler auto registration: [src/AditiKraft.Krafter.Backend/Infrastructure/Persistence/PersistenceConfiguration.cs](../../../src/AditiKraft.Krafter.Backend/Infrastructure/Persistence/PersistenceConfiguration.cs)

Krafter の operation は、`Handler` が処理を実行し、`Route` が Minimal API endpoint を map します。DTO と validator は Contracts 側に置き、Backend-only DTO を増やさない方針です。

## 実務で必要な知識

VSA の利点は、機能変更の影響範囲を見つけやすいことです。たとえば user 一覧の条件を変えるなら `GetUsers.cs` を中心に読めます。従来の Controller、Service、Repository、DTO、Profile がばらばらに分散する構造より、ひとつの use case を追いやすくなります。

ただし、共通化の判断には注意が必要です。複数 operation で本当に共有する処理だけを `Common/` や service に出します。早すぎる service 化は、VSA の見通しの良さを失わせます。

実務では「operation file が肥大化したら分割」ではなく、「その use case の理解に必要なものは近くに置く」という視点を持ちます。validation、permission、route、response shape は operation と一緒に確認できるべきです。

## 確認課題

- `GetUsers.cs` の `Handler` と `Route` の責務を分けて説明する。
- `PersistenceConfiguration.cs` が `IScopedHandler` 実装をどのように DI 登録しているか読む。
- 新しい `Projects` 一覧 endpoint を追加すると仮定し、Contracts / Backend / UI の変更場所をメモする。

## 出典リンク

- [Vertical Slice Architecture by Jimmy Bogard](https://www.jimmybogard.com/vertical-slice-architecture/)
- [Minimal APIs quick reference](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/minimal-apis?view=aspnetcore-9.0)
- [Dependency injection in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-10.0)
- [Policy-based authorization in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/security/authorization/policies?view=aspnetcore-10.0)
