---
title: Общие сведения об использовании Blazor в ASP.NET Core
author: guardrex
description: Узнайте больше об использовании Blazor в ASP.NET Core для создания интерактивного клиентского веб-интерфейса с помощью .NET в приложении ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: seoapril2019
ms.date: 04/15/2019
uid: blazor/index
ms.openlocfilehash: a5b12a5c5c10a74ab192d0d67a2ba9a5617232c7
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614600"
---
# <a name="introduction-to-blazor"></a>Введение в Blazor

Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)

## <a name="razor-components"></a>Компоненты Razor

*Компоненты Razor* (модель размещения на стороне сервера Blazor) — это средства для создания интерактивного клиентского веб-интерфейса с помощью .NET, которые предоставляют такие возможности:

* создание многофункциональных интерактивных пользовательских интерфейсов с помощью C# (а не JavaScript);
* совместное использование серверной и клиентской логик приложений, написанных с помощью .NET;
* отображение пользовательского интерфейса в виде HTML-страницы с CSS для широкой поддержки браузеров, в том числе для мобильных устройств.

Компоненты Razor поддерживают базовые функции, которые требуются большинству приложений, в том числе такие:

* Параметры
* Обработка событий
* привязка данных,
* Маршрутизация
* Внедрение зависимостей
* Макеты
* Использование шаблонов
* Каскадные значения

Компоненты Razor отделяют логику отображения компонентов от способов применения обновлений пользовательского интерфейса. Компоненты Razor в ASP.NET Core в .NET Core 3.0 реализуют поддержку размещения компонентов Razor на сервере в приложении ASP.NET Core. Обновления пользовательского интерфейса обрабатываются через подключение SignalR.

Среда выполнения:

* обрабатывает отправку событий пользовательского интерфейса из браузера на сервер;
* применяет обновления пользовательского интерфейса, отправленные сервером браузеру после выполнения компонентов.

Подключение, используемое компонентами Razor для обмена данными с браузером, также применяется для обработки вызовов взаимодействия JavaScript.

![Компоненты Razor выполняют код .NET на сервере и взаимодействуют с моделью DOM на стороне клиента через подключение SignalR](index/_static/aspnet-core-razor-components.png)

Для получения дополнительной информации см. <xref:blazor/hosting-models#server-side-hosting-model>.

## <a name="components"></a>Компоненты

*Компонент Razor* — это часть пользовательского интерфейса, например страница, диалоговое окно или форма ввода данных. Компоненты обрабатывают для пользователей события и определяют гибкую логику отображения пользовательского интерфейса. Их можно вкладывать друг в друга и использовать повторно.

Компоненты — это классы .NET, встроенные в сборки .NET, которые можно совместно использовать и распространять в виде пакетов NuGet. Этот класс обычно записывается в виде страницы разметки Razor с расширением файла *.razor* (Компоненты Razor) или страницы разметки Razor с расширением файла *.cshtml* (Blazor).

[Razor](xref:mvc/views/razor) — это синтаксис для объединения разметки HTML с кодом C#. Razor предназначен для повышения производительности разработчика и позволяет разработчику переключаться между разметкой и C# в одном файле с поддержкой [IntelliSense](/visualstudio/ide/using-intellisense). Razor Pages и представления MVC также используют Razor. В отличие от Razor Pages и представлений MVC, которые созданы на базе модели "запрос — ответ", компоненты используются исключительно для обработки композиции пользовательского интерфейса. Компоненты Razor можно использовать исключительно для клиентской логики и композиции пользовательского интерфейса.

Следующая разметка является примером настраиваемого диалогового компонента:

```cshtml
<div>
    <h2>@Title</h2>
    @BodyContent
    <button onclick="@OnOK">OK</button>
</div>

@functions {
    public string Title { get; set; }
    public RenderFragment BodyContent { get; set; }
    public Action OnOK { get; set; }
}
```

Когда этот компонент используется в другом месте в приложении, IntelliSense ускоряет разработку с помощью выполнения синтаксиса и параметров.

Компоненты преобразуются в представление DOM браузера в памяти, которое называется *деревом визуализации*. Его затем можно использовать для гибкого и эффективного обновления пользовательского интерфейса.

## <a name="javascript-interop"></a>Взаимодействие с JavaScript

Для приложений, требующих сторонние библиотеки JavaScript и API браузера, компоненты взаимодействуют с JavaScript. Компоненты могут использовать любую библиотеку или API, которые может использовать JavaScript. Код C# может вызывать код JavaScript, а код JavaScript — код C#. Дополнительные сведения см. в разделе [Взаимодействие с JavaScript](xref:blazor/javascript-interop).

