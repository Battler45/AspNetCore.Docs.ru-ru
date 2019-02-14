---
title: Ролевая авторизация в ASP.NET Core
author: rick-anderson
description: Узнайте, как ограничить доступ контроллера и действия в ASP.NET Core, передав атрибут Authorize ролей.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/roles
ms.openlocfilehash: 0467ea82831bffe6882e584930c2fa1212a244c7
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248099"
---
# <a name="role-based-authorization-in-aspnet-core"></a><span data-ttu-id="59a68-103">Ролевая авторизация в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="59a68-103">Role-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-role-based"></a>

<span data-ttu-id="59a68-104">При создании удостоверения может принадлежать одной или нескольких ролей.</span><span class="sxs-lookup"><span data-stu-id="59a68-104">When an identity is created it may belong to one or more roles.</span></span> <span data-ttu-id="59a68-105">Например Трейси может принадлежать к роли администратора и пользователя, пока Скотт может принадлежать только к роли пользователя.</span><span class="sxs-lookup"><span data-stu-id="59a68-105">For example, Tracy may belong to the Administrator and User roles whilst Scott may only belong to the User role.</span></span> <span data-ttu-id="59a68-106">Как эти роли создаются и управляются зависит от хранилище процесса авторизации.</span><span class="sxs-lookup"><span data-stu-id="59a68-106">How these roles are created and managed depends on the backing store of the authorization process.</span></span> <span data-ttu-id="59a68-107">Роли имеют доступ к деятельность разработчика [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) метод [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) класса.</span><span class="sxs-lookup"><span data-stu-id="59a68-107">Roles are exposed to the developer through the [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) method on the [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) class.</span></span>

::: moniker range=">= aspnetcore-2.0"

