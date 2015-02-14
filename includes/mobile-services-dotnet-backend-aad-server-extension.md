### (Opcional) configure seu serviço móvel para logon no AAD

>[AZURE.NOTE] Essas etapas são opcionais porque apenas se aplicam ao provedor de logon do Active Directory do Azure.

1. Instalar o **WindowsAzure.MobileServices.Backend.Security** pacote NuGet.

2. No Visual Studio expandir o App_Start e abrir o arquivo WebApiConfig.cs. Adicionar o seguinte código ao método  `Register`, imediatamente após que o  `options` seja instanciado:

        options.LoginProviders.Remove(typeof(AzureActiveDirectoryLoginProvider));
        options.LoginProviders.Add(typeof(AzureActiveDirectoryExtendedLoginProvider));

<!--HONumber=42-->