## <a name="code-sharing-and-net-standard"></a>Совместное использование кода и .NET Standard

Приложения могут ссылаться и использовать существующие библиотеки [.NET Standard](/dotnet/standard/net-standard). .NET Standard — это формальная спецификация API-интерфейсов .NET, которые доступны во всех реализациях .NET. Blazor реализует .NET Standard 2.0. API, которые нельзя использовать в веб-браузере (например, для доступа к файловой системе, открытия сокета, работы с потоками и других операций), выдают исключение <xref:System.PlatformNotSupportedException>. Библиотеки классов .NET Standard можно использовать на разных платформах .NET, включая Blazor, .NET Framework, .NET Core, Mono, Xamarin и Unity.

## <a name="blazor"></a>Blazor

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

Blazor — это платформа одностраничных приложений для создания интерактивных клиентских веб-приложений с использованием .NET. Blazor использует открытые веб-стандарты без подключаемых модулей или транспиляции кода. Blazor работает во всех современных веб-браузерах, включая браузеры мобильных устройств.

Использование .NET в браузере для веб-разработки на стороне клиента дает множество преимуществ:

* **Язык C#**: создавайте код на C#, а не на JavaScript.
* **Экосистема .NET**: используйте существующую экосистему библиотек .NET.
* **Полностековая разработка**: совместно используйте серверную и клиентскую логики.
* **Скорость и масштабируемость**: NET обеспечивает производительность, надежность и безопасность.
* **Ведущее в отрасли средства**: сохраняйте высокий уровень продуктивности с помощью Visual Studio в Windows, Linux и macOS.
* **Стабильность и согласованность**:  в основе лежит общий набор языков, платформ и инструментов, которые отличаются стабильностью, многофункциональностью и простотой использования.

Выполнение кода .NET в веб-браузерах становится возможным благодаря технологии [WebAssembly](http://webassembly.org) (сокращенно *wasm*). WebAssembly — это открытый веб-стандарт, который поддерживается в веб-браузерах без подключаемых модулей. WebAssembly — это компактный формат байт-кода, оптимизированный для быстрой загрузки и максимального быстродействия.

Код WebAssembly может использовать все возможности браузера с помощью взаимодействия с JavaScript. В то же время код .NET, запускаемый с использованием WebAssembly, выполняется в той же доверенной песочнице, что и код JavaScript, для предотвращения вредоносных действий на клиентском компьютере.

![Blazor выполняет код .NET в браузере с WebAssembly.](index/_static/blazor.png)

Когда приложение Blazor создано и запущено в браузере:

* Файлы кода C# и файлы Razor компилируются в сборки .NET.
* Сборки и среда выполнения .NET загружаются в браузер.
* Blazor осуществляет начальную загрузку среды выполнения .NET и настраивает ее для загрузки сборок для приложения. Операции с моделью DOM и вызовы API браузера обрабатываются средой выполнения Blazor через взаимодействие с JavaScript.

Blazor поддерживает базовые функции, в которых нуждаются большинство приложений, в том числе такие:

* Параметры
* Обработка событий
* привязка данных,
* Маршрутизация
* Внедрение зависимостей
* Макеты
* Использование шаблонов
* Каскадные значения

Чтобы уменьшить размер скачиваемого приложения, [компоновщик промежуточного языка (IL)](xref:host-and-deploy/blazor/configure-linker) отсекает неиспользуемый код от приложения при его публикации.

Blazor предполагает размещение на стороне клиента. Так как Blazor отделяет логику отображения компонента от способов применения обновлений пользовательского интерфейса, Blazor можно размещать разными способами. Используйте [Компоненты Razor](#razor-components) в ASP.NET Core, чтобы разместить их на сервере в приложении ASP.NET Core с возможностью обработки обновлений пользовательского интерфейса через подключение SignalR. Для получения дополнительной информации см. <xref:blazor/hosting-models#server-side-hosting-model>. 

Размер полезных данных критически важен для производительности и, как следствие, удобства использования приложения. Blazor оптимизирует размер полезных данных, чтобы сократить время скачивания:

* неиспользуемые части сборок .NET удаляются в ходе компиляции;
* HTTP-ответы сжимаются;
* среда выполнения и сборки .NET кэшируются в браузере.

[Компоненты Razor](#razor-components) предоставляют меньший размер полезных данных, чем Blazor, поддерживая сборки .NET, сборку приложения и среду выполнения на стороне сервера. Приложения компонентов Razor передают клиентам только файлы разметки и статическое содержимое.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [WebAssembly](http://webassembly.org/)
* [Руководство по языку C#](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)