> [!IMPORTANT]
> <span data-ttu-id="59a68-108">Этот раздел **не** распространяется на приложение Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="59a68-108">This topic does **not** apply to Razor Pages.</span></span> <span data-ttu-id="59a68-109">Razor Pages поддерживает [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter) и [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter).</span><span class="sxs-lookup"><span data-stu-id="59a68-109">Razor Pages supports [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter).</span></span> <span data-ttu-id="59a68-110">Дополнительные сведения см. в разделе [Методы фильтрации для Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="59a68-110">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

::: moniker-end

## <a name="adding-role-checks"></a><span data-ttu-id="59a68-111">Добавление проверки роли</span><span class="sxs-lookup"><span data-stu-id="59a68-111">Adding role checks</span></span>

<span data-ttu-id="59a68-112">Проверки авторизации на основе ролей являются декларативными&mdash;разработчик внедряет их в свой код от контроллера или действия в контроллере, определения ролей, которые текущий пользователь должен быть членом для доступа к запрошенному ресурсу.</span><span class="sxs-lookup"><span data-stu-id="59a68-112">Role-based authorization checks are declarative&mdash;the developer embeds them within their code, against a controller or an action within a controller, specifying roles which the current user must be a member of to access the requested resource.</span></span>

<span data-ttu-id="59a68-113">Например, следующий код ограничивает доступ ко всем действиям на `AdministrationController` пользователям, являются членами `Administrator` роли:</span><span class="sxs-lookup"><span data-stu-id="59a68-113">For example, the following code limits access to any actions on the `AdministrationController` to users who are a member of the `Administrator` role:</span></span>

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

<span data-ttu-id="59a68-114">Можно указать несколько ролей в виде списка с разделителями-запятыми:</span><span class="sxs-lookup"><span data-stu-id="59a68-114">You can specify multiple roles as a comma separated list:</span></span>

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

<span data-ttu-id="59a68-115">Этот контроллер не превысит доступными пользователям, которые являются членами объекта `HRManager` роли или `Finance` роли.</span><span class="sxs-lookup"><span data-stu-id="59a68-115">This controller would be only accessible by users who are members of the `HRManager` role or the `Finance` role.</span></span>

<span data-ttu-id="59a68-116">Если применить несколько атрибутов доступ к пользователю необходимо быть членом всех ролей, указанных; Следующий пример требует, что пользователь должен быть одновременно членом групп `PowerUser` и `ControlPanelUser` роли.</span><span class="sxs-lookup"><span data-stu-id="59a68-116">If you apply multiple attributes then an accessing user must be a member of all the roles specified; the following sample requires that a user must be a member of both the `PowerUser` and `ControlPanelUser` role.</span></span>

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

<span data-ttu-id="59a68-117">Ограничить доступ, применив атрибуты авторизации дополнительных ролей на уровне действия:</span><span class="sxs-lookup"><span data-stu-id="59a68-117">You can further limit access by applying additional role authorization attributes at the action level:</span></span>

```csharp
[Authorize(Roles = "Administrator, PowerUser")]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [Authorize(Roles = "Administrator")]
    public ActionResult ShutDown()
    {
    }
}
```

<span data-ttu-id="59a68-118">В предыдущем коде фрагмент кода членах `Administrator` роли или `PowerUser` роли контроллера и `SetTime` действие, но только члены `Administrator` роли `ShutDown` действие.</span><span class="sxs-lookup"><span data-stu-id="59a68-118">In the previous code snippet members of the `Administrator` role or the `PowerUser` role can access the controller and the `SetTime` action, but only members of the `Administrator` role can access the `ShutDown` action.</span></span>

<span data-ttu-id="59a68-119">Можно также заблокировать контроллера, но Разрешить анонимные, не прошедшие проверку подлинности доступа к отдельным действиям.</span><span class="sxs-lookup"><span data-stu-id="59a68-119">You can also lock down a controller but allow anonymous, unauthenticated access to individual actions.</span></span>

```csharp
[Authorize]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [AllowAnonymous]
    public ActionResult Login()
    {
    }
}
```

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a><span data-ttu-id="59a68-120">Проверок роли на основе политик</span><span class="sxs-lookup"><span data-stu-id="59a68-120">Policy based role checks</span></span>

<span data-ttu-id="59a68-121">Требования к роли могут также выражаться с помощью нового синтаксиса политики, где разработчик регистрирует политику при запуске как часть конфигурации службы авторизации.</span><span class="sxs-lookup"><span data-stu-id="59a68-121">Role requirements can also be expressed using the new Policy syntax, where a developer registers a policy at startup as part of the Authorization service configuration.</span></span> <span data-ttu-id="59a68-122">Это обычно происходит в `ConfigureServices()` в вашей *Startup.cs* файла.</span><span class="sxs-lookup"><span data-stu-id="59a68-122">This normally occurs in `ConfigureServices()` in your *Startup.cs* file.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```

<span data-ttu-id="59a68-123">Политики применяются с помощью `Policy` свойство `AuthorizeAttribute` атрибута:</span><span class="sxs-lookup"><span data-stu-id="59a68-123">Policies are applied using the `Policy` property on the `AuthorizeAttribute` attribute:</span></span>

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

<span data-ttu-id="59a68-124">Если вы хотите указать несколько ролей, разрешенных в требования, а затем их можно указать в качестве параметров для `RequireRole` метод:</span><span class="sxs-lookup"><span data-stu-id="59a68-124">If you want to specify multiple allowed roles in a requirement then you can specify them as parameters to the `RequireRole` method:</span></span>

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

<span data-ttu-id="59a68-125">В этом примере разрешает пользователям, принадлежащим к `Administrator`, `PowerUser` или `BackupAdministrator` ролей.</span><span class="sxs-lookup"><span data-stu-id="59a68-125">This example authorizes users who belong to the `Administrator`, `PowerUser` or `BackupAdministrator` roles.</span></span>

### <a name="add-role-services-to-identity"></a><span data-ttu-id="59a68-126">Добавление служб роли удостоверению</span><span class="sxs-lookup"><span data-stu-id="59a68-126">Add Role services to Identity</span></span>

<span data-ttu-id="59a68-127">Добавление [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) для добавления служб ролей:</span><span class="sxs-lookup"><span data-stu-id="59a68-127">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](roles/samples/Startup.cs?name=snippet&highlight=7)]