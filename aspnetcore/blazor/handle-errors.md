---
title: Обработку ошибок в приложениях ASP.NET Core Blazor
author: guardrex
description: Узнайте, как ASP.NET Core Blazor, как Blazor управляет необработанными исключениями и как разрабатывать приложения, которые обнаруживают и обрабатывают ошибки.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: 7191ae50d64ebd6a9b23b391116aedf3a6d01de2
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447026"
---
# <a name="handle-errors-in-aspnet-core-opno-locblazor-apps"></a><span data-ttu-id="e44c5-103">Обработку ошибок в приложениях ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="e44c5-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="e44c5-104">Автор: [Стив Сандерсон](https://github.com/SteveSandersonMS) (Steve Sanderson)</span><span class="sxs-lookup"><span data-stu-id="e44c5-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="e44c5-105">В этой статье описывается, как Blazor управляет необработанными исключениями и разрабатывает приложения, которые обнаруживают и обрабатывают ошибки.</span><span class="sxs-lookup"><span data-stu-id="e44c5-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="e44c5-106">Подробные сведения об ошибках во время разработки</span><span class="sxs-lookup"><span data-stu-id="e44c5-106">Detailed errors during development</span></span>

<span data-ttu-id="e44c5-107">Если во время разработки приложение Blazor работает неправильно, подробные сведения об ошибках в приложении могут помочь в устранении неполадок.</span><span class="sxs-lookup"><span data-stu-id="e44c5-107">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="e44c5-108">При возникновении ошибки в приложении Blazor в нижней части экрана отображается золотистая полоска.</span><span class="sxs-lookup"><span data-stu-id="e44c5-108">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="e44c5-109">Во время разработки из этой полоски можно перейти в консоль браузера, где можно просмотреть исключение.</span><span class="sxs-lookup"><span data-stu-id="e44c5-109">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="e44c5-110">В рабочей среде эта полоска уведомляет пользователя о том, что произошла ошибка, и рекомендует обновить содержимое окна браузера.</span><span class="sxs-lookup"><span data-stu-id="e44c5-110">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="e44c5-111">Пользовательский интерфейс для этого процесса обработки ошибок входит в состав шаблонов проектов Blazor.</span><span class="sxs-lookup"><span data-stu-id="e44c5-111">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="e44c5-112">В Blazor приложении-сборке настройте интерфейс в файле *wwwroot/index.HTML* :</span><span class="sxs-lookup"><span data-stu-id="e44c5-112">In a Blazor WebAssembly app, customize the experience in the *wwwroot/index.html* file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="e44c5-113">В приложении Blazor Server настройте интерфейс в файле *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="e44c5-113">In a Blazor Server app, customize the experience in the *Pages/_Host.cshtml* file:</span></span>

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="e44c5-114">Элемент `blazor-error-ui` скрыт стилями, включенными в шаблоны Blazor, а затем отображается при возникновении ошибки.</span><span class="sxs-lookup"><span data-stu-id="e44c5-114">The `blazor-error-ui` element is hidden by the styles included with the Blazor templates and then shown when an error occurs.</span></span>

## <a name="how-a-opno-locblazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="e44c5-115">Как Blazor серверное приложение реагирует на необработанные исключения</span><span class="sxs-lookup"><span data-stu-id="e44c5-115">How a Blazor Server app reacts to unhandled exceptions</span></span>

Blazor<span data-ttu-id="e44c5-116"> Server — это платформа с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="e44c5-116"> Server is a stateful framework.</span></span> <span data-ttu-id="e44c5-117">Хотя пользователи взаимодействуют с приложением, они сохраняют подключение к серверу, известному как *канал*.</span><span class="sxs-lookup"><span data-stu-id="e44c5-117">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="e44c5-118">Канал содержит экземпляры активных компонентов, а также многие другие аспекты состояния, например:</span><span class="sxs-lookup"><span data-stu-id="e44c5-118">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="e44c5-119">Последние отображаемые выходные данные компонентов.</span><span class="sxs-lookup"><span data-stu-id="e44c5-119">The most recent rendered output of components.</span></span>
* <span data-ttu-id="e44c5-120">Текущий набор делегатов обработки событий, которые могут вызываться событиями на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="e44c5-120">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="e44c5-121">Если пользователь открывает приложение на нескольких вкладках браузера, у него есть несколько независимых цепей.</span><span class="sxs-lookup"><span data-stu-id="e44c5-121">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

