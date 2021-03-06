---
title: Создание первого приложения Blazor
author: guardrex
description: Узнайте, как создать приложение Blazor по шагам.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/20/2020
no-loc:
- Blazor
uid: tutorials/first-blazor-app
ms.openlocfilehash: 138057c2ceb9ed01bdf958c01f5cf2275387df23
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989437"
---
# <a name="build-your-first-opno-locblazor-app"></a>Создание первого приложения Blazor

Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

В этом учебнике показано, как создать и изменить приложение Blazor.

## <a name="build-components"></a>Сборка компонентов

1. Следуйте указаниям из статьи <xref:blazor/get-started>, чтобы создать проект Blazor для этого учебника. Назовите проект *ToDoList*.

1. Перейдите на каждую из трех страниц, размещенных в папке *Pages*: домашнюю, счетчика и получения данных. Эти страницы реализованы в виде файлов компонентов Razor *Index.razor*, *Counter.razor* и *FetchData.razor*.

1. На странице Counter нажмите кнопку **Click me** (Щелкните здесь), чтобы увеличить значение счетчика без обновления страницы. Обычно для увеличения значений счетчика на веб-странице требуется код JavaScript. Благодаря Blazor можно использовать вместо него C#.

1. Изучите реализацию компонента `Counter` в файле *Counter.razor*.

   *Pages/Counter.razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   Пользовательский интерфейс компонента `Counter` определяется с помощью HTML. Логика динамического отображения (например выражения, циклы и условные выражения) добавляется с помощью встроенного синтаксиса C# под названием [Razor](xref:mvc/views/razor). HTML-разметка и логика отображения C# преобразуются в класс компонента во время сборки. Имя создаваемого класса .NET совпадает с именем файла.

   Элементы класса компонента определяются в блоке `@code`. В блоке `@code` указываются состояние (свойства, поля) и методы компонента для обработки событий или определения другой логики компонента. Эти элементы можно позднее включить в логику отображения компонента и обработки событий.

   При нажатии кнопки **Click me** (Щелкните здесь) выполняются следующие действия:

   * Вызывается обработчик `onclick`, зарегистрированный для компонента `Counter` (метод `IncrementCount`).
   * Компонент `Counter` повторно создает свое дерево отображения.
   * Выполняется сравнение нового и старого деревьев отображения.
   * Применяются только изменения модели DOM (Document Object Model — объектная модель документа). Отображаемое значение счетчика обновляется.

1. Измените логику C# компонента `Counter`, чтобы при нажатии кнопки значение счетчика увеличивалось на две единицы вместо одной.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. Скомпилируйте и запустите приложение, чтобы увидеть изменения. Нажмите кнопку **Click Me** (Щелкните здесь). Значение счетчика увеличится на две единицы.

## <a name="use-components"></a>Использование компонентов

Включите компонент в другой компонент, используя синтаксис HTML.

1. Добавьте компонент `Counter` в компонент `Index` приложения, разместив элемент `<Counter />` внутри компонента `Index` (*Index.razor*).

   Если вы выполняете эту задачу с помощью WebAssembly Blazor, это значит, что компонент `SurveyPrompt` использует компонент `Index`. Замените элемент `<SurveyPrompt>` элементом `<Counter />`. Если вы используете для этой задачи серверное приложение Blazor, добавьте к компоненту `Index` элемент `<Counter />`:

   *Pages/Index.razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. Скомпилируйте и запустите приложение. Компонент `Index` имеет свой собственный счетчик.

## <a name="component-parameters"></a>Параметры компонентов

Компоненты также могут принимать параметры. Параметры для компонентов определяются с помощью открытых свойств в классе компонента с атрибутом `[Parameter]`. Используйте атрибуты, чтобы указать аргументы для компонента в разметке.

1. Обновите код C# для `@code` в компоненте следующим образом:

   * Добавьте открытое свойство `IncrementAmount` с атрибутом `[Parameter]`.
   * Изменение метод `IncrementCount`, чтобы он использовал свойство `IncrementAmount` при увеличении значения `currentCount`.

   *Pages/Counter.razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. Укажите параметр `IncrementAmount` в элементе `<Counter>` для компонента `Index`, используя атрибут. Установите приращение счетчика на десять.

   *Pages/Index.razor*:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. Перезагрузите компонент `Index`. Теперь значение счетчика на домашней странице будет увеличиваться на десять при каждом нажатии кнопки **Click me** (Щелкните здесь). Счетчик в компоненте `Counter` продолжает увеличиваться на единицу.

## <a name="route-to-components"></a>Маршрутизация к компонентам

Директива `@page` в верхней части файла *Counter.razor* указывает, что компонент `Counter` является конечной точкой маршрутизации. Компонент `Counter` обрабатывает запросы, направляемые к `/counter`. Без директивы `@page` этот компонент не будет обрабатывать перенаправленные запросы, но останется доступным для использования другими компонентами.

