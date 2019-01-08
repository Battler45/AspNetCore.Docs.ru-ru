# <a name="how-to-buildrun-secure-user-data-sample"></a><span data-ttu-id="e5e2c-101">Как построение и запуск образца данных безопасности пользователей</span><span class="sxs-lookup"><span data-stu-id="e5e2c-101">How to build/run Secure user data sample</span></span>

* <span data-ttu-id="e5e2c-102">Задайте пароль с помощью диспетчера секретов:</span><span class="sxs-lookup"><span data-stu-id="e5e2c-102">Set password with the Secret Manager tool:</span></span>

  `dotnet user-secrets set SeedUserPW <pw>`

* <span data-ttu-id="e5e2c-103">Обновление базы данных:</span><span class="sxs-lookup"><span data-stu-id="e5e2c-103">Update the database:</span></span>

    `dotnet ef database update`

* <span data-ttu-id="e5e2c-104">Включение протокола HTTPS в проекте</span><span class="sxs-lookup"><span data-stu-id="e5e2c-104">Enable HTTPS in the project</span></span>
