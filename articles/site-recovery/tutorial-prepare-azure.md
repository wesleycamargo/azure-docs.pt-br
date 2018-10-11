---
title: Criar recursos para usar com o Azure Site Recovery | Microsoft Docs
description: Saiba como preparar o Azure para a replicação de máquinas locais usando o Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 10/10/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 4a92bfc9d682346c874ce91fc9835558402bae84
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078980"
---
# <a name="prepare-azure-resources-for-replication-of-on-premises-machines"></a>Preparar recursos do Azure para replicação de máquinas locais

 O [Azure Site Recovery](site-recovery-overview.md) contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) mantendo seus aplicativos de negócios em execução durante interrupções planejadas e não planejadas. O Site Recovery gerencia e orquestra a recuperação de desastre de máquinas locais e de VMs (máquinas virtuais) do Azure, incluindo replicação, failover e recuperação.

Este artigo é o primeiro tutorial em uma série que mostra como configurar a recuperação de desastres para VMs locais. Ele é relevante se você estiver protegendo VMs VMware, VMs Hyper-V locais ou servidores físicos.

Os tutoriais destinam-se a mostrar o caminho de implantação mais simples para um cenário. Eles usam opções padrão quando possível e não mostram todas as possíveis configurações e caminhos. 

Este artigo mostra como preparar componentes do Azure quando você deseja replicar VMs locais (Hyper-V ou VMware) ou servidores físicos Windows/Linux para o Azure. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Verifique se sua conta do Azure tem permissões de replicação.
> * Crie uma conta de armazenamento do Azure. As imagens de máquinas replicadas são mantidas nele.
> * Crie um cofre dos Serviços de Recuperação. Um cofre contém metadados e informações de configuração para máquinas virtuais e outros componentes de replicação.
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

As imagens de máquinas replicadas são mantidas no armazenamento do Azure. As VMs do Azure são criadas do armazenamento quando há failover do local para o Azure. A conta de armazenamento deve estar na mesma região do cofre de Serviços de Recuperação. Neste tutorial, estamos usando Europa Ocidental.

1. No menu do [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** >  **Armazenamento** > **Conta de armazenamento - blob, arquivo, tabela, fila**.
2. Em **Criar conta de armazenamento**, insira um nome para a conta. Para esses tutoriais, estamos usando **contosovmsacct1910171607**. O nome que você selecionar deve ser exclusivo dentro do Azure e ter entre 3 e 24 caracteres, contendo apenas números e letras minúsculas.
3. Em **Modelo de implantação**, selecione **Gerenciador de Recursos**.
4. Em **Tipo de conta**, selecione **Armazenamento (uso geral v1)**. Não selecione armazenamento de blobs.
5. Em **Replicação**, selecione o padrão **Armazenamento com redundância geográfica com acesso de leitura** para redundância de armazenamento. Estamos deixando **Transferência segura obrigatória** como **Desabilitada**.
6. Em **Desempenho**, selecione **Padrão** e, na **Camada de acesso**, escolha a opção padrão **Frequente**.
7. Em **Assinatura**, selecione a assinatura na qual você deseja criar a nova conta de armazenamento.
8. Em **Grupo de recursos**, digite um novo grupo de recursos. Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Para esses tutoriais, estamos usando **ContosoRG**.
9. Em **Localização**, selecione a localização geográfica para sua conta de armazenamento. 

   ![Criar uma conta de armazenamento](media/tutorial-prepare-azure/create-storageacct.png)

9. Selecione **Criar** para criar a conta de armazenamento.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

1. No portal do Azure, selecione **Criar um recurso** > **Armazenamento** > **Backup e Site Recovery (OMS)**.
2. Em **Nome**, digite um nome amigável para identificar o cofre. Para este conjunto de tutoriais estamos usando **ContosoVMVault**.
3. Em **Grupo de recursos**, estamos usando **contosoRG**.
4. Em **Localização**. Estamos usando **Europa Ocidental**.
5. Para acessar rapidamente o cofre do painel, selecione **Fixar no painel** > **Criar**.

   ![Criar um novo cofre](./media/tutorial-prepare-azure/new-vault-settings.png)

   O novo cofre é exibido em **Painel** > **Todos os recursos** e na página principal **Cofres dos Serviços de Recuperação**.

## <a name="set-up-an-azure-network"></a>Configure uma rede do Azure

Quando as VMs do Azure são criadas do armazenamento após o failover, elas são associadas a esta rede.

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
- [Saiba mais](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts) sobre os tipos de armazenamento do Azure.
- [Saiba mais](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs#read-access-geo-redundant-storage) sobre redundância de armazenamento e [transferência segura](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) para armazenamento.



## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Preparar a infraestrutura do VMware local para recuperação de desastre para o Azure](tutorial-prepare-on-premises-vmware.md)