## <a name="dependency-injection"></a>Внедрение зависимостей

### <a name="opno-locblazor-server-experience"></a>Взаимодействие с сервером Blazor

Если вы используете серверное приложение Blazor, служба `WeatherForecastService` зарегистрирована как [отдельная](xref:fundamentals/dependency-injection#service-lifetimes) в `Startup.ConfigureServices`. Экземпляр этой службы предоставляется для всего приложения посредством [внедрения зависимостей (DI)](xref:fundamentals/dependency-injection):

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

Директива `@inject` используется для внедрения экземпляра службы `WeatherForecastService` в компонент `FetchData`.

*Pages/FetchData.razor*:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

Компонент `FetchData` использует внедренную службу как `ForecastService`, чтобы получить массив объектов `WeatherForecast`.

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="opno-locblazor-webassembly-experience"></a>Взаимодействие с WebAssembly Blazor

Если вы работаете с приложением WebAssembly Blazor, внедряется `HttpClient` для получения данных прогноза погоды из файла *weather.json*, расположенного в папке *wwwroot/sample-data*.

*Pages/FetchData.razor*:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

Цикл [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) используется для отображения каждого экземпляра прогноза в отдельной строке в таблице погоды.

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a>Создание списка дел

Добавьте в приложение новый компонент, представляющий собой простой список дел.

1. Добавьте в приложение новый компонент Razor `Todo` в папке *Pages*. Щелкните в Visual Studio правой кнопкой мыши папку **Pages** и выберите пункт **Добавить** > **Создать элемент** > **Компонент Razor**. Присвойте файлу компонента имя *Todo.Razor*. В других средах разработки необходимо добавить в папку **Pages** пустой файл с именем *Todo.razor*.

1. Задайте начальную разметку компонента.

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. Добавьте компонент `Todo` на панель навигации.

   Компонент `NavMenu` (*Shared/NavMenu.razor*) используется в макете этого приложения. Макетами называются компоненты, которые избавляют от дублирования содержимого в приложении.

   Добавьте `<NavLink>` для компонента `Todo`, включив следующую разметку с элементом списка под существующими элементами списка в файл *Shared/NavMenu.razor*.

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Скомпилируйте и запустите приложение. Перейдите на новую страницу Todo, чтобы убедиться, что ссылка на компонент `Todo` работает правильно.

1. Добавьте в корень проекта файл *TodoItem.cs*, который будет размещать класс для элемента списка дел. Используйте следующий код C# для класса `TodoItem`.

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Снова вернитесь к компоненту `Todo` (*Pages/Todo.razor*).

   * Добавьте поле для элементов списка дел в блоке `@code`. Компонент `Todo` использует это поле для сохранения состояния списка дел.
   * Добавьте разметку неупорядоченного списка и цикл `foreach` для отображения каждого элемента списка дела в виде элемента списка (`<li>`).

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. Приложению необходимы элементы пользовательского интерфейса для добавления элементов в список дел. Добавьте текстовое поле (`<input>`) и кнопку (`<button>`) под неупорядоченным списком (`<ul>...</ul>`).

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Скомпилируйте и запустите приложение. При нажатии кнопки **Add todo** (Добавить задачу) ничего не происходит, так как к кнопке не привязан обработчик событий.

1. Добавьте метод `AddTodo` в компонент `Todo` и зарегистрируйте его для нажатий кнопки с помощью атрибута `@onclick`. Теперь при нажатии кнопки вызывается метод C# `AddTodo`:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. Чтобы получить заголовок нового элемента списка дел, добавьте строку поля `newTodo` в верхней части блока `@code` и привяжите ее к значению вводимого текста с помощью атрибута `bind` в элементе `<input>`.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Обновите метод `AddTodo`, чтобы добавить `TodoItem` с указываемым названием в список. Очистите значение текстового поля, задав пустую строку в качестве значения для `newTodo`.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Скомпилируйте и запустите приложение. Добавьте несколько задач в список дел, чтобы проверить работу нового кода.

1. Вы можете сделать текст заголовка для каждого элемента списка дел редактируемым, а по дополнительному флажку пользователь сможет отслеживать завершение задач. Добавьте флажок для каждого элемента списка дел и привяжите его значение к свойству `IsDone`. Замените `@todo.Title` элементом `<input>`, который привязан к `@todo.Title`.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Чтобы проверить привязку значений, обновите заголовок `<h3>`, чтобы в нем отображалось количество незавершенных дел в списке (`IsDone` имеет значение `false`).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Готовый компонент `Todo` (*Pages/Todo.razor*):

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Скомпилируйте и запустите приложение. Добавьте элементы в список дел, чтобы протестировать новый код.

> [!div class="nextstepaction"]
> <xref:blazor/components>
