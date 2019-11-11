---
title: ASP.NET Web フォームから Blazor への移行
description: 既存の ASP.NET Web フォームアプリを Blazor に移行する方法について説明します。
author: twsouthwick
ms.author: tasou
ms.date: 09/19/2019
ms.openlocfilehash: b6604e000eaf79bcd8da15d72a3d85713c620851
ms.sourcegitcommit: 5a28f8eb071fcc09b045b0c4ae4b96898673192e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2019
ms.locfileid: "73842039"
---
# <a name="migrate-from-aspnet-web-forms-to-blazor"></a><span data-ttu-id="929da-103">ASP.NET Web フォームから Blazor への移行</span><span class="sxs-lookup"><span data-stu-id="929da-103">Migrate from ASP.NET Web Forms to Blazor</span></span>

[!INCLUDE [book-preview](../../../includes/book-preview.md)]

<span data-ttu-id="929da-104">ASP.NET Web Forms から Blazor へのコードベースの移行は、計画を必要とする時間のかかる作業です。</span><span class="sxs-lookup"><span data-stu-id="929da-104">Migrating a code base from ASP.NET Web Forms to Blazor is a time-consuming task that requires planning.</span></span> <span data-ttu-id="929da-105">この章では、プロセスの概要を示します。</span><span class="sxs-lookup"><span data-stu-id="929da-105">This chapter outlines the process.</span></span> <span data-ttu-id="929da-106">移行を容易にするには、アプリが*N 層*アーキテクチャに準拠していることを確認し、アプリモデル (この場合は Web フォーム) がビジネスロジックとは別のものになるようにします。</span><span class="sxs-lookup"><span data-stu-id="929da-106">Something that can ease the transition is to ensure the app adheres to an *N-tier* architecture, wherein the app model (in this case, Web Forms) is separate from the business logic.</span></span> <span data-ttu-id="929da-107">レイヤーを論理的に分離することにより、.NET Core と Blazor に移行する必要があることが明確になります。</span><span class="sxs-lookup"><span data-stu-id="929da-107">This logical separation of layers makes it clear what needs to move to .NET Core and Blazor.</span></span>

