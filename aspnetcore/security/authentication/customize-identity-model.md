---
title: Настройка модели удостоверений в ASP.NET Core
author: ajcvickers
description: В этой статье описывается, как настроить базовую модель данных Entity Framework Core для ASP.NET Core удостоверения.
ms.author: avickers
ms.date: 07/01/2019
uid: security/authentication/customize_identity_model
ms.openlocfilehash: f549fdff4a416b5fadcb2b1078b051bbab8e402e
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651460"
---
# <a name="identity-model-customization-in-aspnet-core"></a>Настройка модели удостоверений в ASP.NET Core

По [Артур Виккерс](https://github.com/ajcvickers)

ASP.NET Core Identity предоставляет платформу для управления и хранения учетных записей пользователей в ASP.NET Core приложениях. Удостоверение добавляется в проект, если в качестве механизма проверки подлинности выбраны **отдельные учетные записи пользователей** . По умолчанию в удостоверении используется модель основных данных Entity Framework (EF). В этой статье описывается, как настроить модель удостоверений.

## <a name="identity-and-ef-core-migrations"></a>Миграция удостоверений и EF Core

Перед изучением модели полезно понять, как удостоверение работает с [EF Core миграции](/ef/core/managing-schemas/migrations/) для создания и обновления базы данных. На верхнем уровне процесс:

1. Определение или обновление [модели данных в коде](/ef/core/modeling/).
1. Добавьте миграцию, чтобы преобразовать эту модель в изменения, которые могут быть применены к базе данных.
1. Убедитесь, что миграция правильно соответствует вашим намерения.
1. Примените миграцию, чтобы обновить базу данных для синхронизации с моделью.
1. Повторите шаги 1 – 4, чтобы уточнить модель и синхронизировать базу данных.

Для добавления и применения миграций используйте один из следующих способов.

* Окно **консоли диспетчера пакетов** (PMC) при использовании Visual Studio. Дополнительные сведения см. в разделе [средства EF Core PMC](/ef/core/miscellaneous/cli/powershell).
* .NET Core CLI, если используется Командная строка. Дополнительные сведения см. в разделе [EF Core средства командной строки .NET](/ef/core/miscellaneous/cli/dotnet).
* При запуске приложения нажмите кнопку **Применить миграции** на странице ошибки.

В ASP.NET Core имеется обработчик страницы ошибок времени разработки. Обработчик может применять миграции при запуске приложения. Рабочие приложения обычно создают скрипты SQL из миграции и развертывают изменения базы данных в рамках управляемого приложения и развертывания базы данных.

При создании нового приложения, использующего удостоверение, шаги 1 и 2 уже выполнены. Это значит, что начальная модель данных уже существует, а первоначальная миграция была добавлена в проект. К базе данных все еще необходимо применить первоначальную миграцию. Начальную миграцию можно применить с помощью одного из следующих подходов:

* Запустите `Update-Database` в PMC.
* Запустите `dotnet ef database update` в командной оболочке.
* При запуске приложения нажмите кнопку **Применить миграции** на странице ошибки.

Повторите предыдущие шаги по мере внесения изменений в модель.

## <a name="the-identity-model"></a>Модель удостоверений

### <a name="entity-types"></a>Типы сущностей

Модель удостоверений состоит из следующих типов сущностей.

|Тип сущности|Description                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |Представляет пользователя.                                         |
|`Role`     |Представляет роль.                                           |
|`UserClaim`|Представляет утверждение, которому владеет пользователь.                    |
|`UserToken`|Представляет маркер проверки подлинности для пользователя.               |
|`UserLogin`|Связывает пользователя с именем входа.                              |
|`RoleClaim`|Представляет утверждение, которое предоставляется всем пользователям в роли.|
|`UserRole` |Сущность JOIN, связывающая пользователей и роли.               |

### <a name="entity-type-relationships"></a>Отношения типов сущностей

[Типы сущностей](#entity-types) связаны друг с другом следующими способами.

* Каждый `User` может иметь много `UserClaims`.
* Каждый `User` может иметь много `UserLogins`.
* Каждый `User` может иметь много `UserTokens`.
* Каждый `Role` может иметь много связанных `RoleClaims`.
* Каждый `User` может иметь много связанных `Roles`, и каждый `Role` может быть связан с большим количеством `Users`. Это отношение "многие ко многим", для которого требуется таблица соединений в базе данных. Таблица Join представлена сущностью `UserRole`.

### <a name="default-model-configuration"></a>Конфигурация модели по умолчанию

Identity определяет множество *контекстных классов* , которые наследуют от [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) для настройки и использования модели. Эта конфигурация выполняется с помощью [интерфейса API EF Core Code First Fluent](/ef/core/modeling/) в методе [OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) класса Context. Конфигурация по умолчанию:

```csharp
builder.Entity<TUser>(b =>
{
    // Primary key
    b.HasKey(u => u.Id);

    // Indexes for "normalized" username and email, to allow efficient lookups
    b.HasIndex(u => u.NormalizedUserName).HasName("UserNameIndex").IsUnique();
    b.HasIndex(u => u.NormalizedEmail).HasName("EmailIndex");

    // Maps to the AspNetUsers table
    b.ToTable("AspNetUsers");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(u => u.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.UserName).HasMaxLength(256);
    b.Property(u => u.NormalizedUserName).HasMaxLength(256);
    b.Property(u => u.Email).HasMaxLength(256);
    b.Property(u => u.NormalizedEmail).HasMaxLength(256);

    // The relationships between User and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each User can have many UserClaims
    b.HasMany<TUserClaim>().WithOne().HasForeignKey(uc => uc.UserId).IsRequired();

    // Each User can have many UserLogins
    b.HasMany<TUserLogin>().WithOne().HasForeignKey(ul => ul.UserId).IsRequired();

    // Each User can have many UserTokens
    b.HasMany<TUserToken>().WithOne().HasForeignKey(ut => ut.UserId).IsRequired();

    // Each User can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.UserId).IsRequired();
});

builder.Entity<TUserClaim>(b =>
{
    // Primary key
    b.HasKey(uc => uc.Id);

    // Maps to the AspNetUserClaims table
    b.ToTable("AspNetUserClaims");
});

builder.Entity<TUserLogin>(b =>
{
    // Composite primary key consisting of the LoginProvider and the key to use
    // with that provider
    b.HasKey(l => new { l.LoginProvider, l.ProviderKey });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(l => l.LoginProvider).HasMaxLength(128);
    b.Property(l => l.ProviderKey).HasMaxLength(128);

    // Maps to the AspNetUserLogins table
    b.ToTable("AspNetUserLogins");
});

builder.Entity<TUserToken>(b =>
{
    // Composite primary key consisting of the UserId, LoginProvider and Name
    b.HasKey(t => new { t.UserId, t.LoginProvider, t.Name });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(t => t.LoginProvider).HasMaxLength(maxKeyLength);
    b.Property(t => t.Name).HasMaxLength(maxKeyLength);

    // Maps to the AspNetUserTokens table
    b.ToTable("AspNetUserTokens");
});

builder.Entity<TRole>(b =>
{
    // Primary key
    b.HasKey(r => r.Id);

    // Index for "normalized" role name to allow efficient lookups
    b.HasIndex(r => r.NormalizedName).HasName("RoleNameIndex").IsUnique();

    // Maps to the AspNetRoles table
    b.ToTable("AspNetRoles");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(r => r.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.Name).HasMaxLength(256);
    b.Property(u => u.NormalizedName).HasMaxLength(256);

    // The relationships between Role and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each Role can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.RoleId).IsRequired();

    // Each Role can have many associated RoleClaims
    b.HasMany<TRoleClaim>().WithOne().HasForeignKey(rc => rc.RoleId).IsRequired();
});

builder.Entity<TRoleClaim>(b =>
{
    // Primary key
    b.HasKey(rc => rc.Id);

    // Maps to the AspNetRoleClaims table
    b.ToTable("AspNetRoleClaims");
});

builder.Entity<TUserRole>(b =>
{
    // Primary key
    b.HasKey(r => new { r.UserId, r.RoleId });

    // Maps to the AspNetUserRoles table
    b.ToTable("AspNetUserRoles");
});
```

### <a name="model-generic-types"></a>Универсальные типы модели

Удостоверение определяет [типы среды CLR по умолчанию](/dotnet/standard/glossary#clr) для каждого из перечисленных выше типов сущностей. Все эти типы имеют префикс *Identity*:

* `IdentityUser`
* `IdentityRole`
* `IdentityUserClaim`
* `IdentityUserToken`
* `IdentityUserLogin`
* `IdentityRoleClaim`
* `IdentityUserRole`

Вместо того чтобы использовать эти типы напрямую, эти типы можно использовать в качестве базовых классов для собственных типов приложения. `DbContext` классы, определенные с помощью Identity, являются универсальными, поэтому для одного или нескольких типов сущностей в модели можно использовать разные типы CLR. Эти универсальные типы также позволяют изменять тип данных `User` первичный ключ (PK).

При использовании удостоверения с поддержкой ролей следует использовать класс <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>. Пример:

```csharp
// Uses all the built-in Identity types
// Uses `string` as the key type
public class IdentityDbContext
    : IdentityDbContext<IdentityUser, IdentityRole, string>
{
}

// Uses the built-in Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityDbContext<TUser>
    : IdentityDbContext<TUser, IdentityRole, string>
        where TUser : IdentityUser
{
}

// Uses the built-in Identity types except with custom User and Role types
// The key type is defined by TKey
public class IdentityDbContext<TUser, TRole, TKey> : IdentityDbContext<
    TUser, TRole, TKey, IdentityUserClaim<TKey>, IdentityUserRole<TKey>,
    IdentityUserLogin<TKey>, IdentityRoleClaim<TKey>, IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TRole : IdentityRole<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments
// The key type is defined by TKey
public abstract class IdentityDbContext<
    TUser, TRole, TKey, TUserClaim, TUserRole, TUserLogin, TRoleClaim, TUserToken>
    : IdentityUserContext<TUser, TKey, TUserClaim, TUserLogin, TUserToken>
         where TUser : IdentityUser<TKey>
         where TRole : IdentityRole<TKey>
         where TKey : IEquatable<TKey>
         where TUserClaim : IdentityUserClaim<TKey>
         where TUserRole : IdentityUserRole<TKey>
         where TUserLogin : IdentityUserLogin<TKey>
         where TRoleClaim : IdentityRoleClaim<TKey>
         where TUserToken : IdentityUserToken<TKey>
```

Также можно использовать удостоверение без ролей (только утверждения). в этом случае следует использовать класс <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601>:

```csharp
// Uses the built-in non-role Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityUserContext<TUser>
    : IdentityUserContext<TUser, string>
        where TUser : IdentityUser
{
}

// Uses the built-in non-role Identity types except with a custom User type
// The key type is defined by TKey
public class IdentityUserContext<TUser, TKey> : IdentityUserContext<
    TUser, TKey, IdentityUserClaim<TKey>, IdentityUserLogin<TKey>,
    IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments, with no roles
// The key type is defined by TKey
public abstract class IdentityUserContext<
    TUser, TKey, TUserClaim, TUserLogin, TUserToken> : DbContext
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
        where TUserClaim : IdentityUserClaim<TKey>
        where TUserLogin : IdentityUserLogin<TKey>
        where TUserToken : IdentityUserToken<TKey>
{
}
```

## <a name="customize-the-model"></a>Настройка модели

Начальной точкой настройки модели является наследование от соответствующего типа контекста. См. раздел [универсальные типы модели](#model-generic-types) . Этот тип контекста обычно называется `ApplicationDbContext` и создается шаблонами ASP.NET Core.

Контекст используется для настройки модели двумя способами.

* Предоставление типов сущностей и ключей для параметров универсального типа.
* Переопределение `OnModelCreating` для изменения сопоставления этих типов.

При переопределении `OnModelCreating`сначала необходимо вызвать `base.OnModelCreating`. переопределяющая конфигурация должна вызываться следующим. EF Core обычно имеет политику последнего применения WINS для настройки. Например, если метод `ToTable` для типа сущности вызывается сначала с одним именем таблицы, а затем снова с другим именем таблицы, то используется имя таблицы во втором вызове.

### <a name="custom-user-data"></a>Пользовательские данные пользователя

<!--
set projNam=WebApp1
dotnet new webapp -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design 
dotnet aspnet-codegenerator identity  -dc ApplicationDbContext --useDefaultUI 
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
 -->

[Пользовательские данные пользователя](xref:security/authentication/add-user-data) поддерживаются путем наследования от `IdentityUser`. В качестве имени этого типа можно присвоить имя `ApplicationUser`:

```csharp
public class ApplicationUser : IdentityUser
{
    public string CustomTag { get; set; }
}
```

Используйте тип `ApplicationUser` в качестве универсального аргумента для контекста:

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder builder)
    {
        base.OnModelCreating(builder);
    }
}
```

Нет необходимости переопределять `OnModelCreating` в классе `ApplicationDbContext`. EF Core сопоставляет свойство `CustomTag` по соглашению. Однако базу данных необходимо обновить, чтобы создать новый столбец `CustomTag`. Чтобы создать столбец, добавьте миграцию, а затем обновите базу данных, как описано в статье о [миграции Identity and EF Core](#identity-and-ef-core-migrations).

Обновите *pages/Shared/_LoginPartial. cshtml* и замените `IdentityUser` `ApplicationUser`:

```cshtml
@using Microsoft.AspNetCore.Identity
@using WebApp1.Areas.Identity.Data
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager
```

Обновите *области/Identity/идентитихостингстартуп. CS* или `Startup.ConfigureServices` и замените `IdentityUser` `ApplicationUser`.

```csharp
services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

В ASP.NET Core 2,1 или более поздней версии удостоверение предоставляется в виде библиотеки классов Razor. Дополнительные сведения см. в разделе <xref:security/authentication/scaffold-identity>. Следовательно, приведенный выше код требует вызова <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>. Если для добавления файлов удостоверений в проект использовался механизм идентификации удостоверений, удалите вызов `AddDefaultUI`. Дополнительные сведения см. в разделе:

* [Удостоверение шаблона](xref:security/authentication/scaffold-identity)
* [Добавление, скачивание и удаление настраиваемых данных пользователя в удостоверении](xref:security/authentication/add-user-data)

### <a name="change-the-primary-key-type"></a>Изменение типа первичного ключа

Изменение типа данных столбца PK после создания базы данных является проблематичным для многих систем баз. Изменение ПЕРВИЧного ключа обычно подразумевает удаление и повторное создание таблицы. Поэтому при создании базы данных необходимо указать типы ключей в первоначальной миграции.

Чтобы изменить тип ПЕРВИЧного ключа, выполните следующие действия.

1. Если база данных была создана до изменения PK, запустите `Drop-Database` (PMC) или `dotnet ef database drop` (.NET Core CLI), чтобы удалить ее.
2. После подтверждения удаления базы данных удалите первоначальную миграцию с `Remove-Migration` (PMC) или `dotnet ef migrations remove` (.NET Core CLI).
3. Обновите класс `ApplicationDbContext`, производный от <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603>. Укажите новый тип ключа для `TKey`. Например, чтобы использовать `Guid` тип ключа:

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<IdentityUser<Guid>, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    ::: moniker range=">= aspnetcore-2.0"

    В приведенном выше коде для использования нового типа ключа необходимо указать универсальные классы <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> и <xref:Microsoft.AspNetCore.Identity.IdentityRole%601>.

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    В приведенном выше коде для использования нового типа ключа необходимо указать универсальные классы <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> и <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601>.

    ::: moniker-end

    `Startup.ConfigureServices` необходимо обновить для использования универсального пользователя:

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<IdentityUser<Guid>>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

4. Если используется пользовательский класс `ApplicationUser`, обновите класс, чтобы он наследовался от `IdentityUser`. Пример:

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    Обновите `ApplicationDbContext`, чтобы сослаться на пользовательский класс `ApplicationUser`:

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<ApplicationUser, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    Зарегистрируйте класс контекста пользовательской базы данных при добавлении службы удостоверений в `Startup.ConfigureServices`:

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    Тип данных первичного ключа выводится путем анализа объекта [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .

    В ASP.NET Core 2,1 или более поздней версии удостоверение предоставляется в виде библиотеки классов Razor. Дополнительные сведения см. в разделе <xref:security/authentication/scaffold-identity>. Следовательно, приведенный выше код требует вызова <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>. Если для добавления файлов удостоверений в проект использовался механизм идентификации удостоверений, удалите вызов `AddDefaultUI`.

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    Тип данных первичного ключа выводится путем анализа объекта [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    Метод <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> принимает тип `TKey`, указывающий тип данных первичного ключа.

    ::: moniker-end

5. Если используется пользовательский класс `ApplicationRole`, обновите класс, чтобы он наследовался от `IdentityRole<TKey>`. Пример:

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    Обновите `ApplicationDbContext` для ссылки на пользовательский класс `ApplicationRole`. Например, следующий класс ссылается на пользовательский `ApplicationUser` и на пользовательский `ApplicationRole`:

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Зарегистрируйте класс контекста пользовательской базы данных при добавлении службы удостоверений в `Startup.ConfigureServices`:

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    Тип данных первичного ключа выводится путем анализа объекта [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .

    В ASP.NET Core 2,1 или более поздней версии удостоверение предоставляется в виде библиотеки классов Razor. Дополнительные сведения см. в разделе <xref:security/authentication/scaffold-identity>. Следовательно, приведенный выше код требует вызова <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>. Если для добавления файлов удостоверений в проект использовался механизм идентификации удостоверений, удалите вызов `AddDefaultUI`.

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Зарегистрируйте класс контекста пользовательской базы данных при добавлении службы удостоверений в `Startup.ConfigureServices`:

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    Тип данных первичного ключа выводится путем анализа объекта [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Зарегистрируйте класс контекста пользовательской базы данных при добавлении службы удостоверений в `Startup.ConfigureServices`:

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    Метод <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> принимает тип `TKey`, указывающий тип данных первичного ключа.

    ::: moniker-end

### <a name="add-navigation-properties"></a>Добавление свойств навигации

Изменение конфигурации модели для связей может быть более сложным, чем внесение других изменений. Необходимо соблюдать осторожность, чтобы заменить существующие связи, а не создавать новые, дополнительные связи. В частности, измененная связь должна указывать то же свойство внешнего ключа (FK), что и существующая связь. Например, отношение между `Users` и `UserClaims` по умолчанию имеет значение, заданное следующим образом:

```csharp
builder.Entity<TUser>(b =>
{
    // Each User can have many UserClaims
    b.HasMany<TUserClaim>()
     .WithOne()
     .HasForeignKey(uc => uc.UserId)
     .IsRequired();
});
```

FK для этой связи указывается как свойство `UserClaim.UserId`. `HasMany` и `WithOne` вызываются без аргументов для создания связи без свойств навигации.

Добавьте свойство навигации к `ApplicationUser`, которое позволяет ссылаться от пользователя на связанные `UserClaims`.

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
}
```

`TKey` для `IdentityUserClaim<TKey>` — это тип, указанный для PK пользователей. В этом случае `TKey` `string`, так как используются значения по умолчанию. Это **не** тип PK для `UserClaim` типа сущности.

Теперь, когда свойство навигации существует, оно должно быть настроено в `OnModelCreating`:

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();
        });
    }
}
```

Обратите внимание, что связь настраивается точно так же, как и ранее, только со свойством навигации, указанным в вызове `HasMany`.

Свойства навигации существуют только в модели EF, а не в базе данных. Поскольку тип внешнего ключа для связи не изменился, изменение этой модели не требует обновления базы данных. Это можно проверить, добавив миграцию после внесения изменений. Методы `Up` и `Down` пусты.

### <a name="add-all-user-navigation-properties"></a>Добавить все свойства навигации пользователя

С помощью приведенного выше раздела в качестве руководства в следующем примере настраивается однонаправленные свойства навигации для всех связей пользователя:

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<IdentityUserRole<string>> UserRoles { get; set; }
}
```

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne()
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });
    }
}
```

### <a name="add-user-and-role-navigation-properties"></a>Добавление свойств навигации для пользователей и ролей

В приведенном выше разделе в качестве руководства в следующем примере настраиваются свойства навигации для всех связей пользователя и роли:

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        IdentityUserClaim<string>, ApplicationUserRole, IdentityUserLogin<string>,
        IdentityRoleClaim<string>, IdentityUserToken<string>>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();
        });

    }
}
```

Примечания.

* Этот пример также включает сущность `UserRole` JOIN, необходимую для навигации по связям «многие ко многим» от пользователей к ролям.
* Не забудьте изменить типы свойств навигации, чтобы они отражали, что `ApplicationXxx` типы теперь используются вместо типов `IdentityXxx`.
* Не забудьте использовать `ApplicationXxx` в определении универсального `ApplicationContext`.

### <a name="add-all-navigation-properties"></a>Добавить все свойства навигации

В приведенном выше разделе в качестве руководства в следующем примере настраиваются свойства навигации для всех связей на всех типах сущностей:

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<ApplicationUserClaim> Claims { get; set; }
    public virtual ICollection<ApplicationUserLogin> Logins { get; set; }
    public virtual ICollection<ApplicationUserToken> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
    public virtual ICollection<ApplicationRoleClaim> RoleClaims { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserClaim : IdentityUserClaim<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationUserLogin : IdentityUserLogin<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationRoleClaim : IdentityRoleClaim<string>
{
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserToken : IdentityUserToken<string>
{
    public virtual ApplicationUser User { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        ApplicationUserClaim, ApplicationUserRole, ApplicationUserLogin,
        ApplicationRoleClaim, ApplicationUserToken>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne(e => e.User)
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne(e => e.User)
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne(e => e.User)
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();

            // Each Role can have many associated RoleClaims
            b.HasMany(e => e.RoleClaims)
                .WithOne(e => e.Role)
                .HasForeignKey(rc => rc.RoleId)
                .IsRequired();
        });
    }
}
```

