---
title: "Criação de conta de usuário do Azure AD | Microsoft Docs"
description: "Este artigo descreve como criar uma credencial de conta de usuário do Azure AD para runbooks na Automação do Azure para autenticação no Azure."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
keywords: "usuário do azure active directory, gerenciamento de serviço do azure, conta de usuário do azure ad"
ms.assetid: fcfe266d-b22e-4dfb-8272-adcab09fc0cf
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2017
ms.author: magoedte
ms.openlocfilehash: f0a9664898cd27529daf73d130dd25fd296a9b48
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2018
---
# <a name="authenticate-runbooks-with-azure-classic-deployment-and-resource-manager"></a>Autenticar Runbooks com implantação clássica do Azure e do Resource Manager
Este artigo descreve as etapas que você deve executar para configurar uma conta de Usuário do Azure AD para runbooks de Automação do Azure em execução em relação a modelo de implantação clássico do Azure ou recursos do Azure Resource Manager.  Embora essa continue a ser uma identidade de autenticação com suporte para runbooks com base no Azure Resource Manager, o método recomendado é usar a conta Executar como do Azure.       

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
    > Se você optar por não criar a conta Executar Como selecionando a opção **Não**, verá uma mensagem de aviso com a folha **Adicionar Conta de Automação**.  Embora a conta seja criada e atribuída à função **Colaborador** na assinatura, ela não terá uma identidade de autenticação correspondente em seu serviço de diretório de assinaturas e, portanto, não haverá recursos de acesso em sua assinatura.  Isso impedirá qualquer runbook que faça referência a essa conta seja capaz de se autenticar e de executar tarefas nos recursos do Azure Resource Manager.
    > 
    >

    <br>![Aviso Adicionar Conta de Automação](media/automation-create-aduser-account/add-automation-acct-properties-error.png)<br>  
7. Enquanto o Azure cria a conta de Automação, você poderá acompanhar o andamento em **Notificações** no menu.

Quando a criação da credencial for concluída, você precisará criar um ativo de credencial para associar a conta de automação à conta de usuário do AD criada anteriormente.  Lembre-se de que apenas criamos a conta de Automação e ela não está associada a uma identidade de autenticação.  Execute as etapas descritas no [artigo sobre ativos de credencial na Automação do Azure](automation-credentials.md#creating-a-new-credential-asset) e insira o valor para **nome de usuário** no formato **domínio\usuário**.

## <a name="use-the-credential-in-a-runbook"></a>Usar uma credencial em um runbook
Você pode recuperar a credencial em um runbook usando a atividade [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) e, em seguida, usá-la com [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) para se conectar à sua assinatura do Azure. Se a credencial for um administrador de várias assinaturas do Azure, você também deverá usar [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) para especificar a correta. Isso é mostrado no exemplo do Windows PowerShell abaixo que, normalmente, aparece na parte superior da maioria dos runbooks da Automação do Azure.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
    Add-AzureAccount –Credential $cred
    Select-AzureSubscription –SubscriptionName "My Subscription"

Repita essas linhas após qualquer [ponto de verificação](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) em seu runbook. Se o runbook for suspenso e reiniciado em outro trabalho, será necessário executar novamente a autenticação.

## <a name="next-steps"></a>Próximas etapas
* Examine os diferentes tipos de runbook e as etapas para criar seus próprios runbooks no artigo [Tipos de runbook da Automação do Azure](automation-runbook-types.md)

