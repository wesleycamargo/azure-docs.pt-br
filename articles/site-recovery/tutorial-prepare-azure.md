---
title: Preparar o Azure para a recuperação de desastre de computadores locais com o Azure Site Recovery | Microsoft Docs
description: Saiba como preparar o Azure para a recuperação de desastre de computadores locais usando o Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 8bdb711d39f514375362235388943ec42451b312
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58315565"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Preparar recursos do Azure para recuperação de desastre de computadores locais

 O [Azure Site Recovery](site-recovery-overview.md) contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) mantendo seus aplicativos de negócios em execução durante interrupções planejadas e não planejadas. O Site Recovery gerencia e orquestra a recuperação de desastre de máquinas locais e de VMs (máquinas virtuais) do Azure, incluindo replicação, failover e recuperação.

Este artigo é o primeiro tutorial em uma série que mostra como configurar a recuperação de desastres para VMs locais. Ele é relevante se você estiver protegendo VMs VMware, VMs Hyper-V locais ou servidores físicos.

> [!NOTE]
> Os tutoriais destinam-se a mostrar o caminho de implantação mais simples para um cenário. Eles usam opções padrão quando possível e não mostram todas as possíveis configurações e caminhos. Para obter instruções detalhadas, consulte a seção **Como fazer** para o cenário correspondente.

Este artigo mostra como preparar componentes do Azure quando você deseja replicar VMs locais (Hyper-V ou VMware) ou servidores físicos Windows/Linux para o Azure. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Verifique se sua conta do Azure tem permissões de replicação.
> * Crie um cofre dos Serviços de Recuperação. Um cofre contém metadados e informações de configuração para máquinas virtuais e outros componentes de replicação.
> * Configure uma rede do Azure. Quando as VMs do Azure são criadas após o failover, elas são ingressadas nessa rede do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [Portal do Azure](https://portal.azure.com).

## <a name="verify-account-permissions"></a>Verificar permissões de conta

Se acabou de criar sua conta gratuita do Azure, você é o administrador da assinatura. Se você não for o administrador da assinatura, peça para o administrador atribuir as permissões necessárias. Para habilitar a replicação para uma nova máquina virtual, você deve ter permissão para:

- Criar uma VM no grupo de recursos selecionado.
- Criar uma VM na rede virtual selecionada.
- Gravar para a conta de armazenamento.
- Gravar em disco gerenciado.

Para concluir essas tarefas, sua conta deve receber a função interna de Colaborador de Máquina Virtual. Além disso, para gerenciar as operações do Site Recovery em um cofre, sua conta deve receber a função interna de Colaborador do Site Recovery.


## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

1. No portal do Azure, clique em **+Criar um recurso** e pesquise por **Serviços de recuperação** no Marketplace.
2. Clique em **Backup e Site Recovery (OMS)** e, na página Backup e Site Recovery, clique em **Criar**. 
1. No **cofre dos Serviços de Recuperação** > **Nome**, insira um nome amigável para identificar o cofre. Para este conjunto de tutoriais estamos usando **ContosoVMVault**.
2. No **Grupo de recursos**, selecione um grupo de recursos existente ou crie um. Para este tutorial, estamos usando **contosoRG**.
3. Em **Local**, selecione a região na qual o cofre deve estar localizado. Estamos usando **Europa Ocidental**.
4. Para acessar rapidamente o cofre do painel, selecione **Fixar no painel** > **Criar**.

   ![Criar um novo cofre](./media/tutorial-prepare-azure/new-vault-settings.png)

   O novo cofre é exibido em **Painel** > **Todos os recursos** e na página principal **Cofres dos Serviços de Recuperação**.

## <a name="set-up-an-azure-network"></a>Configure uma rede do Azure

Quando as VMs do Azure são criadas de discos gerenciados após o failover, elas são associadas a esta rede.

1. No [Portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Rede** > **Rede virtual**.
2. Estamos deixando **Gerenciador de Recursos** selecionado como o modelo de implantação.
3. Em **Nome**, insira um nome de rede. O nome deve ser exclusivo dentro do grupo de recursos do Azure. Estamos usando **ContosoASRnet** neste tutorial.
4. Especifique o grupo de recursos no qual a rede será criada. Estamos usando o grupo de recursos existente **contosoRG**.
5. Em **Intervalo de endereços**, digite o intervalo para a rede **10.0.0.0/24**. Nessa rede, não estamos usando uma sub-rede.
6. Em **Assinatura**, selecione a assinatura na qual deseja criar a rede.
7. Em **Localização**, selecione **Europa Ocidental**. A rede deve estar na mesma região do que o cofre de Recuperação de Site.
8. Estamos deixando as opções padrão de proteção contra DDoS básica, sem nenhum ponto de extremidade de serviço na rede.
9. Clique em **Criar**.

   ![Criar uma rede virtual](media/tutorial-prepare-azure/create-network.png)

   A rede virtual leva alguns segundos para ser criada. Depois de ser criada, ela será exibida no painel do Portal do Azure.

## <a name="useful-links"></a>Links úteis

- [Saiba mais](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) sobre as redes do Azure.
- [Saiba mais sobre](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) discos gerenciados.



## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Preparar a infraestrutura do VMware local para recuperação de desastre para o Azure](tutorial-prepare-on-premises-vmware.md)