### <a name="use-composite-keys"></a>Использование составных ключей

В предыдущих разделах было продемонстрировано изменение типа ключа, используемого в модели удостоверений. Изменение модели ключа удостоверения для использования составных ключей не поддерживается или не рекомендуется. Использование составного ключа с удостоверением включает изменение того, как код диспетчера удостоверений взаимодействует с моделью. Эта настройка выходит за рамки данного документа.

### <a name="change-tablecolumn-names-and-facets"></a>Изменение имен таблиц и столбцов и аспектов

Чтобы изменить имена таблиц и столбцов, вызовите `base.OnModelCreating`. Затем добавьте конфигурацию, чтобы переопределить любые значения по умолчанию. Например, чтобы изменить имена всех таблиц удостоверений:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.ToTable("MyUsers");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.ToTable("MyUserClaims");
    });

    modelBuilder.Entity<IdentityUserLogin<string>>(b =>
    {
        b.ToTable("MyUserLogins");
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.ToTable("MyUserTokens");
    });

    modelBuilder.Entity<IdentityRole>(b =>
    {
        b.ToTable("MyRoles");
    });

    modelBuilder.Entity<IdentityRoleClaim<string>>(b =>
    {
        b.ToTable("MyRoleClaims");
    });

    modelBuilder.Entity<IdentityUserRole<string>>(b =>
    {
        b.ToTable("MyUserRoles");
    });
}
```

В этих примерах используются типы удостоверений по умолчанию. Если используется тип приложения, например `ApplicationUser`, настройте этот тип вместо типа по умолчанию.

В следующем примере изменяются некоторые имена столбцов:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(e => e.Email).HasColumnName("EMail");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.Property(e => e.ClaimType).HasColumnName("CType");
        b.Property(e => e.ClaimValue).HasColumnName("CValue");
    });
}
```

