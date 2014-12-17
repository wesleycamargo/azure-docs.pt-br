### (Opcional) configure seu serviço móvel para logon no AAD

>[WACOM.NOTE] Essas etapas são opcionais porque apenas se aplicam ao provedor de logon do Active Directory do Azure.

1. Instale o pacote **WindowsAzure.MobileServices.Backend.Security** NuGet.

2. No Visual Studio, expanda o App_Start e abra o arquivo WebApiConfig.cs. Adicione o seguinte código ao método `Register`, imediatamente após `options` ser instanciado:

        options.LoginProviders.Remove(typeof(AzureActiveDirectoryLoginProvider));
        options.LoginProviders.Add(typeof(AzureActiveDirectoryExtendedLoginProvider));

