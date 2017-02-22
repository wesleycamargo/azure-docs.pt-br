---
title: "Configurar a conta de usuário do Azure AD | Microsoft Docs"
description: "Este artigo descreve como configurar para autenticação a credencial da conta de Usuário do Azure AD para runbooks na Automação do Azure."
services: automation
documentationcenter: 
author: MGoedtel
manager: jwhit
editor: tysonn
keywords: "usuário do azure active directory, gerenciamento de serviço do azure, conta de usuário do azure ad"
ms.assetid: fcfe266d-b22e-4dfb-8272-adcab09fc0cf
ms.service: automation
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/14/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 0078d544d5c30e31874d75fece62ca560d2aa2d6
ms.openlocfilehash: 0544d4df8f50db2182375aec8ec2a0a9d47ed644


---
# <a name="authenticate-runbooks-with-azure-service-management-and-resource-manager"></a>Autenticar Runbooks com o Gerenciador de Recursos e o Gerenciamento de Serviços do Azure
Este artigo descreve as etapas que você deve executar para configurar uma conta de Usuário do Azure AD para runbooks de Automação do Azure em execução em relação a recursos do Gerenciamento de Serviços do Azure ou do Azure Resource Manager.  Embora essa continue a ser uma identidade de autenticação com suporte para runbooks com base no Azure Resource Manager, o método recomendado é usar a nova conta Executar como do Azure.       

## <a name="create-a-new-azure-active-directory-user"></a>Criar um novo usuário do Azure Active Directory
1. Faça logon no Portal Clássico do Azure como administrador de serviço para a assinatura do Azure que você deseja gerenciar.
2. Selecione **Active Directory**e selecione o nome do diretório da sua organização.
3. Selecione a guia **Usuários** e, na área de comando, selecione **Adicionar Usuário**.
4. Na página **Conte-nos sobre este usuário**, em **Tipo de usuário**, selecione **Novo usuário na sua organização**.
5. Insira um nome de usuário.  
6. Selecione o nome do diretório associado à sua assinatura do Azure na página do Active Directory.
7. Na página **perfil do usuário**, forneça um nome e um sobrenome, um nome amigável e Usuário na lista **Funções**.  Não selecione **Habilitar Multi-Factor Authentication**.
8. Anote o nome completo do usuário e a senha temporária.
9. Selecione **Configurações > Administradores > Adicionar**.
10. Digite o nome de usuário completo do usuário que você criou.
11. Selecione a assinatura que você deseja que o usuário gerencie.
12. Faça logoff do Azure e, em seguida, faça logon novamente com a conta que você acabou de criar. Você será solicitado a alterar a senha do usuário.

## <a name="create-an-automation-account-in-azure-classic-portal"></a>Criar uma conta de Automação no Portal Clássico do Azure
Nesta seção, você executará as etapas a seguir para criar uma nova conta de Automação do Azure no Portal do Azure que será usada com seus runbooks para o gerenciamento de recursos nos modos do Gerenciador de Serviços do Azure e do Azure Resource Manager.  

> [!NOTE]
> Contas de automação criadas com o Portal Clássico do Azure podem ser gerenciadas pelo Portal Clássico do Azure e pelo Portal do Azure e qualquer um dos conjuntos de cmdlets. Depois que a conta é criada, não faz diferença como você cria e gerencia recursos dentro da conta. Se planeja continuar a usar o Portal Clássico do Azure, você deve usá-lo em vez do Portal do Azure para criar qualquer conta de Automação.
> 
> 

