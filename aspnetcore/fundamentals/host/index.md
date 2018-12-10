---
title: Веб-узел и универсальный узел в ASP.NET Core
author: guardrex
description: Сведения о веб-узле ASP.NET Core и универсальном узле .NET, которые отвечают за запуск приложений и управление временем существования.
ms.author: riande
ms.custom: mvc,seodec18
ms.date: 08/28/2018
uid: fundamentals/host/index
ms.openlocfilehash: 3e67d8338aa7ac1b1530d0498ee0126d36a8d72b
ms.sourcegitcommit: 49faca2644590fc081d86db46ea5e29edfc28b7b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2018
ms.locfileid: "53121522"
---
# <a name="web-host-and-generic-host-in-aspnet-core"></a><span data-ttu-id="d4ae6-103">Веб-узел и универсальный узел в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d4ae6-103">Web Host and Generic Host in ASP.NET Core</span></span>

<span data-ttu-id="d4ae6-104">Приложения .NET настраивают и запускают *узел*.</span><span class="sxs-lookup"><span data-stu-id="d4ae6-104">.NET apps configure and launch a *host*.</span></span> <span data-ttu-id="d4ae6-105">Узел отвечает за запуск приложения и управление временем существования.</span><span class="sxs-lookup"><span data-stu-id="d4ae6-105">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="d4ae6-106">Для использования доступны два API узла:</span><span class="sxs-lookup"><span data-stu-id="d4ae6-106">Two host APIs are available for use:</span></span>

* <span data-ttu-id="d4ae6-107">[Веб-узел](xref:fundamentals/host/web-host) &ndash; подходит для размещения веб-приложений.</span><span class="sxs-lookup"><span data-stu-id="d4ae6-107">[Web Host](xref:fundamentals/host/web-host) &ndash; Suitable for hosting web apps.</span></span>
* <span data-ttu-id="d4ae6-108">[Универсальный узел](xref:fundamentals/host/generic-host) (ASP.NET Core 2.1 или более поздней версии) &ndash; подходит для размещения других приложений (например, приложений, которые выполняют фоновые задачи).</span><span class="sxs-lookup"><span data-stu-id="d4ae6-108">[Generic Host](xref:fundamentals/host/generic-host) (ASP.NET Core 2.1 or later) &ndash; Suitable for hosting non-web apps (for example, apps that run background tasks).</span></span> <span data-ttu-id="d4ae6-109">В следующем выпуске универсальный узел позволит размещать любые приложения, включая веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="d4ae6-109">In a future release, the Generic Host will be suitable for hosting any kind of app, including web apps.</span></span> <span data-ttu-id="d4ae6-110">Со временем универсальный узел заменит веб-узел.</span><span class="sxs-lookup"><span data-stu-id="d4ae6-110">The Generic Host will eventually replace the Web Host.</span></span>

<span data-ttu-id="d4ae6-111">Для размещения *веб-приложений* ASP.NET Core разработчикам следует использовать веб-узел на основе <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d4ae6-111">For hosting ASP.NET Core *web apps*, developers should use the Web Host based on <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>.</span></span> <span data-ttu-id="d4ae6-112">Если это *не веб-приложения*, для их размещения разработчикам следует использовать универсальный узел на основе <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d4ae6-112">For hosting *non-web apps*, developers should use the Generic Host based on <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span></span>

<xref:fundamentals/host/hosted-services>  
<span data-ttu-id="d4ae6-113">Узнайте, как реализовать фоновые задачи с размещенными службами в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d4ae6-113">Learn how to implement background tasks with hosted services in ASP.NET Core.</span></span>

<xref:fundamentals/configuration/platform-specific-configuration>  
<span data-ttu-id="d4ae6-114">Узнайте, как расширить приложение ASP.NET Core при помощи сборки, заданной по ссылке или без ссылок, используя реализацию <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>.</span><span class="sxs-lookup"><span data-stu-id="d4ae6-114">Discover how to enhance an ASP.NET Core app from a referenced or unreferenced assembly using an <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation.</span></span>
