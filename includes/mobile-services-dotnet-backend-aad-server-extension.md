### (Opcional) configure seu serviço móvel para logon no AAD

> [WACOM.NOTE] Essas etapas são opcionais porque apenas se aplicam ao provedor de logon do Active Directory do Azure.

1.  Instalar o pacote NuGet **WindowsAzure.MobileServices.Backend.Security**.

2.  No Visual Studio expandir o App\_Start e abrir o arquivo WebApiConfig.cs. Adicionar o seguinte código ao método `Register`, imediatamente após que o `options` seja instanciado:

        options.LoginProviders.Remove(typeof(AzureActiveDirectoryLoginProvider));
        options.LoginProviders.Add(typeof(AzureActiveDirectoryExtendedLoginProvider));


