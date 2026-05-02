# Blazor Rendering Model

## 何を学ぶか

Krafter の UI は Blazor Web App と Blazor WebAssembly client を組み合わせています。Server 側 host が初期 HTML、static assets、authentication cookie などを扱い、Client project が interactive WebAssembly component を提供します。

## Krafterでの実装

- Blazor Web host startup: [src/UI/AditiKraft.Krafter.UI.Web/Program.cs](../../../src/UI/AditiKraft.Krafter.UI.Web/Program.cs)
- Blazor client startup: [src/UI/AditiKraft.Krafter.UI.Web.Client/Program.cs](../../../src/UI/AditiKraft.Krafter.UI.Web.Client/Program.cs)
- App component: [src/UI/AditiKraft.Krafter.UI.Web/Components/App.razor](../../../src/UI/AditiKraft.Krafter.UI.Web/Components/App.razor)
- Client routes: [src/UI/AditiKraft.Krafter.UI.Web.Client/Routes.razor](../../../src/UI/AditiKraft.Krafter.UI.Web.Client/Routes.razor)
- Layout component: [src/UI/AditiKraft.Krafter.UI.Web.Client/Common/Components/Layout/MainLayout.razor](../../../src/UI/AditiKraft.Krafter.UI.Web.Client/Common/Components/Layout/MainLayout.razor)

`App.razor` では `InteractiveWebAssemblyRenderMode(true)` を使い、`Routes` と `HeadOutlet` に render mode を渡しています。`Program.cs` は `.AddInteractiveServerComponents()` と `.AddInteractiveWebAssemblyComponents()` を登録します。

## 実務で必要な知識

Blazor Web App では component が server で prerender され、その後 interactive になります。WebAssembly component は client 側で .NET runtime を使って動くため、ブラウザ内で実行できる処理と server 側でしかできない処理を分ける必要があります。

Krafter では `IFormFactor` により server-side と WebAssembly の違いを吸収しています。認証 storage、HTTP context、navigation、SignalR 初期化などは実行場所によって使える API が違います。

UI では `.razor` と `.razor.cs` の code-behind pattern を使います。markup と logic を分けることで、Radzen component の layout と API 呼び出し処理を読みやすくしています。

## 確認課題

- `App.razor` で `Routes` に指定されている render mode を確認する。
- `Program.cs` の server host と client host で登録される service の違いを比較する。
- `IFormFactor` の実装が server と WebAssembly でどう違うか確認する。

## 出典リンク

- [ASP.NET Core Blazor render modes](https://learn.microsoft.com/en-us/aspnet/core/blazor/components/render-modes?view=aspnetcore-10.0)
- [ASP.NET Core Blazor hosting models](https://learn.microsoft.com/en-us/aspnet/core/blazor/hosting-models?view=aspnetcore-10.0)
- [ASP.NET Core Blazor dependency injection](https://learn.microsoft.com/en-us/aspnet/core/blazor/fundamentals/dependency-injection?view=aspnetcore-10.0)
- [Call a web API from an ASP.NET Core Blazor app](https://learn.microsoft.com/en-us/aspnet/core/blazor/call-web-api?view=aspnetcore-10.0)
