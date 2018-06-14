---
title: Criar Conta de Usuário do Microsoft Azure AD
description: Este artigo descreve como criar uma credencial de conta de usuário do Azure AD para runbooks na Automação do Azure para autenticação no Azure.
keywords: usuário do azure active directory, gerenciamento de serviço do azure, conta de usuário do azure ad
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 105dcf5564ec5c1d0b3528e9b5667d89d98c6cab
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195498"
---
# <a name="authenticate-runbooks-with-azure-classic-deployment-and-resource-manager"></a>Autenticar Runbooks com implantação clássica do Azure e do Resource Manager
Este artigo descreve as etapas que você deve executar para configurar uma conta de Usuário do Azure AD para runbooks de Automação do Azure em execução em relação a modelo de implantação clássico do Azure ou recursos do Azure Resource Manager. Embora essa continue a ser uma identidade de autenticação com suporte para runbooks com base no Azure Resource Manager, o método recomendado é usar a conta Executar como do Azure.       

## <a name="create-a-new-azure-active-directory-user"></a>Criar um novo usuário do Azure Active Directory
1. Faça logon no portal do Azure como administrador de serviço para a assinatura do Azure que você deseja gerenciar.
2. Selecione **Azure Active Directory** > **Usuários e grupos** > **Todos os usuários** > **Novo usuário**.
3. Insira os detalhes do usuário, como **Nome** e **Nome de usuário**.  
4. Anote o nome completo do usuário e a senha temporária.
5. Selecione **Função do diretório**.
6. Atribua a função Global ou Administrador Limitado.
7. Faça logoff do Azure e, em seguida, faça logon novamente com a conta que você acabou de criar. Você será solicitado a alterar a senha do usuário.

## <a name="create-an-automation-account-in-the-azure-portal"></a>Como criar uma conta de automação no portal do Azure
Nesta seção, você executará as etapas a seguir para criar uma nova conta de automação do Azure no portal do Azure que será usada com seus recursos de gerenciamento de runbooks no modo do Azure Resource Manager.  

1. Faça logon no portal do Azure como administrador de serviço para a assinatura do Azure que você deseja gerenciar.
2. Selecione **Contas de Automação**.
3. Selecione **Adicionar**.<br><br>![Adicionar Conta de Automação](media/automation-create-aduser-account/add-automation-acct-properties.png)
4. Na folha **Adicionar Conta de Automação**, na caixa **Nome**, digite um nome para a nova conta de Automação.
5. Se você tiver mais de uma assinatura, especifique a assinatura certa para a nova conta, bem como um **Grupo de recursos** novo ou existente e um **Local** de datacenter do Azure.
6. Selecione o valor **Sim** para a opção **Criar uma conta Executar como do Azure** e clique no botão **Criar**.  
   
    > [!NOTE]
    > Se você escolher por não criar a conta Executar Como, selecionando a opção **Não**, uma mensagem de aviso será exibida na folha **Adicionar Conta de Automação** . Embora a conta seja criada e atribuída à função **Colaborador** na assinatura, ela não tem uma identidade de autenticação correspondente em seu serviço de diretório de assinaturas e, portanto, não há recursos de acesso em sua assinatura. Isso impede que quaisquer runbooks que fazem referência a essa conta possam autenticar e executar tarefas nos recursos do Azure Resource Manager.
    > 
    >

    <br>![Aviso Adicionar Conta de Automação](media/automation-create-aduser-account/add-automation-acct-properties-error.png)<br>  
7. Enquanto o Azure cria a conta de Automação, você poderá acompanhar o andamento em **Notificações** no menu.

Quando a criação da credencial for concluída, você precisará criar um ativo de credencial para associar a conta de automação à conta de usuário do AD criada anteriormente. Lembre-se, você criou apenas a conta de automação e essa conta não está associada a uma identidade de autenticação. Execute as etapas descritas no [artigo sobre ativos de credencial na Automação do Azure](automation-credentials.md#creating-a-new-credential-asset) e insira o valor para **nome de usuário** no formato **domínio\usuário**.

## <a name="use-the-credential-in-a-runbook"></a>Usar uma credencial em um runbook
Você pode recuperar a credencial em um runbook usando a atividade [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) e, em seguida, usá-la com [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) para se conectar à sua assinatura do Azure. Se a credencial for um administrador de várias assinaturas do Azure, você também deverá usar [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) para especificar a correta. Isso é mostrado no exemplo do PowerShell a seguir, que geralmente aparece na parte superior da maioria dos runbooks de Automação do Azure.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
    Add-AzureAccount –Credential $cred
    Select-AzureSubscription –SubscriptionName "My Subscription"

Repita essas linhas após quaisquer [pontos de verificação](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) no runbook. Se o runbook for suspenso e, em seguida, for retomado em outro trabalho, ele precisará executar a autenticação novamente.

## <a name="next-steps"></a>Próximas etapas
* Examine os diferentes tipos de runbook e as etapas para criar seus próprios runbooks no artigo [Tipos de runbook da Automação do Azure](automation-runbook-types.md)