<span data-ttu-id="929da-108">この例では、 [GitHub](https://github.com/dotnet-architecture/eShopOnBlazor)で入手できる eShop アプリが使用されています。</span><span class="sxs-lookup"><span data-stu-id="929da-108">For this example, the eShop app available on [GitHub](https://github.com/dotnet-architecture/eShopOnBlazor) is used.</span></span> <span data-ttu-id="929da-109">eShop は、フォームの入力と検証によって CRUD 機能を提供するカタログサービスです。</span><span class="sxs-lookup"><span data-stu-id="929da-109">eShop is a catalog service that provides CRUD capabilities via form entry and validation.</span></span>

<span data-ttu-id="929da-110">作業中のアプリを Blazor に移行する理由</span><span class="sxs-lookup"><span data-stu-id="929da-110">Why should a working app be migrated to Blazor?</span></span> <span data-ttu-id="929da-111">何度も必要ありません。</span><span class="sxs-lookup"><span data-stu-id="929da-111">Many times, there's no need.</span></span> <span data-ttu-id="929da-112">ASP.NET Web フォームは、長年にわたって引き続きサポートされます。</span><span class="sxs-lookup"><span data-stu-id="929da-112">ASP.NET Web Forms will continue to be supported for many years.</span></span> <span data-ttu-id="929da-113">ただし、Blazor が提供する機能の多くは、移行されたアプリでのみサポートされています。</span><span class="sxs-lookup"><span data-stu-id="929da-113">However, many of the features that Blazor provides are only supported on a migrated app.</span></span> <span data-ttu-id="929da-114">次のような機能があります。</span><span class="sxs-lookup"><span data-stu-id="929da-114">Such features include:</span></span>

- <span data-ttu-id="929da-115">`Span<T>` などのフレームワークにおけるパフォーマンスの向上</span><span class="sxs-lookup"><span data-stu-id="929da-115">Performance improvements in the framework such as `Span<T>`</span></span>
- <span data-ttu-id="929da-116">Webasとして実行する機能</span><span class="sxs-lookup"><span data-stu-id="929da-116">Ability to run as WebAssembly</span></span>
- <span data-ttu-id="929da-117">Linux と macOS のクロスプラットフォームサポート</span><span class="sxs-lookup"><span data-stu-id="929da-117">Cross-platform support for Linux and macOS</span></span>
- <span data-ttu-id="929da-118">アプリローカル配置または共有フレームワークの配置 (他のアプリに影響を与えることはありません)</span><span class="sxs-lookup"><span data-stu-id="929da-118">App-local deployment or shared framework deployment without impacting other apps</span></span>

<span data-ttu-id="929da-119">これらの新機能が十分に説得力を持っている場合は、アプリの移行に価値があるかもしれません。</span><span class="sxs-lookup"><span data-stu-id="929da-119">If these or other new features are compelling enough, there may be value in migrating the app.</span></span> <span data-ttu-id="929da-120">移行にはさまざまな形があります。アプリ全体、または変更を必要とする特定のエンドポイントのみを指定できます。</span><span class="sxs-lookup"><span data-stu-id="929da-120">The migration can take different shapes; it can be the entire app, or only certain endpoints that require the changes.</span></span> <span data-ttu-id="929da-121">移行の決定は、最終的には、開発者によって解決されるビジネス上の問題に基づいています。</span><span class="sxs-lookup"><span data-stu-id="929da-121">The decision to migrate is ultimately based on the business problems to be solved by the developer.</span></span>

## <a name="server-side-versus-client-side-hosting"></a><span data-ttu-id="929da-122">サーバー側とクライアント側のホスト</span><span class="sxs-lookup"><span data-stu-id="929da-122">Server-side versus client-side hosting</span></span>

<span data-ttu-id="929da-123">[ホスティングモデル](hosting-models.md)の章で説明されているように、Blazor アプリは、サーバー側とクライアント側の2つの異なる方法でホストできます。</span><span class="sxs-lookup"><span data-stu-id="929da-123">As described in the [hosting models](hosting-models.md) chapter, a Blazor app can be hosted in two different ways: server-side and client-side.</span></span> <span data-ttu-id="929da-124">サーバー側モデルでは ASP.NET Core SignalR 接続を使用して、サーバー上の実際のコードを実行しながら、DOM の更新を管理します。</span><span class="sxs-lookup"><span data-stu-id="929da-124">The server-side model uses ASP.NET Core SignalR connections to manage the DOM updates while running any actual code on the server.</span></span> <span data-ttu-id="929da-125">クライアント側モデルはブラウザー内で実行されるため、サーバー接続は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="929da-125">The client-side model runs as WebAssembly within a browser and requires no server connections.</span></span> <span data-ttu-id="929da-126">特定のアプリに最適な違いがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="929da-126">There are a number of differences that may affect which is best for a specific app:</span></span>

- <span data-ttu-id="929da-127">WebAssembly 実行はまだ開発中であり、現在の時点では、すべての機能 (スレッド処理など) をサポートしていない場合があります。</span><span class="sxs-lookup"><span data-stu-id="929da-127">Running as WebAssembly is still in development and may not support all features (such as threading) at the current time</span></span>
- <span data-ttu-id="929da-128">クライアントとサーバー間の通信を高いと、サーバー側モードで待機時間の問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="929da-128">Chatty communication between the client and server may cause latency issues in server-side mode</span></span>
- <span data-ttu-id="929da-129">データベースおよび内部または保護されたサービスへのアクセスには、クライアント側のホストとは別のサービスが必要です。</span><span class="sxs-lookup"><span data-stu-id="929da-129">Access to databases and internal or protected services require a separate service with client-side hosting</span></span>

<span data-ttu-id="929da-130">このドキュメントの執筆時点では、サーバー側モデルは Web フォームによく似ています。</span><span class="sxs-lookup"><span data-stu-id="929da-130">At the time of writing, the server-side model more closely resembles Web Forms.</span></span> <span data-ttu-id="929da-131">この章では、実稼働の準備として、サーバー側のホスティングモデルに焦点を当てています。</span><span class="sxs-lookup"><span data-stu-id="929da-131">Most of this chapter focuses on the server-side hosting model, as it's production-ready.</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="929da-132">新しいプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="929da-132">Create a new project</span></span>

<span data-ttu-id="929da-133">この最初の移行手順では、新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="929da-133">This initial migration step is to create a new project.</span></span> <span data-ttu-id="929da-134">このプロジェクトの種類は、.NET Core の SDK スタイルプロジェクトに基づいており、以前のプロジェクト形式で使用されていた定型句の多くが単純化されています。</span><span class="sxs-lookup"><span data-stu-id="929da-134">This project type is based on the SDK style projects of .NET Core and simplifies much of the boilerplate that was used in previous project formats.</span></span> <span data-ttu-id="929da-135">詳細については、「プロジェクトの[構造](project-structure.md)」の章を参照してください。</span><span class="sxs-lookup"><span data-stu-id="929da-135">For more detail, please see the chapter on [Project Structure](project-structure.md).</span></span>

<span data-ttu-id="929da-136">プロジェクトが作成されたら、前のプロジェクトで使用したライブラリをインストールします。</span><span class="sxs-lookup"><span data-stu-id="929da-136">Once the project has been created, install the libraries that were used in the previous project.</span></span> <span data-ttu-id="929da-137">以前の Web フォームプロジェクトでは、 *app.config*ファイルを使用して必要な NuGet パッケージを一覧表示している可能性があります。</span><span class="sxs-lookup"><span data-stu-id="929da-137">In older Web Forms projects, you may have used the *packages.config* file to list the required NuGet packages.</span></span> <span data-ttu-id="929da-138">新しい SDK スタイルのプロジェクトでは、 *app.config*がプロジェクトファイル内の `<PackageReference>` 要素に置き換えられました。</span><span class="sxs-lookup"><span data-stu-id="929da-138">In the new SDK-style project, *packages.config* has been replaced with `<PackageReference>` elements in the project file.</span></span> <span data-ttu-id="929da-139">この方法の利点は、すべての依存関係が推移的にインストールされることです。</span><span class="sxs-lookup"><span data-stu-id="929da-139">A benefit to this approach is that all dependencies are installed transitively.</span></span> <span data-ttu-id="929da-140">必要な最上位レベルの依存関係のみが表示されます。</span><span class="sxs-lookup"><span data-stu-id="929da-140">You only list the top-level dependencies you care about.</span></span>

<span data-ttu-id="929da-141">使用している依存関係の多くは、Entity Framework 6 や log4net など、.NET Core で使用できます。</span><span class="sxs-lookup"><span data-stu-id="929da-141">Many of the dependencies you're using are available for .NET Core, including Entity Framework 6 and log4net.</span></span> <span data-ttu-id="929da-142">使用可能な .NET Core または .NET Standard バージョンがない場合は、.NET Framework バージョンを使用することがよくあります。</span><span class="sxs-lookup"><span data-stu-id="929da-142">If there's no .NET Core or .NET Standard version available, the .NET Framework version can often be used.</span></span> <span data-ttu-id="929da-143">マイレージは異なる場合があります。</span><span class="sxs-lookup"><span data-stu-id="929da-143">Your mileage may vary.</span></span> <span data-ttu-id="929da-144">.NET Core で使用できない API を使用すると、ランタイムエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="929da-144">Any API used that isn't available in .NET Core causes a runtime error.</span></span> <span data-ttu-id="929da-145">Visual Studio は、このようなパッケージを通知します。</span><span class="sxs-lookup"><span data-stu-id="929da-145">Visual Studio notifies you of such packages.</span></span> <span data-ttu-id="929da-146">**ソリューションエクスプローラー**のプロジェクトの **[参照]** ノードに黄色いアイコンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="929da-146">A yellow icon appears on the project's **References** node in **Solution Explorer**.</span></span>

<span data-ttu-id="929da-147">Blazor ベースの eShop プロジェクトでは、インストールされているパッケージを確認できます。</span><span class="sxs-lookup"><span data-stu-id="929da-147">In the Blazor-based eShop project, you can see the packages that are installed.</span></span> <span data-ttu-id="929da-148">以前は、*パッケージ .config*ファイルには、プロジェクトで使用されているすべてのパッケージが一覧表示されており、約50行のファイルが生成されていました。</span><span class="sxs-lookup"><span data-stu-id="929da-148">Previously, the *packages.config* file listed every package used in the project, resulting in a file almost 50 lines long.</span></span> <span data-ttu-id="929da-149">*App.config*のスニペットは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="929da-149">A snippet of *packages.config* is:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  ...
  <package id="Microsoft.ApplicationInsights.Agent.Intercept" version="2.4.0" targetFramework="net472" />
  <package id="Microsoft.ApplicationInsights.DependencyCollector" version="2.9.1" targetFramework="net472" />
  <package id="Microsoft.ApplicationInsights.PerfCounterCollector" version="2.9.1" targetFramework="net472" />
  <package id="Microsoft.ApplicationInsights.Web" version="2.9.1" targetFramework="net472" />
  <package id="Microsoft.ApplicationInsights.WindowsServer" version="2.9.1" targetFramework="net472" />
  <package id="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" version="2.9.1" targetFramework="net472" />
  <package id="Microsoft.AspNet.FriendlyUrls" version="1.0.2" targetFramework="net472" />
  <package id="Microsoft.AspNet.FriendlyUrls.Core" version="1.0.2" targetFramework="net472" />
  <package id="Microsoft.AspNet.ScriptManager.MSAjax" version="5.0.0" targetFramework="net472" />
  <package id="Microsoft.AspNet.ScriptManager.WebForms" version="5.0.0" targetFramework="net472" />
  ...
  <package id="System.Memory" version="4.5.1" targetFramework="net472" />
  <package id="System.Numerics.Vectors" version="4.4.0" targetFramework="net472" />
  <package id="System.Runtime.CompilerServices.Unsafe" version="4.5.0" targetFramework="net472" />
  <package id="System.Threading.Channels" version="4.5.0" targetFramework="net472" />
  <package id="System.Threading.Tasks.Extensions" version="4.5.1" targetFramework="net472" />
  <package id="WebGrease" version="1.6.0" targetFramework="net472" />
</packages>
```

<span data-ttu-id="929da-150">`<packages>` 要素には、すべての必要な依存関係が含まれます。</span><span class="sxs-lookup"><span data-stu-id="929da-150">The `<packages>` element includes all necessary dependencies.</span></span> <span data-ttu-id="929da-151">これらのパッケージが必要であるため、どのパッケージが含まれているかを特定するのは困難です。</span><span class="sxs-lookup"><span data-stu-id="929da-151">It's difficult to identify which of these packages are included because you require them.</span></span> <span data-ttu-id="929da-152">いくつかの `<package>` 要素は、必要な依存関係のニーズを満たすためだけに一覧表示されます。</span><span class="sxs-lookup"><span data-stu-id="929da-152">Some `<package>` elements are listed simply to satisfy the needs of dependencies you require.</span></span>

<span data-ttu-id="929da-153">Blazor プロジェクトには、プロジェクトファイル内の `<ItemGroup>` 要素内で必要な依存関係が一覧表示されます。</span><span class="sxs-lookup"><span data-stu-id="929da-153">The Blazor project lists the dependencies you require within an `<ItemGroup>` element in the project file:</span></span>

```xml
<ItemGroup>
    <PackageReference Include="Autofac" Version="4.9.3" />
    <PackageReference Include="EntityFramework" Version="6.3.0-preview9-19423-04" />
    <PackageReference Include="log4net" Version="2.0.8" />
</ItemGroup>
```

<span data-ttu-id="929da-154">Web フォーム開発者の有効期間を簡略化する NuGet パッケージの1つは、 [Windows 互換機能パック](../../core/porting/windows-compat-pack.md)です。</span><span class="sxs-lookup"><span data-stu-id="929da-154">One NuGet package that simplifies the life of Web Forms developers is the [Windows Compatibility Pack](../../core/porting/windows-compat-pack.md).</span></span> <span data-ttu-id="929da-155">.NET Core はクロスプラットフォームですが、一部の機能は Windows でのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="929da-155">Although .NET Core is cross-platform, some features are only available on Windows.</span></span> <span data-ttu-id="929da-156">Windows 固有の機能は、互換機能パックをインストールすることによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="929da-156">Windows-specific features are made available by installing the compatibility pack.</span></span> <span data-ttu-id="929da-157">このような機能の例としては、レジストリ、WMI、ディレクトリサービスなどがあります。</span><span class="sxs-lookup"><span data-stu-id="929da-157">Examples of such features include the Registry, WMI, and Directory Services.</span></span> <span data-ttu-id="929da-158">パッケージによって2万の Api が追加され、既に使い慣れている多くのサービスがアクティブ化されます。</span><span class="sxs-lookup"><span data-stu-id="929da-158">The package adds around 20,000 APIs and activates many services with which you may already be familiar.</span></span> <span data-ttu-id="929da-159">EShop プロジェクトは、互換性パックを必要としません。ただし、プロジェクトで Windows 固有の機能が使用されている場合は、パッケージによって移行作業が容易になります。</span><span class="sxs-lookup"><span data-stu-id="929da-159">The eShop project doesn't require the compatibility pack; but if your projects use Windows-specific features, the package eases the migration efforts.</span></span>

## <a name="enable-startup-process"></a><span data-ttu-id="929da-160">スタートアッププロセスを有効にする</span><span class="sxs-lookup"><span data-stu-id="929da-160">Enable startup process</span></span>

<span data-ttu-id="929da-161">Blazor のスタートアッププロセスは Web フォームから変更され、他の ASP.NET Core サービスと同様のセットアップに従います。</span><span class="sxs-lookup"><span data-stu-id="929da-161">The startup process for Blazor has changed from Web Forms and follows a similar setup for other ASP.NET Core services.</span></span> <span data-ttu-id="929da-162">ホストされるサーバー側では、Blazor コンポーネントは通常の ASP.NET Core アプリの一部として実行されます。</span><span class="sxs-lookup"><span data-stu-id="929da-162">When hosted server-side, Blazor components are run as part of a normal ASP.NET Core app.</span></span> <span data-ttu-id="929da-163">WebAssembly ブラウザーでホストされている場合、Blazor コンポーネントは同様のホスティングモデルを使用します。</span><span class="sxs-lookup"><span data-stu-id="929da-163">When hosted in the browser with WebAssembly, Blazor components use a similar hosting model.</span></span> <span data-ttu-id="929da-164">違いは、コンポーネントが任意のバックエンドプロセスから独立したサービスとして実行されることです。</span><span class="sxs-lookup"><span data-stu-id="929da-164">The difference is the components are run as a separate service from any of the backend processes.</span></span> <span data-ttu-id="929da-165">どちらの方法でも、スタートアップは似ています。</span><span class="sxs-lookup"><span data-stu-id="929da-165">Either way, the startup is similar.</span></span>

<span data-ttu-id="929da-166">*Global.asax.cs*ファイルは、Web フォームプロジェクトの既定のスタートアップページです。</span><span class="sxs-lookup"><span data-stu-id="929da-166">The *Global.asax.cs* file is the default startup page for Web Forms projects.</span></span> <span data-ttu-id="929da-167">EShop プロジェクトでは、このファイルによってコントロールの反転 (IoC) コンテナーが構成され、アプリまたは要求のさまざまなライフサイクルイベントが処理されます。</span><span class="sxs-lookup"><span data-stu-id="929da-167">In the eShop project, this file configures the Inversion of Control (IoC) container and handles the various lifecycle events of the app or request.</span></span> <span data-ttu-id="929da-168">これらのイベントの一部は、ミドルウェア (`Application_BeginRequest`など) で処理されます。</span><span class="sxs-lookup"><span data-stu-id="929da-168">Some of these events are handled with middleware (such as `Application_BeginRequest`).</span></span> <span data-ttu-id="929da-169">他のイベントでは、依存関係の挿入 (DI) によって特定のサービスをオーバーライドする必要があります。</span><span class="sxs-lookup"><span data-stu-id="929da-169">Other events require the overriding of specific services via dependency injection (DI).</span></span>

<span data-ttu-id="929da-170">たとえば、eShop の*Global.asax.cs*ファイルには、次のコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="929da-170">By way of example, the *Global.asax.cs* file for eShop, contains the following code:</span></span>

```csharp
public class Global : HttpApplication, IContainerProviderAccessor
{
    private static readonly ILog _log = LogManager.GetLogger(System.Reflection.MethodBase.GetCurrentMethod().DeclaringType);

    static IContainerProvider _containerProvider;
    IContainer container;

    public IContainerProvider ContainerProvider
    {
        get { return _containerProvider; }
    }

    protected void Application_Start(object sender, EventArgs e)
    {
        // Code that runs on app startup
        RouteConfig.RegisterRoutes(RouteTable.Routes);
        BundleConfig.RegisterBundles(BundleTable.Bundles);
        ConfigureContainer();
        ConfigDataBase();
    }

    /// <summary>
    /// Track the machine name and the start time for the session inside the current session
    /// </summary>
    protected void Session_Start(Object sender, EventArgs e)
    {
        HttpContext.Current.Session["MachineName"] = Environment.MachineName;
        HttpContext.Current.Session["SessionStartTime"] = DateTime.Now;
    }

    /// <summary>
    /// http://docs.autofac.org/en/latest/integration/webforms.html
    /// </summary>
    private void ConfigureContainer()
    {
        var builder = new ContainerBuilder();
        var mockData = bool.Parse(ConfigurationManager.AppSettings["UseMockData"]);
        builder.RegisterModule(new ApplicationModule(mockData));
        container = builder.Build();
        _containerProvider = new ContainerProvider(container);
    }

    private void ConfigDataBase()
    {
        var mockData = bool.Parse(ConfigurationManager.AppSettings["UseMockData"]);

        if (!mockData)
        {
            Database.SetInitializer<CatalogDBContext>(container.Resolve<CatalogDBInitializer>());
        }
    }

    protected void Application_BeginRequest(object sender, EventArgs e)
    {
        //set the property to our new object
        LogicalThreadContext.Properties["activityid"] = new ActivityIdHelper();

        LogicalThreadContext.Properties["requestinfo"] = new WebRequestInfo();

        _log.Debug("Application_BeginRequest");
    }
}
```

<span data-ttu-id="929da-171">上記のファイルは、サーバー側 Blazor の `Startup` クラスになります。</span><span class="sxs-lookup"><span data-stu-id="929da-171">The preceding file becomes the `Startup` class in server-side Blazor:</span></span>

```csharp
public class Startup
{
    public Startup(IConfiguration configuration, IWebHostEnvironment env)
    {
        Configuration = configuration;
        Env = env;
    }

    public IConfiguration Configuration { get; }

    public IWebHostEnvironment Env { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    // For more information on how to configure your application, visit https://go.microsoft.com/fwlink/?LinkID=398940
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages();
        services.AddServerSideBlazor();

        if (Configuration.GetValue<bool>("UseMockData"))
        {
            services.AddSingleton<ICatalogService, CatalogServiceMock>();
        }
        else
        {
            services.AddScoped<ICatalogService, CatalogService>();
            services.AddScoped<IDatabaseInitializer<CatalogDBContext>, CatalogDBInitializer>();
            services.AddSingleton<CatalogItemHiLoGenerator>();
            services.AddScoped(_ => new CatalogDBContext(Configuration.GetConnectionString("CatalogDBContext")));
        }
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, ILoggerFactory loggerFactory)
    {
        loggerFactory.AddLog4Net("log4Net.xml");

        if (Env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }
        else
        {
            app.UseExceptionHandler("/Home/Error");
        }

        // Middleware for Application_BeginRequest
        app.Use((ctx, next) =>
        {
            LogicalThreadContext.Properties["activityid"] = new ActivityIdHelper(ctx);
            LogicalThreadContext.Properties["requestinfo"] = new WebRequestInfo(ctx);
            return next();
        });

        app.UseStaticFiles();

        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapBlazorHub();
            endpoints.MapFallbackToPage("/_Host");
        });

        ConfigDataBase(app);
    }

    private void ConfigDataBase(IApplicationBuilder app)
    {
        using (var scope = app.ApplicationServices.CreateScope())
        {
            var initializer = scope.ServiceProvider.GetService<IDatabaseInitializer<CatalogDBContext>>();

            if (initializer != null)
            {
                Database.SetInitializer(initializer);
            }
        }
    }
}
```

<span data-ttu-id="929da-172">Web フォームからの重要な変更点の1つに、DI のこうし突出があります。</span><span class="sxs-lookup"><span data-stu-id="929da-172">One significant change you may notice from Web Forms is the prominence of DI.</span></span> <span data-ttu-id="929da-173">DI は、ASP.NET Core 設計の基本原則でした。</span><span class="sxs-lookup"><span data-stu-id="929da-173">DI has been a guiding principle in the ASP.NET Core design.</span></span> <span data-ttu-id="929da-174">ASP.NET Core framework のほぼすべての側面のカスタマイズをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="929da-174">It supports customization of almost all aspects of the ASP.NET Core framework.</span></span> <span data-ttu-id="929da-175">多くのシナリオで使用できる組み込みのサービスプロバイダーもあります。</span><span class="sxs-lookup"><span data-stu-id="929da-175">There's even a built-in service provider that can be used for many scenarios.</span></span> <span data-ttu-id="929da-176">より多くのカスタマイズが必要な場合は、多くのコミュニティプロジェクトでサポートできます。</span><span class="sxs-lookup"><span data-stu-id="929da-176">If more customization is required, it can be supported by the many community projects.</span></span> <span data-ttu-id="929da-177">たとえば、サードパーティの DI ライブラリの投資を進めることができます。</span><span class="sxs-lookup"><span data-stu-id="929da-177">For example, you can carry forward your third-party DI library investment.</span></span>

<span data-ttu-id="929da-178">元の eShop アプリには、セッション管理の構成がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="929da-178">In the original eShop app, there's some configuration for session management.</span></span> <span data-ttu-id="929da-179">サーバー側の Blazor は通信に ASP.NET Core SignalR を使用するため、接続が HTTP コンテキストとは関係なく発生する可能性があるため、セッション状態はサポートされません。</span><span class="sxs-lookup"><span data-stu-id="929da-179">Since server-side Blazor uses ASP.NET Core SignalR for communication, session state isn't supported as the connections may occur independent of an HTTP context.</span></span> <span data-ttu-id="929da-180">セッション状態を使用するアプリでは、Blazor アプリとして実行する前に、再設計が必要です。</span><span class="sxs-lookup"><span data-stu-id="929da-180">An app that uses session state requires rearchitecting before running as a Blazor app.</span></span>

<span data-ttu-id="929da-181">アプリの起動の詳細については、「[アプリのスタートアップ](app-startup.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="929da-181">For more information about app startup, see [App startup](app-startup.md).</span></span>

## <a name="migrate-http-modules-and-handlers-to-middleware"></a><span data-ttu-id="929da-182">HTTP モジュールとハンドラーのミドルウェアへの移行</span><span class="sxs-lookup"><span data-stu-id="929da-182">Migrate HTTP modules and handlers to middleware</span></span>

<span data-ttu-id="929da-183">Http モジュールとハンドラーは、HTTP 要求パイプラインを制御するための Web フォームの一般的なパターンです。</span><span class="sxs-lookup"><span data-stu-id="929da-183">HTTP modules and handlers are common patterns in Web Forms to control the HTTP request pipeline.</span></span> <span data-ttu-id="929da-184">`IHttpModule` または `IHttpHandler` を実装するクラスは、登録して、受信要求を処理することができます。</span><span class="sxs-lookup"><span data-stu-id="929da-184">Classes that implement `IHttpModule` or `IHttpHandler` could be registered and process incoming requests.</span></span> <span data-ttu-id="929da-185">Web フォームは、 *web.config ファイルで*モジュールとハンドラーを構成します。</span><span class="sxs-lookup"><span data-stu-id="929da-185">Web Forms configures modules and handlers in the *web.config* file.</span></span> <span data-ttu-id="929da-186">Web フォームは、アプリライフサイクルイベントの処理にも大きく基づいています。</span><span class="sxs-lookup"><span data-stu-id="929da-186">Web Forms is also heavily based on app lifecycle event handling.</span></span> <span data-ttu-id="929da-187">代わりに、ミドルウェアを使用 ASP.NET Core ます。</span><span class="sxs-lookup"><span data-stu-id="929da-187">ASP.NET Core uses middleware instead.</span></span> <span data-ttu-id="929da-188">ミドルウェアは、`Startup` クラスの `Configure` メソッドに登録されます。</span><span class="sxs-lookup"><span data-stu-id="929da-188">Middlewares are registered in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="929da-189">ミドルウェアの実行順序は、登録順序によって決まります。</span><span class="sxs-lookup"><span data-stu-id="929da-189">Middleware execution order is determined by the registration order.</span></span>

<span data-ttu-id="929da-190">[[スタートアッププロセスを有効](#enable-startup-process)にする] セクションでは、`Application_BeginRequest` メソッドとして Web フォームによってライフサイクルイベントが発生しました。</span><span class="sxs-lookup"><span data-stu-id="929da-190">In the [Enable startup process](#enable-startup-process) section, a lifecycle event was raised by Web Forms as the `Application_BeginRequest` method.</span></span> <span data-ttu-id="929da-191">このイベントは ASP.NET Core では使用できません。</span><span class="sxs-lookup"><span data-stu-id="929da-191">This event isn't available in ASP.NET Core.</span></span> <span data-ttu-id="929da-192">この動作を実現する1つの方法は、 *Startup.cs*ファイルの例に示すようにミドルウェアを実装することです。</span><span class="sxs-lookup"><span data-stu-id="929da-192">One way to achieve this behavior is to implement middleware as seen in the *Startup.cs* file example.</span></span> <span data-ttu-id="929da-193">このミドルウェアは同じロジックを行い、ミドルウェアパイプライン内の次のハンドラーに制御を転送します。</span><span class="sxs-lookup"><span data-stu-id="929da-193">This middleware does the same logic and then transfers control to the next handler in the middleware pipeline.</span></span>

<span data-ttu-id="929da-194">モジュールとハンドラーの移行の詳細については、「 [ASP.NET Core ミドルウェアへの HTTP ハンドラーとモジュールの移行](/aspnet/core/migration/http-modules)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="929da-194">For more information on migrating modules and handlers, see [Migrate HTTP handlers and modules to ASP.NET Core middleware](/aspnet/core/migration/http-modules).</span></span>

## <a name="migrate-static-files"></a><span data-ttu-id="929da-195">静的ファイルを移行する</span><span class="sxs-lookup"><span data-stu-id="929da-195">Migrate static files</span></span>

<span data-ttu-id="929da-196">静的ファイル (HTML、CSS、画像、JavaScript など) を提供するには、ミドルウェアによってファイルが公開されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="929da-196">To serve static files (for example, HTML, CSS, images, and JavaScript), the files must be exposed by middleware.</span></span> <span data-ttu-id="929da-197">`UseStaticFiles` メソッドを呼び出すと、web ルートパスからの静的ファイルを提供できるようになります。</span><span class="sxs-lookup"><span data-stu-id="929da-197">Calling the `UseStaticFiles` method enables the serving of static files from the web root path.</span></span> <span data-ttu-id="929da-198">既定の web ルートディレクトリは*wwwroot*ですが、カスタマイズすることができます。</span><span class="sxs-lookup"><span data-stu-id="929da-198">The default web root directory is *wwwroot*, but it can be customized.</span></span> <span data-ttu-id="929da-199">EShop の `Startup` クラスの `Configure` メソッドに含まれるように、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="929da-199">As included in the `Configure` method of eShop's `Startup` class:</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="929da-200">EShop プロジェクトは、基本的な静的ファイルアクセスを可能にします。</span><span class="sxs-lookup"><span data-stu-id="929da-200">The eShop project enables basic static file access.</span></span> <span data-ttu-id="929da-201">静的ファイルアクセスには多くのカスタマイズが用意されています。</span><span class="sxs-lookup"><span data-stu-id="929da-201">There are many customizations available for static file access.</span></span> <span data-ttu-id="929da-202">既定のファイルまたはファイルブラウザーを有効にする方法については、「 [ASP.NET Core の静的ファイル](/aspnet/core/fundamentals/static-files)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="929da-202">For information on enabling default files or a file browser, see [Static files in ASP.NET Core](/aspnet/core/fundamentals/static-files).</span></span>

## <a name="migrate-runtime-bundling-and-minification-setup"></a><span data-ttu-id="929da-203">ランタイムのバンドルと縮小のセットアップを移行する</span><span class="sxs-lookup"><span data-stu-id="929da-203">Migrate runtime bundling and minification setup</span></span>

<span data-ttu-id="929da-204">バンドルと縮小は、特定のファイルの種類を取得するためのサーバー要求の数とサイズを減らすためのパフォーマンスの最適化手法です。</span><span class="sxs-lookup"><span data-stu-id="929da-204">Bundling and minification are performance optimization techniques for reducing the number and size of server requests to retrieve certain file types.</span></span> <span data-ttu-id="929da-205">多くの場合、JavaScript と CSS では、クライアントに送信される前に何らかの形式のバンドルまたは縮小が行われます。</span><span class="sxs-lookup"><span data-stu-id="929da-205">JavaScript and CSS often undergo some form of bundling or minification before being sent to the client.</span></span> <span data-ttu-id="929da-206">ASP.NET Web フォームでは、これらの最適化は実行時に処理されます。</span><span class="sxs-lookup"><span data-stu-id="929da-206">In ASP.NET Web Forms, these optimizations are handled at runtime.</span></span> <span data-ttu-id="929da-207">最適化規則は、 *App_Start/bundleconfig.cs*ファイルに定義されています。</span><span class="sxs-lookup"><span data-stu-id="929da-207">The optimization conventions are defined an *App_Start/BundleConfig.cs* file.</span></span> <span data-ttu-id="929da-208">ASP.NET Core では、より宣言的な方法が採用されています。</span><span class="sxs-lookup"><span data-stu-id="929da-208">In ASP.NET Core, a more declarative approach is adopted.</span></span> <span data-ttu-id="929da-209">ファイルには、特定の縮小設定と共に、縮小するファイルが一覧表示されます。</span><span class="sxs-lookup"><span data-stu-id="929da-209">A file lists the files to be minified, along with specific minification settings.</span></span>

<span data-ttu-id="929da-210">バンドルと縮小の詳細については、「 [ASP.NET Core での静的資産のバンドルと](/aspnet/core/client-side/bundling-and-minification)縮小」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="929da-210">For more information on bundling and minification, see [Bundle and minify static assets in ASP.NET Core](/aspnet/core/client-side/bundling-and-minification).</span></span>

## <a name="migrate-aspx-pages"></a><span data-ttu-id="929da-211">ASPX ページの移行</span><span class="sxs-lookup"><span data-stu-id="929da-211">Migrate ASPX pages</span></span>

<span data-ttu-id="929da-212">Web フォームアプリのページは、 *.aspx*拡張子を持つファイルです。</span><span class="sxs-lookup"><span data-stu-id="929da-212">A page in a Web Forms app is a file with the *.aspx* extension.</span></span> <span data-ttu-id="929da-213">Web フォームページは、多くの場合、Blazor 内のコンポーネントにマップできます。</span><span class="sxs-lookup"><span data-stu-id="929da-213">A Web Forms page can often be mapped to a component in Blazor.</span></span> <span data-ttu-id="929da-214">Blazor コンポーネントは、拡張子が*razor*のファイルで作成されます。</span><span class="sxs-lookup"><span data-stu-id="929da-214">A Blazor component is authored in a file with the *.razor* extension.</span></span> <span data-ttu-id="929da-215">EShop プロジェクトの場合、5つのページが Razor ページに変換されます。</span><span class="sxs-lookup"><span data-stu-id="929da-215">For the eShop project, five pages are converted to a Razor page.</span></span>

<span data-ttu-id="929da-216">たとえば、詳細ビューは、Web フォームプロジェクトでは、 *details*、 *Details.aspx.cs*、および*Details.aspx.designer.cs*の3つのファイルで構成されます。</span><span class="sxs-lookup"><span data-stu-id="929da-216">For example, the details view is comprised of three files in the Web Forms project: *Details.aspx*, *Details.aspx.cs*, and *Details.aspx.designer.cs*.</span></span> <span data-ttu-id="929da-217">Blazor に変換する場合、分離コードとマークアップは、*詳細な razor*に結合されます。</span><span class="sxs-lookup"><span data-stu-id="929da-217">When converting to Blazor, the code-behind and markup are combined into *Details.razor*.</span></span> <span data-ttu-id="929da-218">Razor コンパイル ( *designer.cs*ファイルの内容と同じ) は、 *obj*ディレクトリに格納されていますが、既定では**ソリューションエクスプローラー**で表示できません。</span><span class="sxs-lookup"><span data-stu-id="929da-218">Razor compilation (equivalent to what's in *.designer.cs* files) is stored in the *obj* directory and aren't, by default, viewable in **Solution Explorer**.</span></span> <span data-ttu-id="929da-219">Web フォームページは、次のマークアップで構成されています。</span><span class="sxs-lookup"><span data-stu-id="929da-219">The Web Forms page consists of the following markup:</span></span>

```aspx-csharp
<%@ Page Title="Details" Language="C#" MasterPageFile="~/Site.Master" AutoEventWireup="true" CodeBehind="Details.aspx.cs" Inherits="eShopLegacyWebForms.Catalog.Details" %>

