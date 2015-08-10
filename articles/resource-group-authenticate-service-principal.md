<properties
   pageTitle="Autenticação de uma entidade de serviço com o Gerenciador de Recursos do Azure"
   description="Descreve como conceder acesso a uma entidade de serviço por meio do controle de acesso baseado em função e autenticá-la. Mostra como executar essas tarefas com o PowerShell e CLI do Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="07/24/2015"
   ms.author="tomfitz"/>

# Autenticação de uma entidade de serviço com o Gerenciador de Recursos do Azure

Este tópico mostra como permitir que uma entidade de serviço (como um processo automatizado, aplicativo ou serviço) acesse outros recursos em sua assinatura. Com o Gerenciador de Recursos do Azure, você pode usar o controle de acesso baseado em função para conceder ações permitidas para uma entidade de serviço e autenticar essa entidade de serviço. Este tópico mostra como usar o PowerShell e CLI do Azure para atribuir uma função à entidade de serviço e autenticar essa entidade.


## Conceitos
1. AAD (Active Directory do Azure) - um serviço de gerenciamento de identidades e acesso para a nuvem. Para obter mais informações, consulte [O que é o Active Directory do Azure](active-directory/active-directory-whatis.md)
2. Entidade de serviço - uma instância de um aplicativo em um diretório que precisa acessar outros recursos.
3. Aplicativo do AD - registro de diretório que identifica um aplicativo ao AAD. Para obter mais informações, consulte [Noções básicas de autenticação no AD do Azure](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth).

## Conceder acesso e autenticar uma entidade de serviço usando o PowerShell

Se você não tem o Azure PowerShell instalado, consulte [Como instalar e configurar o Azure PowerShell](./powershell-install-configure.md).

Você começará criando uma entidade de serviço. Para fazer isso, devemos usar a opção criar um aplicativo no diretório. Esta seção nos guiará pela criação de um novo aplicativo no diretório.

1. Criar um novo aplicativo AAD executando o comando **New-AzureADApplication**. Forneça um nome de exibição para seu aplicativo, o URI para uma página que descreve o aplicativo (o link não é verificado), os URIs que identificam seu aplicativo e a senha para a identidade do seu aplicativo.

        PS C:> $azureAdApplication = New-AzureADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password "<Your_Password>"

     O aplicativo do Azure AD é retornado. A propriedade **ApplicationId** é necessária para criar entidades de serviço, atribuições de função e aquisição de tokens JWT. Salve a saída ou capture-a em uma variável.

        Type                    : Application
        ApplicationId           : a41acfda-d588-47c9-8166-d659a335a865
        ApplicationObjectId     : a26aaa48-bd52-4a7f-b29f-1bebf74c91e3
        AvailableToOtherTenants : False
        AppPermissions          : {{
                            "claimValue": "user_impersonation",
                            "description": "Allow the application to access My
                              Application on behalf of the signed-in user.",
                            "directAccessGrantTypes": [],
                            "displayName": "Access <<Your Application Display Name>>",
                            "impersonationAccessGrantTypes": [
                              {
                                "impersonated": "User",
                                "impersonator": "Application"
                              }
                            ],
                            "isDisabled": false,
                            "origin": "Application",
                            "permissionId":
                            "b866ef28-9abb-4698-8c8f-eb4328533831",
                            "resourceScopeType": "Personal",
                            "userConsentDescription": "Allow the application
                             to access <<Your Application Display Name>> on your behalf.",
                            "userConsentDisplayName": "Access <<Your Application Display Name>>",
                            "lang": null
                          }}


2. Crie uma entidade de serviço para seu aplicativo.

        PS C:> New-AzureADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

     Você criou uma entidade de serviço no diretório, mas o serviço não tem quaisquer permissões ou escopo atribuídos. Você precisará conceder explicitamente à entidade de serviço permissões para executar operações em algum escopo.

3. Conceda à entidade de serviço permissões em sua assinatura. Neste exemplo você concederá à entidade de serviço a permissão para ler todos os recursos na assinatura. Para o parâmetro **ServicePrincipalName**, forneça um dos parâmetros **ApplicationId** ou **IdentifierUris** que você usou ao criar o aplicativo. Para obter mais informações sobre controle de acesso baseado em função, consulte [Gerenciando e auditando o acesso a recursos](azure-portal/resource-group-rbac.md)

        PS C:> New-AzureRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

4. Obtenha a assinatura na qual a atribuição de função foi criada. Essa assinatura será usada posteriormente para obter a **TenantId** do locatário no qual reside a atribuição de função da entidade de serviço.

        PS C:> $subscription = Get-AzureSubscription | where { $_.IsCurrent }

     Se você criou a atribuição de função em uma assinatura que não seja a assinatura selecionada, você pode especificar os parâmetros **SubscriptoinId** ou **SubscriptionName** para recuperar uma assinatura diferente.

