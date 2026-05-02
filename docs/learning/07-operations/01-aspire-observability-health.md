# Aspire、Observability、Health

## 何を学ぶか

Krafter の local development は .NET Aspire AppHost を中心にしています。AppHost は PostgreSQL、Migrator、Backend、UI を resource として宣言し、依存関係、service discovery、dashboard、OpenTelemetry、health checks を扱いやすくします。

## Krafterでの実装

- Split Host AppHost: [aspire/AditiKraft.Krafter.Aspire.AppHost/Program.cs](../../../aspire/AditiKraft.Krafter.Aspire.AppHost/Program.cs)
- Single Host AppHost: [aspire-single/AditiKraft.Krafter.Aspire.AppHost/Program.cs](../../../aspire-single/AditiKraft.Krafter.Aspire.AppHost/Program.cs)
- Service defaults: [aspire/AditiKraft.Krafter.Aspire.ServiceDefaults/Extensions.cs](../../../aspire/AditiKraft.Krafter.Aspire.ServiceDefaults/Extensions.cs)
- AppHost settings: [aspire/AditiKraft.Krafter.Aspire.AppHost/appsettings.json](../../../aspire/AditiKraft.Krafter.Aspire.AppHost/appsettings.json)
- Backend startup: [src/AditiKraft.Krafter.Backend/Program.cs](../../../src/AditiKraft.Krafter.Backend/Program.cs)
- UI Web startup: [src/UI/AditiKraft.Krafter.UI.Web/Program.cs](../../../src/UI/AditiKraft.Krafter.UI.Web/Program.cs)

`AddServiceDefaults()` は OpenTelemetry、health checks、service discovery、HttpClient resilience をまとめて登録します。`MapDefaultEndpoints()` は development environment で `/health` と `/alive` を map します。

## 実務で必要な知識

Aspire AppHost は production orchestrator そのものではなく、アプリの構成をコードで表す開発体験の中心です。`WithReference` は依存先の connection string や service discovery 情報を consumer に注入します。`WaitFor` と `WaitForCompletion` は startup order を明確にします。

Observability は logs、traces、metrics の 3 本柱です。Krafter の ServiceDefaults は OpenTelemetry instrumentation を登録し、Aspire dashboard で request、dependency、log を見やすくします。問題調査では、例外 message だけでなく、どの service からどの service に request が流れたかを確認します。

Health check は readiness と liveness の区別が重要です。`/health` は traffic を受けてよいか、`/alive` は process が生きているかの判断に使われます。Krafter では development のみで default endpoints を公開する設定です。

## 確認課題

- AppHost で PostgreSQL に `.WithDataVolume()` が設定されている理由を説明する。
- `ServiceDefaults/Extensions.cs` の OpenTelemetry instrumentation を列挙する。
- `/health` と `/alive` が development のみで map される理由を考える。

## 出典リンク

- [What is Aspire?](https://learn.microsoft.com/en-us/dotnet/aspire/get-started/aspire-overview)
- [What is the AppHost?](https://learn.microsoft.com/en-us/dotnet/aspire/fundamentals/app-host-overview)
- [Aspire service defaults](https://learn.microsoft.com/en-us/dotnet/aspire/fundamentals/service-defaults)
- [Aspire telemetry](https://learn.microsoft.com/en-us/dotnet/aspire/fundamentals/telemetry)
- [Aspire health checks](https://learn.microsoft.com/en-us/dotnet/aspire/fundamentals/health-checks)
- [Aspire PostgreSQL integration](https://learn.microsoft.com/en-us/dotnet/aspire/database/postgresql-integration)
