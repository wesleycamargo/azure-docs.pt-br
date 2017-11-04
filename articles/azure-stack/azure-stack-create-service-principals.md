---
title: "Criar uma entidade de serviço para a pilha do Azure | Microsoft Docs"
description: "Descreve como criar uma nova entidade de serviço que pode ser usada com o controle de acesso baseado em função no Gerenciador de recursos do Azure para gerenciar o acesso aos recursos."
services: azure-resource-manager
documentationcenter: na
author: heathl17
manager: byronr
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2017
ms.author: helaw
ms.openlocfilehash: 96d5cdfc28759fd516eab5fd97c6cf444af08cf6
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2017
---
# <a name="provide-applications-access-to-azure-stack"></a>Fornecer acesso de aplicativos a pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Quando um aplicativo precisa acessar implanta nem configura recursos por meio do Gerenciador de recursos do Azure na pilha do Azure, você pode criar um serviço principal, que é uma credencial para o seu aplicativo.  Em seguida, você pode delegar apenas as permissões necessárias para essa entidade de serviço.  

Por exemplo, você pode ter uma ferramenta de gerenciamento de configuração que usa o Gerenciador de recursos do Azure para recursos do Azure de inventário.  Nesse cenário, você pode criar uma entidade de serviço, conceda a função de leitor a essa entidade de serviço e limitar a ferramenta de gerenciamento de configuração para acesso somente leitura. 

Entidades de serviço são preferível executando o aplicativo com suas próprias credenciais, porque:

* Você pode atribuir permissões à entidade que são diferentes de suas próprias permissões de conta de serviço. Normalmente, essas permissões são restritas a exatamente o que o aplicativo precisa fazer.
* Você não precisa alterar as credenciais do aplicativo se alterar suas responsabilidades.
* Você pode usar um certificado para automatizar a autenticação ao executar um script autônomo.  

## <a name="getting-started"></a>Introdução