<asp:Content ID="Details" ContentPlaceHolderID="MainContent" runat="server">
    <h2 class="esh-body-title">Details</h2>

    <div class="container">
        <div class="row">
            <asp:Image runat="server" CssClass="col-md-6 esh-picture" ImageUrl='<%#"/Pics/" + product.PictureFileName%>' />
            <dl class="col-md-6 dl-horizontal">
                <dt>Name
                </dt>

                <dd>
                    <asp:Label runat="server" Text='<%#product.Name%>' />
                </dd>

                <dt>Description
                </dt>

                <dd>
                    <asp:Label runat="server" Text='<%#product.Description%>' />
                </dd>

                <dt>Brand
                </dt>

                <dd>
                    <asp:Label runat="server" Text='<%#product.CatalogBrand.Brand%>' />
                </dd>

                <dt>Type
                </dt>

                <dd>
                    <asp:Label runat="server" Text='<%#product.CatalogType.Type%>' />
                </dd>
                <dt>Price
                </dt>

                <dd>
                    <asp:Label CssClass="esh-price" runat="server" Text='<%#product.Price%>' />
                </dd>

                <dt>Picture name
                </dt>

                <dd>
                    <asp:Label runat="server" Text='<%#product.PictureFileName%>' />
                </dd>

                <dt>Stock
                </dt>

                <dd>
                    <asp:Label runat="server" Text='<%#product.AvailableStock%>' />
                </dd>

                <dt>Restock
                </dt>

                <dd>
                    <asp:Label runat="server" Text='<%#product.RestockThreshold%>' />
                </dd>

                <dt>Max stock
                </dt>

                <dd>
                    <asp:Label runat="server" Text='<%#product.MaxStockThreshold%>' />
                </dd>

            </dl>
        </div>

        <div class="form-actions no-color esh-link-list">
            <a runat="server" href='<%# GetRouteUrl("EditProductRoute", new {id =product.Id}) %>' class="esh-link-item">Edit
            </a>
            |
            <a runat="server" href="~" class="esh-link-item">Back to list
            </a>
        </div>

    </div>
