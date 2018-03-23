---
title: Configurar a replicação do VMware no Azure no ambiente de multilocação usando o Site Recovery e o programa CSP (Provedor de Solução de Nuvem) | Microsoft Docs
description: Descreve como criar e gerenciar assinaturas de locatários por meio do CSP e implantar o Azure Site Recovery em uma configuração com vários locatários
services: site-recovery
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: manayar
ms.openlocfilehash: 25591acb3f046744400f5dcf20a7ea651a7bcf54
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2018
---
# <a name="set-up-vmware-replication-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>Configurar a replicação do VMware em um ambiente de multilocação com o programa CSP (Provedor de Solução de Nuvem)

O [Programa CSP](https://partner.microsoft.com/en-US/cloud-solution-provider) promove histórias de colaboração de sucesso para serviços em de nuvem da Microsoft, incluindo o Office 365, Enterprise Mobility Suite e o Microsoft Azure. Com o CSP, os parceiros controlam o relacionamento de ponta a ponta com os clientes e se tornam o principal ponto de contato desse relacionamento. Os parceiros podem implantar as assinaturas do Azure para os clientes e combiná-las com suas próprias ofertas personalizadas de valor agregado.

Com o [Azure Site Recovery](site-recovery-overview.md), como parceiros, você pode gerenciar a recuperação de desastre para os clientes diretamente por meio de do CSP. Como alternativa, você pode usar o CSP para configurar ambientes de Site Recovery e permitir que os clientes gerenciem suas próprias necessidades de recuperação de desastre como autoatendimento. Em ambos os cenários, os parceiros são o elo entre o Site Recovery e seus clientes. Os parceiros cuidam do relacionamento com o cliente e cobram pelo uso do Site Recovery.

Este artigo descreve como você como um parceiro pode criar e gerenciar assinaturas de locatários por meio do CSP, para um cenário de replicação do VMware multilocatário.

## <a name="prerequisites"></a>pré-requisitos

Para configurar a replicação do VMware, você precisa fazer o seguinte:

- [Preparar](tutorial-prepare-azure.md) recursos do Azure, incluindo uma assinatura do Azure, uma rede virtual do Azure e uma conta de armazenamento.
- [Preparar](vmware-azure-tutorial-prepare-on-premises.md) as VMs e os servidores VMware locais. 
- Para cada locatário, crie um servidor de gerenciamento separado que possa se comunicar com as VMs do locatário e seus servidores vCenter. Somente você como um parceiro deve ter direitos de acesso ao servidor de gerenciamento. Saiba mais sobre [ambientes multilocatários](vmware-azure-multi-tenant-overview.md).

## <a name="create-a-tenant-account"></a>Criar uma conta de locatário

1. Entre na sua conta do CSP por meio do [Microsoft Partner Center](https://partnercenter.microsoft.com/).
2. No menu **Painel**, selecione **Clientes**.
3. Na página que se abre, clique no botão **Adicionar cliente**.
4. Na página **Novo Cliente**, preencha todos os detalhes de informações da conta para o locatário. 

    ![A página Informações da Conta](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. Em seguida, clique em **Próximo: assinaturas**.
6. Na página de seleção de assinaturas, marque a caixa de seleção **Microsoft Azure**. Você pode adicionar outras assinaturas agora ou a qualquer momento.
7. Na página **Revisão**, confirme os detalhes do locatário e clique em **Enviar**.
8. Depois de criar a conta do locatário, uma página de confirmação é mostrada exibindo os detalhes da conta e a senha padrão para essa assinatura. Salve as informações e altere a senha mais tarde, conforme necessário, na página de Logon do Portal do Azure.

Você pode compartilhar essas informações dessa forma com o locatário ou criar e compartilhar uma conta separada, se necessário.

## <a name="access-the-tenant-account"></a>Acessar a conta de locatário

Você pode acessar a assinatura do locatário por meio do Painel do Microsoft Partner Center.

1. Na página **Clientes**, clique no nome da conta de locatário.
2. Na página **Assinaturas** da conta do locatário, você pode monitorar as assinaturas existentes da conta e adicionar mais assinaturas, conforme necessário.
3. Para gerenciar operações de recuperação de desastre do locatário, selecione **Todos os recursos (Portal do Azure)**. Isso concede a você o acesso às assinaturas do Azure do locatário.

    ![O link Todos os Recursos](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. Você pode verificar o acesso clicando no link do Azure Active Directory na parte superior direita do Portal do Azure.

    ![Link do Azure Active Directory](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

Agora você pode executar e gerenciar todas as operações do Site Recovery do locatário no portal do Azure. Para acessar a assinatura do locatário por meio de CSP para recuperação de desastre gerenciada, siga o processo descrito anteriormente.

## <a name="deploy-resources-to-the-tenant-subscription"></a>Implantar recursos na assinatura do locatário

1. No Portal do Azure, crie um grupo de recursos e implante um cofre dos Serviços de Recuperação de acordo com o processo normal.
2. Baixe a chave do registro do cofre.
3. Registre o CS para o locatário usando a chave de registro do cofre.

4. Insira as credenciais para as duas contas de acesso, a conta para acessar o servidor vCenter e a conta para acessar a máquina virtual.

    ![Contas do servidor de configurações do gerenciador](./media/vmware-azure-multi-tenant-csp-disaster-recovery/config-server-account-display.png)

## <a name="register-servers-in-the-vault"></a>Registrar servidores no cofre

1. No portal do Azure, no cofre que você criou anteriormente, registre o servidor vCenter para o servidor de configuração, usando a conta do vCenter que você criou. 
2. Conclua o processo de “Preparação da infraestrutura” para o Site Recovery de acordo com o processo normal.
3. Agora, as VMs estão prontas para serem replicadas. Verifique se apenas as VMs do locatário estão visíveis em **Replicar** > **Selecionar as máquinas virtuais**.


## <a name="assign-tenant-access-to-the-subscription"></a>Atribuir acesso do locatário à assinatura

1. Certifique-se de que a infra-estrutura de recuperação de desastres está configurada. Observe que você deve acessar as assinaturas de locatários por meio do portal CSP, independentemente de se a recuperação de desastres for gerenciada ou autoatendimento. Você deve configurar seu cofre e registrar a infraestrutura para as assinaturas do locatário.
2. Forneça o locatário com a [conta que você criou](#create-a-tenant-account)
3. Você pode adicionar um novo usuário à assinatura de locatário por meio do portal do CSP da seguinte maneira:

    a) Acesse a página da assinatura do CSP do locatário e selecione a opção **Usuários e licenças**.

        ![The tenant's CSP subscription page](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

    b) Agora crie um novo usuário inserindo os detalhes relevantes e selecionando as permissões ou carregando a lista de usuários em um arquivo CSV.
    c) Depois de criar um novo usuário, volte para o portal do Azure. Na página **Assinatura**, selecione a assinatura relevante.
    d) Selecione **IAM (Controle de Acesso)** e clique em **Adicionar** para adicionar um usuário com o nível de acesso relevante. Os usuários criados por meio do Portal do CSP são exibidos automaticamente na página que é aberta após você clicar em um nível de acesso.

        ![Add a user](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- Para a maioria das operações de gerenciamento, a função de *Colaborador* é suficiente. Usuários com esse nível de acesso podem fazer tudo em uma assinatura, exceto alterar os níveis de acesso (para tal é necessário o nível de acesso *Proprietário*).
- O Site Recovery também tem três [funções de usuário predefinidas](site-recovery-role-based-linked-access-control.md) que podem ser usadas para restringir ainda mais os níveis de acesso conforme o necessário.

## <a name="next-steps"></a>Próximas etapas
- [Saiba mais](site-recovery-role-based-linked-access-control.md) sobre o controle de acesso baseado em função para gerenciar implantações do Azure Site Recovery.
- [Saiba mais](vmware-azure-architecture.md) sobre a arquitetura de replicação de VMware para o Azure.
- [Revise o tutorial](vmware-azure-tutorial.md) para replicar VMs VMware no Azure.
