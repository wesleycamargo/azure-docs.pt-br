---
title: Criar recursos para usar com o Azure Site Recovery | Microsoft Docs
description: Saiba como preparar o Azure para a replicação de máquinas locais usando o Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/08/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 0aec94ce4d53e1d0f5ecfbc7c667f7d4ceea1d2d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="prepare-azure-resources-for-replication-of-on-premises-machines"></a>Preparar recursos do Azure para replicação de máquinas locais

 O [Azure Site Recovery](site-recovery-overview.md) contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) mantendo seus aplicativos de negócios em execução durante interrupções planejadas e não planejadas. O Site Recovery gerencia e orquestra a recuperação de desastre de máquinas locais e de VMs (máquinas virtuais) do Azure, incluindo replicação, failover e recuperação.

Este tutorial mostra como preparar componentes do Azure quando você deseja replicar VMs locais (Hyper-V ou VMware) ou servidores físicos Windows/Linux para o Azure. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Verifique se sua conta do Azure tem permissões de replicação.
> * Crie uma conta de armazenamento do Azure. Os dados replicados são armazenados nela.
> * Crie um cofre dos Serviços de Recuperação.
> * Configure uma rede do Azure. Quando as VMs do Azure são criadas após o failover, elas são ingressadas nessa rede do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [Portal do Azure](http://portal.azure.com).

## <a name="verify-account-permissions"></a>Verificar permissões de conta

Se acabou de criar sua conta gratuita do Azure, você é o administrador da assinatura. Se você não for o administrador da assinatura, peça para o administrador atribuir as permissões necessárias. Para habilitar a replicação para uma nova máquina virtual, você deve ter permissão para:

- Criar uma VM no grupo de recursos selecionado.
- Criar uma VM na rede virtual selecionada.
- Gravar na conta de armazenamento selecionada.

Para concluir essas tarefas, sua conta deve receber a função interna de Colaborador de Máquina Virtual. Além disso, para gerenciar as operações do Site Recovery em um cofre, sua conta deve receber a função interna de Colaborador do Site Recovery.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

As imagens de máquinas replicadas são mantidas no armazenamento do Azure. As VMs do Azure são criadas do armazenamento quando há failover do local para o Azure.

1. No menu [Portal do Azure](https://portal.azure.com), selecione **Novo** > **Armazenamento** > **Conta de armazenamento**.
2. Em **Criar conta de armazenamento**, insira um nome para a conta. Para esses tutoriais, use o nome **contosovmsacct1910171607**. O nome deve ser exclusivo dentro do Azure e ter entre 3 e 24 caracteres, contendo apenas números e letras minúsculas.
3. Em **Modelo de implantação**, selecione **Gerenciador de Recursos**.
4. Em **Tipo de conta**, selecione **Uso geral**. Em **Desempenho**, selecione **Padrão**. Não selecione armazenamento de blobs.
5. Em **Replicação**, selecione o padrão **Armazenamento com redundância geográfica com acesso de leitura** para redundância de armazenamento.
6. Em **Assinatura**, selecione a assinatura na qual você deseja criar a nova conta de armazenamento.
7. Em **Grupo de recursos**, digite um novo grupo de recursos. Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Para estes tutoriais, use o nome **ContosoRG**.
8. Em **Localização**, selecione a localização geográfica para sua conta de armazenamento. A conta de armazenamento deve estar na mesma região do cofre de Serviços de Recuperação. Para estes tutoriais, use a região **Europa Ocidental**.

   ![Criar uma conta de armazenamento](media/tutorial-prepare-azure/create-storageacct.png)

9. Selecione **Criar** para criar a conta de armazenamento.

## <a name="create-a-vault"></a>Criar um cofre

1. No portal do Azure, selecione **Criar um recurso** > **Monitoramento + Gerenciamento** > **Backup e Site Recovery**.
2. Em **Nome**, digite um nome amigável para identificar o cofre. Para este tutorial, use **ContosoVMVault**.
3. Em **Grupo de recursos**, selecione o grupo de recursos existente denominado **contosoRG**.
4. Em **Localização**, insira a região do Azure **Europa Ocidental** que é usada neste conjunto de tutoriais.
5. Para acessar rapidamente o cofre do painel, selecione **Fixar no painel** > **Criar**.

   ![Criar um novo cofre](./media/tutorial-prepare-azure/new-vault-settings.png)

   O novo cofre é exibido em **Painel** > **Todos os recursos** e na página principal **Cofres dos Serviços de Recuperação**.

## <a name="set-up-an-azure-network"></a>Configure uma rede do Azure

Quando as VMs do Azure são criadas do armazenamento após o failover, elas são associadas a esta rede.

1. No [Portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Rede** > **Rede virtual**.
2. Deixe **Resource Manager** selecionado como o modelo de implantação. Resource Manager é o modelo de implantação preferencial. Em seguida, siga estas etapas:

   a. Em **Nome**, insira um nome de rede. O nome deve ser exclusivo dentro do grupo de recursos do Azure. Use o nome **ContosoASRnet**.

   b. Em **Grupo de recursos**, use o grupo de recursos existente **contosoRG**.

   c. Em **Intervalo de endereços**, digite o novo intervalo de endereços **10.0.0.0/24**.

   d. Para este tutorial, você não precisa de uma sub-rede.

   e. Em **Assinatura**, selecione a assinatura na qual deseja criar a rede.

   f. Em **Localização**, selecione **Europa Ocidental**. A rede deve estar na mesma região do que o cofre de Recuperação de Site.

3. Selecione **Criar**.

   ![Criar uma rede virtual](media/tutorial-prepare-azure/create-network.png)

   A rede virtual leva alguns segundos para ser criada. Depois de ser criada, ela será exibida no painel do Portal do Azure.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Preparar a infraestrutura do VMware local para recuperação de desastre para o Azure](tutorial-prepare-on-premises-vmware.md)