Некоторые типы столбцов базы данных можно настроить с помощью определенных *аспектов* (например, максимально допустимой длины `string`). В следующем примере задается максимальная длина столбца для нескольких `string` свойств в модели:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(u => u.UserName).HasMaxLength(128);
        b.Property(u => u.NormalizedUserName).HasMaxLength(128);
        b.Property(u => u.Email).HasMaxLength(128);
        b.Property(u => u.NormalizedEmail).HasMaxLength(128);
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.Property(t => t.LoginProvider).HasMaxLength(128);
        b.Property(t => t.Name).HasMaxLength(128);
    });
}
```

### <a name="map-to-a-different-schema"></a>Сопоставьте с другой схемой

Схемы могут вести себя по-разному для поставщиков баз данных. Для SQL Server значение по умолчанию — создать все таблицы в схеме *dbo* . Таблицы могут быть созданы в другой схеме. Пример:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a>Отложенная загрузка

В этом разделе добавляется поддержка прокси-серверов с отложенной загрузкой в модели удостоверений. Отложенная загрузка полезна, так как позволяет использовать свойства навигации без предварительного подтверждения их загрузки.

Типы сущностей можно сделать доступными для отложенной загрузки несколькими способами, как описано в документации по [EF Core](/ef/core/querying/related-data#lazy-loading). Для простоты используйте прокси-серверы с отложенной загрузкой, для которых требуется:

* Установка пакета [Microsoft. EntityFrameworkCore. прокси](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) .
* Вызов <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> внутри [AddDbContext\<тконтекст >](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).
* Открытые типы сущностей с `public virtual` свойствами навигации.

В следующем примере демонстрируется вызов `UseLazyLoadingProxies` в `Startup.ConfigureServices`:

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefaultIdentity<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

Инструкции по добавлению свойств навигации в типы сущностей см. в предыдущих примерах.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:security/authentication/scaffold-identity>

::: moniker-end
