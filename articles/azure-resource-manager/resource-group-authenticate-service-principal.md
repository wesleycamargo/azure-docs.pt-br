---
title: Criar identidade para o aplicativo do Azure com o PowerShell | Microsoft Docs
description: "Descreve como usar o Azure PowerShell para criar um aplicativo do Active Directory e uma entidade de serviço, e conceder acesso a recursos por meio do controle de acesso baseado em função. Ele mostra como autenticar um aplicativo com uma senha ou certificado."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/17/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 2a9075f4c9f10d05df3b275a39b3629d4ffd095f
ms.openlocfilehash: 31495f402b810c524bd7b906498774302500b732
ms.lasthandoff: 01/24/2017


---
# <a name="use-azure-powershell-to-create-a-service-principal-to-access-resources"></a>Usar o Azure PowerShell para criar uma entidade de serviço a fim de acessar recursos
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-authenticate-service-principal.md)
> * [CLI do Azure](resource-group-authenticate-service-principal-cli.md)
> * [Portal](resource-group-create-service-principal-portal.md)
> 
> 

Quando você tiver um aplicativo ou script que precisa acessar recursos, poderá configurar uma identidade para o aplicativo e autenticá-la com suas próprias credenciais. Esta abordagem é preferencial para executar o aplicativo com suas próprias credenciais porque:

* Você pode atribuir permissões para a identidade do aplicativo que são diferentes de suas próprias permissões. Normalmente, essas permissões são restritas a exatamente o que o aplicativo precisa fazer.
* Você não precisa alterar as credenciais do aplicativo se alterar suas responsabilidades. 
* Você pode usar um certificado para automatizar a autenticação ao executar um script autônomo.

Este tópico mostra como usar o [Azure PowerShell](/powershell/azureps-cmdlets-docs) para configurar tudo que você precisa para um aplicativo ser executado com suas próprias credenciais e identidade.

Com o PowerShell, você tem duas opções para autenticar seu aplicativo do AD:

* Senha
* certificado