Dependendo de como você implantou pilha do Azure, comece criando um serviço principal.  Este documento orienta você pelo processo de criação de uma entidade de serviço para ambos [do Azure Active Directory (AD do Azure)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad) e [Active Directory Federation Services(AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).  Depois de criar a entidade de serviço, um conjunto de etapas comuns para AD FS e o Active Directory do Azure são usados para [delegar permissões](azure-stack-create-service-principals.md#assign-role-to-service-principal) para a função.     

## <a name="create-service-principal-for-azure-ad"></a>Criar entidade de serviço do AD do Azure

Se você implantou o Azure pilha usando o Azure AD como repositório de identidades, você pode criar entidades de serviço, exatamente como faria para o Azure.  Esta seção mostra como executar as etapas no portal.  Verifique se você tiver o [necessárias permissões do AD do Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions) antes de começar.

### <a name="create-service-principal"></a>Criar uma entidade de serviço
Nesta seção, você criará um aplicativo (entidade de serviço) no AD do Azure que representa o aplicativo.

1. Entre na sua conta do Azure por meio do [Portal do Azure](https://portal.azure.com).
2. Selecione **Active Directory do Azure** > **registros do aplicativo** > **adicionar**   
3. Forneça um nome e uma URL para o aplicativo. Selecione **aplicativo Web/API** ou **Nativo** para o tipo de aplicativo que você deseja criar. Depois de definir os valores, selecione **Criar**.

Você criou uma entidade de serviço para seu aplicativo.

### <a name="get-credentials"></a>Obter credenciais
Ao fazer logon por meio de programação, você use a ID do seu aplicativo e uma chave de autenticação. Para obter esses valores, use as seguintes etapas:

1. De **Registros do Aplicativo** no Active Directory, selecione seu aplicativo.

2. Copie a **ID do aplicativo** e armazene-a no código do aplicativo. Os aplicativos na seção [aplicativos de exemplo](#sample-applications) referem-se a esse valor como a ID do cliente.

     ![ID do CLIENTE](./media/azure-stack-create-service-principal/image12.png)
3. Para gerar uma chave de autenticação, selecione **Chaves**.

4. Forneça uma descrição da chave e uma duração para a chave. Ao terminar, escolha **Salvar**.

Após salvar a chave, o valor da chave é exibido. Copie este valor, pois não é possível recuperar a chave posteriormente. Você pode fornecer o valor de chave com a ID do aplicativo para assinar o aplicativo. Armazene o valor da chave onde seu aplicativo possa recuperá-lo.

![chave salva](./media/azure-stack-create-service-principal/image15.png)


Uma vez concluído, vá para [atribuição de uma função de aplicativo](azure-stack-create-service-principals.md#assign-role-to-service-principal).

## <a name="create-service-principal-for-ad-fs"></a>Criar entidade de serviço do AD FS
Se você tiver implantado a pilha do Azure com o AD FS, você pode usar o PowerShell para criar uma entidade de serviço, atribuir uma função de acesso e entrar no PowerShell usando essa identidade.

O script é executado do ponto de extremidade com privilégios em uma máquina virtual ERCS.


Requisitos:
- Um certificado é necessário.

**Parâmetros**

As informações a seguir são necessárias como entrada para os parâmetros de automação:


|Parâmetro|Descrição|Exemplo|
|---------|---------|---------|
|Nome|Nome da conta SPN|MyAPP|
|ClientCertificates|Matriz de objetos de certificado|X509 certificado|
|ClientRedirectUris<br>(Opcional)|URI de redirecionamento do aplicativo|         |

**Exemplo**

1. Abra uma sessão do Windows PowerShell com privilégios elevados e execute os seguintes comandos:

   > [!NOTE]
   > Este exemplo cria um certificado autoassinado. Quando você executar esses comandos em uma implantação de produção, use Get-certificado para recuperar o objeto de certificado para o certificado que você deseja usar.

   ```
   $creds = Get-Credential

   $session = New-PSSession -ComputerName <IP Address of ECRS> -ConfigurationName PrivilegedEndpoint -Credential $creds

   $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=testspn2" -KeySpec KeyExchange

   Invoke-Command -Session $session -ScriptBlock { New-GraphApplication -Name 'MyApp' -ClientCertificates $using:cert}

   $session|remove-pssession

   ```

2. Após a conclusão da automação, ele exibe os detalhes necessários para usar o SPN. 

   Por exemplo:

   ```
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```
### <a name="assign-a-role"></a>Atribuir uma função
Depois que a entidade de serviço é criada, você deve [atribuí-la a uma função](azure-stack-create-service-principals.md#assign-role-to-service-principal)

### <a name="sign-in-through-powershell"></a>Entrar por meio do PowerShell
Depois que você tenha atribuído a uma função, você pode entrar pilha do Azure usando a entidade de serviço com o seguinte comando:

```powershell
Add-AzureRmAccount -EnvironmentName "<AzureStackEnvironmentName>" `
 -ServicePrincipal `
 -CertificateThumbprint $servicePrincipal.Thumbprint `
 -ApplicationId $servicePrincipal.ApplicationId ` 
 -TenantId $directoryTenantId
```

## <a name="assign-role-to-service-principal"></a>Atribuir função a entidade de serviço
Para acessar recursos em sua assinatura, você deve atribuir o aplicativo a uma função. Decida qual função representa as permissões corretas para o aplicativo. Para saber mais sobre as funções disponíveis, consulte [RBAC: funções internas](../active-directory/role-based-access-built-in-roles.md).

Você pode definir o escopo no nível da assinatura, do grupo de recursos ou do recurso. As permissão são herdadas para níveis inferiores do escopo. Por exemplo, adicionar um aplicativo à função Leitor de um grupo de recursos significa que ele pode ler o grupo de recursos e todos os recursos que ele contiver.

1. No portal do Azure pilha, navegue até o nível de escopo que você deseja atribuir o aplicativo. Por exemplo, para atribuir uma função no escopo da assinatura, escolha **Assinaturas**. Em vez disso, você pode selecionar um grupo de recursos ou um recurso.

2. Escolha a assinatura específica (grupo de recursos ou recurso) à qual atribuir o aplicativo.

     ![selecionar assinatura para atribuição](./media/azure-stack-create-service-principal/image16.png)

3. Selecione **Controle de Acesso (IAM)**.

     ![selecionar acesso](./media/azure-stack-create-service-principal/image17.png)

4. Selecione **Adicionar**.

5. Selecione a função que deseja atribuir ao aplicativo.

6. Procure seu aplicativo e selecione-o.

7. Selecione **OK** para finalizar a atribuição da função. Agora você vê o aplicativo na lista de usuários atribuídos a uma função para esse escopo.

Agora que você criou uma entidade de serviço e atribuídos a uma função, você pode começar a usar isso dentro de seu aplicativo para acessar recursos da pilha do Azure.  

## <a name="next-steps"></a>Próximas etapas

[Adicionar usuários para que o ADFS](azure-stack-add-users-adfs.md)
[gerenciar permissões de usuário](azure-stack-manage-permissions.md)