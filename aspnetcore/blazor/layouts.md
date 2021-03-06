---
title: Макеты Blazor в ASP.NET Core
author: guardrex
description: Узнайте, как создавать многократно используемые компоненты макета для Blazor приложений.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 5b6e1c7ceb4a6e41230e31bbe379bde1bb0a8286
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78647926"
---
# <a name="aspnet-core-opno-locblazor-layouts"></a>Макеты Blazor в ASP.NET Core

Авторы: [Райнер Стропек](https://www.timecockpit.com) (Rainer Stropek) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)

Некоторые элементы приложения, такие как меню, сообщения об авторских правах и логотипы компании, обычно являются частью общего макета приложения и используются каждым компонентом в приложении. Копирование кода этих элементов во все компоненты приложения не является эффективным подходом &mdash; каждый раз, когда одному из элементов требуется обновление, требуется обновлять каждый компонент. Такое дублирование сложно поддерживать, и это может привести к несогласованному содержимому с течением времени. Для решения этой проблемы используются *макеты*.

Технически макет представляет собой просто другой компонент. Макет определяется в шаблоне Razor или в коде C# и может использовать [привязки данных](xref:blazor/data-binding), [внедрения зависимостей](xref:blazor/dependency-injection) и другие сценарии компонентов.

Чтобы превратить *компонент* в *макет*, компонент:

* Наследует от `LayoutComponentBase`, который определяет свойство `Body` для отображаемого содержимого внутри макета.
* Использует синтаксис Razor `@Body` для указания расположения в разметке макета, в которой отображается содержимое.

В следующем примере кода показан шаблон Razor компонента макета *MainLayout.razor*. Макет наследует `LayoutComponentBase` и задает `@Body` между панелью навигации и нижним колонтитулом:

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

В приложении на основе одного из шаблонов приложений Blazor компонент `MainLayout` (*MainLayout.razor*) находится в папке *Shared* приложения.

## <a name="default-layout"></a>Макет по умолчанию

Укажите макет приложения по умолчанию в компоненте `Router` в файле *App.razor* приложения. Следующий компонент `Router`, предоставляемый шаблонами Blazor по умолчанию, задает для макета по умолчанию компонент `MainLayout`:

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

Чтобы предоставить макет по умолчанию для содержимого `NotFound`, укажите `LayoutView` для содержимого `NotFound`:

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

Дополнительные сведения о компоненте `Router` см. в разделе <xref:blazor/routing>.

Указание макета в качестве макета по умолчанию в маршрутизаторе достаточно полезно, так как в этом случае его можно переопределить отдельно для каждого компонента или папки. Для настройки макета приложения по умолчанию предпочтительнее использовать маршрутизатор, поскольку это наиболее общий способ.

## <a name="specify-a-layout-in-a-component"></a>Указание макета в компоненте

Используйте директиву Razor `@layout`, чтобы применить макет к компоненту. Компилятор преобразует `@layout` в `LayoutAttribute`, который применяется к классу компонента.

Содержимое следующего компонента `MasterList` вставляется в `MasterLayout` в позиции `@Body`:

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

Указание макета непосредственно в компоненте переопределяет *макет по умолчанию*, заданный в маршрутизаторе, или директиву `@layout`, импортированную из файла *_Imports.razor*.

## <a name="centralized-layout-selection"></a>Централизованный выбор макетов

В каждой папке приложения может дополнительно содержаться файл шаблона с именем *_Imports.razor*. Компилятор включает директивы, указанные в файле импорта, во все шаблоны Razor в одной и той же папке и рекурсивно во всех ее вложенных папках. Таким образом, файл *_Imports.razor*, содержащий `@layout MyCoolLayout`, гарантирует, что все компоненты в папке используют `MyCoolLayout`. Нет необходимости многократно добавлять `@layout MyCoolLayout` во все файлы *RAZOR* в папке и вложенных папках. Директивы `@using` применяются к компонентам таким же образом.

Следующий файл *_Imports.razor*:

* `MyCoolLayout`.
* Все компоненты Razor в одной и той же папке и во всех вложенных папках.
* Пространство имен `BlazorApp1.Data` .
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

Файл *_Imports.razor* аналогичен файлу [_ViewImports.cshtml для представлений и страниц Razor](xref:mvc/views/layout#importing-shared-directives), однако он применяется специально к файлам компонентов Razor.

Указание макета в файле *_Imports.razor* переопределяет макет, указанный в качестве *макета по умолчанию* маршрутизатора.

## <a name="nested-layouts"></a>Вложенные макеты

Приложения могут состоять из вложенных макетов. Компонент может ссылаться на макет, который, в свою очередь, ссылается на другой макет. Например, для создания структуры многоуровневого меню используются вложенные макеты.

В следующем примере показано использование вложенных макетов. Файл *EpisodesComponent.razor* является отображаемым компонентом. Компонент ссылается на `MasterListLayout`:

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

Файл *MasterListLayout.razor* предоставляет `MasterListLayout`. Макет ссылается на другой макет, `MasterLayout`, где он преобразуется для просмотра. `EpisodesComponent` отображается там, где находится `@Body`:

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Наконец, `MasterLayout` в файле *MasterLayout.razor* содержит элементы макета верхнего уровня, такие как заголовок, главное меню и нижний колонтитул. `MasterListLayout` с `EpisodesComponent` отображается там, где находится `@Body`:

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a>Совместное использование макета Razor Pages с интегрированными компонентами

Если маршрутизируемые компоненты интегрированы в приложение Razor Pages, общий макет приложения можно использовать с компонентами. Для получения дополнительной информации см. <xref:blazor/integrate-components>.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:mvc/views/layout>
