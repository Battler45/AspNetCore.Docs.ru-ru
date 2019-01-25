---
title: Управление пользователями и группами в SignalR
author: bradygaster
description: Общие сведения о ASP.NET Core SignalR пользователей и групп управления.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/04/2018
uid: signalr/groups
ms.openlocfilehash: 0a4836cfa3cf79136b56da1ff05ce8533b4df16c
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837888"
---
# <a name="manage-users-and-groups-in-signalr"></a><span data-ttu-id="66f4d-103">Управление пользователями и группами в SignalR</span><span class="sxs-lookup"><span data-stu-id="66f4d-103">Manage users and groups in SignalR</span></span>

<span data-ttu-id="66f4d-104">По [Бреннан Конрой](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="66f4d-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="66f4d-105">SignalR позволяет сообщения отправляться на все подключения, связанные с конкретным пользователем, а также с именем группы подключений.</span><span class="sxs-lookup"><span data-stu-id="66f4d-105">SignalR allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="66f4d-106">[Просмотреть или скачать образец кода](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/groups/sample/) [(способ загрузки)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="66f4d-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-signalr"></a><span data-ttu-id="66f4d-107">Пользователи в SignalR</span><span class="sxs-lookup"><span data-stu-id="66f4d-107">Users in SignalR</span></span>

<span data-ttu-id="66f4d-108">SignalR позволяет отправлять сообщения для всех подключений, связанных с конкретным пользователем.</span><span class="sxs-lookup"><span data-stu-id="66f4d-108">SignalR allows you to send messages to all connections associated with a specific user.</span></span> <span data-ttu-id="66f4d-109">По умолчанию использует SignalR `ClaimTypes.NameIdentifier` из `ClaimsPrincipal` связан с соединением в качестве идентификатора пользователя.</span><span class="sxs-lookup"><span data-stu-id="66f4d-109">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="66f4d-110">Один пользователь может иметь несколько подключений к приложению SignalR.</span><span class="sxs-lookup"><span data-stu-id="66f4d-110">A single user can have multiple connections to a SignalR app.</span></span> <span data-ttu-id="66f4d-111">Например пользователь может подключаться на их рабочем столе, а также свой телефон.</span><span class="sxs-lookup"><span data-stu-id="66f4d-111">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="66f4d-112">Каждое устройство имеет отдельное соединение SignalR, но они все связанные с тем же пользователем.</span><span class="sxs-lookup"><span data-stu-id="66f4d-112">Each device has a separate SignalR connection, but they're all associated with the same user.</span></span> <span data-ttu-id="66f4d-113">Если сообщение отправляется пользователю, все подключения, связанный с данным пользователем сообщение.</span><span class="sxs-lookup"><span data-stu-id="66f4d-113">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="66f4d-114">Идентификатор пользователя для подключения может осуществляться `Context.UserIdentifier` свойство в концентраторе.</span><span class="sxs-lookup"><span data-stu-id="66f4d-114">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in your hub.</span></span>

<span data-ttu-id="66f4d-115">Отправить сообщение конкретному пользователю, передав идентификатор пользователя для `User` функции в методе концентратора, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="66f4d-115">Send a message to a specific user by passing the user identifier to the `User` function in your hub method as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="66f4d-116">Идентификатор пользователя с учетом регистра.</span><span class="sxs-lookup"><span data-stu-id="66f4d-116">The user identifier is case-sensitive.</span></span>

```csharp
public Task SendPrivateMessage(string user, string message)
{
    return Clients.User(user).SendAsync("ReceiveMessage", message);
}
```

<span data-ttu-id="66f4d-117">Идентификатор пользователя можно настроить, создав `IUserIdProvider`и его регистрации в `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="66f4d-117">The user identifier can be customized by creating an `IUserIdProvider`, and registering it in `ConfigureServices`.</span></span>

[!code-csharp[UserIdProvider](groups/sample/customuseridprovider.cs?range=4-10)]

[!code-csharp[Configure service](groups/sample/startup.cs?range=21-22,39-42)]

> [!NOTE]
> <span data-ttu-id="66f4d-118">AddSignalR должен вызываться перед регистрацией пользовательских служб SignalR.</span><span class="sxs-lookup"><span data-stu-id="66f4d-118">AddSignalR must be called before registering your custom SignalR services.</span></span>

## <a name="groups-in-signalr"></a><span data-ttu-id="66f4d-119">Группами в SignalR</span><span class="sxs-lookup"><span data-stu-id="66f4d-119">Groups in SignalR</span></span>

<span data-ttu-id="66f4d-120">Группа — коллекция соединений, связанный с именем.</span><span class="sxs-lookup"><span data-stu-id="66f4d-120">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="66f4d-121">Сообщения могут отправляться для всех подключений в группе.</span><span class="sxs-lookup"><span data-stu-id="66f4d-121">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="66f4d-122">Группы — рекомендуемый способ отправить подключения или несколько подключений, так как группы управляются приложением.</span><span class="sxs-lookup"><span data-stu-id="66f4d-122">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="66f4d-123">Соединение может быть членом нескольких групп.</span><span class="sxs-lookup"><span data-stu-id="66f4d-123">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="66f4d-124">Это делает групп идеальной для примерно приложения чата, где каждой комнате, можно представить в виде группы.</span><span class="sxs-lookup"><span data-stu-id="66f4d-124">This makes groups ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="66f4d-125">Соединения можно добавлять к или удалены из групп с помощью `AddToGroupAsync` и `RemoveFromGroupAsync` методы.</span><span class="sxs-lookup"><span data-stu-id="66f4d-125">Connections can be added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

<span data-ttu-id="66f4d-126">Членство в группе не сохраняется при повторном подключении.</span><span class="sxs-lookup"><span data-stu-id="66f4d-126">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="66f4d-127">Подключение должно повторно присоединиться к группе, когда она будет восстановлена.</span><span class="sxs-lookup"><span data-stu-id="66f4d-127">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="66f4d-128">Число членов группы, так как эта информация недоступна, если приложение масштабируется на несколько серверов невозможна.</span><span class="sxs-lookup"><span data-stu-id="66f4d-128">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="66f4d-129">Чтобы защитить доступ к ресурсам при использовании групп, используйте [проверки подлинности и авторизации](xref:signalr/authn-and-authz) функциональные возможности в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="66f4d-129">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="66f4d-130">Если только добавление пользователей в группу при учетные данные действительны для этой группы сообщений, отправляемых в эту группу только перейдет авторизованным пользователям.</span><span class="sxs-lookup"><span data-stu-id="66f4d-130">If you only add users to a group when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="66f4d-131">Однако группы не являются функцией безопасности.</span><span class="sxs-lookup"><span data-stu-id="66f4d-131">However, groups are not a security feature.</span></span> <span data-ttu-id="66f4d-132">Проверка подлинности утверждений содержат функции, которые группы этого не сделать, например окончания срока действия и отзыв прав доступа.</span><span class="sxs-lookup"><span data-stu-id="66f4d-132">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="66f4d-133">Если отменяется разрешение пользователя для доступа к группе, необходимо вручную обнаружит это и удалить их из группы.</span><span class="sxs-lookup"><span data-stu-id="66f4d-133">If a user's permission to access the group is revoked, you have to manually detect that and remove them from the group.</span></span>

> [!NOTE]
> <span data-ttu-id="66f4d-134">Имена групп учитывается регистр.</span><span class="sxs-lookup"><span data-stu-id="66f4d-134">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="66f4d-135">Связанные ресурсы</span><span class="sxs-lookup"><span data-stu-id="66f4d-135">Related resources</span></span>

* [<span data-ttu-id="66f4d-136">Начало работы</span><span class="sxs-lookup"><span data-stu-id="66f4d-136">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="66f4d-137">Центры</span><span class="sxs-lookup"><span data-stu-id="66f4d-137">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="66f4d-138">Публикация в Azure</span><span class="sxs-lookup"><span data-stu-id="66f4d-138">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