</asp:Content>
```

<span data-ttu-id="929da-220">前のマークアップの分離コードには、次のコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="929da-220">The preceding markup's code-behind includes the following code:</span></span>

```csharp
using eShopLegacyWebForms.Models;
using eShopLegacyWebForms.Services;
using log4net;
using System;
using System.Web.UI;

namespace eShopLegacyWebForms.Catalog
{
    public partial class Details : System.Web.UI.Page
    {
        private static readonly ILog _log = LogManager.GetLogger(System.Reflection.MethodBase.GetCurrentMethod().DeclaringType);

        protected CatalogItem product;

        public ICatalogService CatalogService { get; set; }

        protected void Page_Load(object sender, EventArgs e)
        {
            var productId = Convert.ToInt32(Page.RouteData.Values["id"]);
            _log.Info($"Now loading... /Catalog/Details.aspx?id={productId}");
            product = CatalogService.FindCatalogItem(productId);

            this.DataBind();
        }
    }
}
```

<span data-ttu-id="929da-221">Blazor に変換されると、Web フォームページは次のコードに変換されます。</span><span class="sxs-lookup"><span data-stu-id="929da-221">When converted to Blazor, the Web Forms page translates to the following code:</span></span>

```razor
@page "/Catalog/Details/{id:int}"
@inject ICatalogService CatalogService
@inject ILogger<Details> Logger

