---
title: Criar uma entidade de serviço para a pilha do Azure | Microsoft Docs
description: Descreve como criar uma entidade de serviço que pode ser usada com o controle de acesso baseado em função no Gerenciador de recursos do Azure para gerenciar o acesso aos recursos.
services: azure-resource-manager
documentationcenter: na
author: mattbriggs
manager: femila
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2018
ms.author: mabrigg
ms.openlocfilehash: de5712fd7b48a759b366f5b9808bbbefc6e305cd
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="give-applications-access-to-azure-stack-resources-by-creating-service-principals"></a>Conceder acesso de aplicativos aos recursos de pilha do Azure, Criando entidades de serviço

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode conceder um acesso de aplicativo aos recursos da pilha do Azure, criando um serviço principal que usa o Gerenciador de recursos do Azure. Uma entidade de serviço permite delegar permissões específicas usando [controle de acesso baseado em função](azure-stack-manage-permissions.md).

Como prática recomendada, você deve usar as entidades de serviço para seus aplicativos. Entidades de serviço são preferível à execução de um aplicativo usando suas próprias credenciais pelos seguintes motivos:

* Você pode atribuir permissões à entidade que são diferentes de suas próprias permissões de conta de serviço. Normalmente, as permissões de uma entidade de serviço são restritas a exatamente o que o aplicativo precisa fazer.
* Você não precisa alterar as credenciais do aplicativo se alteram a função ou responsabilidades.
* Você pode usar um certificado para automatizar a autenticação ao executar um script autônomo.

## <a name="example-scenario"></a>Cenário de exemplo

Você tem um aplicativo de gerenciamento de configuração que precisa de inventário de recursos do Azure usando o Gerenciador de recursos do Azure. Você pode criar um serviço principal e atribuí-lo à função Leitor. Essa função fornece ao aplicativo acesso somente leitura aos recursos do Azure.

## <a name="getting-started"></a>Introdução

Use as etapas neste artigo como um guia para:

* Crie uma entidade de serviço para seu aplicativo.
* Registrar seu aplicativo e crie uma chave de autenticação.
* Atribua o aplicativo a uma função.

A maneira como você configurou o Active Directory para a pilha do Azure determina como você pode criar uma entidade de serviço. Escolha uma das seguintes opções:

