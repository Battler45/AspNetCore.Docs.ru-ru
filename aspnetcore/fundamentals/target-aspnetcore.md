---
title: Использование интерфейсов API ASP.NET Core в библиотеке классов
author: scottaddie
description: Узнайте, как использовать интерфейсы API ASP.NET Core в библиотеке классов.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- Blazor
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: 72096fc2f03033dfe8325b5129e074913a2fbd1f
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463136"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a><span data-ttu-id="1df78-103">Использование интерфейсов API ASP.NET Core в библиотеке классов</span><span class="sxs-lookup"><span data-stu-id="1df78-103">Use ASP.NET Core APIs in a class library</span></span>

<span data-ttu-id="1df78-104">Автор: [Скотт Адди](https://github.com/scottaddie) (Scott Addie)</span><span class="sxs-lookup"><span data-stu-id="1df78-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="1df78-105">В этом документе приводятся рекомендации по использованию интерфейсов API ASP.NET Core в библиотеке классов.</span><span class="sxs-lookup"><span data-stu-id="1df78-105">This document provides guidance for using ASP.NET Core APIs in a class library.</span></span> <span data-ttu-id="1df78-106">Остальные рекомендации по работе с библиотекой см. в статье [Руководство по библиотекам с открытым кодом](/dotnet/standard/library-guidance/).</span><span class="sxs-lookup"><span data-stu-id="1df78-106">For all other library guidance, see [Open-source library guidance](/dotnet/standard/library-guidance/).</span></span>

## <a name="determine-which-aspnet-core-versions-to-support"></a><span data-ttu-id="1df78-107">Определение поддерживаемых версий ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1df78-107">Determine which ASP.NET Core versions to support</span></span>

<span data-ttu-id="1df78-108">ASP.NET Core соответствует [политике поддержки .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="1df78-108">ASP.NET Core adheres to the [.NET Core support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span> <span data-ttu-id="1df78-109">Ознакомьтесь с политикой поддержки, чтобы узнать, какие версии ASP.NET Core поддерживаются в библиотеке.</span><span class="sxs-lookup"><span data-stu-id="1df78-109">Consult the support policy when determining which ASP.NET Core versions to support in a library.</span></span> <span data-ttu-id="1df78-110">Библиотека должна:</span><span class="sxs-lookup"><span data-stu-id="1df78-110">A library should:</span></span>

* <span data-ttu-id="1df78-111">по возможности предоставлять поддержку всех версий ASP.NET Core, классифицированную как *долгосрочная поддержка* (LTS).</span><span class="sxs-lookup"><span data-stu-id="1df78-111">Make an effort to support all ASP.NET Core versions classified as *Long-Term Support* (LTS).</span></span>
* <span data-ttu-id="1df78-112">не обязательно предоставлять поддержку версий ASP.NET Core, классифицированную как *окончание жизненного цикла* (EOL).</span><span class="sxs-lookup"><span data-stu-id="1df78-112">Not feel obligated to support ASP.NET Core versions classified as *End of Life* (EOL).</span></span>

<span data-ttu-id="1df78-113">По мере того как предварительные выпуски ASP.NET Core становятся доступными, критические изменения публикуются в репозитории [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) на GitHub.</span><span class="sxs-lookup"><span data-stu-id="1df78-113">As preview releases of ASP.NET Core are made available, breaking changes are posted in the [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub repository.</span></span> <span data-ttu-id="1df78-114">Тестирование совместимости библиотек можно выполнять как разработку функций платформы.</span><span class="sxs-lookup"><span data-stu-id="1df78-114">Compatibility testing of libraries can be conducted as framework features are being developed.</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="1df78-115">Использование общей платформы .NET Core</span><span class="sxs-lookup"><span data-stu-id="1df78-115">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="1df78-116">В выпуске .NET Core версии 3.0 многие сборки ASP.NET Core больше не публикуются в NuGet в качестве пакетов.</span><span class="sxs-lookup"><span data-stu-id="1df78-116">With the release of .NET Core 3.0, many ASP.NET Core assemblies are no longer published to NuGet as packages.</span></span> <span data-ttu-id="1df78-117">Вместо этого сборки включаются в состав общей платформы `Microsoft.AspNetCore.App`, которая устанавливается вместе с пакетом SDK для .NET Core и установщиками среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="1df78-117">Instead, the assemblies are included in the `Microsoft.AspNetCore.App` shared framework, which is installed with the .NET Core SDK and runtime installers.</span></span> <span data-ttu-id="1df78-118">Список пакетов, которые больше не публикуются, см. в разделе [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references) (Удаление устаревших ссылок на пакеты).</span><span class="sxs-lookup"><span data-stu-id="1df78-118">For a list of packages no longer being published, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="1df78-119">Начиная с .NET Core версии 3.0, проекты, использующие пакет SDK `Microsoft.NET.Sdk.Web` для MSBuild, неявно ссылаются на общую платформу.</span><span class="sxs-lookup"><span data-stu-id="1df78-119">As of .NET Core 3.0, projects using the `Microsoft.NET.Sdk.Web` MSBuild SDK implicitly reference the shared framework.</span></span> <span data-ttu-id="1df78-120">Проекты, использующие пакет SDK для `Microsoft.NET.Sdk` или `Microsoft.NET.Sdk.Razor`, должны ссылаться на ASP.NET Core, чтобы использовать интерфейсы API ASP.NET Core в общей платформе.</span><span class="sxs-lookup"><span data-stu-id="1df78-120">Projects using the `Microsoft.NET.Sdk` or `Microsoft.NET.Sdk.Razor` SDK must reference ASP.NET Core to use ASP.NET Core APIs in the shared framework.</span></span>

<span data-ttu-id="1df78-121">Чтобы сослаться на ASP.NET Core, добавьте в файл проекта следующий элемент `<FrameworkReference>`:</span><span class="sxs-lookup"><span data-stu-id="1df78-121">To reference ASP.NET Core, add the following `<FrameworkReference>` element to your project file:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

<span data-ttu-id="1df78-122">Такая ссылка на ASP.NET Core поддерживается только для проектов, предназначенных для .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="1df78-122">Referencing ASP.NET Core in this manner is only supported for projects targeting .NET Core 3.x.</span></span>

## <a name="include-opno-locblazor-extensibility"></a><span data-ttu-id="1df78-123">Включение расширяемости Blazor</span><span class="sxs-lookup"><span data-stu-id="1df78-123">Include Blazor extensibility</span></span>

Blazor<span data-ttu-id="1df78-124"> поддерживает [модели размещения](xref:blazor/hosting-models) Server и WebAssembly (WASM).</span><span class="sxs-lookup"><span data-stu-id="1df78-124"> supports WebAssembly (WASM) and Server [hosting models](xref:blazor/hosting-models).</span></span> <span data-ttu-id="1df78-125">Если не требуется иное, то библиотека [компонентов Razor](xref:blazor/components) должна поддерживать обе модели размещения.</span><span class="sxs-lookup"><span data-stu-id="1df78-125">Unless there's a specific reason not to, a [Razor components](xref:blazor/components) library should support both hosting models.</span></span> <span data-ttu-id="1df78-126">Библиотека компонентов Razor должна использовать пакет SDK для [Microsoft.NET.SDK.Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="1df78-126">A Razor components library must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

### <a name="support-both-hosting-models"></a><span data-ttu-id="1df78-127">Поддержка обеих моделей размещения</span><span class="sxs-lookup"><span data-stu-id="1df78-127">Support both hosting models</span></span>

<span data-ttu-id="1df78-128">Для поддержки использования компонентов Razor из проектов [Blazor Server](xref:blazor/hosting-models#blazor-server) и [Blazor WASM](xref:blazor/hosting-models#blazor-webassembly) используйте следующие инструкции для редактора.</span><span class="sxs-lookup"><span data-stu-id="1df78-128">To support Razor component consumption from both [Blazor Server](xref:blazor/hosting-models#blazor-server) and [Blazor WASM](xref:blazor/hosting-models#blazor-webassembly) projects, use the following instructions for your editor.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1df78-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1df78-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1df78-130">Используйте шаблон проекта **Библиотека классов Razor**.</span><span class="sxs-lookup"><span data-stu-id="1df78-130">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="1df78-131">Снимите флажок **Support pages and views** (Представления и страницы поддержки).</span><span class="sxs-lookup"><span data-stu-id="1df78-131">The template's **Support pages and views** checkbox should be deselected.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1df78-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1df78-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1df78-133">Во встроенном терминале выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="1df78-133">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1df78-134">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="1df78-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1df78-135">Используйте шаблон проекта **Библиотека классов Razor**.</span><span class="sxs-lookup"><span data-stu-id="1df78-135">Use the **Razor Class Library** project template.</span></span>

---

<span data-ttu-id="1df78-136">Проект, созданный на основе шаблона, выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="1df78-136">The project generated from the template does the following things:</span></span>

* <span data-ttu-id="1df78-137">Использует целевые объекты .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="1df78-137">Targets .NET Standard 2.0.</span></span>
* <span data-ttu-id="1df78-138">Задает для свойства `RazorLangVersion` значение `3.0`.</span><span class="sxs-lookup"><span data-stu-id="1df78-138">Sets the `RazorLangVersion` property to `3.0`.</span></span> <span data-ttu-id="1df78-139">`3.0` — это значение по умолчанию для .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="1df78-139">`3.0` is the default value for .NET Core 3.x.</span></span>
* <span data-ttu-id="1df78-140">Добавляет следующие ссылки на пакеты:</span><span class="sxs-lookup"><span data-stu-id="1df78-140">Adds the following package references:</span></span>
  * <span data-ttu-id="1df78-141">[Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components);</span><span class="sxs-lookup"><span data-stu-id="1df78-141">[Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)</span></span>
  * <span data-ttu-id="1df78-142">[Microsoft.AspNetCore.Components.Web](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web).</span><span class="sxs-lookup"><span data-stu-id="1df78-142">[Microsoft.AspNetCore.Components.Web](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)</span></span>

<span data-ttu-id="1df78-143">Пример:</span><span class="sxs-lookup"><span data-stu-id="1df78-143">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a><span data-ttu-id="1df78-144">Поддержка определенной модели размещения</span><span class="sxs-lookup"><span data-stu-id="1df78-144">Support a specific hosting model</span></span>

<span data-ttu-id="1df78-145">Гораздо менее распространена поддержка одной модели размещения Blazor.</span><span class="sxs-lookup"><span data-stu-id="1df78-145">It's far less common to support a single Blazor hosting model.</span></span> <span data-ttu-id="1df78-146">Например, для поддержки использования компонентов Razor только из проектов [Blazor Server](xref:blazor/hosting-models#blazor-server):</span><span class="sxs-lookup"><span data-stu-id="1df78-146">As an example, to support Razor component consumption from [Blazor Server](xref:blazor/hosting-models#blazor-server) projects only:</span></span>

* <span data-ttu-id="1df78-147">Целевая версия .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="1df78-147">Target .NET Core 3.x.</span></span>
* <span data-ttu-id="1df78-148">Добавьте элемент `<FrameworkReference>` для общей платформы.</span><span class="sxs-lookup"><span data-stu-id="1df78-148">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="1df78-149">Пример:</span><span class="sxs-lookup"><span data-stu-id="1df78-149">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

<span data-ttu-id="1df78-150">Дополнительные сведения о библиотеках, содержащих компоненты Razor, см. в статье [ASP.NET Core Razor components class libraries](xref:blazor/class-libraries) (Библиотеки классов компонентов Razor для ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="1df78-150">For more information on libraries containing Razor components, see [ASP.NET Core Razor components class libraries](xref:blazor/class-libraries).</span></span>

## <a name="include-mvc-extensibility"></a><span data-ttu-id="1df78-151">Включение расширяемости MVC</span><span class="sxs-lookup"><span data-stu-id="1df78-151">Include MVC extensibility</span></span>

<span data-ttu-id="1df78-152">В этом разделе приведены рекомендации по библиотекам, включая:</span><span class="sxs-lookup"><span data-stu-id="1df78-152">This section outlines recommendations for libraries that include:</span></span>

* [<span data-ttu-id="1df78-153">Представления Razor или Razor Pages</span><span class="sxs-lookup"><span data-stu-id="1df78-153">Razor views or Razor Pages</span></span>](#razor-views-or-razor-pages)
* [<span data-ttu-id="1df78-154">Вспомогательные функции тегов</span><span class="sxs-lookup"><span data-stu-id="1df78-154">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="1df78-155">Компоненты представлений</span><span class="sxs-lookup"><span data-stu-id="1df78-155">View components</span></span>](#view-components)

<span data-ttu-id="1df78-156">В этом разделе не рассматривается настройка различных версий для поддержки нескольких версий MVC.</span><span class="sxs-lookup"><span data-stu-id="1df78-156">This section doesn't discuss multi-targeting to support multiple versions of MVC.</span></span> <span data-ttu-id="1df78-157">Рекомендации по поддержке нескольких версий ASP.NET Core см. в разделе [Support multiple ASP.NET Core versions](#support-multiple-aspnet-core-versions) (Поддержка нескольких ASP.NET Core версий).</span><span class="sxs-lookup"><span data-stu-id="1df78-157">For guidance on supporting multiple ASP.NET Core versions, see [Support multiple ASP.NET Core versions](#support-multiple-aspnet-core-versions).</span></span>

### <a name="razor-views-or-razor-pages"></a><span data-ttu-id="1df78-158">Представления Razor или Razor Pages</span><span class="sxs-lookup"><span data-stu-id="1df78-158">Razor views or Razor Pages</span></span>

<span data-ttu-id="1df78-159">Проект, включающий [представления Razor](xref:mvc/views/overview) или [Razor Pages](xref:razor-pages/index), должен использовать [пакет SDK Microsoft.NET.Sdk.Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="1df78-159">A project that includes [Razor views](xref:mvc/views/overview) or [Razor Pages](xref:razor-pages/index) must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

<span data-ttu-id="1df78-160">Если проект предназначен для .NET Core 3.x, для него требуется:</span><span class="sxs-lookup"><span data-stu-id="1df78-160">If the project targets .NET Core 3.x, it requires:</span></span>

* <span data-ttu-id="1df78-161">Для свойства MSBuild `AddRazorSupportForMvc` задать значение `true`.</span><span class="sxs-lookup"><span data-stu-id="1df78-161">An `AddRazorSupportForMvc` MSBuild property set to `true`.</span></span>
* <span data-ttu-id="1df78-162">Элемент `<FrameworkReference>` для общей платформы.</span><span class="sxs-lookup"><span data-stu-id="1df78-162">A `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="1df78-163">Шаблон проекта **Библиотека классов Razor** должен удовлетворять описанным выше требованиям для проектов, предназначенных для .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="1df78-163">The **Razor Class Library** project template satisfies the preceding requirements for projects targeting .NET Core 3.x.</span></span> <span data-ttu-id="1df78-164">Для редактора следует использовать следующие инструкции.</span><span class="sxs-lookup"><span data-stu-id="1df78-164">Use the following instructions for your editor.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1df78-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1df78-165">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1df78-166">Используйте шаблон проекта **Библиотека классов Razor**.</span><span class="sxs-lookup"><span data-stu-id="1df78-166">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="1df78-167">Установите флажок **Support pages and views** (Представления и страницы поддержки).</span><span class="sxs-lookup"><span data-stu-id="1df78-167">The template's **Support pages and views** checkbox should be selected.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1df78-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1df78-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1df78-169">Во встроенном терминале выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="1df78-169">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1df78-170">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="1df78-170">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1df78-171">В настоящее время поддержка шаблона проекта отсутствует.</span><span class="sxs-lookup"><span data-stu-id="1df78-171">No project template support at this time.</span></span>

---

<span data-ttu-id="1df78-172">Пример:</span><span class="sxs-lookup"><span data-stu-id="1df78-172">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

<span data-ttu-id="1df78-173">Если проект предназначен для .NET Standard, требуется ссылка на пакет [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc).</span><span class="sxs-lookup"><span data-stu-id="1df78-173">If the project targets .NET Standard instead, a [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) package reference is required.</span></span> <span data-ttu-id="1df78-174">Пакет `Microsoft.AspNetCore.Mvc` перемещен в общую платформу ASP.NET Core 3.0 и поэтому больше не публикуется.</span><span class="sxs-lookup"><span data-stu-id="1df78-174">The `Microsoft.AspNetCore.Mvc` package moved into the shared framework in ASP.NET Core 3.0 and is therefore no longer published.</span></span> <span data-ttu-id="1df78-175">Пример:</span><span class="sxs-lookup"><span data-stu-id="1df78-175">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a><span data-ttu-id="1df78-176">Вспомогательные функции тегов</span><span class="sxs-lookup"><span data-stu-id="1df78-176">Tag Helpers</span></span>

<span data-ttu-id="1df78-177">Проект, содержащий [вспомогательные функции тегов](xref:mvc/views/tag-helpers/intro) должен использовать пакет SDK для `Microsoft.NET.Sdk`.</span><span class="sxs-lookup"><span data-stu-id="1df78-177">A project that includes [Tag Helpers](xref:mvc/views/tag-helpers/intro) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="1df78-178">При использовании .NET Core 3.x добавьте элемент `<FrameworkReference>` для общей платформы.</span><span class="sxs-lookup"><span data-stu-id="1df78-178">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="1df78-179">Пример:</span><span class="sxs-lookup"><span data-stu-id="1df78-179">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="1df78-180">Если используется .NET Standard (для поддержки версий, предшествующих ASP.NET Core 3.x), добавьте ссылку на пакет [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor).</span><span class="sxs-lookup"><span data-stu-id="1df78-180">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor).</span></span> <span data-ttu-id="1df78-181">Пакет `Microsoft.AspNetCore.Mvc.Razor` перемещен в общую платформу и поэтому больше не публикуется.</span><span class="sxs-lookup"><span data-stu-id="1df78-181">The `Microsoft.AspNetCore.Mvc.Razor` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="1df78-182">Пример:</span><span class="sxs-lookup"><span data-stu-id="1df78-182">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a><span data-ttu-id="1df78-183">Компоненты представлений</span><span class="sxs-lookup"><span data-stu-id="1df78-183">View components</span></span>

<span data-ttu-id="1df78-184">Проект, содержащий [вспомогательные функции тегов](xref:mvc/views/view-components), должен использовать пакет SDK для `Microsoft.NET.Sdk`.</span><span class="sxs-lookup"><span data-stu-id="1df78-184">A project that includes [View components](xref:mvc/views/view-components) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="1df78-185">При использовании .NET Core 3.x добавьте элемент `<FrameworkReference>` для общей платформы.</span><span class="sxs-lookup"><span data-stu-id="1df78-185">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="1df78-186">Пример:</span><span class="sxs-lookup"><span data-stu-id="1df78-186">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="1df78-187">Если используется .NET Standard (для поддержки версий, предшествующих ASP.NET Core 3.x), добавьте ссылку на пакет [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span><span class="sxs-lookup"><span data-stu-id="1df78-187">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span></span> <span data-ttu-id="1df78-188">Пакет `Microsoft.AspNetCore.Mvc.ViewFeatures` перемещен в общую платформу и поэтому больше не публикуется.</span><span class="sxs-lookup"><span data-stu-id="1df78-188">The `Microsoft.AspNetCore.Mvc.ViewFeatures` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="1df78-189">Пример:</span><span class="sxs-lookup"><span data-stu-id="1df78-189">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a><span data-ttu-id="1df78-190">Поддержка нескольких версий ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1df78-190">Support multiple ASP.NET Core versions</span></span>

<span data-ttu-id="1df78-191">Для создания библиотеки, поддерживающей множественные варианты ASP.NET Core, требуется настройка для различных версий.</span><span class="sxs-lookup"><span data-stu-id="1df78-191">Multi-targeting is required to author a library that supports multiple variants of ASP.NET Core.</span></span> <span data-ttu-id="1df78-192">Рассмотрим ситуацию, в которой библиотека вспомогательных функций тегов должна поддерживать следующие варианты ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="1df78-192">Consider a scenario in which a Tag Helpers library must support the following ASP.NET Core variants:</span></span>

* <span data-ttu-id="1df78-193">ASP.NET Core 2.1 для платформы .NET Framework 4.6.1;</span><span class="sxs-lookup"><span data-stu-id="1df78-193">ASP.NET Core 2.1 targeting .NET Framework 4.6.1</span></span>
* <span data-ttu-id="1df78-194">ASP.NET Core 2.x для платформы .NET Framework 2.x;</span><span class="sxs-lookup"><span data-stu-id="1df78-194">ASP.NET Core 2.x targeting .NET Core 2.x</span></span>
* <span data-ttu-id="1df78-195">ASP.NET Core 3.x для платформы .NET Framework 3.x.</span><span class="sxs-lookup"><span data-stu-id="1df78-195">ASP.NET Core 3.x targeting .NET Core 3.x</span></span>

<span data-ttu-id="1df78-196">Следующий файл проекта поддерживает эти варианты с помощью свойства `TargetFrameworks`:</span><span class="sxs-lookup"><span data-stu-id="1df78-196">The following project file supports these variants via the `TargetFrameworks` property:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

<span data-ttu-id="1df78-197">Для предыдущего файла проекта:</span><span class="sxs-lookup"><span data-stu-id="1df78-197">With the preceding project file:</span></span>

* <span data-ttu-id="1df78-198">Для всех потребителей добавляется пакет `Markdig`.</span><span class="sxs-lookup"><span data-stu-id="1df78-198">The `Markdig` package is added for all consumers.</span></span>
* <span data-ttu-id="1df78-199">Для потребителей, использующих .NET Framework 4.6.1 или более поздней версии или .NET Core 2.x, добавляется ссылка на [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor).</span><span class="sxs-lookup"><span data-stu-id="1df78-199">A reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) is added for consumers targeting .NET Framework 4.6.1 or later or .NET Core 2.x.</span></span> <span data-ttu-id="1df78-200">Версия 2.1.0 пакета работает с ASP.NET Core 2.2 из-за обратной совместимости.</span><span class="sxs-lookup"><span data-stu-id="1df78-200">Version 2.1.0 of the package works with ASP.NET Core 2.2 because of backwards compatibility.</span></span>
* <span data-ttu-id="1df78-201">На общую платформу ссылаются потребители, использующие .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="1df78-201">The shared framework is referenced for consumers targeting .NET Core 3.x.</span></span> <span data-ttu-id="1df78-202">Пакет `Microsoft.AspNetCore.Mvc.Razor` включен в общую платформу.</span><span class="sxs-lookup"><span data-stu-id="1df78-202">The `Microsoft.AspNetCore.Mvc.Razor` package is included in the shared framework.</span></span>

<span data-ttu-id="1df78-203">Кроме того, можно использовать .NET Standard 2.0 вместо .NET Core 2.1 и .NET Framework 4.6.1:</span><span class="sxs-lookup"><span data-stu-id="1df78-203">Alternatively, .NET Standard 2.0 could be targeted instead of targeting both .NET Core 2.1 and .NET Framework 4.6.1:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

<span data-ttu-id="1df78-204">В предыдущем файле проекта есть следующие предупреждения.</span><span class="sxs-lookup"><span data-stu-id="1df78-204">With the preceding project file, the following caveats exist:</span></span>

* <span data-ttu-id="1df78-205">Так как библиотека содержит только вспомогательные функции тегов, она проще для определенных платформ, на которых выполняется ASP.NET Core: .NET Core и .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="1df78-205">Since the library only contains Tag Helpers, it's more straightforward to target the specific platforms on which ASP.NET Core runs: .NET Core and .NET Framework.</span></span> <span data-ttu-id="1df78-206">Вспомогательные функции тегов нельзя использовать с другими совместимыми с .NET Standard 2.0 целевыми платформами, такими как Unity, UWP и Xamarin.</span><span class="sxs-lookup"><span data-stu-id="1df78-206">Tag Helpers can't be used by other .NET Standard 2.0-compliant target frameworks such as Unity, UWP, and Xamarin.</span></span>
* <span data-ttu-id="1df78-207">Некоторые проблемы с использованием .NET Standard 2.0 в .NET Framework были устраненные в .NET Framework версии 4.7.2.</span><span class="sxs-lookup"><span data-stu-id="1df78-207">Using .NET Standard 2.0 from .NET Framework has some issues that were addressed in .NET Framework 4.7.2.</span></span> <span data-ttu-id="1df78-208">Улучшить работу потребителей, использующих .NET Framework 4.6.1–4.7.1, можно, используя .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="1df78-208">You can improve the experience for consumers using .NET Framework 4.6.1 through 4.7.1 by targeting .NET Framework 4.6.1.</span></span>

<span data-ttu-id="1df78-209">Если библиотека должна вызывать интерфейсы API для определенных платформ, вместо .NET Standard используйте конкретные реализации .NET.</span><span class="sxs-lookup"><span data-stu-id="1df78-209">If your library needs to call platform-specific APIs, target specific .NET implementations instead of .NET Standard.</span></span> <span data-ttu-id="1df78-210">Дополнительные сведения см. в разделе [Настройка для различных версий](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span><span class="sxs-lookup"><span data-stu-id="1df78-210">For more information, see [Multi-targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span></span>

## <a name="use-an-api-that-hasnt-changed"></a><span data-ttu-id="1df78-211">Использование API, который еще не изменился</span><span class="sxs-lookup"><span data-stu-id="1df78-211">Use an API that hasn't changed</span></span>

<span data-ttu-id="1df78-212">Представьте, что вы обновляете библиотеку ПО промежуточного слоя с .NET Core версии 2.2–3.0.</span><span class="sxs-lookup"><span data-stu-id="1df78-212">Imagine a scenario in which you're upgrading a middleware library from .NET Core 2.2 to 3.0.</span></span> <span data-ttu-id="1df78-213">API-интерфейсы ASP.NET Core ПО промежуточного слоя, используемые в библиотеке, не изменились в версиях ASP.NET Core 2.2 и 3.0.</span><span class="sxs-lookup"><span data-stu-id="1df78-213">The ASP.NET Core middleware APIs being used in the library haven't changed between ASP.NET Core 2.2 and 3.0.</span></span> <span data-ttu-id="1df78-214">Чтобы продолжить поддержку библиотеки ПО промежуточного слоя в .NET Core 3.0, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="1df78-214">To continue supporting the middleware library in .NET Core 3.0, take the following steps:</span></span>

* <span data-ttu-id="1df78-215">Следуйте указаниям в руководстве по работе со [стандартной библиотекой](/dotnet/standard/library-guidance/).</span><span class="sxs-lookup"><span data-stu-id="1df78-215">Follow the [standard library guidance](/dotnet/standard/library-guidance/).</span></span>
* <span data-ttu-id="1df78-216">Добавьте ссылку на пакет для каждого пакета API-интерфейса NuGet, если соответствующая сборка отсутствует в общей платформе.</span><span class="sxs-lookup"><span data-stu-id="1df78-216">Add a package reference for each API's NuGet package if the corresponding assembly doesn't exist in the shared framework.</span></span>

## <a name="use-an-api-that-changed"></a><span data-ttu-id="1df78-217">Использование измененного API</span><span class="sxs-lookup"><span data-stu-id="1df78-217">Use an API that changed</span></span>

<span data-ttu-id="1df78-218">Представьте, что вы обновляете библиотеку с .NET Core версии 2.2 до 3.0.</span><span class="sxs-lookup"><span data-stu-id="1df78-218">Imagine a scenario in which you're upgrading a library from .NET Core 2.2 to .NET Core 3.0.</span></span> <span data-ttu-id="1df78-219">API-интерфейс ASP.NET Core, используемый в библиотеке, имеет [критические изменения](/dotnet/core/compatibility/breaking-changes) в ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="1df78-219">An ASP.NET Core API being used in the library has a [breaking change](/dotnet/core/compatibility/breaking-changes) in ASP.NET Core 3.0.</span></span> <span data-ttu-id="1df78-220">Определите, можно ли перезаписывать библиотеку, чтобы не использовать неработающий API во всех версиях.</span><span class="sxs-lookup"><span data-stu-id="1df78-220">Consider whether the library can be rewritten to not use the broken API in all versions.</span></span>

<span data-ttu-id="1df78-221">Если вы можете переписать библиотеку, сделайте это и продолжайте использовать более раннюю целевую платформу (например, .NET Standard 2.0 или .NET Framework 4.6.1) со ссылками на пакеты.</span><span class="sxs-lookup"><span data-stu-id="1df78-221">If you can rewrite the library, do so and continue to target an earlier target framework (for example, .NET Standard 2.0 or .NET Framework 4.6.1) with package references.</span></span>

<span data-ttu-id="1df78-222">Если вы не можете переписать библиотеку, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="1df78-222">If you can't rewrite the library, take the following steps:</span></span>

* <span data-ttu-id="1df78-223">Добавьте целевой объект для .NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="1df78-223">Add a target for .NET Core 3.0.</span></span>
* <span data-ttu-id="1df78-224">Добавьте элемент `<FrameworkReference>` для общей платформы.</span><span class="sxs-lookup"><span data-stu-id="1df78-224">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="1df78-225">Используйте [директиву препроцессора #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) с соответствующим символом целевой платформы для условной компиляции кода.</span><span class="sxs-lookup"><span data-stu-id="1df78-225">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="1df78-226">Например, синхронные операции чтения и записи по потокам HTTP-запросов и ответов по умолчанию отключены по ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="1df78-226">For example, synchronous reads and writes on HTTP request and response streams are disabled by default as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="1df78-227">По умолчанию ASP.NET Core 2.2 поддерживает синхронное поведение.</span><span class="sxs-lookup"><span data-stu-id="1df78-227">ASP.NET Core 2.2 supports the synchronous behavior by default.</span></span> <span data-ttu-id="1df78-228">Рассмотрим библиотеку ПО промежуточного слоя, в которой должны быть включены синхронные операции чтения и записи, когда происходит операция ввода-вывода.</span><span class="sxs-lookup"><span data-stu-id="1df78-228">Consider a middleware library in which synchronous reads and writes should be enabled where IO is occurring.</span></span> <span data-ttu-id="1df78-229">Библиотека должна содержать код для включения синхронных функций в соответствующей директиве препроцессора.</span><span class="sxs-lookup"><span data-stu-id="1df78-229">The library should enclose the code to enable synchronous features in the appropriate preprocessor directive.</span></span> <span data-ttu-id="1df78-230">Пример:</span><span class="sxs-lookup"><span data-stu-id="1df78-230">For example:</span></span>

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a><span data-ttu-id="1df78-231">Использование API, представленного в версии 3.0</span><span class="sxs-lookup"><span data-stu-id="1df78-231">Use an API introduced in 3.0</span></span>

<span data-ttu-id="1df78-232">Представьте, что вы хотите использовать API-интерфейс ASP.NET Core, который появился в ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="1df78-232">Imagine that you want to use an ASP.NET Core API that was introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="1df78-233">Ответьте на следующие вопросы.</span><span class="sxs-lookup"><span data-stu-id="1df78-233">Consider the following questions:</span></span>

1. <span data-ttu-id="1df78-234">Работает ли в библиотеке новый API?</span><span class="sxs-lookup"><span data-stu-id="1df78-234">Does the library functionally require the new API?</span></span>
1. <span data-ttu-id="1df78-235">Может ли библиотека реализовать эту функцию другим способом?</span><span class="sxs-lookup"><span data-stu-id="1df78-235">Can the library implement this feature in a different way?</span></span>

<span data-ttu-id="1df78-236">Если для работы библиотеки требуется API и нет способа реализовать его на уровне ниже:</span><span class="sxs-lookup"><span data-stu-id="1df78-236">If the library functionally requires the API and there's no way to implement it down-level:</span></span>

* <span data-ttu-id="1df78-237">Используйте только .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="1df78-237">Target .NET Core 3.x only.</span></span>
* <span data-ttu-id="1df78-238">Добавьте элемент `<FrameworkReference>` для общей платформы.</span><span class="sxs-lookup"><span data-stu-id="1df78-238">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="1df78-239">Если библиотека может реализовать эту функцию другим способом:</span><span class="sxs-lookup"><span data-stu-id="1df78-239">If the library can implement the feature in a different way:</span></span>

* <span data-ttu-id="1df78-240">Добавьте .NET Core 3.x в качестве целевой платформы.</span><span class="sxs-lookup"><span data-stu-id="1df78-240">Add .NET Core 3.x as a target framework.</span></span>
* <span data-ttu-id="1df78-241">Добавьте элемент `<FrameworkReference>` для общей платформы.</span><span class="sxs-lookup"><span data-stu-id="1df78-241">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="1df78-242">Используйте [директиву препроцессора #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) с соответствующим символом целевой платформы для условной компиляции кода.</span><span class="sxs-lookup"><span data-stu-id="1df78-242">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="1df78-243">Например, следующая вспомогательная функция тега использует интерфейс <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>, представленный в ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="1df78-243">For example, the following Tag Helper uses the <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> interface introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="1df78-244">Потребители, использующие .NET Core 3.0, выполняют путь кода, определенный символом `NETCOREAPP3_0` целевой платформы.</span><span class="sxs-lookup"><span data-stu-id="1df78-244">Consumers targeting .NET Core 3.0 execute the code path defined by the `NETCOREAPP3_0` target framework symbol.</span></span> <span data-ttu-id="1df78-245">Тип параметра конструктора вспомогательной функции тега изменяется на <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> для потребителей .NET Core 2.1 и .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="1df78-245">The Tag Helper's constructor parameter type changes to <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> for .NET Core 2.1 and .NET Framework 4.6.1 consumers.</span></span> <span data-ttu-id="1df78-246">Это изменение было необходимо, так как в ASP.NET Core 3.0 свойство `IHostingEnvironment` помечено как устаревшее и в качестве замены рекомендуется использовать `IWebHostEnvironment`.</span><span class="sxs-lookup"><span data-stu-id="1df78-246">This change was necessary because ASP.NET Core 3.0 marked `IHostingEnvironment` as obsolete and recommended `IWebHostEnvironment` as the replacement.</span></span>

```csharp
[HtmlTargetElement("script", Attributes = "asp-inline")]
public class ScriptInliningTagHelper : TagHelper
{
    private readonly IFileProvider _wwwroot;

#if NETCOREAPP3_0
    public ScriptInliningTagHelper(IWebHostEnvironment env)
#else
    public ScriptInliningTagHelper(IHostingEnvironment env)
#endif
    {
        _wwwroot = env.WebRootFileProvider;
    }

    // code omitted for brevity
}
```

<span data-ttu-id="1df78-247">Следующий файл проекта с несколькими целевыми файлами поддерживает этот сценарий вспомогательной функции тега:</span><span class="sxs-lookup"><span data-stu-id="1df78-247">The following multi-targeted project file supports this Tag Helper scenario:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a><span data-ttu-id="1df78-248">Использование API, удаленного из общей платформы</span><span class="sxs-lookup"><span data-stu-id="1df78-248">Use an API removed from the shared framework</span></span>

<span data-ttu-id="1df78-249">Чтобы использовать сборку ASP.NET Core, которая была удалена из общей платформы, добавьте соответствующую ссылку на пакет.</span><span class="sxs-lookup"><span data-stu-id="1df78-249">To use an ASP.NET Core assembly that was removed from the shared framework, add the appropriate package reference.</span></span> <span data-ttu-id="1df78-250">Список пакетов, удаленных из общей платформы в ASP.NET Core 3.0, см. в разделе [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references) (Удаление устаревших ссылок на пакеты).</span><span class="sxs-lookup"><span data-stu-id="1df78-250">For a list of packages removed from the shared framework in ASP.NET Core 3.0, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="1df78-251">Например, чтобы добавить клиент веб-API:</span><span class="sxs-lookup"><span data-stu-id="1df78-251">For example, to add the web API client:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNet.WebApi.Client" Version="5.2.7" />
  </ItemGroup>

</Project>
```

## <a name="additional-resources"></a><span data-ttu-id="1df78-252">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="1df78-252">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* <xref:blazor/class-libraries>
* [<span data-ttu-id="1df78-253">Поддержка реализации .NET</span><span class="sxs-lookup"><span data-stu-id="1df78-253">.NET implementation support</span></span>](/dotnet/standard/net-standard#net-implementation-support)
* [<span data-ttu-id="1df78-254">Политики поддержки .NET</span><span class="sxs-lookup"><span data-stu-id="1df78-254">.NET support policies</span></span>](https://dotnet.microsoft.com/platform/support/policy)