<h2 class="esh-body-title">Details</h2>

<div class="container">
    <div class="row">
        <img class="col-md-6 esh-picture" src="@($"/Pics/{_item.PictureFileName}")">

        <dl class="col-md-6 dl-horizontal">
            <dt>
                Name
            </dt>

            <dd>
                @_item.Name
            </dd>

            <dt>
                Description
            </dt>

            <dd>
                @_item.Description
            </dd>

            <dt>
                Brand
            </dt>

            <dd>
                @_item.CatalogBrand.Brand
            </dd>

            <dt>
                Type
            </dt>

            <dd>
                @_item.CatalogType.Type
            </dd>
            <dt>
                Price
            </dt>

            <dd>
                @_item.Price
            </dd>

            <dt>
                Picture name
            </dt>

            <dd>
                @_item.PictureFileName
            </dd>

            <dt>
                Stock
            </dt>

            <dd>
                @_item.AvailableStock
            </dd>

            <dt>
                Restock
            </dt>

            <dd>
                @_item.RestockThreshold
            </dd>

            <dt>
                Max stock
            </dt>

            <dd>
                @_item.MaxStockThreshold
            </dd>

        </dl>
    </div>

    <div class="form-actions no-color esh-link-list">
        <a href="@($"/Catalog/Edit/{_item.Id}")" class="esh-link-item">
            Edit
        </a>
        |
        <a href="/" class="esh-link-item">
            Back to list
        </a>
    </div>

