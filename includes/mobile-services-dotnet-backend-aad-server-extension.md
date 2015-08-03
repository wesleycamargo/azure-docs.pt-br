### (Opcional) Configure seu serviço móvel do .NET para o Active Directory do Azure

>[AZURE.NOTE]Essas etapas são opcionais porque apenas se aplicam ao provedor de logon do Active Directory do Azure.

1. Instale o [pacote NuGet WindowsAzure.MobileServices.Backend.Security.](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend.Security)

2. No Visual Studio, expanda o App_Start e abra o WebApiConfig.cs. Adicione a seguinte instrução `using` na parte superior:

        using Microsoft.WindowsAzure.Mobile.Service.Security.Providers;

3. Também no WebApiConfig.cs, adicione o seguinte código para o método `Register`, imediatamente após `options` ser instanciado:

        options.LoginProviders.Remove(typeof(AzureActiveDirectoryLoginProvider));
        options.LoginProviders.Add(typeof(AzureActiveDirectoryExtendedLoginProvider));

<!---HONumber=July15_HO4-->