Blazor<span data-ttu-id="e44c5-122"> обрабатывает большинство необработанных исключений как неустранимые для канала, где они происходят.</span><span class="sxs-lookup"><span data-stu-id="e44c5-122"> treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="e44c5-123">Если канал завершается из-за необработанного исключения, пользователь может продолжить взаимодействие с приложением только путем перезагрузки страницы для создания нового канала.</span><span class="sxs-lookup"><span data-stu-id="e44c5-123">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="e44c5-124">Цепи за пределами того, что прерывается, являются цепями для других пользователей или других вкладок браузера, не затрагиваются.</span><span class="sxs-lookup"><span data-stu-id="e44c5-124">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="e44c5-125">Этот сценарий аналогичен классу для настольных приложений, который аварийно завершает работу&mdash;сбой приложения должен быть перезапущен, но другие приложения не затрагиваются.</span><span class="sxs-lookup"><span data-stu-id="e44c5-125">This scenario is similar to a desktop app that crashes&mdash;the crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="e44c5-126">Цепь завершается, когда необработанное исключение возникает по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="e44c5-126">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="e44c5-127">Необработанное исключение часто оставляет цепь в неопределенном состоянии.</span><span class="sxs-lookup"><span data-stu-id="e44c5-127">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="e44c5-128">Нормальная работа приложения не может быть гарантирована после необработанного исключения.</span><span class="sxs-lookup"><span data-stu-id="e44c5-128">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="e44c5-129">В случае продолжения канала в приложении могут появиться уязвимости системы безопасности.</span><span class="sxs-lookup"><span data-stu-id="e44c5-129">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="e44c5-130">Управление необработанными исключениями в коде разработчика</span><span class="sxs-lookup"><span data-stu-id="e44c5-130">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="e44c5-131">Чтобы продолжить работу приложения после возникновения ошибки, приложение должно иметь логику обработки ошибок.</span><span class="sxs-lookup"><span data-stu-id="e44c5-131">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="e44c5-132">В последующих разделах этой статьи описываются потенциальные источники необработанных исключений.</span><span class="sxs-lookup"><span data-stu-id="e44c5-132">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="e44c5-133">В рабочей среде не следует отображать сообщения об исключениях инфраструктуры или трассировки стека в пользовательском интерфейсе.</span><span class="sxs-lookup"><span data-stu-id="e44c5-133">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="e44c5-134">Отрисовка сообщений об исключениях или трассировок стека может:</span><span class="sxs-lookup"><span data-stu-id="e44c5-134">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="e44c5-135">Раскрывать конфиденциальные сведения конечным пользователям.</span><span class="sxs-lookup"><span data-stu-id="e44c5-135">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="e44c5-136">Помогите злонамеренному пользователю обнаружить слабые места в приложении, которое может нарушить безопасность приложения, сервера или сети.</span><span class="sxs-lookup"><span data-stu-id="e44c5-136">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="e44c5-137">Регистрация ошибок с помощью постоянного поставщика</span><span class="sxs-lookup"><span data-stu-id="e44c5-137">Log errors with a persistent provider</span></span>

<span data-ttu-id="e44c5-138">При возникновении необработанного исключения исключение заносится в журнал <xref:Microsoft.Extensions.Logging.ILogger> экземпляров, настроенных в контейнере службы.</span><span class="sxs-lookup"><span data-stu-id="e44c5-138">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="e44c5-139">По умолчанию Blazor приложения записывается в выходные данные консоли с помощью поставщика ведения журнала консоли.</span><span class="sxs-lookup"><span data-stu-id="e44c5-139">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="e44c5-140">Рассмотрите возможность входа в более постоянное расположение с поставщиком, который управляет размером журнала и поворотом журнала.</span><span class="sxs-lookup"><span data-stu-id="e44c5-140">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="e44c5-141">Дополнительные сведения см. в разделе <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="e44c5-141">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="e44c5-142">Во время разработки Blazor обычно отправляет полные сведения об исключениях в консоль браузера для облегчения отладки.</span><span class="sxs-lookup"><span data-stu-id="e44c5-142">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="e44c5-143">В рабочей среде подробные ошибки в консоли браузера по умолчанию отключены, что означает, что ошибки не отправляются клиентам, но все сведения об исключении все еще регистрируются на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="e44c5-143">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="e44c5-144">Дополнительные сведения см. в разделе <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="e44c5-144">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="e44c5-145">Необходимо решить, какие инциденты следует заносить в журнал, и уровень серьезности регистрируемых инцидентов.</span><span class="sxs-lookup"><span data-stu-id="e44c5-145">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="e44c5-146">Злонамеренные пользователи могут попытаться вызывать ошибки намеренно.</span><span class="sxs-lookup"><span data-stu-id="e44c5-146">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="e44c5-147">Например, не заносить инцидент в журнал из ошибки, если в URL-адресе компонента, отображающего сведения о продукте, указан неизвестный `ProductId`.</span><span class="sxs-lookup"><span data-stu-id="e44c5-147">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="e44c5-148">Не все ошибки следует рассматривать как инциденты высокой серьезности для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="e44c5-148">Not all errors should be treated as high-severity incidents for logging.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="e44c5-149">Места, где могут возникнуть ошибки</span><span class="sxs-lookup"><span data-stu-id="e44c5-149">Places where errors may occur</span></span>

