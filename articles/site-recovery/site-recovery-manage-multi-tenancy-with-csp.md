---
title: "Gerenciar multilocação no Azure Site Recovery com o programa CSP (Provedor de Solução de Nuvem) | Microsoft Docs"
description: "Descreve como criar e gerenciar assinaturas de locatários por meio do CSP e implantar o Azure Site Recovery em uma configuração com vários locatários"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: manayar
ms.openlocfilehash: a49da33c8038ad467389c66e59727c7e195baf82
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2017
---
# <a name="manage-multi-tenancy-with-the-cloud-solution-provider-csp-program"></a>Gerenciar a multilocação com o programa CSP (Provedor de Solução de Nuvem)

O [Programa CSP](https://partner.microsoft.com/en-US/cloud-solution-provider) promove histórias de colaboração de sucesso para serviços em de nuvem da Microsoft, incluindo o Office 365, Enterprise Mobility Suite e o Microsoft Azure. Com o CSP, os parceiros controlam o relacionamento de ponta a ponta com os clientes e se tornam o principal ponto de contato desse relacionamento. Os parceiros pode implantar as assinaturas do Azure para os clientes e combiná-las com suas próprias ofertas personalizadas de valor agregado.

Com o Azure Site Recovery, os parceiros podem gerenciar a solução completa de Recuperação de Desastre para os clientes diretamente por meio de do CSP. Ou então, eles podem usar o CSP para configurar ambientes de Site Recovery e permitir que os clientes gerenciem suas próprias necessidades de recuperação de desastre como autoatendimento. Em ambos os cenários, os parceiros são o elo entre o Site Recovery e seus clientes. Os parceiros cuidam do relacionamento com o cliente e cobram pelo uso do Site Recovery.

Este artigo descreve como um parceiro pode criar e gerenciar assinaturas de locatários por meio do CSP, para uma configuração multilocatária do VMware.

## <a name="prerequisites"></a>pré-requisitos

- [Preparar](tutorial-prepare-azure.md) recursos do Azure, incluindo uma assinatura do Azure, uma rede virtual do Azure e uma conta de armazenamento.
- [Preparar](tutorial-prepare-on-premises-vmware.md) os servidores e as VMs VMware locais.
- Para cada locatário, crie um servidor de gerenciamento separado que possa se comunicar com as VMs do locatário e com o vCenter do parceiro. Somente o parceiro tem direitos de acesso a esse servidor. Para obter mais detalhes sobre ambientes multilocatários diferentes, consulte o guia [VMware multilocatário](site-recovery-multi-tenant-support-vmware-using-csp.md).

## <a name="create-a-tenant-account"></a>Criar uma conta de locatário

1. Entre na sua conta do CSP por meio do [Microsoft Partner Center](https://partnercenter.microsoft.com/).

2. No menu **Painel**, selecione **Clientes**.

    ![O link de clientes do Microsoft Partner Center](./media/site-recovery-manage-multi-tenancy-with-csp/csp-dashboard-display.png)

3. Na página que se abre, clique no botão **Adicionar cliente**.

    ![O botão Adicionar Cliente](./media/site-recovery-manage-multi-tenancy-with-csp/add-new-customer.png)

4. Na página **Novo Cliente**, preencha todos os detalhes de informações da conta para o locatário e clique em **Avançar: Assinaturas**.

    ![A página Informações da Conta](./media/site-recovery-manage-multi-tenancy-with-csp/customer-add-filled.png)

5. Na página de seleção de assinaturas, marque a caixa de seleção **Microsoft Azure**. Você pode adicionar outras assinaturas agora ou a qualquer momento.

    ![A caixa de seleção da assinatura do Microsoft Azure](./media/site-recovery-manage-multi-tenancy-with-csp/azure-subscription-selection.png)

6. Na página **Revisão**, confirme os detalhes do locatário e clique em **Enviar**.

    ![A página Revisão](./media/site-recovery-manage-multi-tenancy-with-csp/customer-summary-page.png)  

    Depois de criar a conta do locatário, uma página de confirmação é mostrada exibindo os detalhes da conta e a senha padrão para essa assinatura.

7. Salve as informações e altere a senha mais tarde, conforme necessário, na página de Logon do Portal do Azure.  

    Você pode compartilhar essas informações dessa forma com o locatário ou criar e compartilhar uma conta separada, se necessário.

## <a name="access-the-tenant-account"></a>Acessar a conta de locatário

Você pode acessar a assinatura do locatário por meio do Painel do Microsoft Partner Center, conforme descrito em “Etapa 1: criar uma conta de locatário”.

1. Acesse a página **Clientes** e clique no nome da conta de locatário.

2. Na página **Assinaturas** da conta do locatário, você pode monitorar as assinaturas existentes da conta e adicionar mais assinaturas conforme necessário. Para gerenciar operações de recuperação de desastre do locatário, selecione **Todos os recursos (Portal do Azure)**.

    ![O link Todos os Recursos](./media/site-recovery-manage-multi-tenancy-with-csp/all-resources-select.png)  

    Clicar em **Todos os recursos** concede a você o acesso às assinaturas do Azure do locatário. Você pode verificar o acesso clicando no link do Azure Active Directory na parte superior direita do Portal do Azure.

    ![Link do Azure Active Directory](./media/site-recovery-manage-multi-tenancy-with-csp/aad-admin-display.png)

Agora você pode executar todas as operações de recuperação de site para o locatário por meio do Portal do Azure e gerenciar as operações de recuperação de desastre. Para acessar a assinatura do locatário por meio de CSP para recuperação de desastre gerenciada, siga o processo descrito anteriormente.

## <a name="deploy-resources-to-the-tenant-subscription"></a>Implantar recursos na assinatura do locatário
1. No Portal do Azure, crie um grupo de recursos e implante um cofre dos Serviços de Recuperação de acordo com o processo normal.

2. Baixe a chave do registro do cofre.

3. Registre o CS para o locatário usando a chave de registro do cofre.

4. Insira as credenciais das duas contas de acesso: a conta de acesso do vCenter e a conta de acesso da VM.

    ![Contas do servidor de configurações do gerenciador](./media/site-recovery-manage-multi-tenancy-with-csp/config-server-account-display.png)

## <a name="register-site-recovery-infrastructure-to-the-recovery-services-vault"></a>Registrar a infraestrutura do Site Recovery no cofre dos Serviços de Recuperação
1. No Portal do Azure, no cofre que você criou anteriormente, registre o servidor do vCenter para o CS que você registrou na “Etapa 3: implantar recursos para a assinatura do locatário”. Use a conta de acesso do vCenter para essa finalidade.
2. Conclua o processo de “Preparação da infraestrutura” para o Site Recovery de acordo com o processo normal.
3. Agora, as VMs estão prontas para serem replicadas. Verifique se apenas as VMs do locatário estão visíveis na folha **Selecione as máquinas virtuais** na opção **Replicar**.

    ![Lista de VMs do locatário na folha Selecione as máquinas virtuais](./media/site-recovery-manage-multi-tenancy-with-csp/tenant-vm-display.png)

## <a name="assign-tenant-access-to-the-subscription"></a>Atribuir acesso do locatário à assinatura

Para recuperação de desastre de autoatendimento, forneça os detalhes da conta ao locatário, conforme mencionado no passo 6 da seção “Etapa 1: criar uma conta de locatário”. Execute esta ação depois que o parceiro configurar a infraestrutura de recuperação de desastre. Se o tipo de recuperação de desastre for gerenciado ou autoatendimento, os parceiros precisarão acessar as assinaturas de locatário por meio do portal do CSP. Eles configuram o cofre do parceiro e registram a infraestrutura para as assinaturas do locatário.

Os parceiros também podem adicionar um novo usuário à assinatura do locatário por meio do portal do CSP da seguinte maneira:

1. Acesse a página da assinatura do CSP do locatário e selecione a opção **Usuários e licenças**.

    ![A página da assinatura do CSP do locatário](./media/site-recovery-manage-multi-tenancy-with-csp/users-and-licences.png)

    Agora você pode criar um novo usuário inserindo os detalhes relevantes e selecionando as permissões ou carregando a lista de usuários em um arquivo CSV.

2. Depois de criar um novo usuário, volte para o Portal do Azure e, na folha **Assinatura**, selecione a assinatura relevante.

3. Na nova folha que se abre, selecione **IAM (Controle de Acesso)** e clique em **Adicionar** para adicionar um usuário com o nível de acesso relevante.      
    Os usuários criados por meio do Portal do CSP são exibidos automaticamente na folha que é aberta após você clicar em um nível de acesso.

    ![Adicionar um usuário](./media/site-recovery-manage-multi-tenancy-with-csp/add-user-subscription.png)

    Para a maioria das operações de gerenciamento, a função de *Colaborador* é suficiente. Usuários com esse nível de acesso podem fazer tudo em uma assinatura, exceto alterar os níveis de acesso (para tal é necessário o nível de acesso *Proprietário*).

  O Azure Site Recovery também tem três [funções de usuário predefinidas](site-recovery-role-based-linked-access-control.md) que podem ser usadas para restringir ainda mais os níveis de acesso conforme o necessário.

## <a name="next-steps"></a>Próximas etapas
  [Saiba mais](site-recovery-role-based-linked-access-control.md) sobre o controle de acesso baseado em função para gerenciar implantações do Azure Site Recovery.

  [Gerenciar ambientes multilocatários do VMware](site-recovery-multi-tenant-support-vmware-using-csp.md)
