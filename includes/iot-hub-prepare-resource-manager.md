## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Preparar para autenticar solicitações do Azure Resource Manager
Autentique todas as operações que podem ser executadas nos recursos usando o [Azure Resource Manager][lnk-authenticate-arm] com o Azure Active Directory (AD). A maneira mais fácil de configurar isso é usar o PowerShell ou Azure CLI.

Você deve instalar o [Azure PowerShell 1.0][lnk-powershell-install] ou posterior antes de continuar.

As etapas a seguir mostram como configurar a autenticação de senha para um aplicativo do AD usando o PowerShell. Você pode executar esses comandos em uma sessão do PowerShell padrão.

1. Faça logon em sua assinatura do Azure usando o seguinte comando:
   
    ```
    Login-AzureRmAccount
    ```
2. Anote seu **TenantId** e **SubscriptionId**. Você precisará delas mais tarde.
3. Crie um novo aplicativo do Active Directory do Azure usando o seguinte comando, substituindo os espaços reservados:
   
   * **{Display name}:** um nome de exibição para seu aplicativo, como **MySampleApp**
   * **{Home page URL}:** a URL da página inicial de seu aplicativo, como **http://mysampleapp/home**. Essa URL não precisa levar para um aplicativo real.
   * **{Application identifier}:** um identificador exclusivo, como **http://mysampleapp**. Essa URL não precisa levar para um aplicativo real.
   * **{Password}:** uma senha que você usará para autenticar com seu aplicativo.
     
     ```
     New-AzureRmADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password {Password}
     ```
4. Anote o **ApplicationId** do aplicativo que você criou. Você precisará dessas informações posteriormente.
5. Criar uma nova entidade de serviço usando o comando a seguir, substituindo **{MyApplicationId}** pelo **ApplicationId** da etapa anterior:
   
    ```
    New-AzureRmADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. Configurar uma atribuição de função usando o comando a seguir, substituindo **{MyApplicationId}** por **ApplicationId**.
   
    ```
    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

Você terminou de criar o aplicativo do AD do Azure que permitirá autenticar a partir do seu aplicativo c# personalizado. Você precisará dos seguintes valores mais à frente neste tutorial:

* TenantId
* SubscriptionId
* ApplicationId
* Senha

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: /powershell/azureps-cmdlets-docs


<!--HONumber=Dec16_HO1-->


