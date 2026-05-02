# Krafter で使われる C# 基礎

## 何を学ぶか

Krafter のコードを読むには、C# の文法を広く全部覚えるより、実装で頻出する形を先に押さえるのが近道です。特に nullable reference types、file-scoped namespace、primary constructor、`async` / `await`、dependency injection 前提のクラス設計を理解すると、Backend と UI のどちらも追いやすくなります。

## Krafterでの実装

- Backend entry point: [src/AditiKraft.Krafter.Backend/Program.cs](../../../src/AditiKraft.Krafter.Backend/Program.cs)
- VSA operation: [src/AditiKraft.Krafter.Backend/Features/Users/GetUsers.cs](../../../src/AditiKraft.Krafter.Backend/Features/Users/GetUsers.cs)
- DI registration: [src/AditiKraft.Krafter.Backend/Web/HostingExtensions.cs](../../../src/AditiKraft.Krafter.Backend/Web/HostingExtensions.cs)
- Blazor code-behind: [src/UI/AditiKraft.Krafter.UI.Web.Client/Features/Users/Users.razor.cs](../../../src/UI/AditiKraft.Krafter.UI.Web.Client/Features/Users/Users.razor.cs)
- Shared response model: [src/AditiKraft.Krafter.Contracts/Common/Models/Response.cs](../../../src/AditiKraft.Krafter.Contracts/Common/Models/Response.cs)

Krafter では namespace は多くのファイルで file-scoped 形式です。たとえば `namespace AditiKraft.Krafter.Backend.Features.Users;` のように書くことで、そのファイル全体の namespace を指定しています。

## 実務で必要な知識

nullable reference types は、`string` と `string?` を区別して null の可能性をコンパイル時に見つける仕組みです。DTO では deserialization 後に値が入る前提の property に `= default!;` が使われます。これは「実行時に必ずセットされる」という開発者の意図をコンパイラに伝える書き方です。

primary constructor は `class Users(DialogService dialogService, ApiCallService api)` のように、クラス宣言の横で依存関係を受け取る書き方です。Krafter では DI container がこの constructor に service を渡します。`new` で直接作るより、DI に任せる設計が基本です。

`async` / `await` はデータベース、HTTP、SignalR、storage など待ち時間がある処理で使います。実務では `Task<T>` を返すメソッドでは例外や cancellation token の扱いも意識します。EF Core の query や Refit の API 呼び出しは必ず `await` し、同じ `DbContext` を並列に使わないようにします。

## 確認課題

- `src/AditiKraft.Krafter.Contracts/Contracts/Users/CreateUserRequest.cs` を開き、`string?` と `default!` の使い分けを確認する。
- `Users.razor.cs` の primary constructor に渡されている service がどこで登録されているか探す。
- `GetUsers.cs` の `ToListAsync` と `CountAsync` がなぜ `await` されているか説明する。

## 出典リンク

- [Nullable reference types](https://learn.microsoft.com/en-us/dotnet/csharp/nullable-references)
- [Namespaces and using directives](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/types/namespaces)
- [Classes](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/types/classes)
- [Asynchronous programming with async and await](https://learn.microsoft.com/en-us/dotnet/csharp/asynchronous-programming/)
- [Dependency injection in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-10.0)