* Criar um serviço principal para [do Azure Active Directory (AD do Azure)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad).
* Criar um serviço principal para [os serviços de Federação do Active Directory (AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).

As etapas para atribuir uma entidade de serviço a uma função o mesmo para o Azure AD e o AD FS. Depois de criar a entidade de serviço, você pode [delegar permissões](azure-stack-create-service-principals.md#assign-role-to-service-principal) atribuindo-a uma função.

## <a name="create-a-service-principal-for-azure-ad"></a>Criar uma entidade de serviço do AD do Azure

Se a pilha do Azure usa o AD do Azure como o repositório de identidade, você pode criar um serviço principal usando as mesmas etapas do Azure, usando o portal do Azure.

>[!NOTE]
Verifique se você tem o [necessárias permissões do AD do Azure](../../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions) antes de começar a criar uma entidade de serviço.

### <a name="create-service-principal"></a>Criar uma entidade de serviço

Para criar uma entidade de serviço para seu aplicativo:

1. Entre na sua conta do Azure por meio do [Portal do Azure](https://portal.azure.com).
2. Selecione **Active Directory do Azure** > **registros do aplicativo** > **adicionar**.
3. Forneça um nome e uma URL para o aplicativo. Selecione **aplicativo Web/API** ou **Nativo** para o tipo de aplicativo que você deseja criar. Depois de definir os valores, selecione **Criar**.

### <a name="get-credentials"></a>Obter credenciais

Quando o log programaticamente, use a ID do seu aplicativo e uma chave de autenticação. Para obter estes valores:

1. De **Registros do Aplicativo** no Active Directory, selecione seu aplicativo.

2. Copie a **ID do aplicativo** e armazene-a no código do aplicativo. Os aplicativos a [aplicativos de exemplo](#sample-applications) usar **id do cliente** ao fazer referência ao **ID do aplicativo**.

     ![ID do aplicativo para o aplicativo](./media/azure-stack-create-service-principal/image12.png)
3. Para gerar uma chave de autenticação, selecione **Chaves**.

4. Forneça uma descrição da chave e uma duração para a chave. Ao terminar, escolha **Salvar**.

>[!IMPORTANT]
Depois de salvar a chave, a chave **valor** é exibido. Anote esse valor, porque você não pode recuperar a chave mais tarde. Armazene o valor da chave onde seu aplicativo possa recuperá-lo.

![Aviso de valor de chave para a chave salva.](./media/azure-stack-create-service-principal/image15.png)

A etapa final é [atribuição de uma função de aplicativo](azure-stack-create-service-principals.md#assign-role-to-service-principal).

## <a name="create-service-principal-for-ad-fs"></a>Criar entidade de serviço do AD FS

Se você implantou a pilha do Azure usando o AD FS como repositório de identidades, você pode usar o PowerShell para as seguintes tarefas:

* Crie uma entidade de serviço.
* Atribua a entidade de serviço a uma função.
* Entre usando a identidade da entidade de serviço.

### <a name="before-you-begin"></a>Antes de começar

[Baixe as ferramentas de pilha do Azure necessárias para o computador local.](azure-stack-powershell-download.md)

### <a name="import-the-identity-powershell-module"></a>Importe o módulo do PowerShell de identidade

Navegue até a pasta de download para as ferramentas de pilha do Azure e importe o módulo do PowerShell de identidade usando o seguinte comando:

```PowerShell
Import-Module .\Identity\AzureStack.Identity.psm1
```

Quando você importa o módulo de identidade, você pode receber essa mensagem de erro: "AzureStack.Connect.psm1 não foi assinado digitalmente. O script não será executado no sistema".

Para corrigir esse problema, você precisa configurar a política de execução para permitir a execução do script. Para definir a política de execução, execute o seguinte comando em uma sessão do PowerShell com privilégios elevados:

```PowerShell
Set-ExecutionPolicy Unrestricted
```

### <a name="create-the-service-principal"></a>Criar a entidade de serviço

Você pode criar uma entidade de serviço executando o comando a seguir, certificando-se de atualizar o **DisplayName** parâmetro:

```powershell
$servicePrincipal = New-AzSADGraphServicePrincipal `
 -DisplayName "<YourServicePrincipalName>" `
 -AdminCredential $(Get-Credential) `
 -AdfsMachineName "AZS-ADFS01" `
 -Verbose

```

### <a name="assign-a-role"></a>Atribuir uma função

Depois de criar a entidade de serviço, você deve [atribuí-la a uma função](azure-stack-create-service-principals.md#assign-role-to-service-principal).

### <a name="sign-in-using-powershell"></a>Entrar usando o PowerShell

Você pode entrar com a pilha do Azure executando o comando a seguir, certificando-se de atualizar o **EnvironmentName** parâmetro com o nome do seu aplicativo:

```powershell
Add-AzureRmAccount -EnvironmentName "<AzureStackEnvironmentName>" `
 -ServicePrincipal `
 -CertificateThumbprint $servicePrincipal.Thumbprint `
 -ApplicationId $servicePrincipal.ApplicationId `
 -TenantId $directoryTenantId
```

## <a name="assign-the-service-principal-to-a-role"></a>Atribuir a entidade de serviço a uma função

Para acessar recursos em sua assinatura, você deve atribuir o aplicativo a uma função. Decida qual função representa as permissões corretas para o aplicativo. Para saber mais sobre as funções disponíveis, consulte [RBAC: funções internas](../../role-based-access-control/built-in-roles.md).

>[!NOTE]
Você pode definir o escopo da função no nível de uma assinatura, um grupo de recursos ou um recurso. As permissão são herdadas para níveis inferiores do escopo. Por exemplo, um aplicativo com a função de leitor de um grupo de recursos significa que o aplicativo pode ler qualquer um dos recursos no grupo de recursos.

Use as etapas a seguir como um guia para atribuir uma função a uma entidade de serviço.

1. No portal do Azure pilha, navegue até o nível de escopo que você deseja atribuir o aplicativo. Por exemplo, para atribuir uma função no escopo da assinatura, escolha **Assinaturas**.

2. Selecione a assinatura para atribuir o aplicativo. Neste exemplo, a assinatura é o Visual Studio Enterprise.

     ![Selecione a assinatura do Visual Studio Enterprise para atribuição](./media/azure-stack-create-service-principal/image16.png)

3. Selecione **controle de acesso (IAM)** para a assinatura.

     ![Selecione o controle de acesso](./media/azure-stack-create-service-principal/image17.png)

4. Selecione **Adicionar**.

5. Selecione a função que deseja atribuir ao aplicativo.

6. Procure seu aplicativo e selecione-o.

7. Selecione **OK** para finalizar a atribuição da função. Você pode ver o seu aplicativo na lista de usuários atribuídos a uma função para esse escopo.

Agora que você criou uma entidade de serviço e atribuídos a uma função, seu aplicativo pode acessar recursos da pilha do Azure.

## <a name="next-steps"></a>Próximas etapas

[Gerenciar permissões de usuário](azure-stack-manage-permissions.md)
