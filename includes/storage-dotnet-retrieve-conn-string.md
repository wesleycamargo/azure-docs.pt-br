### Recuperando sua cadeia de conexão
Você pode usar o tipo **CloudStorageAccount** para representar as informações da sua conta de armazenamento. Se você estiver usando um modelo de projeto do Azure e/ou tiver uma referência ao namespace Microsoft.WindowsAzure.CloudConfigurationManager, poderá usar o tipo **CloudConfigurationManager** para recuperar a cadeia de conexão do armazenamento e as informações de conta de armazenamento da configuração de serviços do Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Se estiver criando um aplicativo sem nenhuma referência ao Microsoft.WindowsAzure.CloudConfigurationManager, e sua cadeia de conexão estiver localizada no `web.config` ou `app.config`, como mostrado acima, você poderá usar o **ConfigurationManager** para recuperar a cadeia de conexão. Você precisará adicionar uma referência ao System.Configuration.dll a seu projeto e adicionar outra declaração de namespace para ele:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"]);

<!---HONumber=July15_HO2-->