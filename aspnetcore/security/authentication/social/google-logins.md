---
title: Настройка внешней учетной записи Google в ASP.NET Core
author: rick-anderson
description: В этом учебнике показано интеграцию Google учетной записи пользователя и проверки подлинности в существующее приложение ASP.NET Core.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/19/2020
uid: security/authentication/google-logins
ms.openlocfilehash: a114d23c25201c9fe31ad0397efaf99fe98a312a
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989775"
---
# <a name="google-external-login-setup-in-aspnet-core"></a>Настройка внешней учетной записи Google в ASP.NET Core

Авторы: [Валерий Новицкий](https://github.com/01binary) (Valeriy Novytskyy) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

В этом руководстве показано, как разрешить пользователям входить в систему с помощью учетной записи Google, используя проект ASP.NET Core 3,0, созданный на [предыдущей странице](xref:security/authentication/social/index).

## <a name="create-a-google-api-console-project-and-client-id"></a>Создание проекта консоли Google API и идентификатора клиента

* Установите [Microsoft. AspNetCore. Authentication. Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google).
* Перейдите к разделу [Интеграция Google-входа в веб-приложение](https://developers.google.com/identity/sign-in/web/devconsole-project) и выберите **Настройка проекта**.
* В диалоговом окне **Настройка клиента OAuth** выберите **веб-сервер**.
* В текстовом поле " **зарегистрированные URI перенаправления** " задайте URI перенаправления. Например `https://localhost:44312/signin-google`.
* Сохраните **идентификатор клиента** и **секрет клиента**.
* При развертывании сайта зарегистрируйте новый общедоступный URL-адрес из **консоли Google**.

## <a name="store-the-google-client-id-and-secret"></a>Хранение идентификатора и секрета клиента Google

Храните конфиденциальные параметры, такие как идентификатор клиента Google и секретные значения, с помощью [диспетчера секретов](xref:security/app-secrets). Для этого примера выполните следующие действия.

1. Инициализируйте проект для хранения секретных данных согласно инструкциям в разделе [Включение секретного хранилища](xref:security/app-secrets#enable-secret-storage).
1. Храните конфиденциальные параметры в локальном хранилище секретов с секретными ключами `Authentication:Google:ClientId` и `Authentication:Google:ClientSecret`:

    ```dotnetcli
    dotnet user-secrets set "Authentication:Google:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Google:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Вы можете управлять учетными данными и использованием API в [консоли API](https://console.developers.google.com/apis/dashboard).

## <a name="configure-google-authentication"></a>Настройка проверки подлинности Google

Добавьте службу Google в `Startup.ConfigureServices`:

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a>Вход с учетными данными Google

* Запустите приложение и нажмите кнопку **войти**. Появится возможность войти в систему с помощью Google.
* Нажмите кнопку **Google** , которая перенаправляет на Google для проверки подлинности.
* После ввода учетных данных Google вы будете перенаправлены обратно на веб-сайт.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Дополнительные сведения о параметрах конфигурации, поддерживаемых проверкой подлинности Google, см. в справочнике по API <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>. Это может использоваться для запроса различные сведения о пользователе.

## <a name="change-the-default-callback-uri"></a>Изменение URI обратного вызова по умолчанию

Сегмент URI `/signin-google` задается в качестве обратного вызова по умолчанию для поставщика проверки подлинности Google. Вы можете изменить URI обратного вызова по умолчанию при настройке по промежуточного слоя для проверки подлинности Google с помощью унаследованного свойства [ремотеаусентикатионоптионс. каллбаккпас](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) класса [гуглеоптионс](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) .

## <a name="troubleshooting"></a>Диагностика

* Если вход не работает и вы не получаете никаких ошибок, переключитесь в режим разработки, чтобы упростить отладку.
* Если удостоверение не настроено путем вызова `services.AddIdentity` в `ConfigureServices`, попытка проверить подлинность результатов в *ArgumentException: необходимо указать параметр "сигнинсчеме"* . Шаблон проекта, используемый в этом руководстве гарантирует, что это будет сделано.
* Если база данных сайта не была создана путем применения первоначальной миграции, то при обработке ошибки запроса возникнет *Ошибка операции с базой данных* . Выберите **Применить миграции** , чтобы создать базу данных, и обновите страницу, чтобы продолжить работу после ошибки.

## <a name="next-steps"></a>Следующие шаги

* В этой статье объясняется, как можно выполнить проверку подлинности с помощью Google. Аналогичный подход можно использовать для проверки подлинности с другими поставщиками, перечисленными на [предыдущей странице](xref:security/authentication/social/index).
* После публикации приложения в Azure сбросьте `ClientSecret` в консоли Google API.
* Задайте `Authentication:Google:ClientId` и `Authentication:Google:ClientSecret` в качестве параметров приложения в портал Azure. Система конфигурации предназначена для чтения разделов из переменных среды.
