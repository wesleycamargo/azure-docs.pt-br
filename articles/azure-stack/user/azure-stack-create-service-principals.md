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
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 058b01a37e2858801895fd22cf73dd6bd342ca04
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
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

Se você implantou o Azure pilha usando o Azure AD como repositório de identidades, você pode criar entidades de serviço, exatamente como faria para o Azure.  Esta seção mostra como executar as etapas no portal.  Verifique se você tiver o [necessárias permissões do AD do Azure](../../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions) antes de começar.

### <a name="create-service-principal"></a>Criar uma entidade de serviço
Nesta seção, você criará um aplicativo (entidade de serviço) no AD do Azure que representarão seu aplicativo.

1. Entre na sua conta do Azure por meio do [Portal do Azure](https://portal.azure.com).
2. Selecione **Active Directory do Azure** > **registros do aplicativo** > **adicionar**   
3. Forneça um nome e uma URL para o aplicativo. Selecione **aplicativo Web/API** ou **Nativo** para o tipo de aplicativo que você deseja criar. Depois de definir os valores, selecione **Criar**.

Você criou uma entidade de serviço para seu aplicativo.

### <a name="get-credentials"></a>Obter credenciais
Ao fazer logon por meio de programação, você use a ID do seu aplicativo e uma chave de autenticação. Para obter esses valores, use as seguintes etapas:

1. De **Registros do Aplicativo** no Active Directory, selecione seu aplicativo.

2. Copie a **ID do aplicativo** e armazene-a no código do aplicativo. Os aplicativos na seção [Aplicativos de exemplo](#sample-applications) referem-se a esse valor como a ID do cliente.

     ![ID do CLIENTE](./media/azure-stack-create-service-principal/image12.png)
3. Para gerar uma chave de autenticação, selecione **Chaves**.

4. Forneça uma descrição da chave e uma duração para a chave. Ao terminar, escolha **Salvar**.

Após salvar a chave, o valor da chave é exibido. Copie este valor, pois não é possível recuperar a chave posteriormente. Você pode fornecer o valor de chave com a ID do aplicativo para assinar o aplicativo. Armazene o valor da chave onde seu aplicativo possa recuperá-lo.

![chave salva](./media/azure-stack-create-service-principal/image15.png)


Uma vez concluído, vá para [atribuição de uma função de aplicativo](azure-stack-create-service-principals.md#assign-role-to-service-principal).

## <a name="create-service-principal-for-ad-fs"></a>Criar entidade de serviço do AD FS
Se você tiver implantado a pilha do Azure com o AD FS, você pode usar o PowerShell para criar uma entidade de serviço, atribuir uma função de acesso e entrar no PowerShell usando essa identidade.

### <a name="before-you-begin"></a>Antes de começar

[Baixe as ferramentas necessárias para trabalhar com a pilha do Azure para o computador local.](azure-stack-powershell-download.md)

### <a name="import-the-identity-powershell-module"></a>Importe o módulo do PowerShell de identidade
Depois de baixar as ferramentas, navegue até a pasta de download e importar o módulo do PowerShell de identidade usando o seguinte comando:

```PowerShell
Import-Module .\Identity\AzureStack.Identity.psm1
```

Quando você importa o módulo, você poderá receber um erro que diz "AzureStack.Connect.psm1 não foi assinado digitalmente. O script não será executado no sistema". Para resolver esse problema, você pode definir a política de execução para permitir a execução do script com o seguinte comando em uma sessão do PowerShell com privilégios elevados:

```PowerShell
Set-ExecutionPolicy Unrestricted
```

### <a name="create-the-service-principal"></a>Criar a entidade de serviço
Você pode criar uma entidade de serviço executando o comando a seguir, certificando-se de atualizar o *DisplayName* parâmetro:
```powershell
$servicePrincipal = New-AzSADGraphServicePrincipal `
 -DisplayName "<YourServicePrincipalName>" `
 -AdminCredential $(Get-Credential) `
 -AdfsMachineName "AZS-ADFS01" `
 -Verbose
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
Para acessar recursos em sua assinatura, você deve atribuir o aplicativo a uma função. Decida qual função representa as permissões corretas para o aplicativo. Para saber mais sobre as funções disponíveis, consulte [RBAC: funções internas](../../active-directory/role-based-access-built-in-roles.md).

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

[Gerenciar permissões de usuário](azure-stack-manage-permissions.md)