# Migrations と Migrator

## 何を学ぶか

EF Core migrations は、C# の model 変更を database schema の変更履歴として管理する仕組みです。Krafter では migrations を手動で database に適用するのではなく、Aspire AppHost が short-lived migrator process を起動し、main app の前に migration を適用します。

## Krafterでの実装

- Migration workflow: [README.md](../../../README.md)
- AppHost migrator orchestration: [aspire/AditiKraft.Krafter.Aspire.AppHost/Program.cs](../../../aspire/AditiKraft.Krafter.Aspire.AppHost/Program.cs)
- Migrator entry point: [src/AditiKraft.Krafter.Backend.Migrator/Program.cs](../../../src/AditiKraft.Krafter.Backend.Migrator/Program.cs)
- Database initializer: [src/AditiKraft.Krafter.Backend.Migrator/ApiDbInitializer.cs](../../../src/AditiKraft.Krafter.Backend.Migrator/ApiDbInitializer.cs)
- Main migrations: [src/AditiKraft.Krafter.Backend/Migrations/](../../../src/AditiKraft.Krafter.Backend/Migrations/)
- Tenant migrations: [src/AditiKraft.Krafter.Backend/Migrations/TenantDb/](../../../src/AditiKraft.Krafter.Backend/Migrations/TenantDb/)
- Background job migrations: [src/AditiKraft.Krafter.Backend/Migrations/BackgroundJobs/](../../../src/AditiKraft.Krafter.Backend/Migrations/BackgroundJobs/)

AppHost では `app-migrator` executable を `AddExecutable` で登録し、database を `WaitFor(database)` で待ちます。API と UI は `WaitForCompletion(migrator)` によって migrator 完了後に起動します。

## 実務で必要な知識

Schema を変える変更では、entity や DbContext だけでなく migration file も必要です。Krafter には複数 DbContext があるため、どの context に対する変更かを間違えないことが重要です。

local development では `dotnet ef migrations add <Name> --context ApplicationDbContext` のように migration を追加し、AppHost を起動して migrator に適用させます。README では context ごとの command が案内されています。

production では migration 適用は慎重に扱います。EF Core の `database update` は便利ですが、production では SQL script や migration bundle、事前検証を使うチームもあります。Krafter の migrator は local/development の体験を簡単にする設計として理解してください。

## 確認課題

- `aspire/.../Program.cs` で migrator、backend、web の依存順序を線で書く。
- `src/AditiKraft.Krafter.Backend/Migrations/DesignTimeDbContextFactory.cs` が何のためにあるか調べる。
- 新しい entity を追加した場合、どの context の migration を作るべきか判断する。

## 出典リンク

- [Managing Migrations](https://learn.microsoft.com/en-us/ef/core/managing-schemas/migrations/managing)
- [Applying Migrations](https://learn.microsoft.com/en-us/ef/core/managing-schemas/migrations/applying)
- [Design-time DbContext Creation](https://learn.microsoft.com/en-us/ef/core/cli/dbcontext-creation)
- [What is the AppHost?](https://learn.microsoft.com/en-us/dotnet/aspire/fundamentals/app-host-overview)
- [Aspire PostgreSQL integration](https://learn.microsoft.com/en-us/dotnet/aspire/database/postgresql-integration)