</div>

@code {
    private CatalogItem _item;

    [Parameter]
    public int Id { get; set; }

    protected override void OnInitialized()
    {
        Logger.LogInformation("Now loading... /Catalog/Details/{Id}", Id);

        _item = CatalogService.FindCatalogItem(Id);
    }
}
```

<span data-ttu-id="929da-222">コードとマークアップが同じファイル内にあることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="929da-222">Notice that the code and markup are in the same file.</span></span> <span data-ttu-id="929da-223">必要なサービスは、`@inject` 属性を使用してアクセスできるようになります。</span><span class="sxs-lookup"><span data-stu-id="929da-223">Any required services are made accessible with the `@inject` attribute.</span></span> <span data-ttu-id="929da-224">`@page` ディレクティブごとに、このページには `Catalog/Details/{id}` ルートでアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="929da-224">Per the `@page` directive, this page can be accessed at the `Catalog/Details/{id}` route.</span></span> <span data-ttu-id="929da-225">ルートの `{id}` プレースホルダーの値が、整数に制限されています。</span><span class="sxs-lookup"><span data-stu-id="929da-225">The value of the route's `{id}` placeholder has been constrained to an integer.</span></span> <span data-ttu-id="929da-226">「[ルーティング](pages-routing-layouts.md)」セクションで説明したように、Web フォームとは異なり、Razor コンポーネントはそのルートと、含まれるすべてのパラメーターを明示的に指定します。</span><span class="sxs-lookup"><span data-stu-id="929da-226">As described in the [routing](pages-routing-layouts.md) section, unlike Web Forms, a Razor component explicitly states its route and any parameters that are included.</span></span> <span data-ttu-id="929da-227">多くの Web フォームコントロールには、Blazor の対応するものが含まれていない場合があります。</span><span class="sxs-lookup"><span data-stu-id="929da-227">Many Web Forms controls may not have exact counterparts in Blazor.</span></span> <span data-ttu-id="929da-228">多くの場合、同じ目的で使用される同等の HTML スニペットがあります。</span><span class="sxs-lookup"><span data-stu-id="929da-228">There's often an equivalent HTML snippet that will serve the same purpose.</span></span> <span data-ttu-id="929da-229">たとえば、`<asp:Label />` コントロールは HTML `<label>` 要素に置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="929da-229">For example, the `<asp:Label />` control can be replaced with an HTML `<label>` element.</span></span>

### <a name="model-validation-in-blazor"></a><span data-ttu-id="929da-230">Blazor でのモデルの検証</span><span class="sxs-lookup"><span data-stu-id="929da-230">Model validation in Blazor</span></span>

<span data-ttu-id="929da-231">Web フォームのコードに検証が含まれている場合は、ほとんど変更せずに、必要なものの多くを転送できます。</span><span class="sxs-lookup"><span data-stu-id="929da-231">If your Web Forms code includes validation, you can transfer much of what you have with little-to-no changes.</span></span> <span data-ttu-id="929da-232">Blazor で実行する利点は、カスタム JavaScript がなくても同じ検証ロジックを実行できることです。</span><span class="sxs-lookup"><span data-stu-id="929da-232">A benefit to running in Blazor is that the same validation logic can be run without needing custom JavaScript.</span></span> <span data-ttu-id="929da-233">データ注釈を使用すると、モデルの検証が容易になります。</span><span class="sxs-lookup"><span data-stu-id="929da-233">Data annotations enable easy model validation.</span></span>

<span data-ttu-id="929da-234">たとえば、 *Create .aspx*ページには、検証を含むデータ入力フォームがあります。</span><span class="sxs-lookup"><span data-stu-id="929da-234">For example, the *Create.aspx* page has a data entry form with validation.</span></span> <span data-ttu-id="929da-235">スニペットの例は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="929da-235">An example snippet would look like this:</span></span>

```aspx
<div class="form-group">
    <label class="control-label col-md-2">Name</label>
    <div class="col-md-3">
        <asp:TextBox ID="Name" runat="server" CssClass="form-control"></asp:TextBox>
        <asp:RequiredFieldValidator runat="server" ControlToValidate="Name" Display="Dynamic"
            CssClass="field-validation-valid text-danger" ErrorMessage="The Name field is required." />
    </div>
</div>
```

<span data-ttu-id="929da-236">Blazor では、同じマークアップが、*作成した razor*ファイルで提供されます。</span><span class="sxs-lookup"><span data-stu-id="929da-236">In Blazor, the equivalent markup is provided in a *Create.razor* file:</span></span>

```razor
<EditForm Model="_item" OnValidSubmit="@...">
    <DataAnnotationsValidator />

    <div class="form-group">
        <label class="control-label col-md-2">Name</label>
        <div class="col-md-3">
            <InputText class="form-control" @bind-Value="_item.Name" />
            <ValidationMessage For="(() => _item.Name)" />
        </div>
    </div>

    ...
