---
ms.openlocfilehash: 260f774fdba4d16a4fcb00ac1c699acf4d1bf5b5
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59615402"
---
* <span data-ttu-id="29479-101">Настройте доверие сертификату разработки HTTPS с помощью следующей команды:</span><span class="sxs-lookup"><span data-stu-id="29479-101">Trust the HTTPS development certificate by running the following command:</span></span>

  ```console
  dotnet dev-certs https --trust
  ```

  <span data-ttu-id="29479-102">Приведенная выше команда отображает следующее диалоговое окно.</span><span class="sxs-lookup"><span data-stu-id="29479-102">The preceding command displays the following dialog:</span></span>

  ![Диалоговое окно "Предупреждение о безопасности"](~/getting-started/_static/cert.png)

* <span data-ttu-id="29479-104">Выберите **Да**, если согласны доверять сертификату разработки.</span><span class="sxs-lookup"><span data-stu-id="29479-104">Select **Yes** if you agree to trust the development certificate.</span></span>

  <span data-ttu-id="29479-105">Дополнительные сведения см. в разделе [Настройка доверия к сертификату разработки HTTPS ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="29479-105">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>
  
