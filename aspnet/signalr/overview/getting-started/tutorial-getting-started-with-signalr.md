---
uid: signalr/overview/getting-started/tutorial-getting-started-with-signalr
title: Учебник. Начало работы с SignalR 2 | Документация Майкрософт
author: pfletcher
description: В этом учебнике содержатся сведения об использовании SignalR для создания приложения разговора в режиме реального времени. Следует добавить пустой веб-приложения ASP.NET SignalR и создать pa HTML...
ms.author: riande
ms.date: 06/10/2014
ms.assetid: a8b3b778-f009-4369-85c7-e90f9878d8b4
msc.legacyurl: /signalr/overview/getting-started/tutorial-getting-started-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: 3b06e7d0a602e061112adbceba92276f836f6311
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287342"
---
<a name="tutorial-getting-started-with-signalr-2"></a><span data-ttu-id="69fa2-104">Учебник. Начало работы с SignalR 2</span><span class="sxs-lookup"><span data-stu-id="69fa2-104">Tutorial: Getting Started with SignalR 2</span></span>
====================
<span data-ttu-id="69fa2-105">по [Патрик Флетчера](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="69fa2-105">by [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

[<span data-ttu-id="69fa2-106">Скачать завершенный проект</span><span class="sxs-lookup"><span data-stu-id="69fa2-106">Download Completed Project</span></span>](http://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9)

> <span data-ttu-id="69fa2-107">В этом учебнике содержатся сведения об использовании SignalR для создания приложения разговора в режиме реального времени.</span><span class="sxs-lookup"><span data-stu-id="69fa2-107">This tutorial shows how to use SignalR to create a real-time chat application.</span></span> <span data-ttu-id="69fa2-108">Следует добавить пустой веб-приложения ASP.NET SignalR и создать HTML-страницы для отправки и отображения сообщений.</span><span class="sxs-lookup"><span data-stu-id="69fa2-108">You will add SignalR to an empty ASP.NET web application and create an HTML page to send and display messages.</span></span> 
> 
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="69fa2-109">Версии программного обеспечения, используемые в этом руководстве</span><span class="sxs-lookup"><span data-stu-id="69fa2-109">Software versions used in the tutorial</span></span>
> 
> 
> - [<span data-ttu-id="69fa2-110">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="69fa2-110">Visual Studio 2013</span></span>](https://www.microsoft.com/visualstudio/eng/2013-downloads)
> - <span data-ttu-id="69fa2-111">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="69fa2-111">.NET 4.5</span></span>
> - <span data-ttu-id="69fa2-112">SignalR версии 2</span><span class="sxs-lookup"><span data-stu-id="69fa2-112">SignalR version 2</span></span>
>   
> 
> 
> ## <a name="using-visual-studio-2012-with-this-tutorial"></a><span data-ttu-id="69fa2-113">С помощью Visual Studio 2012 с помощью этого руководства</span><span class="sxs-lookup"><span data-stu-id="69fa2-113">Using Visual Studio 2012 with this tutorial</span></span>
> 
> 
> <span data-ttu-id="69fa2-114">Чтобы использовать Visual Studio 2012 с этим руководством, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="69fa2-114">To use Visual Studio 2012 with this tutorial, do the following:</span></span>
> 
> - <span data-ttu-id="69fa2-115">Обновление вашей [диспетчера пакетов](http://docs.nuget.org/docs/start-here/installing-nuget) до последней версии.</span><span class="sxs-lookup"><span data-stu-id="69fa2-115">Update your [Package Manager](http://docs.nuget.org/docs/start-here/installing-nuget) to the latest version.</span></span>
> - <span data-ttu-id="69fa2-116">Установка [установщик веб-платформы](https://www.microsoft.com/web/downloads/platform.aspx).</span><span class="sxs-lookup"><span data-stu-id="69fa2-116">Install the [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).</span></span>
> - <span data-ttu-id="69fa2-117">В установщик веб-платформы, найдите и установите **ASP.NET и Web Tools 2013.1 для Visual Studio 2012**.</span><span class="sxs-lookup"><span data-stu-id="69fa2-117">In the Web Platform Installer, search for and install **ASP.NET and Web Tools 2013.1 for Visual Studio 2012**.</span></span> <span data-ttu-id="69fa2-118">Это будет установки Visual Studio шаблоны для SignalR классов, таких как **центр**.</span><span class="sxs-lookup"><span data-stu-id="69fa2-118">This will install Visual Studio templates for SignalR classes such as **Hub**.</span></span>
> - <span data-ttu-id="69fa2-119">Некоторые шаблоны (такие как **класс запуска OWIN**) будут недоступны; для этого используйте файл класса.</span><span class="sxs-lookup"><span data-stu-id="69fa2-119">Some templates (such as **OWIN Startup Class**) will not be available; for these, use a Class file instead.</span></span>
> 
> 
> ## <a name="tutorial-versions"></a><span data-ttu-id="69fa2-120">Учебника по версии</span><span class="sxs-lookup"><span data-stu-id="69fa2-120">Tutorial versions</span></span>
> 
> <span data-ttu-id="69fa2-121">Сведения о более ранних версий SignalR, см. в разделе [более старых версий SignalR](../older-versions/index.md).</span><span class="sxs-lookup"><span data-stu-id="69fa2-121">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
> 
> ## <a name="questions-and-comments"></a><span data-ttu-id="69fa2-122">Вопросы и комментарии</span><span class="sxs-lookup"><span data-stu-id="69fa2-122">Questions and comments</span></span>
> 
> <span data-ttu-id="69fa2-123">Оставьте свои отзывы на том, как вам понравилось, и этот учебник и что можно улучшить в комментариях в нижней части страницы.</span><span class="sxs-lookup"><span data-stu-id="69fa2-123">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="69fa2-124">Если у вас есть вопросы, которые не имеют отношения к руководству, их можно разместить [форум по ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) или [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="69fa2-124">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


## <a name="overview"></a><span data-ttu-id="69fa2-125">Обзор</span><span class="sxs-lookup"><span data-stu-id="69fa2-125">Overview</span></span>

<span data-ttu-id="69fa2-126">В этом учебнике представлена разработки SignalR, демонстрирующий создание приложения простой чата на основе веб-обозревателя.</span><span class="sxs-lookup"><span data-stu-id="69fa2-126">This tutorial introduces SignalR development by showing how to build a simple browser-based chat application.</span></span> <span data-ttu-id="69fa2-127">Будет добавьте библиотеку SignalR в пустой веб-приложения ASP.NET, создайте класс концентратора для отправки сообщений клиентам и создать HTML-страницу, которая позволяет пользователям отправлять и получать сообщения.</span><span class="sxs-lookup"><span data-stu-id="69fa2-127">You will add the SignalR library to an empty ASP.NET web application, create a hub class for sending messages to clients, and create an HTML page that lets users send and receive chat messages.</span></span> <span data-ttu-id="69fa2-128">Похожий учебник, демонстрирующий создание приложения чата в MVC 5 с помощью представления MVC, см. в разделе [начало работы с SignalR 2 и MVC 5](tutorial-getting-started-with-signalr-and-mvc.md).</span><span class="sxs-lookup"><span data-stu-id="69fa2-128">For a similar tutorial that shows how to create a chat application in MVC 5 using an MVC view, see [Getting Started with SignalR 2 and MVC 5](tutorial-getting-started-with-signalr-and-mvc.md).</span></span>

> [!NOTE]
> <span data-ttu-id="69fa2-129">Этот учебник демонстрирует создание приложений SignalR в версии 2.</span><span class="sxs-lookup"><span data-stu-id="69fa2-129">This tutorial demonstrates how to create SignalR applications in version 2.</span></span> <span data-ttu-id="69fa2-130">Сведения об изменениях между SignalR 1.x и 2, см. в разделе [проектов обновление SignalR 1.x](../releases/upgrading-signalr-1x-projects-to-20.md) и [заметки о выпуске Visual Studio 2013](../../../visual-studio/overview/2013/release-notes.md#TOC13).</span><span class="sxs-lookup"><span data-stu-id="69fa2-130">For details on changes between SignalR 1.x and 2, see [Upgrading SignalR 1.x Projects](../releases/upgrading-signalr-1x-projects-to-20.md) and [Visual Studio 2013 Release Notes](../../../visual-studio/overview/2013/release-notes.md#TOC13).</span></span>

<span data-ttu-id="69fa2-131">SignalR является библиотекой .NET с открытым исходным кодом для создания веб-приложений, требующих взаимодействия с пользователем в реальном времени или обновления данных в реальном времени.</span><span class="sxs-lookup"><span data-stu-id="69fa2-131">SignalR is an open-source .NET library for building web applications that require live user interaction or real-time data updates.</span></span> <span data-ttu-id="69fa2-132">Примеры включают социальных приложений, многопользовательские игры, совместной работы и новости, погода бизнеса или финансовых обновления приложений.</span><span class="sxs-lookup"><span data-stu-id="69fa2-132">Examples include social applications, multiuser games, business collaboration, and news, weather, or financial update applications.</span></span> <span data-ttu-id="69fa2-133">Они часто называются приложениями в реальном времени.</span><span class="sxs-lookup"><span data-stu-id="69fa2-133">These are often called real-time applications.</span></span>

<span data-ttu-id="69fa2-134">SignalR упрощает процесс создания приложений в реальном времени.</span><span class="sxs-lookup"><span data-stu-id="69fa2-134">SignalR simplifies the process of building real-time applications.</span></span> <span data-ttu-id="69fa2-135">Он включает библиотеку ASP.NET сервера и клиентскую библиотеку JavaScript, чтобы упростить управление соединениями между клиентом и сервером и Push-обновления содержимого для клиентов.</span><span class="sxs-lookup"><span data-stu-id="69fa2-135">It includes an ASP.NET server library and a JavaScript client library to make it easier to manage client-server connections and push content updates to clients.</span></span> <span data-ttu-id="69fa2-136">Библиотека SignalR можно добавить в существующее приложение ASP.NET для достижения функциональности в режиме реального времени.</span><span class="sxs-lookup"><span data-stu-id="69fa2-136">You can add the SignalR library to an existing ASP.NET application to gain real-time functionality.</span></span>

<span data-ttu-id="69fa2-137">В данном учебнике показано следующие задачи разработки SignalR:</span><span class="sxs-lookup"><span data-stu-id="69fa2-137">The tutorial demonstrates the following SignalR development tasks:</span></span>

- <span data-ttu-id="69fa2-138">Добавление библиотеки SignalR в веб-приложения ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="69fa2-138">Adding the SignalR library to an ASP.NET web application.</span></span>
- <span data-ttu-id="69fa2-139">Создание класса концентратора для отправки содержимого клиентам.</span><span class="sxs-lookup"><span data-stu-id="69fa2-139">Creating a hub class to push content to clients.</span></span>
- <span data-ttu-id="69fa2-140">Создание класса запуска OWIN для настройки приложения.</span><span class="sxs-lookup"><span data-stu-id="69fa2-140">Creating an OWIN startup class to configure the application.</span></span>
- <span data-ttu-id="69fa2-141">С помощью библиотеки jQuery SignalR на веб-странице для отправки сообщений и отобразить обновления от концентратора.</span><span class="sxs-lookup"><span data-stu-id="69fa2-141">Using the SignalR jQuery library in a web page to send messages and display updates from the hub.</span></span>

<span data-ttu-id="69fa2-142">На следующем снимке экрана показано приложение чата, работающих в браузере.</span><span class="sxs-lookup"><span data-stu-id="69fa2-142">The following screen shot shows the chat application running in a browser.</span></span> <span data-ttu-id="69fa2-143">Каждого нового пользователя можно отправить комментарии и комментарии, добавленные после пользователь не присоединит чате см. в разделе.</span><span class="sxs-lookup"><span data-stu-id="69fa2-143">Each new user can post comments and see comments added after the user joins the chat.</span></span>

![Экземпляры чата](tutorial-getting-started-with-signalr/_static/image1.png)

<span data-ttu-id="69fa2-145">Разделы:</span><span class="sxs-lookup"><span data-stu-id="69fa2-145">Sections:</span></span>

- [<span data-ttu-id="69fa2-146">Настройка проекта</span><span class="sxs-lookup"><span data-stu-id="69fa2-146">Set up the Project</span></span>](#setup)
- [<span data-ttu-id="69fa2-147">Запуск образца</span><span class="sxs-lookup"><span data-stu-id="69fa2-147">Run the Sample</span></span>](#run)
- [<span data-ttu-id="69fa2-148">Изучите код</span><span class="sxs-lookup"><span data-stu-id="69fa2-148">Examine the Code</span></span>](#code)
- [<span data-ttu-id="69fa2-149">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="69fa2-149">Next Steps</span></span>](#next)

<a id="setup"></a>

## <a name="set-up-the-project"></a><span data-ttu-id="69fa2-150">Настройка проекта</span><span class="sxs-lookup"><span data-stu-id="69fa2-150">Set up the Project</span></span>

<span data-ttu-id="69fa2-151">В этом разделе показано, как использовать Visual Studio 2013 и SignalR версии 2 для создания пустой веб-приложения ASP.NET, добавьте SignalR и создание приложения чата.</span><span class="sxs-lookup"><span data-stu-id="69fa2-151">This section shows how to use Visual Studio 2013 and SignalR version 2 to create an empty ASP.NET web application, add SignalR, and create the chat application.</span></span>

<span data-ttu-id="69fa2-152">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="69fa2-152">Prerequisites:</span></span>

- <span data-ttu-id="69fa2-153">Visual Studio 2013.</span><span class="sxs-lookup"><span data-stu-id="69fa2-153">Visual Studio 2013.</span></span> <span data-ttu-id="69fa2-154">Если у вас нет Visual Studio, см. в разделе [загрузок ASP.NET](https://www.asp.net/downloads) для получения бесплатной Visual Studio 2013 Express средство разработки.</span><span class="sxs-lookup"><span data-stu-id="69fa2-154">If you do not have Visual Studio, see [ASP.NET Downloads](https://www.asp.net/downloads) to get the free Visual Studio 2013 Express Development Tool.</span></span>

<span data-ttu-id="69fa2-155">Далее используется Visual Studio 2013 для создания пустой веб-приложения ASP.NET и добавить библиотеку SignalR:</span><span class="sxs-lookup"><span data-stu-id="69fa2-155">The following steps use Visual Studio 2013 to create an ASP.NET Empty Web Application and add the SignalR library:</span></span>

1. <span data-ttu-id="69fa2-156">В Visual Studio создайте веб-приложения ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="69fa2-156">In Visual Studio, create an ASP.NET Web Application.</span></span>

    ![Создание веб-](tutorial-getting-started-with-signalr/_static/image2.png)
2. <span data-ttu-id="69fa2-158">В **новый проект ASP.NET** окне оставьте **пустой** и нажмите кнопку **Создание проекта**.</span><span class="sxs-lookup"><span data-stu-id="69fa2-158">In the **New ASP.NET Project** window, leave **Empty** selected and click **Create Project**.</span></span>

    ![Создать пустой веб-узел](tutorial-getting-started-with-signalr/_static/image3.png)
3. <span data-ttu-id="69fa2-160">В **обозревателе решений**, щелкните правой кнопкой мыши проект, выберите **Add | Класс концентратора SignalR (v2)**.</span><span class="sxs-lookup"><span data-stu-id="69fa2-160">In **Solution Explorer**, right-click the project, select **Add | SignalR Hub Class (v2)**.</span></span> <span data-ttu-id="69fa2-161">Назовите класс **ChatHub.cs** и добавьте его в проект.</span><span class="sxs-lookup"><span data-stu-id="69fa2-161">Name the class **ChatHub.cs** and add it to the project.</span></span> <span data-ttu-id="69fa2-162">На этом шаге создается **ChatHub** и добавляет в проект набор файлов сценариев и ссылки на сборки, которые поддерживают SignalR.</span><span class="sxs-lookup"><span data-stu-id="69fa2-162">This step creates the **ChatHub** class and adds to the project a set of script files and assembly references that support SignalR.</span></span>

    > [!NOTE]
    > <span data-ttu-id="69fa2-163">Можно также добавить SignalR в проект, открыв **Сервис > Диспетчер пакетов NuGet > консоль диспетчера пакетов** и выполнив команду:</span><span class="sxs-lookup"><span data-stu-id="69fa2-163">You can also add SignalR to a project by opening the **Tools > NuGet Package Manager > Package Manager Console** and running a command:</span></span>

    `install-package Microsoft.AspNet.SignalR`

    <span data-ttu-id="69fa2-164">Если вы используете консоль для добавления SignalR, создайте класс концентратора SignalR как отдельный шаг после добавления SignalR.</span><span class="sxs-lookup"><span data-stu-id="69fa2-164">If you use the console to add SignalR, create the SignalR hub class as a separate step after you add SignalR.</span></span>

    > [!NOTE]
    > <span data-ttu-id="69fa2-165">Если вы используете Visual Studio 2012, **класс концентратора SignalR (v2)** шаблон не будет доступен.</span><span class="sxs-lookup"><span data-stu-id="69fa2-165">If you are using Visual Studio 2012, the **SignalR Hub Class (v2)** template will not be available.</span></span> <span data-ttu-id="69fa2-166">Вы можете добавить простого **класс** вызывается `ChatHub` вместо этого.</span><span class="sxs-lookup"><span data-stu-id="69fa2-166">You can add a plain **Class** called `ChatHub` instead.</span></span>
4. <span data-ttu-id="69fa2-167">В **обозревателе решений**, разверните узел скриптов.</span><span class="sxs-lookup"><span data-stu-id="69fa2-167">In **Solution Explorer**, expand the Scripts node.</span></span> <span data-ttu-id="69fa2-168">Библиотеки скрипта для jQuery и SignalR, отображаются в проект.</span><span class="sxs-lookup"><span data-stu-id="69fa2-168">Script libraries for jQuery and SignalR are visible in the project.</span></span>
5. <span data-ttu-id="69fa2-169">Замените код в новом **ChatHub** класса следующим кодом.</span><span class="sxs-lookup"><span data-stu-id="69fa2-169">Replace the code in the new **ChatHub** class with the following code.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample1.cs)]
6. <span data-ttu-id="69fa2-170">В **обозревателе решений**, щелкните правой кнопкой мыши проект, а затем нажмите кнопку **Add | Класс запуска OWIN**.</span><span class="sxs-lookup"><span data-stu-id="69fa2-170">In **Solution Explorer**, right-click the project, then click **Add | OWIN Startup Class**.</span></span> <span data-ttu-id="69fa2-171">Назовите новый класс `Startup` и нажмите кнопку ОК.</span><span class="sxs-lookup"><span data-stu-id="69fa2-171">Name the new class `Startup` and click OK.</span></span>

    > [!NOTE]
    > <span data-ttu-id="69fa2-172">Если вы используете Visual Studio 2012, **класс запуска OWIN** шаблон не будет доступен.</span><span class="sxs-lookup"><span data-stu-id="69fa2-172">If you are using Visual Studio 2012, the **OWIN Startup Class** template will not be available.</span></span> <span data-ttu-id="69fa2-173">Вы можете добавить простого **класс** вызывается `Startup` вместо этого.</span><span class="sxs-lookup"><span data-stu-id="69fa2-173">You can add a plain **Class** called `Startup` instead.</span></span>
7. <span data-ttu-id="69fa2-174">Измените содержимое нового класса запуска следующим образом.</span><span class="sxs-lookup"><span data-stu-id="69fa2-174">Change the contents of the new Startup class to the following.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample2.cs)]
8. <span data-ttu-id="69fa2-175">В **обозревателе решений**, щелкните правой кнопкой мыши проект, а затем нажмите кнопку **Add | HTML-страницу**.</span><span class="sxs-lookup"><span data-stu-id="69fa2-175">In **Solution Explorer**, right-click the project, then click **Add | HTML Page**.</span></span> <span data-ttu-id="69fa2-176">Имя новой страницы `index.html`.</span><span class="sxs-lookup"><span data-stu-id="69fa2-176">Name the new page `index.html`.</span></span>
    >[!NOTE]
    ><span data-ttu-id="69fa2-177">Может потребоваться изменить номера версий для ссылок на библиотеки JQuery и SignalR</span><span class="sxs-lookup"><span data-stu-id="69fa2-177">You might need to change the version numbers for the references to JQuery and SignalR libraries</span></span>
9. <span data-ttu-id="69fa2-178">В **обозревателе решений**, щелкните правой кнопкой мыши только что созданный HTML-страницы и нажмите кнопку **задать в качестве начальной страницы**.</span><span class="sxs-lookup"><span data-stu-id="69fa2-178">In **Solution Explorer**, right-click the HTML page you just created and click **Set as Start Page**.</span></span>
10. <span data-ttu-id="69fa2-179">Замените код по умолчанию в HTML-страницу следующий код.</span><span class="sxs-lookup"><span data-stu-id="69fa2-179">Replace the default code in the HTML page with the following code.</span></span>

    > [!NOTE]
    > <span data-ttu-id="69fa2-180">Можно установить более позднюю версию сценариев SignalR с диспетчер пакетов.</span><span class="sxs-lookup"><span data-stu-id="69fa2-180">A later version of the SignalR scripts may be installed by the package manager.</span></span> <span data-ttu-id="69fa2-181">Убедитесь, что ссылки на скрипты соответствуют версиям файлов сценариев в проекте (они будет другим, если вы добавили SignalR с помощью NuGet вместо добавления концентратору.)</span><span class="sxs-lookup"><span data-stu-id="69fa2-181">Verify that the script references below correspond to the versions of the script files in the project (they will be different if you added SignalR using NuGet rather than adding a hub.)</span></span>

    [!code-html[Main](tutorial-getting-started-with-signalr/samples/sample3.html)]
11. <span data-ttu-id="69fa2-182">**Сохранить все** для проекта.</span><span class="sxs-lookup"><span data-stu-id="69fa2-182">**Save All** for the project.</span></span>

<a id="run"></a>

## <a name="run-the-sample"></a><span data-ttu-id="69fa2-183">Запуск образца</span><span class="sxs-lookup"><span data-stu-id="69fa2-183">Run the Sample</span></span>

1. <span data-ttu-id="69fa2-184">Нажмите клавишу F5, чтобы запустить проект в режиме отладки.</span><span class="sxs-lookup"><span data-stu-id="69fa2-184">Press F5 to run the project in debug mode.</span></span> <span data-ttu-id="69fa2-185">HTML-страница загружается в экземпляре обозревателя и предлагает ввести имя пользователя.</span><span class="sxs-lookup"><span data-stu-id="69fa2-185">The HTML page loads in a browser instance and prompts for a user name.</span></span>

    ![Введите имя пользователя](tutorial-getting-started-with-signalr/_static/image4.png)
2. <span data-ttu-id="69fa2-187">Введите имя пользователя.</span><span class="sxs-lookup"><span data-stu-id="69fa2-187">Enter a user name.</span></span>
3. <span data-ttu-id="69fa2-188">Скопируйте URL-адрес в адресной строке браузера и использовать его для открытия двух экземпляров дополнительные браузера.</span><span class="sxs-lookup"><span data-stu-id="69fa2-188">Copy the URL from the address line of the browser and use it to open two more browser instances.</span></span> <span data-ttu-id="69fa2-189">В каждом экземпляре обозревателя введите уникальное имя пользователя.</span><span class="sxs-lookup"><span data-stu-id="69fa2-189">In each browser instance, enter a unique user name.</span></span>
4. <span data-ttu-id="69fa2-190">В каждом экземпляре браузера добавьте комментарий и нажмите кнопку **отправки**.</span><span class="sxs-lookup"><span data-stu-id="69fa2-190">In each browser instance, add a comment and click **Send**.</span></span> <span data-ttu-id="69fa2-191">Комментарии должны отображаться в все экземпляры браузера.</span><span class="sxs-lookup"><span data-stu-id="69fa2-191">The comments should display in all browser instances.</span></span>

    > [!NOTE]
    > <span data-ttu-id="69fa2-192">Это приложение на простом чате не ведет контекста обсуждения на сервере.</span><span class="sxs-lookup"><span data-stu-id="69fa2-192">This simple chat application does not maintain the discussion context on the server.</span></span> <span data-ttu-id="69fa2-193">Концентратор осуществляет широковещательную передачу комментарии для всех текущих пользователей.</span><span class="sxs-lookup"><span data-stu-id="69fa2-193">The hub broadcasts comments to all current users.</span></span> <span data-ttu-id="69fa2-194">Пользователи, беседа позже будет видеть сообщения добавлены с момента их присоединения к.</span><span class="sxs-lookup"><span data-stu-id="69fa2-194">Users who join the chat later will see messages added from the time they join.</span></span>

    <span data-ttu-id="69fa2-195">На следующем снимке экрана показано приложение чата в трех экземплярах обозревателя, все из которых обновляются, когда один экземпляр отправляет сообщение:</span><span class="sxs-lookup"><span data-stu-id="69fa2-195">The following screen shot shows the chat application running in three browser instances, all of which are updated when one instance sends a message:</span></span>

    ![Браузеры чата](tutorial-getting-started-with-signalr/_static/image5.png)
5. <span data-ttu-id="69fa2-197">В **обозревателе решений**, проверять **документы скриптов** узел для запущенного приложения.</span><span class="sxs-lookup"><span data-stu-id="69fa2-197">In **Solution Explorer**, inspect the **Script Documents** node for the running application.</span></span> <span data-ttu-id="69fa2-198">Файл скрипта с именем **концентраторов** , библиотека SignalR динамически создает во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="69fa2-198">There is a script file named **hubs** that the SignalR library dynamically generates at runtime.</span></span> <span data-ttu-id="69fa2-199">Этот файл управляет обменом данных между jQuery-сценарий и код на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="69fa2-199">This file manages the communication between jQuery script and server-side code.</span></span>

    ![](tutorial-getting-started-with-signalr/_static/image6.png)

<a id="code"></a>

## <a name="examine-the-code"></a><span data-ttu-id="69fa2-200">Изучите код</span><span class="sxs-lookup"><span data-stu-id="69fa2-200">Examine the Code</span></span>

<span data-ttu-id="69fa2-201">Приложение чата SignalR демонстрирует две основные задачи разработки SignalR: создание концентратора в качестве основной координации объекта на сервере и с помощью библиотеки jQuery SignalR для отправки и получения сообщений.</span><span class="sxs-lookup"><span data-stu-id="69fa2-201">The SignalR chat application demonstrates two basic SignalR development tasks: creating a hub as the main coordination object on the server, and using the SignalR jQuery library to send and receive messages.</span></span>

### <a name="signalr-hubs"></a><span data-ttu-id="69fa2-202">Концентраторы SignalR</span><span class="sxs-lookup"><span data-stu-id="69fa2-202">SignalR Hubs</span></span>

<span data-ttu-id="69fa2-203">В следующем образце кода **ChatHub** класс является производным от **Microsoft.AspNet.SignalR.Hub** класса.</span><span class="sxs-lookup"><span data-stu-id="69fa2-203">In the code sample the **ChatHub** class derives from the **Microsoft.AspNet.SignalR.Hub** class.</span></span> <span data-ttu-id="69fa2-204">Наследование от **концентратора** класс является эффективным методом для построения приложения SignalR.</span><span class="sxs-lookup"><span data-stu-id="69fa2-204">Deriving from the **Hub** class is a useful way to build a SignalR application.</span></span> <span data-ttu-id="69fa2-205">Можно создавать открытые методы в классе концентратора и затем получить доступ к этих методов, вызывая их с помощью сценариев на веб-странице.</span><span class="sxs-lookup"><span data-stu-id="69fa2-205">You can create public methods on your hub class and then access those methods by calling them from scripts in a web page.</span></span>

<span data-ttu-id="69fa2-206">В коде чата, клиенты вызывают **ChatHub.Send** метод для отправки нового сообщения.</span><span class="sxs-lookup"><span data-stu-id="69fa2-206">In the chat code, clients call the **ChatHub.Send** method to send a new message.</span></span> <span data-ttu-id="69fa2-207">Концентратор в свою очередь отправляет сообщение всем клиентам, вызвав **Clients.All.broadcastMessage**.</span><span class="sxs-lookup"><span data-stu-id="69fa2-207">The hub in turn sends the message to all clients by calling **Clients.All.broadcastMessage**.</span></span>

<span data-ttu-id="69fa2-208">**Отправки** метод демонстрирует несколько вещей понятия:</span><span class="sxs-lookup"><span data-stu-id="69fa2-208">The **Send** method demonstrates several hub concepts :</span></span>

- <span data-ttu-id="69fa2-209">Объявления открытых методов концентратора, таким образом, клиенты могут вызывать их.</span><span class="sxs-lookup"><span data-stu-id="69fa2-209">Declare public methods on a hub so that clients can call them.</span></span>
- <span data-ttu-id="69fa2-210">Используйте **Microsoft.AspNet.SignalR.Hub.Clients** динамических свойств для доступа к всех клиентов, подключенных к этой вещей.</span><span class="sxs-lookup"><span data-stu-id="69fa2-210">Use the **Microsoft.AspNet.SignalR.Hub.Clients** dynamic property to access all clients connected to this hub.</span></span>
- <span data-ttu-id="69fa2-211">Вызов функции на стороне клиента (например, `broadcastMessage` функции) для обновления клиентов.</span><span class="sxs-lookup"><span data-stu-id="69fa2-211">Call a function on the client (such as the `broadcastMessage` function) to update clients.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample4.cs)]

### <a name="signalr-and-jquery"></a><span data-ttu-id="69fa2-212">SignalR и jQuery</span><span class="sxs-lookup"><span data-stu-id="69fa2-212">SignalR and jQuery</span></span>

<span data-ttu-id="69fa2-213">HTML-страницы в примере кода показано, как использовать библиотеку jQuery SignalR для взаимодействия с центром SignalR.</span><span class="sxs-lookup"><span data-stu-id="69fa2-213">The HTML page in the code sample shows how to use the SignalR jQuery library to communicate with a SignalR hub.</span></span> <span data-ttu-id="69fa2-214">Основные задачи в коде объявляется прокси-сервер для ссылки на центр, объявление функции, который сервер может обратиться к содержимому Push-уведомлений для клиентов и устанавливается подключение для отправки сообщений в концентратор.</span><span class="sxs-lookup"><span data-stu-id="69fa2-214">The essential tasks in the code are declaring a proxy to reference the hub, declaring a function that the server can call to push content to clients, and starting a connection to send messages to the hub.</span></span>

<span data-ttu-id="69fa2-215">Следующий код объявляет ссылку на прокси-сервера концентратора.</span><span class="sxs-lookup"><span data-stu-id="69fa2-215">The following code declares a reference to a hub proxy.</span></span>

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample5.js)]

> [!NOTE]
> <span data-ttu-id="69fa2-216">В JavaScript ссылку на класс сервера и его членах находится в верхний регистр.</span><span class="sxs-lookup"><span data-stu-id="69fa2-216">In JavaScript the reference to the server class and its members is in camel case.</span></span> <span data-ttu-id="69fa2-217">Ссылается на пример кода C# **ChatHub** класс в JavaScript в виде **chatHub**.</span><span class="sxs-lookup"><span data-stu-id="69fa2-217">The code sample references the C# **ChatHub** class in JavaScript as **chatHub**.</span></span>


<span data-ttu-id="69fa2-218">Следующий код является, как создать функцию обратного вызова в скрипте.</span><span class="sxs-lookup"><span data-stu-id="69fa2-218">The following code is how you create a callback function in the script.</span></span> <span data-ttu-id="69fa2-219">Класс концентратора на сервере вызывает эту функцию для отправки обновлений содержимого для каждого клиента.</span><span class="sxs-lookup"><span data-stu-id="69fa2-219">The hub class on the server calls this function to push content updates to each client.</span></span> <span data-ttu-id="69fa2-220">Две строки, кодировать в HTML содержимое перед его отображением являются необязательными и Показать простой способ предотвратить внедрение скрипта.</span><span class="sxs-lookup"><span data-stu-id="69fa2-220">The two lines that HTML encode the content before displaying it are optional and show a simple way to prevent script injection.</span></span>

[!code-html[Main](tutorial-getting-started-with-signalr/samples/sample6.html)]

<span data-ttu-id="69fa2-221">Ниже показано, как открыть соединение с концентратором.</span><span class="sxs-lookup"><span data-stu-id="69fa2-221">The following code shows how to open a connection with the hub.</span></span> <span data-ttu-id="69fa2-222">Код запускает подключение, а затем передает его функции, чтобы обрабатывать событие щелчка на **отправки** кнопку в HTML-страницы.</span><span class="sxs-lookup"><span data-stu-id="69fa2-222">The code starts the connection and then passes it a function to handle the click event on the **Send** button in the HTML page.</span></span>

> [!NOTE]
> <span data-ttu-id="69fa2-223">Такой подход гарантирует, что соединение установлено, перед выполнением обработчика событий.</span><span class="sxs-lookup"><span data-stu-id="69fa2-223">This approach ensures that the connection is established before the event handler executes.</span></span>


[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample7.js)]

<a id="next"></a>

## <a name="next-steps"></a><span data-ttu-id="69fa2-224">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="69fa2-224">Next Steps</span></span>

<span data-ttu-id="69fa2-225">Вы узнали, что SignalR — это платформа для построения в режиме реального времени веб-приложений.</span><span class="sxs-lookup"><span data-stu-id="69fa2-225">You learned that SignalR is a framework for building real-time web applications.</span></span> <span data-ttu-id="69fa2-226">Вы также узнали несколько задач разработки SignalR: Добавление в приложение ASP.NET SignalR, как создать класс концентратора и как отправлять и получать сообщения из концентратора.</span><span class="sxs-lookup"><span data-stu-id="69fa2-226">You also learned several SignalR development tasks: how to add SignalR to an ASP.NET application, how to create a hub class, and how to send and receive messages from the hub.</span></span>

<span data-ttu-id="69fa2-227">Пошаговое руководство о том, как развернуть пример приложения SignalR в Azure, см. в разделе [с помощью SignalR с веб-приложениями в службе приложений Azure](../deployment/using-signalr-with-azure-web-sites.md).</span><span class="sxs-lookup"><span data-stu-id="69fa2-227">For a walkthrough on how to deploy the sample SignalR application to Azure, see [Using SignalR with Web Apps in Azure App Service](../deployment/using-signalr-with-azure-web-sites.md).</span></span> <span data-ttu-id="69fa2-228">Подробные сведения о развертывании веб-проекта Visual Studio для веб-сайта Windows Azure см. в разделе [создать веб-приложение ASP.NET в службе приложений Azure](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).</span><span class="sxs-lookup"><span data-stu-id="69fa2-228">For detailed information about how to deploy a Visual Studio web project to a Windows Azure Web Site, see [Create an ASP.NET web app in Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).</span></span>

<span data-ttu-id="69fa2-229">Для более сложных концепций разработки SignalR см. на следующих узлах SignalR исходный код и ресурсы:</span><span class="sxs-lookup"><span data-stu-id="69fa2-229">To learn more advanced SignalR developments concepts, visit the following sites for SignalR source code and resources:</span></span>

- [<span data-ttu-id="69fa2-230">Проект SignalR</span><span class="sxs-lookup"><span data-stu-id="69fa2-230">SignalR Project</span></span>](http://signalr.net)
- [<span data-ttu-id="69fa2-231">SignalR Github и примерами</span><span class="sxs-lookup"><span data-stu-id="69fa2-231">SignalR Github and Samples</span></span>](https://github.com/SignalR/SignalR)
- [<span data-ttu-id="69fa2-232">Вики-сайте SignalR</span><span class="sxs-lookup"><span data-stu-id="69fa2-232">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)