</EditForm>
```

<span data-ttu-id="929da-237">`EditForm` コンテキストには検証のサポートが含まれており、入力をラップすることができます。</span><span class="sxs-lookup"><span data-stu-id="929da-237">The `EditForm` context includes validation support and can be wrapped around input.</span></span> <span data-ttu-id="929da-238">データ注釈は、検証を追加する一般的な方法です。</span><span class="sxs-lookup"><span data-stu-id="929da-238">Data annotations are a common way to add validation.</span></span> <span data-ttu-id="929da-239">このような検証のサポートは、`DataAnnotationsValidator` コンポーネントを通じて追加できます。</span><span class="sxs-lookup"><span data-stu-id="929da-239">Such validation support can be added via the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="929da-240">このメカニズムの詳細については、「 [ASP.NET Core Blazor フォームと検証](/aspnet/core/blazor/forms-validation)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="929da-240">For more information on this mechanism, see [ASP.NET Core Blazor forms and validation](/aspnet/core/blazor/forms-validation).</span></span>

## <a name="migrate-built-in-web-forms-controls"></a><span data-ttu-id="929da-241">組み込みの Web フォームコントロールを移行する</span><span class="sxs-lookup"><span data-stu-id="929da-241">Migrate built-in Web Forms controls</span></span>

<span data-ttu-id="929da-242">*このコンテンツは近日公開予定です。*</span><span class="sxs-lookup"><span data-stu-id="929da-242">*This content is coming soon.*</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="929da-243">構成の移行</span><span class="sxs-lookup"><span data-stu-id="929da-243">Migrate configuration</span></span>

<span data-ttu-id="929da-244">Web フォームプロジェクトでは、通常、構成データは*web.config ファイルに*格納されます。</span><span class="sxs-lookup"><span data-stu-id="929da-244">In a Web Forms project, configuration data is most commonly stored in the *web.config* file.</span></span> <span data-ttu-id="929da-245">構成データには `ConfigurationManager`を使用してアクセスします。</span><span class="sxs-lookup"><span data-stu-id="929da-245">The configuration data is accessed with `ConfigurationManager`.</span></span> <span data-ttu-id="929da-246">多くの場合、オブジェクトの解析にはサービスが必要でした。</span><span class="sxs-lookup"><span data-stu-id="929da-246">Services were often required to parse objects.</span></span> <span data-ttu-id="929da-247">.NET Framework 4.7.2 では、`ConfigurationBuilders`を使用して構成に省かが追加されました。</span><span class="sxs-lookup"><span data-stu-id="929da-247">With .NET Framework 4.7.2, composability was added to configuration via `ConfigurationBuilders`.</span></span> <span data-ttu-id="929da-248">これらのビルダーを使用すると、開発者は、必要な値を取得するために実行時に構成された構成のさまざまなソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="929da-248">These builders allowed developers to add various sources for configuration that was then composed at runtime to retrieve the necessary values.</span></span>

<span data-ttu-id="929da-249">ASP.NET Core には、アプリとデプロイで使用される構成ソースを定義できる柔軟な構成システムが導入されました。</span><span class="sxs-lookup"><span data-stu-id="929da-249">ASP.NET Core introduced a flexible configuration system that allows you to define the configuration source or sources used by your app and deployment.</span></span> <span data-ttu-id="929da-250">Web フォームアプリで使用している `ConfigurationBuilder` インフラストラクチャは、ASP.NET Core 構成システムで使用されている概念に基づいてモデル化されています。</span><span class="sxs-lookup"><span data-stu-id="929da-250">The `ConfigurationBuilder` infrastructure that you may be using in your Web Forms app was modeled after the concepts used in the ASP.NET Core configuration system.</span></span>

<span data-ttu-id="929da-251">次のスニペットは、web フォームの eShop プロジェクトが web.config を使用して構成値を格納する方法を示して*い*ます。</span><span class="sxs-lookup"><span data-stu-id="929da-251">The following snippet demonstrates how the Web Forms eShop project uses *web.config* to store configuration values:</span></span>

```xml
<configuration>
  <configSections>
    <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
  </configSections>
  <connectionStrings>
    <add name="CatalogDBContext" connectionString="Data Source=(localdb)\MSSQLLocalDB; Initial Catalog=Microsoft.eShopOnContainers.Services.CatalogDb; Integrated Security=True; MultipleActiveResultSets=True;" providerName="System.Data.SqlClient" />
  </connectionStrings>
  <appSettings>
    <add key="UseMockData" value="true" />
    <add key="UseCustomizationData" value="false" />
  </appSettings>
```

<span data-ttu-id="929da-252">データベース接続文字列などの機密情報は、web.config 内に格納されるのが一般的*です。* シークレットは、ソース管理などの安全でない場所に必然的に保持されます。</span><span class="sxs-lookup"><span data-stu-id="929da-252">It's common for secrets, such as database connection strings, to be stored within the *web.config*. The secrets are inevitably persisted in unsecure locations, such as source control.</span></span> <span data-ttu-id="929da-253">ASP.NET Core 上の Blazor では、上記の XML ベースの構成が次の JSON に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="929da-253">With Blazor on ASP.NET Core, the preceding XML-based configuration is replaced with the following JSON:</span></span>

```json
{
  "ConnectionStrings": {
    "CatalogDBContext": "Data Source=(localdb)\\MSSQLLocalDB; Initial Catalog=Microsoft.eShopOnContainers.Services.CatalogDb; Integrated Security=True; MultipleActiveResultSets=True;"
  },
  "UseMockData": true,
  "UseCustomizationData": false
}
```

<span data-ttu-id="929da-254">JSON は、既定の構成形式です。ただし、ASP.NET Core は XML などの他の多くの形式をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="929da-254">JSON is the default configuration format; however, ASP.NET Core supports many other formats, including XML.</span></span> <span data-ttu-id="929da-255">コミュニティでサポートされている形式もいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="929da-255">There are also several community-supported formats.</span></span>

<span data-ttu-id="929da-256">Blazor プロジェクトの `Startup` クラスのコンストラクターは、コンストラクターの挿入と呼ばれる DI の手法を使用して `IConfiguration` インスタンスを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="929da-256">The constructor in the Blazor project's `Startup` class accepts an `IConfiguration` instance through a DI technique known as constructor injection:</span></span>

```csharp
public class Startup
{
    public Startup(IConfiguration configuration, IWebHostEnvironment env)
    {
        Configuration = configuration;
        Env = env;
    }