1. Faça logon no Portal Clássico do Azure como administrador de serviço para a assinatura do Azure que você deseja gerenciar.
2. Selecione **Automação**.
3. Na página **Automação**, selecione **Criar uma Conta de Automação**.
4. Na caixa **Criar uma Conta de Automação**, digite um nome para a nova conta de Automação e selecione uma **Região** na lista suspensa.  
5. Clique em **OK** para aceitar as configurações e criar a conta.
6. Após ser criada, ela será listada na página **Automação** .
7. Clique na conta e você será levado para página Painel.  
8. Na página Painel de Automação, selecione **Ativos**.
9. Na página **Ativos**, selecione a opção **Adicionar Configurações**, localizada na parte inferior da página.
10. Na página **Adicionar Configurações**, selecione **Adicionar Credencial**.
11. Na página **Definir Credencial**, selecione **Credencial do Windows PowerShell** na lista suspensa **Tipo de Credencial** e forneça um nome para a credencial.
12. Na página **Definir Credencial** a seguir, digite o nome de usuário da conta de usuário do AD criada anteriormente no campo **Nome de Usuário** e a senha nos campos **Senha** e **Confirmar Senha**. Clique em **OK** para salvar as alterações.

## <a name="create-an-automation-account-in-the-azure-portal"></a>Criar uma conta de Automação no Portal do Azure
Nesta seção, você executará as etapas a seguir para criar uma nova conta de Automação do Azure no Portal do Azure que será usada com seus recursos de gerenciamento de runbooks no modo do Azure Resource Manager.  

1. Faça logon no portal do Azure como administrador de serviço para a assinatura do Azure que você deseja gerenciar.
2. Selecione **Contas de Automação**.
3. Na folha Contas de Automação, clique em **Adicionar**.<br>![Adicionar Conta de Automação](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties.png)
4. Na folha **Adicionar Conta de Automação**, na caixa **Nome**, digite um nome para a nova conta de Automação.
5. Se você tiver mais de uma assinatura, especifique a assinatura certa para a nova conta, bem como um **Grupo de recursos** novo ou existente e um **Local** de datacenter do Azure.
6. Selecione o valor **Não** para a opção **Criar conta Executar como do Azure** e clique no botão **Criar**.  
   
   > [!NOTE]
   > Se você optar por não criar a conta Executar Como selecionando a opção **Não**, verá uma mensagem de aviso com a folha **Adicionar Conta de Automação**.  Embora a conta seja criada e atribuída à função **Colaborador** na assinatura, ela não terá uma identidade de autenticação correspondente em seu serviço de diretório de assinaturas e, portanto, não haverá recursos de acesso em sua assinatura.  Isso impedirá qualquer runbook que faça referência a essa conta seja capaz de se autenticar e de executar tarefas nos recursos do Azure Resource Manager.
   > 
   > 
   
    ![Aviso Adicionar Conta de Automação](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties-error.png)
7. Enquanto o Azure cria a conta de Automação, você poderá acompanhar o andamento em **Notificações** no menu.

Quando a criação da credencial for concluída, você precisará criar um Ativo de Credencial para associar a Conta de Automação à conta de Usuário do AD criada anteriormente.  Lembre-se de que apenas criamos a conta de Automação e ela não está associada a uma identidade de autenticação.  Execute as etapas descritas no [artigo sobre ativos de credencial na Automação do Azure](automation-credentials.md#creating-a-new-credential-asset) e insira o valor para **nome de usuário** no formato **domínio\usuário**.

## <a name="use-the-credential-in-a-runbook"></a>Usar uma credencial em um runbook
Você pode recuperar a credencial em um runbook usando a atividade [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) e, em seguida, usá-la com [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) para se conectar à sua assinatura do Azure. Se a credencial for um administrador de várias assinaturas do Azure, você também deverá usar [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) para especificar a correta. Isso é mostrado no exemplo do Windows PowerShell abaixo que, normalmente, aparece na parte superior da maioria dos runbooks da Automação do Azure.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
    Add-AzureAccount –Credential $cred
    Select-AzureSubscription –SubscriptionName "My Subscription"

Repita essas linhas após qualquer [ponto de verificação](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) em seu runbook. Se o runbook for suspenso e reiniciado em outro trabalho, será necessário executar novamente a autenticação.

## <a name="next-steps"></a>Próximas etapas
* Examine os diferentes tipos de runbook e as etapas para criar seus próprios runbooks no artigo [Tipos de runbook da Automação do Azure](automation-runbook-types.md)




<!--HONumber=Nov16_HO3-->