5. Crie um novo objeto **PSCredential** que contenha suas credenciais ao executar o comando **Get-Credential**.

        PS C:> $creds = Get-Credential

     Será solicitado que você insira suas credenciais de conta do Azure.

     ![][1]

     Como nome de usuário, use um dos parâmetros **ApplicationId** ou **IdentifierUris** que você usou ao criar o aplicativo. Como senha, use aquela especificada ao criar a conta.

6. Use as credenciais que você inseriu como uma entrada para o cmdlet **Add-AzureAccount**, que fará o logon da entidade de serviço:

        PS C:> Add-AzureAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId

     Agora, você deve ser autenticado como a entidade de serviço para o aplicativo AAD que você criou.


## Conceder acesso e autenticar uma entidade de serviço usando o CLI do Azure

Se você não tiver CLI do Azure para Mac, Linux e Windows instalada, consulte [Instalar e configurar a CLI do Azure](xplat-cli-install.md)

1. Crie um novo aplicativo AAD executando o comando **azure ad app create**. Forneça um nome de exibição para seu aplicativo, o URI para uma página que descreve o aplicativo (o link não é verificado), os URIs que identificam seu aplicativo e a senha para a identidade do seu aplicativo.

        azure ad app create --name "<Your Application Display Name>" --home-page "<https://YourApplicationHomePage>" --identifier-uris "<https://YouApplicationUri>" --password <Your_Password>
        
    O aplicativo do Azure AD é retornado. A propriedade ApplicationId é necessária para criar entidades de serviço, atribuições de função e aquisição de tokens JWT.

        info:    Executing command ad app create
        + Creating application exampleapp                                                
        data:    Application Id:          b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Application Object Id:   d5c519e2-6149-447e-b323-88d2c4ea27de
        data:    Application Permissions:  
        data:                             claimValue:  user_impersonation
        data:                             description:  Allow the application to access exampleapp on behalf of the signed-in user.
        ...
        info:    ad app create command OK

2. Crie uma entidade de serviço para seu aplicativo. Forneça a ID do aplicativo que foi retornada na etapa anterior.

        azure ad sp create b57dd71d-036c-4840-865e-23b71d8098ec
        
    A nova entidade de serviço será retornada. A ID de objeto é necessária ao conceder permissões.
    
        info:    Executing command ad sp create
        + Creating service principal for application b57dd71d-036c-4840-865e-23b71d8098ec
        data:    Object Id:               47193a0a-63e4-46bd-9bee-6a9f6f9c03cb
        data:    Display Name:            exampleapp
        ...
        info:    ad sp create command OK

    Você criou uma entidade de serviço no diretório, mas o serviço não tem quaisquer permissões ou escopo atribuídos. Você precisará conceder explicitamente à entidade de serviço permissões para executar operações em algum escopo.

3. Conceda à entidade de serviço permissões em sua assinatura. Neste exemplo você concederá à entidade de serviço a permissão para ler todos os recursos na assinatura. Para o parâmetro **ServicePrincipalName**, forneça um dos parâmetros **ApplicationId** ou **IdentifierUris** que você usou ao criar o aplicativo. Para obter mais informações sobre controle de acesso baseado em função, consulte [Gerenciando e auditando o acesso a recursos](azure-portal/resource-group-rbac.md)

        azure role assignment create --objectId 47193a0a-63e4-46bd-9bee-6a9f6f9c03cb -o Reader -c /subscriptions/{subscriptionId}/

4. Determinar a **TenantId** do locatário em que a entidade de serviço está atribuição de função reside listando as contas e procurando a propriedade **TenantId** na saída.

        azure account list

5. Entre usando a entidade de serviço como sua identidade. Como nome de usuário, use o parâmetro **ApplicationId** que você usou ao criar o aplicativo. Como senha, use aquela especificada ao criar a conta.

        azure login -u "<ApplicationId>" -p "<password>" --service-principal --tenant "<TenantId>"

    Agora, você deve ser autenticado como a entidade de serviço para o aplicativo AAD que você criou.

## Próximas etapas
  
- Para obter uma visão geral sobre o controle de acesso baseado em função, consulte [Gerenciando e auditando o acesso a recursos](azure-portal/resource-group-rbac.md)  
- Para obter informações sobre como usar o portal com entidades de serviço, consulte [Criar uma nova entidade de serviço do Azure usando o Portal do Azure](./resource-group-create-service-principal-portal.md)  
- Para obter orientações sobre como implantar recursos de segurança com o Gerenciador de Recursos do Azure, consulte [Considerações de segurança do Gerenciador de Recursos do Azure](best-practices-resource-manager-security.md).


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!---HONumber=July15_HO5-->