---
title: Активация ПО промежуточного слоя с помощью контейнера сторонних разработчиков в ASP.NET Core
author: guardrex
description: В этой статье приводятся сведения о том, как использовать строго типизированное ПО промежуточного слоя с активацией на основе фабрики и контейнером сторонних разработчиков в ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2018
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: 9e4d4c6c0232ebc51ad08923e10164262b652280
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/27/2019
ms.locfileid: "64888079"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="505c9-103">Активация ПО промежуточного слоя с помощью контейнера сторонних разработчиков в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="505c9-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

<span data-ttu-id="505c9-104">Автор [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="505c9-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="505c9-105">В этой статье демонстрируется использование [IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) и [IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) в качестве точки расширяемости для активации [ПО промежуточного слоя](xref:fundamentals/middleware/index) с помощью контейнера сторонних разработчиков.</span><span class="sxs-lookup"><span data-stu-id="505c9-105">This article demonstrates how to use [IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) and [IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="505c9-106">Вводные сведения о `IMiddlewareFactory` и `IMiddleware` см. в разделе [Активация ПО промежуточного слоя на основе фабрики](xref:fundamentals/middleware/extensibility).</span><span class="sxs-lookup"><span data-stu-id="505c9-106">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see the [Factory-based middleware activation](xref:fundamentals/middleware/extensibility) topic.</span></span>

<span data-ttu-id="505c9-107">[Просмотреть или скачать образец кода](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="505c9-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="505c9-108">В примере приложения показана активация ПО промежуточного слоя путем реализации `IMiddlewareFactory`, `SimpleInjectorMiddlewareFactory`.</span><span class="sxs-lookup"><span data-stu-id="505c9-108">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="505c9-109">В этом образце используется контейнер внедрения зависимостей [Simple Injector](https://simpleinjector.org).</span><span class="sxs-lookup"><span data-stu-id="505c9-109">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="505c9-110">Реализация ПО промежуточного слоя в примере записывает значение, предоставленное в параметре строки запроса (`key`).</span><span class="sxs-lookup"><span data-stu-id="505c9-110">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="505c9-111">ПО промежуточного слоя использует внедренный контекст базы данных (служба с заданной областью) для записи значения строки запроса в базу данных, выполняющуюся в памяти.</span><span class="sxs-lookup"><span data-stu-id="505c9-111">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="505c9-112">В примере приложения [Simple Injector](https://github.com/simpleinjector/SimpleInjector) используется исключительно для демонстрации.</span><span class="sxs-lookup"><span data-stu-id="505c9-112">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="505c9-113">Использование Simple Injector не означает поддержку данного инструмента.</span><span class="sxs-lookup"><span data-stu-id="505c9-113">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="505c9-114">Подходы к активации ПО промежуточного слоя, описанные в документации по Simple Injector и статьях на GitHub, рекомендованы командой Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="505c9-114">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="505c9-115">Дополнительные сведения см. в [документации по Simple Injector](https://simpleinjector.readthedocs.io/en/latest/index.html) и [в репозитории Simple Injector в GitHub](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="505c9-115">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="505c9-116">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="505c9-116">IMiddlewareFactory</span></span>

<span data-ttu-id="505c9-117">[IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) предоставляет методы для создания ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="505c9-117">[IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) provides methods to create middleware.</span></span>

<span data-ttu-id="505c9-118">В примере приложения реализуется фабрика ПО промежуточного слоя для создания экземпляра `SimpleInjectorActivatedMiddleware`.</span><span class="sxs-lookup"><span data-stu-id="505c9-118">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="505c9-119">Фабрика ПО промежуточного слоя использует контейнер Simple Injector для разрешения по промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="505c9-119">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/sample/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="505c9-120">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="505c9-120">IMiddleware</span></span>

<span data-ttu-id="505c9-121">[IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) определяет ПО промежуточного слоя для конвейера запросов приложения.</span><span class="sxs-lookup"><span data-stu-id="505c9-121">[IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="505c9-122">ПО промежуточного слоя, активированное реализацией `IMiddlewareFactory` (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span><span class="sxs-lookup"><span data-stu-id="505c9-122">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/sample/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="505c9-123">Для ПО промежуточного слоя создается расширение (*Middleware/MiddlewareExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="505c9-123">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/sample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="505c9-124">`Startup.ConfigureServices` должен выполнять несколько задач:</span><span class="sxs-lookup"><span data-stu-id="505c9-124">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="505c9-125">Настройка контейнера Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="505c9-125">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="505c9-126">Регистрация фабрики и ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="505c9-126">Register the factory and middleware.</span></span>
* <span data-ttu-id="505c9-127">Предоставление контекста базы данных приложения из контейнера Simple Injector для страницы Razor.</span><span class="sxs-lookup"><span data-stu-id="505c9-127">Make the app's database context available from the Simple Injector container for a Razor Page.</span></span>

[!code-csharp[](extensibility-third-party-container/sample/Startup.cs?name=snippet1)]

<span data-ttu-id="505c9-128">ПО промежуточного слоя регистрируется в конвейере обработки запросов в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="505c9-128">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/sample/Startup.cs?name=snippet2&highlight=13)]

## <a name="additional-resources"></a><span data-ttu-id="505c9-129">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="505c9-129">Additional resources</span></span>

* [<span data-ttu-id="505c9-130">ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="505c9-130">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="505c9-131">Активация фабричного ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="505c9-131">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="505c9-132">Репозиторий Simple Injector в GitHub</span><span class="sxs-lookup"><span data-stu-id="505c9-132">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="505c9-133">Документация по Simple Injector</span><span class="sxs-lookup"><span data-stu-id="505c9-133">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)