    ...
}
```

<span data-ttu-id="929da-257">既定では、環境変数、JSON ファイル (*appsettings*および*appsettings. {Environment}. json*)、およびコマンドラインオプションは、構成オブジェクトの有効な構成ソースとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="929da-257">By default, environment variables, JSON files (*appsettings.json* and *appsettings.{Environment}.json*), and command-line options are registered as valid configuration sources in the configuration object.</span></span> <span data-ttu-id="929da-258">構成ソースには `Configuration[key]`経由でアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="929da-258">The configuration sources can be accessed via `Configuration[key]`.</span></span> <span data-ttu-id="929da-259">より高度な手法は、オプションパターンを使用して、構成データをオブジェクトにバインドすることです。</span><span class="sxs-lookup"><span data-stu-id="929da-259">A more advanced technique is to bind the configuration data to objects using the options pattern.</span></span> <span data-ttu-id="929da-260">構成とオプションのパターンの詳細については、それぞれ ASP.NET Core の「ASP.NET Core と[オプションのパターン](/aspnet/core/fundamentals/configuration/options)の[構成](/aspnet/core/fundamentals/configuration/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="929da-260">For more information on configuration and the options pattern, see [Configuration in ASP.NET Core](/aspnet/core/fundamentals/configuration/) and [Options pattern in ASP.NET Core](/aspnet/core/fundamentals/configuration/options), respectively.</span></span>

## <a name="migrate-data-access"></a><span data-ttu-id="929da-261">データアクセスの移行</span><span class="sxs-lookup"><span data-stu-id="929da-261">Migrate data access</span></span>

<span data-ttu-id="929da-262">データアクセスは、あらゆるアプリの重要な側面です。</span><span class="sxs-lookup"><span data-stu-id="929da-262">Data access is an important aspect of any app.</span></span> <span data-ttu-id="929da-263">EShop プロジェクトは、カタログ情報をデータベースに格納し、Entity Framework (EF) 6 でデータを取得します。</span><span class="sxs-lookup"><span data-stu-id="929da-263">The eShop project stores catalog information in a database and retrieves the data with Entity Framework (EF) 6.</span></span> <span data-ttu-id="929da-264">EF 6 は .NET Core 3.0 でサポートされているため、プロジェクトで引き続き使用できます。</span><span class="sxs-lookup"><span data-stu-id="929da-264">Since EF 6 is supported in .NET Core 3.0, the project can continue to use it.</span></span>

<span data-ttu-id="929da-265">EShop には、次の EF 関連の変更が必要でした。</span><span class="sxs-lookup"><span data-stu-id="929da-265">The following EF-related changes were necessary to eShop:</span></span>

- <span data-ttu-id="929da-266">.NET Framework では、`DbContext` オブジェクトは*name = ConnectionString*という形式の文字列を受け取り、`ConfigurationManager.AppSettings[ConnectionString]` の接続文字列を使用して接続します。</span><span class="sxs-lookup"><span data-stu-id="929da-266">In .NET Framework, the `DbContext` object accepts a string of the form *name=ConnectionString* and uses the connection string from `ConfigurationManager.AppSettings[ConnectionString]` to connect.</span></span> <span data-ttu-id="929da-267">.NET Core では、これはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="929da-267">In .NET Core, this isn't supported.</span></span> <span data-ttu-id="929da-268">接続文字列を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="929da-268">The connection string must be supplied.</span></span>
- <span data-ttu-id="929da-269">データベースは同期的にアクセスされました。</span><span class="sxs-lookup"><span data-stu-id="929da-269">The database was accessed in a synchronous way.</span></span> <span data-ttu-id="929da-270">これは機能しますが、スケーラビリティが低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="929da-270">Though this works, scalability may suffer.</span></span> <span data-ttu-id="929da-271">このロジックは、非同期パターンに移行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="929da-271">This logic should be moved to an asynchronous pattern.</span></span>

<span data-ttu-id="929da-272">データセットバインドに対するネイティブサポートは同じではありませんが、Blazor でC#は、Razor ページでのサポートの柔軟性と性能が提供されます。</span><span class="sxs-lookup"><span data-stu-id="929da-272">Although there isn't the same native support for dataset binding, Blazor provides flexibility and power with its C# support in a Razor page.</span></span> <span data-ttu-id="929da-273">たとえば、計算を実行して結果を表示できます。</span><span class="sxs-lookup"><span data-stu-id="929da-273">For example, you can perform calculations and display the result.</span></span> <span data-ttu-id="929da-274">Blazor のデータパターンの詳細については、「[データアクセス](data.md)」の章を参照してください。</span><span class="sxs-lookup"><span data-stu-id="929da-274">For more information on data patterns in Blazor, see the [Data access](data.md) chapter.</span></span>

## <a name="architectural-changes"></a><span data-ttu-id="929da-275">アーキテクチャの変更</span><span class="sxs-lookup"><span data-stu-id="929da-275">Architectural changes</span></span>

<span data-ttu-id="929da-276">最後に、Blazor に移行するときに考慮すべき重要なアーキテクチャの違いがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="929da-276">Finally, there are some important architectural differences to consider when migrating to Blazor.</span></span> <span data-ttu-id="929da-277">これらの変更の多くは、.NET Core または ASP.NET Core に基づくすべてのものに適用されます。</span><span class="sxs-lookup"><span data-stu-id="929da-277">Many of these changes are applicable to anything based on .NET Core or ASP.NET Core.</span></span>

<span data-ttu-id="929da-278">Blazor は .NET Core 上に構築されているため、.NET Core でのサポートを保証する際に考慮すべき点があります。</span><span class="sxs-lookup"><span data-stu-id="929da-278">Because Blazor is built on .NET Core, there are considerations in ensuring support on .NET Core.</span></span> <span data-ttu-id="929da-279">主な変更点には、次の機能の削除が含まれます。</span><span class="sxs-lookup"><span data-stu-id="929da-279">Some of the major changes include the removal of the following features:</span></span>

- <span data-ttu-id="929da-280">複数の Appdomain</span><span class="sxs-lookup"><span data-stu-id="929da-280">Multiple AppDomains</span></span>
- <span data-ttu-id="929da-281">リモート処理</span><span class="sxs-lookup"><span data-stu-id="929da-281">Remoting</span></span>
- <span data-ttu-id="929da-282">コード アクセス セキュリティ (CAS)</span><span class="sxs-lookup"><span data-stu-id="929da-282">Code Access Security (CAS)</span></span>
- <span data-ttu-id="929da-283">セキュリティ透過性</span><span class="sxs-lookup"><span data-stu-id="929da-283">Security Transparency</span></span>

<span data-ttu-id="929da-284">.NET Core での実行をサポートするために必要な変更を識別する方法の詳細については、「 [.NET Framework から .Net core へのコードの移植](/dotnet/core/porting)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="929da-284">For more information on techniques to identify necessary changes to support running on .NET Core, see [Port your code from .NET Framework to .NET Core](/dotnet/core/porting).</span></span>

<span data-ttu-id="929da-285">ASP.NET Core は ASP.NET の再想像されたバージョンであり、初期に明らかでないと思われる変更がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="929da-285">ASP.NET Core is a reimagined version of ASP.NET and has some changes that may not initially seem obvious.</span></span> <span data-ttu-id="929da-286">主な変更点は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="929da-286">The main changes are:</span></span>

- <span data-ttu-id="929da-287">同期コンテキストがありません。つまり、`HttpContext.Current`、`Thread.CurrentPrincipal`、またはその他の静的アクセサーは存在しません。</span><span class="sxs-lookup"><span data-stu-id="929da-287">No synchronization context, which means there's no `HttpContext.Current`, `Thread.CurrentPrincipal`, or other static accessors</span></span>
- <span data-ttu-id="929da-288">シャドウコピーなし</span><span class="sxs-lookup"><span data-stu-id="929da-288">No shadow copying</span></span>
- <span data-ttu-id="929da-289">要求キューがありません</span><span class="sxs-lookup"><span data-stu-id="929da-289">No request queue</span></span>

<span data-ttu-id="929da-290">ASP.NET Core の多くの操作は非同期であるため、i/o バインドタスクを簡単にオフロードできます。</span><span class="sxs-lookup"><span data-stu-id="929da-290">Many operations in ASP.NET Core are asynchronous, which allows easier off-loading of I/O-bound tasks.</span></span> <span data-ttu-id="929da-291">`Task.Wait()` または `Task.GetResult()`を使用してブロックしないことが重要です。これにより、スレッドプールのリソースがすぐに枯渇する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="929da-291">It's important to never block by using `Task.Wait()` or `Task.GetResult()`, which can quickly exhaust thread pool resources.</span></span>

## <a name="migration-conclusion"></a><span data-ttu-id="929da-292">移行の結論</span><span class="sxs-lookup"><span data-stu-id="929da-292">Migration conclusion</span></span>

<span data-ttu-id="929da-293">この時点で、Web フォームプロジェクトを Blazor に移動するために必要ないくつかの例を見てきました。</span><span class="sxs-lookup"><span data-stu-id="929da-293">At this point, you've seen many examples of what it takes to move a Web Forms project to Blazor.</span></span> <span data-ttu-id="929da-294">完全な例については、 [eShopOnBlazor](https://github.com/dotnet-architecture/eShopOnBlazor)プロジェクトを参照してください。</span><span class="sxs-lookup"><span data-stu-id="929da-294">For a full example, see the [eShopOnBlazor](https://github.com/dotnet-architecture/eShopOnBlazor) project.</span></span>

>[!div class="step-by-step"]
>[<span data-ttu-id="929da-295">前へ</span><span class="sxs-lookup"><span data-stu-id="929da-295">Previous</span></span>](security-authentication-authorization.md)