Este tópico mostra como usar as duas opções no PowerShell. Se você pretende fazer logon no Azure a partir de uma estrutura de programação (como o Python, Ruby ou Node.js), a autenticação de senha poderá ser a melhor opção. Antes de decidir se você deve usar uma senha ou um certificado, consulte a seção [Aplicativos de exemplo](#sample-applications) para obter exemplos de autenticação em diferentes estruturas.

## <a name="active-directory-concepts"></a>Conceitos do Active Directory
Neste artigo, você cria dois objetos: o aplicativo do Active Directory (AD) e a entidade de serviço. O aplicativo do AD é a representação global do seu aplicativo. Ele contém as credenciais (uma id do aplicativo e uma senha ou certificado). A entidade de serviço é a representação local de seu aplicativo em um Active Directory. Ele contém a atribuição de função. Este tópico foca em um aplicativo com um único locatário no qual o aplicativo se destina a ser executado dentro de uma só organização. Você normalmente usa os aplicativos com um único locatário para os aplicativos da linha de negócios executados em sua organização. Em um aplicativo de locatário único, você tem um aplicativo AD e uma entidade de serviço.

Você pode estar imaginando: Por que preciso dos dois objetos? Essa abordagem faz mais sentido quando você considera os aplicativos multilocatários. Normalmente, você usa aplicativos multilocatários para o software como serviço (aplicativos SaaS), no qual o aplicativo é executado em várias assinaturas diferentes. Para os aplicativos multilocatários, você tem um aplicativo AD e várias entidades de serviço (uma em cada Active Directory que concede acesso ao aplicativo). Para configurar um aplicativo multilocatário, consulte [Guia do desenvolvedor para a autorização com a API do Azure Resource Manager](resource-manager-api-authentication.md).

## <a name="required-permissions"></a>Permissões necessárias
Para concluir este tópico, você deve ter permissões suficientes no Azure Active Directory e em sua assinatura do Azure. Especificamente, você deve ser capaz de criar um aplicativo no Active Directory e atribuir a entidade de serviço a uma função. 

A maneira mais fácil de verificar se a sua conta tem as permissões adequadas é por meio do portal. Consulte [Verificar permissão necessária](resource-group-create-service-principal-portal.md#required-permissions).

Agora, vá para uma seção para ver uma autenticação da [senha](#create-service-principal-with-password) ou do [certificado](#create-service-principal-with-certificate).

## <a name="create-service-principal-with-password"></a>Criar a entidade de serviço com a senha
Nesta seção, você deve executar as etapas para:

* criar um aplicativo do AD com uma senha
* criar a entidade de serviço
* atribuir a função Leitor à entidade de serviço

Para executar essas etapas rapidamente, use os seguintes cmdlets:

```powershell
$app = New-AzureRmADApplication -DisplayName "{app-name}" -HomePage "https://{your-domain}/{app-name}" -IdentifierUris "https://{your-domain}/{app-name}" -Password "{your-password}"
New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
Start-Sleep 15
New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId
```

O script fica suspenso 15 segundos para dar tempo à nova entidade de serviço de se propagar pelo Active Directory. Se o script não esperar tempo suficiente, você verá um erro dizendo: "PrincipalNotFound: a {id} da entidade não existe no diretório”. Se você receber esse erro, execute o cmdlet novamente para atribuí-lo a uma função.

Vejamos essas etapas com mais cuidado para assegurar que você entende o processo.

1. Entre na sua conta.
   
   ```powershell
   Login-AzureRmAccount
   ```

2. Crie um novo aplicativo do Active Directory fornecendo um nome de exibição, URI que descreve o aplicativo, URIs que identificam seu aplicativo e senha para a identidade do seu aplicativo.

   ```powershell   
   $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org/exampleapp" -IdentifierUris "https://www.contoso.org/exampleapp" -Password "{Your_Password}"
   ```

     Para os aplicativos de locatário único, os URIs não são validados.
   
     Se sua conta não tiver as [permissões necessárias](#required-permissions) no Active Directory, você verá uma mensagem de erro informando "Authentication_Unauthorized" ou "Nenhuma assinatura encontrada no contexto".
3. Examine o novo objeto de aplicativo. 
   
   ```powershell
   $app
   ```
   
     Observe especificamente a propriedade `ApplicationId`, que é necessária para criar as entidades de serviço, atribuições de função e adquirir o token de acesso.
   
   ```powershell
   DisplayName             : exampleapp
   ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
   IdentifierUris          : {https://www.contoso.org/example}
   HomePage                : https://www.contoso.org
   Type                    : Application
   ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
   AvailableToOtherTenants : False
   AppPermissions          : 
   ReplyUrls               : {}
   ```
4. Crie uma entidade de serviço para seu aplicativo passando a id do aplicativo do Active Directory.
   
   ```powershell
   New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
   ```

5. Conceda à entidade de serviço permissões em sua assinatura. Neste exemplo, você adiciona a entidade de serviço à função Leitor , que concede permissão para ler todos os recursos na assinatura. Para ver outras funções, confira [RBAC: funções internas](../active-directory/role-based-access-built-in-roles.md). No caso do parâmetro `ServicePrincipalName`, forneça a `ApplicationId` que você usou ao criar o aplicativo. Antes de executar esse cmdlet, dê um tempo para a nova entidade de serviço se propagar pelo Active Directory. Geralmente, ao executar esses cmdlets manualmente, já se passou tempo suficiente entre os cmdlets. Em um script, adicione uma etapa de suspensão entre os comandos (como `Start-Sleep 15`). Se você vir um erro dizendo: "PrincipalNotFound: {id} da entidade não existe no diretório", execute o cmdlet novamente.

   ```powershell   
   New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId
   ```

    Se sua conta não tiver permissões suficientes para atribuir uma função, verá uma mensagem de erro. A mensagem informa que sua conta “não tem autorização para executar a ação 'Microsoft.Authorization/roleAssignments/write' no escopo '/subscriptions/{guid}'”. 

É isso! Seu aplicativo do AD e a entidade de serviço estão configurados. A próxima seção mostra como fazer logon com as credenciais por meio do PowerShell. Se você quiser usar a credencial em seu aplicativo de código, poderá ir para os [Aplicativos de exemplo](#sample-applications). 

### <a name="provide-credentials-through-powershell"></a>Fornecer as credenciais por meio do PowerShell
Agora, você precisa fazer logon como o aplicativo para executar as operações.

1. Crie um objeto `PSCredential` que contém suas credenciais executando o comando `Get-Credential`. Você precisará de `ApplicationId` antes de executar esse comando. Não deixe de tê-lo à mão para colar.

   ```powershell   
   $creds = Get-Credential
   ```

2. Será solicitado que você insira suas credenciais. Como nome de usuário, use o parâmetro `ApplicationId` que você usou ao criar o aplicativo. Como senha, use aquela especificada ao criar a conta.
   
     ![inserir as credenciais](./media/resource-group-authenticate-service-principal/arm-get-credential.png)
3. Sempre que você entrar como uma entidade de serviço, precisará fornecer a ID do locatário do diretório para seu aplicativo do AD. Um locatário é uma instância do Active Directory. Se você tiver apenas uma assinatura, poderá usar:

   ```powershell   
   $tenant = (Get-AzureRmSubscription).TenantId
   ```
   
     Se você tiver mais de uma assinatura, especifique a assinatura onde o Active Directory reside. Para saber mais sobre, confira [Como as assinaturas do Azure são associadas ao Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md).

   ```powershell
   $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
   ```

4. Faça logon como a entidade de serviço especificando que essa conta é uma entidade de serviço e fornecendo o objeto das credenciais. 
   
   ```powershell
   Login-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $tenant
   ```
   
     Agora, você já deve ser autenticado como a entidade de serviço do aplicativo do Active Directory criado.

### <a name="save-access-token-to-simplify-log-in"></a>Salvar o token de acesso para simplificar o logon
Para evitar fornecer as credenciais de serviço principal toda vez que precisar fazer logon, você poderá salvar o token de acesso.

1. Para usar o token de acesso atual em uma sessão posterior, salve o perfil.
   
   ```powershell
   Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
   ```
   
     Abra o perfil e examine seu conteúdo. Observe que ele contém um token de acesso. 
2. Em vez fazer o logon manualmente de novo, basta carregar o perfil.
   
   ```powershell
   Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
   ```

  > [!NOTE]
  > O token de acesso expira, portanto, usar um perfil salvo funcionará somente se o token for válido.
  >  

Como alternativa, você pode invocar operações REST do PowerShell para fazer logon. Na resposta de autenticação, você pode recuperar o token de acesso para uso com outras operações. Para obter um exemplo de como recuperar o token de acesso invocando operações REST, consulte [Gerar um token de acesso](resource-manager-rest-api.md#generating-an-access-token).

## <a name="create-service-principal-with-certificate"></a>Criar a entidade de serviço com o certificado
Nesta seção, você deve executar as etapas para:

* criar um certificado autoassinado
* criar um aplicativo do AD com certificado
* criar a entidade de serviço
* atribuir a função Leitor à entidade de serviço

Para executar rapidamente essas etapas com o Azure PowerShell 2.0 no Windows 10 ou no Windows Server 2016 Technical Preview, consulte os seguintes cmdlets:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
$app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
Start-Sleep 15
New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId
```

O script fica suspenso 15 segundos para dar tempo à nova entidade de serviço de se propagar pelo Active Directory. Se o script não esperar tempo suficiente, você verá um erro dizendo: "PrincipalNotFound: a {id} da entidade não existe no diretório”. Se você receber esse erro, execute o cmdlet novamente para atribuí-lo a uma função.

Vejamos essas etapas com mais cuidado para assegurar que você entende o processo. Este artigo também mostra como realizar as tarefas ao usar as versões anteriores do Azure PowerShell ou dos sistemas operacionais.

### <a name="create-the-self-signed-certificate"></a>Criar um certificado autoassinado
A versão do PowerShell disponível com o Windows 10 e o Windows Server 2016 Technical Preview tem um cmdlet `New-SelfSignedCertificate` atualizado para gerar um certificado autoassinado. Os sistemas operacionais anteriores têm o cmdlet New-SelfSignedCertificate, mas ele não oferece os parâmetros necessários para este tópico. Em vez disso, você precisa importar um módulo para gerar o certificado. Este tópico mostra as duas abordagens para gerar o certificado com base no sistema operacional que você tem. 

* Se você tiver o **Windows 10 ou Windows Server 2016 Technical Preview**, execute o seguinte comando para criar um certificado autoassinado: 
   
  ```powershell
  $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
  ```
* Se você **não tiver o Windows 10 ou o Windows Server 2016 Technical Preview**, precisará baixar o [Gerador de certificado autoassinado](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) no Script Center da Microsoft. Extraia seu conteúdo e importe o cmdlet necessário.

  ```powershell  
  # Only run if you could not use New-SelfSignedCertificate
  Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
  ```
  
     Em seguida, gere o certificado.
  
  ```powershell
  $cert = New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
  ```

Você tem seu certificado e pode continuar a criar seu aplicativo do AD.

### <a name="create-the-active-directory-app-and-service-principal"></a>Criar o aplicativo do Active Directory e a entidade de serviço
1. Recupere o valor da chave a partir do certificado.
   
   ```powershell
   $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
   ```
2. Entre na sua conta do Azure.
   
   ```powershell
   Login-AzureRmAccount
   ```
3. Crie um novo aplicativo do Active Directory fornecendo um nome de exibição, URI que descreve o aplicativo, URIs que identificam seu aplicativo e senha para a identidade do seu aplicativo.
   
     Se você tiver o Azure PowerShell 2.0 (agosto de 2016 ou posterior), use o seguinte cmdlet:

   ```powershell   
   $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
   ```
   
    Se tiver o Azure PowerShell 1.0, use o seguinte cmdlet:

   ```powershell
   $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert  -EndDate $cert.NotAfter -StartDate $cert.NotBefore
   ```
   
    Para os aplicativos de locatário único, os URIs não são validados.
   
    Se sua conta não tiver as [permissões necessárias](#required-permissions) no Active Directory, você verá uma mensagem de erro informando "Authentication_Unauthorized" ou "Nenhuma assinatura encontrada no contexto".
   
    Examine o novo objeto de aplicativo. 
   
   ```powershell
   $app
   ```
   
    Observe a propriedade **ApplicationId** , que é necessária para criar as entidades de serviço, as atribuições de função e adquirir os tokens de acesso.

   ```powershell
   DisplayName             : exampleapp
   ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
   IdentifierUris          : {https://www.contoso.org/example}
   HomePage                : https://www.contoso.org
   Type                    : Application
   ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
   AvailableToOtherTenants : False
   AppPermissions          : 
   ReplyUrls               : {}
   ```
4. Crie uma entidade de serviço para seu aplicativo passando a id do aplicativo do Active Directory.
   
   ```powershell
   New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
   ```
5. Conceda à entidade de serviço permissões em sua assinatura. Neste exemplo, você adiciona a entidade de serviço à função Leitor , que concede permissão para ler todos os recursos na assinatura. Para ver outras funções, confira [RBAC: funções internas](../active-directory/role-based-access-built-in-roles.md). No caso do parâmetro `ServicePrincipalName`, forneça a `ApplicationId` que você usou ao criar o aplicativo. Antes de executar esse cmdlet, dê um tempo para a nova entidade de serviço se propagar pelo Active Directory. Geralmente, ao executar esses cmdlets manualmente, já se passou tempo suficiente entre os cmdlets. Em um script, adicione uma etapa de suspensão entre os comandos (como `Start-Sleep 15`). Se você vir um erro dizendo: "PrincipalNotFound: {id} da entidade não existe no diretório", execute o cmdlet novamente.
   
   ```powershell
   New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId
   ```
   
    Se sua conta não tiver permissões suficientes para atribuir uma função, verá uma mensagem de erro. A mensagem informa que sua conta “não tem autorização para executar a ação 'Microsoft.Authorization/roleAssignments/write' no escopo '/subscriptions/{guid}'”.

É isso! Seu aplicativo do AD e a entidade de serviço estão configurados. A próxima seção mostra como fazer logon com um certificado por meio do PowerShell.

### <a name="provide-certificate-through-automated-powershell-script"></a>Fornecer certificado por meio do script PowerShell automatizado
Sempre que você entrar como uma entidade de serviço, precisará fornecer a ID do locatário do diretório para seu aplicativo do AD. Um locatário é uma instância do Active Directory. Se você tiver apenas uma assinatura, poderá usar:

```powershell
$tenant = (Get-AzureRmSubscription).TenantId
```

Se você tiver mais de uma assinatura, especifique a assinatura onde o Active Directory reside. Para obter mais informações, consulte [Administrar seu diretório do AD do Azure](../active-directory/active-directory-administer.md).

```powershell
$tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Para autenticar em seu script, especifique que a conta é uma entidade de serviço e forneça a impressão digital do certificado, a id do aplicativo e a id do locatário. Para automatizar o script, você pode armazenar esses valores como variáveis de ambiente e recuperá-los durante a execução, ou pode incluí-los em seu script.

```powershell
Login-AzureRmAccount -ServicePrincipal -CertificateThumbprint $cert.Thumbprint -ApplicationId $app.ApplicationId -TenantId $tenant
```

Agora, você já deve ser autenticado como a entidade de serviço do aplicativo do Active Directory criado.

## <a name="change-credentials"></a>Alterar credenciais

Para alterar as credenciais para um aplicativo do AD, por causa de uma violação de segurança ou expiração de credencial, use os cmdlets [Remove-AzureRmADAppCredential](/powershell/resourcemanager/azurerm.resources/v3.3.0/remove-azurermadappcredential) e [New-AzureRmADAppCredential](/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermadappcredential).

Para remover todas as credenciais de um aplicativo, use:

```powershell
Remove-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -All
```

Para adicionar uma senha, use:

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -Password p@ssword!
```

Para adicionar um valor de certificado, crie um certificado autoassinado conforme mostrado neste tópico. Em seguida, use:

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
```

## <a name="sample-applications"></a>Aplicativos de exemplo
Os aplicativos de exemplo a seguir mostram como fazer logon como a entidade de serviço.

**.NET**

* [Implantar uma VM Habilitada para SSH com um Modelo com .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
* [Gerenciar recursos e grupos de recursos do Azure com .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

* [Introdução aos Recursos - Implantar Usando o Modelo do Azure Resource Manager - em Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
* [Introdução aos Recursos - Gerenciar o Grupo de Recursos - em Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

* [Implantar uma VM Habilitada para SSH com um Modelo no Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
* [Gerenciando o Recurso e Grupos de Recursos do Azure com o Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

* [Implantar uma VM Habilitada para SSH com um Modelo no Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
* [Gerenciar recursos e grupos de recursos do Azure com Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

* [Implantar uma VM Habilitada para SSH com um Modelo no Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
* [Gerenciando o Recurso e Grupos de Recursos do Azure com Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Próximas etapas
* Para ver as etapas detalhadas sobre como integrar um aplicativo no Azure para gerenciar os recursos, consulte [Guia do desenvolvedor para a autorização com a API do Azure Resource Manager](resource-manager-api-authentication.md).
* Para obter uma explicação mais detalhada de aplicativos e entidades de serviço, consulte [Objetos de aplicativo e de entidade de serviço](../active-directory/active-directory-application-objects.md). 
* Para obter mais informações sobre a autenticação do Active Directory, consulte [Cenários de Autenticação do Azure AD](../active-directory/active-directory-authentication-scenarios.md).


