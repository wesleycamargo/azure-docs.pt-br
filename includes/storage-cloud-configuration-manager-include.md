### Analisar a cadeia de conexão

A biblioteca do Microsoft Azure Configuration Manager mencionada acima fornece uma classe para analisar uma cadeia de conexão de um arquivo de configuração. A [classe CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) analisa as definições de configuração independentemente de o aplicativo cliente estar em execução na área de trabalho, em um dispositivo móvel, em uma máquina virtual do Azure ou em um serviço de nuvem do Azure.

Adicione o seguinte código ao método `Main()`em `program.cs`:

    // Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		CloudConfigurationManager.GetSetting("StorageConnectionString"));

O uso do Gerenciador de Configurações do Azure é opcional. Você também pode usar uma API como a [classe ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) do .NET Framework.

<!---HONumber=AcomDC_0413_2016-->