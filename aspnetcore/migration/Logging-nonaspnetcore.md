---
title: Перенос из Microsoft.Extensions.Logging в 2.1 и 2.2 или 3.0
author: pakrym
description: Узнайте, как перенести приложение ASP.NET Core, которое использует Microsoft.Extensions.Logging из 2.1, 2.2 или 3.0.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 2519ddc02cee5978483bcaef4341a52aad3ba2a6
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099474"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="67ed3-103">Перенос из Microsoft.Extensions.Logging в 2.1 и 2.2 или 3.0</span><span class="sxs-lookup"><span data-stu-id="67ed3-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="67ed3-104">В этой статье приведены общие шаги по переносу приложений ASP.NET Core, который использует `Microsoft.Extensions.Logging` из 2.1, 2.2 или 3.0.</span><span class="sxs-lookup"><span data-stu-id="67ed3-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="67ed3-105">Чтобы 2.1 и 2.2</span><span class="sxs-lookup"><span data-stu-id="67ed3-105">2.1 to 2.2</span></span>

<span data-ttu-id="67ed3-106">Вручную создайте `ServiceCollection` и вызвать `AddLogging`.</span><span class="sxs-lookup"><span data-stu-id="67ed3-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="67ed3-107">Пример 2.1.</span><span class="sxs-lookup"><span data-stu-id="67ed3-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="67ed3-108">2,2 пример.</span><span class="sxs-lookup"><span data-stu-id="67ed3-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="67ed3-109">2.1 до 3.0</span><span class="sxs-lookup"><span data-stu-id="67ed3-109">2.1 to 3.0</span></span>

<span data-ttu-id="67ed3-110">В 3.0, используйте `LoggingFactory.Create`.</span><span class="sxs-lookup"><span data-stu-id="67ed3-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="67ed3-111">Пример 2.1.</span><span class="sxs-lookup"><span data-stu-id="67ed3-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="67ed3-112">Пример 3.0.</span><span class="sxs-lookup"><span data-stu-id="67ed3-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="67ed3-113">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="67ed3-113">Additional resources</span></span>

<xref:fundamentals/logging/index>