<span data-ttu-id="e44c5-150">Код платформы и приложения может вызывать необработанные исключения в любом из следующих расположений:</span><span class="sxs-lookup"><span data-stu-id="e44c5-150">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="e44c5-151">Создание экземпляра компонента</span><span class="sxs-lookup"><span data-stu-id="e44c5-151">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="e44c5-152">Методы жизненного цикла</span><span class="sxs-lookup"><span data-stu-id="e44c5-152">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="e44c5-153">Логика отрисовки</span><span class="sxs-lookup"><span data-stu-id="e44c5-153">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="e44c5-154">Обработчики событий</span><span class="sxs-lookup"><span data-stu-id="e44c5-154">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="e44c5-155">Реализация компонента</span><span class="sxs-lookup"><span data-stu-id="e44c5-155">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="e44c5-156">Взаимодействие JavaScript</span><span class="sxs-lookup"><span data-stu-id="e44c5-156">JavaScript interop</span></span>](#javascript-interop)
* <span data-ttu-id="e44c5-157">[Blazor обработчиков канала сервера](#blazor-server-circuit-handlers)</span><span class="sxs-lookup"><span data-stu-id="e44c5-157">[Blazor Server circuit handlers](#blazor-server-circuit-handlers)</span></span>
* <span data-ttu-id="e44c5-158">[Blazorная реализация канала сервера](#blazor-server-circuit-disposal)</span><span class="sxs-lookup"><span data-stu-id="e44c5-158">[Blazor Server circuit disposal](#blazor-server-circuit-disposal)</span></span>
* <span data-ttu-id="e44c5-159">[Blazorная визуализация сервера](#blazor-server-prerendering)</span><span class="sxs-lookup"><span data-stu-id="e44c5-159">[Blazor Server rerendering](#blazor-server-prerendering)</span></span>

<span data-ttu-id="e44c5-160">Предыдущие необработанные исключения описаны в следующих разделах этой статьи.</span><span class="sxs-lookup"><span data-stu-id="e44c5-160">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="e44c5-161">Создание экземпляра компонента</span><span class="sxs-lookup"><span data-stu-id="e44c5-161">Component instantiation</span></span>

<span data-ttu-id="e44c5-162">Когда Blazor создает экземпляр компонента:</span><span class="sxs-lookup"><span data-stu-id="e44c5-162">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="e44c5-163">Вызывается конструктор компонента.</span><span class="sxs-lookup"><span data-stu-id="e44c5-163">The component's constructor is invoked.</span></span>
* <span data-ttu-id="e44c5-164">Вызываются конструкторы любых служб DI, не являющихся Singleton-классами, предоставляемых конструктору компонента через директиву [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) или атрибут [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) .</span><span class="sxs-lookup"><span data-stu-id="e44c5-164">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) directive or the [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span>

<span data-ttu-id="e44c5-165">Сбой канала Blazor сервера, если любой выполненный конструктор или метод задания для любого свойства `[Inject]` выдает необработанное исключение.</span><span class="sxs-lookup"><span data-stu-id="e44c5-165">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="e44c5-166">Исключение является неустранимым, так как платформе не удается создать экземпляр компонента.</span><span class="sxs-lookup"><span data-stu-id="e44c5-166">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="e44c5-167">Если логика конструктора может вызывать исключения, приложение должно выполнить перехват исключений с помощью оператора [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) с обработкой ошибок и ведением журнала.</span><span class="sxs-lookup"><span data-stu-id="e44c5-167">If constructor logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="e44c5-168">Методы жизненного цикла</span><span class="sxs-lookup"><span data-stu-id="e44c5-168">Lifecycle methods</span></span>

<span data-ttu-id="e44c5-169">В течение времени существования компонента Blazor вызывает следующие [методы жизненного цикла](xref:blazor/lifecycle):</span><span class="sxs-lookup"><span data-stu-id="e44c5-169">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/lifecycle):</span></span>

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

<span data-ttu-id="e44c5-170">Если какой-либо метод жизненного цикла вызывает исключение синхронно или асинхронно, исключение является неустранимым для Blazorной цепи сервера.</span><span class="sxs-lookup"><span data-stu-id="e44c5-170">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="e44c5-171">Для компонентов, обрабатывающих ошибки в методах жизненного цикла, добавьте логику обработки ошибок.</span><span class="sxs-lookup"><span data-stu-id="e44c5-171">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="e44c5-172">В следующем примере `OnParametersSetAsync` вызывает метод для получения продукта:</span><span class="sxs-lookup"><span data-stu-id="e44c5-172">In the following example where `OnParametersSetAsync` calls a method to obtain a product:</span></span>

* <span data-ttu-id="e44c5-173">Исключение, вызванное в методе `ProductRepository.GetProductByIdAsync`, обрабатывается оператором `try-catch`.</span><span class="sxs-lookup"><span data-stu-id="e44c5-173">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a `try-catch` statement.</span></span>
* <span data-ttu-id="e44c5-174">При выполнении блока `catch`:</span><span class="sxs-lookup"><span data-stu-id="e44c5-174">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="e44c5-175">`loadFailed` имеет значение `true`, которое используется для вывода сообщения об ошибке пользователю.</span><span class="sxs-lookup"><span data-stu-id="e44c5-175">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="e44c5-176">Ошибка заносится в журнал.</span><span class="sxs-lookup"><span data-stu-id="e44c5-176">The error is logged.</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="e44c5-177">Логика отрисовки</span><span class="sxs-lookup"><span data-stu-id="e44c5-177">Rendering logic</span></span>

<span data-ttu-id="e44c5-178">Декларативная разметка в файле компонента `.razor` компилируется в метод C# с именем `BuildRenderTree`.</span><span class="sxs-lookup"><span data-stu-id="e44c5-178">The declarative markup in a `.razor` component file is compiled into a C# method called `BuildRenderTree`.</span></span> <span data-ttu-id="e44c5-179">Когда компонент подготавливается к просмотру, `BuildRenderTree` выполняет и создает структуру данных, описывающую элементы, текст и дочерние компоненты отображаемого компонента.</span><span class="sxs-lookup"><span data-stu-id="e44c5-179">When a component renders, `BuildRenderTree` executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="e44c5-180">Логика отрисовки может вызывать исключение.</span><span class="sxs-lookup"><span data-stu-id="e44c5-180">Rendering logic can throw an exception.</span></span> <span data-ttu-id="e44c5-181">Пример этого сценария происходит, когда `@someObject.PropertyName` вычисляется, но `@someObject` `null`.</span><span class="sxs-lookup"><span data-stu-id="e44c5-181">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="e44c5-182">Необработанное исключение, созданное логикой отрисовки, является неустранимым для Blazorной цепи сервера.</span><span class="sxs-lookup"><span data-stu-id="e44c5-182">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="e44c5-183">Чтобы предотвратить исключение пустой ссылки в логике отрисовки, проверьте объект `null` перед доступом к его членам.</span><span class="sxs-lookup"><span data-stu-id="e44c5-183">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="e44c5-184">В следующем примере свойства `person.Address` недоступны, если `person.Address` `null`:</span><span class="sxs-lookup"><span data-stu-id="e44c5-184">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="e44c5-185">В приведенном выше коде предполагается, что `person` не `null`.</span><span class="sxs-lookup"><span data-stu-id="e44c5-185">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="e44c5-186">Часто структура кода гарантирует, что объект существует на момент подготовки компонента к просмотру.</span><span class="sxs-lookup"><span data-stu-id="e44c5-186">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="e44c5-187">В таких случаях нет необходимости проверять наличие `null` в логике отрисовки.</span><span class="sxs-lookup"><span data-stu-id="e44c5-187">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="e44c5-188">В предыдущем примере `person` может быть гарантированно существовать, поскольку `person` создается при создании экземпляра компонента.</span><span class="sxs-lookup"><span data-stu-id="e44c5-188">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="e44c5-189">Обработчики событий</span><span class="sxs-lookup"><span data-stu-id="e44c5-189">Event handlers</span></span>

<span data-ttu-id="e44c5-190">Код на стороне клиента активирует вызовы C# кода при создании обработчиков событий с помощью:</span><span class="sxs-lookup"><span data-stu-id="e44c5-190">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="e44c5-191">Другие атрибуты `@on...`</span><span class="sxs-lookup"><span data-stu-id="e44c5-191">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="e44c5-192">В этих сценариях код обработчика событий может вызывать необработанное исключение.</span><span class="sxs-lookup"><span data-stu-id="e44c5-192">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="e44c5-193">Если обработчик событий создает необработанное исключение (например, запрос к базе данных завершается ошибкой), исключение является неустранимым для Blazorной цепи сервера.</span><span class="sxs-lookup"><span data-stu-id="e44c5-193">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="e44c5-194">Если приложение вызывает код, который может завершиться ошибкой по внешним причинам, необходимо выполнить перехват исключений с помощью оператора [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) с обработкой ошибок и ведением журнала.</span><span class="sxs-lookup"><span data-stu-id="e44c5-194">If the app calls code that could fail for external reasons, trap exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="e44c5-195">Если пользовательский код не выполняет перехват и обработку исключения, платформа регистрирует исключение и завершает цепь.</span><span class="sxs-lookup"><span data-stu-id="e44c5-195">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="e44c5-196">Реализация компонента</span><span class="sxs-lookup"><span data-stu-id="e44c5-196">Component disposal</span></span>

<span data-ttu-id="e44c5-197">Компонент может быть удален из пользовательского интерфейса, например, потому, что пользователь перешел на другую страницу.</span><span class="sxs-lookup"><span data-stu-id="e44c5-197">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="e44c5-198">Когда компонент, реализующий <xref:System.IDisposable?displayProperty=fullName>, удаляется из пользовательского интерфейса, платформа вызывает метод <xref:System.IDisposable.Dispose*> компонента.</span><span class="sxs-lookup"><span data-stu-id="e44c5-198">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose*> method.</span></span>

<span data-ttu-id="e44c5-199">Если метод `Dispose` компонента создает необработанное исключение, исключение является неустранимым для канала Blazor сервера.</span><span class="sxs-lookup"><span data-stu-id="e44c5-199">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="e44c5-200">Если логика реализации может вызывать исключения, приложение должно выполнить перехват исключений с помощью оператора [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) с обработкой ошибок и ведением журнала.</span><span class="sxs-lookup"><span data-stu-id="e44c5-200">If disposal logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="e44c5-201">Дополнительные сведения об утилизации компонентов см. в разделе <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="e44c5-201">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="e44c5-202">Взаимодействие с JavaScript</span><span class="sxs-lookup"><span data-stu-id="e44c5-202">JavaScript interop</span></span>

<span data-ttu-id="e44c5-203">`IJSRuntime.InvokeAsync<T>` позволяет коду .NET выполнять асинхронные вызовы среды выполнения JavaScript в браузере пользователя.</span><span class="sxs-lookup"><span data-stu-id="e44c5-203">`IJSRuntime.InvokeAsync<T>` allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="e44c5-204">При обработке ошибок с помощью `InvokeAsync<T>`применяются следующие условия.</span><span class="sxs-lookup"><span data-stu-id="e44c5-204">The following conditions apply to error handling with `InvokeAsync<T>`:</span></span>

* <span data-ttu-id="e44c5-205">Если вызов `InvokeAsync<T>` выполняется синхронно, возникает исключение .NET.</span><span class="sxs-lookup"><span data-stu-id="e44c5-205">If a call to `InvokeAsync<T>` fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="e44c5-206">Вызов `InvokeAsync<T>` может завершиться ошибкой, например, поскольку передаваемые аргументы не могут быть сериализованы.</span><span class="sxs-lookup"><span data-stu-id="e44c5-206">A call to `InvokeAsync<T>` may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="e44c5-207">Код разработчика должен перехватить исключение.</span><span class="sxs-lookup"><span data-stu-id="e44c5-207">Developer code must catch the exception.</span></span> <span data-ttu-id="e44c5-208">Если код приложения в обработчике событий или в методе жизненного цикла компонента не обрабатывает исключение, то полученное исключение является неустранимым для Blazorной цепи сервера.</span><span class="sxs-lookup"><span data-stu-id="e44c5-208">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="e44c5-209">Если вызов `InvokeAsync<T>` асинхронно завершается сбоем, <xref:System.Threading.Tasks.Task> .NET завершается ошибкой.</span><span class="sxs-lookup"><span data-stu-id="e44c5-209">If a call to `InvokeAsync<T>` fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="e44c5-210">Вызов `InvokeAsync<T>` может завершиться ошибкой, например, так как код на стороне JavaScript вызывает исключение или возвращает `Promise`, который был завершен как `rejected`.</span><span class="sxs-lookup"><span data-stu-id="e44c5-210">A call to `InvokeAsync<T>` may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="e44c5-211">Код разработчика должен перехватить исключение.</span><span class="sxs-lookup"><span data-stu-id="e44c5-211">Developer code must catch the exception.</span></span> <span data-ttu-id="e44c5-212">При использовании оператора [await](/dotnet/csharp/language-reference/keywords/await) рекомендуется обернуть вызов метода в инструкцию [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) с обработкой ошибок и ведением журнала.</span><span class="sxs-lookup"><span data-stu-id="e44c5-212">If using the [await](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="e44c5-213">В противном случае сбой кода приводит к возникновению необработанного исключения, которое является неустранимым для канала Blazor сервера.</span><span class="sxs-lookup"><span data-stu-id="e44c5-213">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="e44c5-214">По умолчанию вызовы `InvokeAsync<T>` должны завершаться в течение определенной точки или в другом случае истекает время ожидания вызова. По умолчанию используется период ожидания в одну минуту.</span><span class="sxs-lookup"><span data-stu-id="e44c5-214">By default, calls to `InvokeAsync<T>` must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="e44c5-215">Время ожидания защищает код от потери сетевого подключения или кода JavaScript, который никогда не отправляет обратно сообщение о завершении.</span><span class="sxs-lookup"><span data-stu-id="e44c5-215">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="e44c5-216">Если время ожидания вызова истекает, полученный `Task` завершается с <xref:System.OperationCanceledException>.</span><span class="sxs-lookup"><span data-stu-id="e44c5-216">If the call times out, the resulting `Task` fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="e44c5-217">Ловушка и обработка исключения с ведением журнала.</span><span class="sxs-lookup"><span data-stu-id="e44c5-217">Trap and process the exception with logging.</span></span>

<span data-ttu-id="e44c5-218">Аналогичным образом код JavaScript может инициировать вызовы методов .NET, указанных атрибутом [`[JSInvokable]`](xref:blazor/javascript-interop#invoke-net-methods-from-javascript-functions) .</span><span class="sxs-lookup"><span data-stu-id="e44c5-218">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]`](xref:blazor/javascript-interop#invoke-net-methods-from-javascript-functions) attribute.</span></span> <span data-ttu-id="e44c5-219">Если эти методы .NET создают необработанное исключение:</span><span class="sxs-lookup"><span data-stu-id="e44c5-219">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="e44c5-220">Исключение не рассматривается как неустранимое для канала Blazor сервера.</span><span class="sxs-lookup"><span data-stu-id="e44c5-220">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="e44c5-221">`Promise` на стороне JavaScript отклоняется.</span><span class="sxs-lookup"><span data-stu-id="e44c5-221">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="e44c5-222">Вы можете использовать код обработки ошибок либо на стороне .NET, либо на стороне JavaScript вызова метода.</span><span class="sxs-lookup"><span data-stu-id="e44c5-222">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="e44c5-223">Дополнительные сведения см. в разделе <xref:blazor/javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="e44c5-223">For more information, see <xref:blazor/javascript-interop>.</span></span>

### <a name="opno-locblazor-server-circuit-handlers"></a>Blazor<span data-ttu-id="e44c5-224"> обработчиков канала сервера</span><span class="sxs-lookup"><span data-stu-id="e44c5-224"> Server circuit handlers</span></span>

Blazor<span data-ttu-id="e44c5-225"> Server позволяет коду определять *обработчик цепи*, который позволяет выполнять код на изменениях в состоянии пользовательского канала.</span><span class="sxs-lookup"><span data-stu-id="e44c5-225"> Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="e44c5-226">Обработчик цепи реализуется путем наследования от `CircuitHandler` и регистрации класса в контейнере службы приложения.</span><span class="sxs-lookup"><span data-stu-id="e44c5-226">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="e44c5-227">В следующем примере обработчика цепи отслеживает открытые SignalR подключения:</span><span class="sxs-lookup"><span data-stu-id="e44c5-227">The following example of a circuit handler tracks open SignalR connections:</span></span>

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> _circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => _circuits.Count;
}
```

<span data-ttu-id="e44c5-228">Обработчики цепи регистрируются с помощью DI.</span><span class="sxs-lookup"><span data-stu-id="e44c5-228">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="e44c5-229">Экземпляры с заданной областью создаются для каждого экземпляра канала.</span><span class="sxs-lookup"><span data-stu-id="e44c5-229">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="e44c5-230">С помощью `TrackingCircuitHandler` в предыдущем примере создается одноэлементная служба, поскольку необходимо относиться к состоянию всех цепей:</span><span class="sxs-lookup"><span data-stu-id="e44c5-230">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="e44c5-231">Если методы пользовательского обработчика цепи создают необработанное исключение, это исключение является неустранимым для Blazorного канала сервера.</span><span class="sxs-lookup"><span data-stu-id="e44c5-231">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the Blazor Server circuit.</span></span> <span data-ttu-id="e44c5-232">Чтобы допускать исключения в коде обработчика или вызывать методы, заключите код в один или несколько инструкций [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) с обработкой ошибок и ведением журнала.</span><span class="sxs-lookup"><span data-stu-id="e44c5-232">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

### <a name="opno-locblazor-server-circuit-disposal"></a>Blazor<span data-ttu-id="e44c5-233">ная реализация канала сервера</span><span class="sxs-lookup"><span data-stu-id="e44c5-233"> Server circuit disposal</span></span>

<span data-ttu-id="e44c5-234">Когда цепь завершается из-за того, что пользователь отключился и платформа очищает состояние канала, платформа удаляет область DI цепи.</span><span class="sxs-lookup"><span data-stu-id="e44c5-234">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="e44c5-235">Удаление области удаляет все службы DI с областью действия цепи, реализующие <xref:System.IDisposable?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="e44c5-235">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="e44c5-236">Если любая служба DI вызывает необработанное исключение во время удаления, платформа регистрирует исключение.</span><span class="sxs-lookup"><span data-stu-id="e44c5-236">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

### <a name="opno-locblazor-server-prerendering"></a><span data-ttu-id="e44c5-237">Предварительная подготовка сервера Blazor</span><span class="sxs-lookup"><span data-stu-id="e44c5-237">Blazor Server prerendering</span></span>

Blazor<span data-ttu-id="e44c5-238"> компоненты можно предварительно отобразить с помощью вспомогательной функции тега `Component`, чтобы их отображаемая разметка HTML возвращалась как часть исходного HTTP-запроса пользователя.</span><span class="sxs-lookup"><span data-stu-id="e44c5-238"> components can be prerendered using the `Component` Tag Helper so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="e44c5-239">Это работает следующим образом.</span><span class="sxs-lookup"><span data-stu-id="e44c5-239">This works by:</span></span>

* <span data-ttu-id="e44c5-240">Создание нового канала для всех предварительно отображенных компонентов, которые являются частью одной страницы.</span><span class="sxs-lookup"><span data-stu-id="e44c5-240">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="e44c5-241">Создание исходного HTML-кода.</span><span class="sxs-lookup"><span data-stu-id="e44c5-241">Generating the initial HTML.</span></span>
* <span data-ttu-id="e44c5-242">Рассматривая канал как `disconnected` до тех пор, пока браузер пользователя не установит SignalR подключение к тому же серверу.</span><span class="sxs-lookup"><span data-stu-id="e44c5-242">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="e44c5-243">Если соединение установлено, взаимодействие с каналом возобновляется, а HTML-разметка компонентов обновляется.</span><span class="sxs-lookup"><span data-stu-id="e44c5-243">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="e44c5-244">Если какой-либо компонент создает необработанное исключение во время предварительной отрисовки, например во время метода жизненного цикла или в логике отрисовки:</span><span class="sxs-lookup"><span data-stu-id="e44c5-244">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="e44c5-245">Исключение является неустранимым для канала.</span><span class="sxs-lookup"><span data-stu-id="e44c5-245">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="e44c5-246">Исключение вызывается из стека вызовов из вспомогательной функции тега `Component`.</span><span class="sxs-lookup"><span data-stu-id="e44c5-246">The exception is thrown up the call stack from the `Component` Tag Helper.</span></span> <span data-ttu-id="e44c5-247">Таким образом, весь HTTP-запрос завершается ошибкой, если только исключение явно не перехвачено кодом разработчика.</span><span class="sxs-lookup"><span data-stu-id="e44c5-247">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="e44c5-248">В нормальных обстоятельствах при сбое предварительной визуализации продолжение сборки и визуализации компонента не имеет смысла, так как не удается подготовить рабочий компонент к просмотру.</span><span class="sxs-lookup"><span data-stu-id="e44c5-248">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="e44c5-249">Чтобы допускать ошибки, которые могут возникнуть во время предварительной подготовки, логика обработки ошибок должна размещаться внутри компонента, который может вызывать исключения.</span><span class="sxs-lookup"><span data-stu-id="e44c5-249">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="e44c5-250">Используйте операторы [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) с обработкой ошибок и ведением журнала.</span><span class="sxs-lookup"><span data-stu-id="e44c5-250">Use [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="e44c5-251">Вместо того чтобы упаковывать вспомогательную функцию тега `Component` в инструкцию `try-catch`, поместите логику обработки ошибок в компонент, отображаемый вспомогательной функцией тега `Component`.</span><span class="sxs-lookup"><span data-stu-id="e44c5-251">Instead of wrapping the `Component` Tag Helper in a `try-catch` statement, place error handling logic in the component rendered by the `Component` Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="e44c5-252">Сложные сценарии</span><span class="sxs-lookup"><span data-stu-id="e44c5-252">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="e44c5-253">Рекурсивная визуализация</span><span class="sxs-lookup"><span data-stu-id="e44c5-253">Recursive rendering</span></span>

<span data-ttu-id="e44c5-254">Компоненты могут быть вложенными рекурсивно.</span><span class="sxs-lookup"><span data-stu-id="e44c5-254">Components can be nested recursively.</span></span> <span data-ttu-id="e44c5-255">Это полезно для представления рекурсивных структур данных.</span><span class="sxs-lookup"><span data-stu-id="e44c5-255">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="e44c5-256">Например, компонент `TreeNode` может отображать больше `TreeNode` компонентов для каждого из дочерних узлов узла.</span><span class="sxs-lookup"><span data-stu-id="e44c5-256">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="e44c5-257">При рекурсивной отрисовке Избегайте шаблонов программирования, которые приводят к бесконечной рекурсии:</span><span class="sxs-lookup"><span data-stu-id="e44c5-257">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="e44c5-258">Не следует рекурсивно подготавливать структуру данных, содержащую цикл.</span><span class="sxs-lookup"><span data-stu-id="e44c5-258">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="e44c5-259">Например, не следует визуализировать узел дерева, дочерние элементы которого содержат сам себя.</span><span class="sxs-lookup"><span data-stu-id="e44c5-259">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="e44c5-260">Не создавайте цепочку макетов, содержащих цикл.</span><span class="sxs-lookup"><span data-stu-id="e44c5-260">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="e44c5-261">Например, не создавайте макет, макет которого сам является.</span><span class="sxs-lookup"><span data-stu-id="e44c5-261">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="e44c5-262">Не разрешайте пользователю нарушать инварианты рекурсии (правила) с помощью ввода вредоносных данных или вызовов взаимодействия JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e44c5-262">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="e44c5-263">Бесконечные циклы во время подготовки к просмотру:</span><span class="sxs-lookup"><span data-stu-id="e44c5-263">Infinite loops during rendering:</span></span>

* <span data-ttu-id="e44c5-264">Приводит к тому, что процесс подготовки к просмотру будет выполняться бессрочно.</span><span class="sxs-lookup"><span data-stu-id="e44c5-264">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="e44c5-265">Эквивалентно созданию незавершенного цикла.</span><span class="sxs-lookup"><span data-stu-id="e44c5-265">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="e44c5-266">В этих сценариях происходит сбой Blazorной цепи сервера, и поток обычно пытается выполнить следующие действия.</span><span class="sxs-lookup"><span data-stu-id="e44c5-266">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="e44c5-267">Потребляет столько процессорных ресурсов, сколько разрешено операционной системой, неопределенное время.</span><span class="sxs-lookup"><span data-stu-id="e44c5-267">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="e44c5-268">Использование неограниченного объема памяти сервера.</span><span class="sxs-lookup"><span data-stu-id="e44c5-268">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="e44c5-269">Использование неограниченного объема памяти эквивалентно сценарию, в котором незавершенный цикл добавляет записи в коллекцию при каждой итерации.</span><span class="sxs-lookup"><span data-stu-id="e44c5-269">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="e44c5-270">Чтобы избежать бесконечного шаблона рекурсии, убедитесь, что рекурсивный код отрисовки содержит подходящие условия остановки.</span><span class="sxs-lookup"><span data-stu-id="e44c5-270">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="e44c5-271">Логика пользовательского дерева отрисовки</span><span class="sxs-lookup"><span data-stu-id="e44c5-271">Custom render tree logic</span></span>

<span data-ttu-id="e44c5-272">Большинство Blazor компонентов реализуются как файлы *Razor* и компилируются для создания логики, работающей в `RenderTreeBuilder` для отрисовки выходных данных.</span><span class="sxs-lookup"><span data-stu-id="e44c5-272">Most Blazor components are implemented as *.razor* files and are compiled to produce logic that operates on a `RenderTreeBuilder` to render their output.</span></span> <span data-ttu-id="e44c5-273">Разработчик может вручную реализовать логику `RenderTreeBuilder` с помощью C# процедурного кода.</span><span class="sxs-lookup"><span data-stu-id="e44c5-273">A developer may manually implement `RenderTreeBuilder` logic using procedural C# code.</span></span> <span data-ttu-id="e44c5-274">Дополнительные сведения см. в разделе <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span><span class="sxs-lookup"><span data-stu-id="e44c5-274">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="e44c5-275">Использование логики построителя дерева ручного просмотра считается расширенным и небезопасным сценарием, не рекомендуемым для разработки общих компонентов.</span><span class="sxs-lookup"><span data-stu-id="e44c5-275">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="e44c5-276">При написании кода `RenderTreeBuilder` разработчик должен гарантировать правильность кода.</span><span class="sxs-lookup"><span data-stu-id="e44c5-276">If `RenderTreeBuilder` code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="e44c5-277">Например, разработчик должен убедиться в том, что:</span><span class="sxs-lookup"><span data-stu-id="e44c5-277">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="e44c5-278">Вызовы `OpenElement` и `CloseElement` правильно сбалансированы.</span><span class="sxs-lookup"><span data-stu-id="e44c5-278">Calls to `OpenElement` and `CloseElement` are correctly balanced.</span></span>
* <span data-ttu-id="e44c5-279">Атрибуты добавляются только в нужные места.</span><span class="sxs-lookup"><span data-stu-id="e44c5-279">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="e44c5-280">Неправильная логика конструктора ручного отображения дерева может вызвать произвольное неопределенное поведение, включая сбои, зависания сервера и уязвимости системы безопасности.</span><span class="sxs-lookup"><span data-stu-id="e44c5-280">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="e44c5-281">Рассматривайте логику построителя дерева вручную на том же уровне сложности и с тем же уровнем *опасности* , что и при написании кода сборки или инструкций MSIL вручную.</span><span class="sxs-lookup"><span data-stu-id="e44c5-281">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
