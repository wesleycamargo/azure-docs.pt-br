---
title: Configurar replicação para VMs do Azure no Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como configurar a replicação para VMs do Azure, de uma região do Azure para outra, usando o Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: asgang
ms.openlocfilehash: 7002e8a63ca0223a38ba099b17955a86034fa057
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46295454"
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Replicação de máquinas virtuais do Azure para outra região do Azure



Este artigo descreve como habilitar a replicação de VMs do Azure, de uma região do Azure para outra.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já tenha configurado o Site Recovery para este cenário, conforme descrito no [Tutorial do Azure para o Azure](azure-to-azure-tutorial-enable-replication.md). Verifique se você preparou os pré-requisitos e criou o cofre dos Serviços de Recuperação.



## <a name="enable-replication"></a>Habilitar a replicação

Habilite a replicação. Este procedimento pressupõe que a região do Azure primária seja Leste da Ásia e a região secundária seja Sudeste Asiático.

1. No cofre, clique em **+Replicar**.
2. Observe os seguintes campos:
    - **Fonte**: o ponto de origem das VMs que, neste caso, é o **Azure**.
    - **Local de origem**: a região do Azure onde você deseja proteger as máquinas virtuais. Para esta ilustração, a localização da fonte é 'Ásia Oriental'
    - **Modelo de implantação**: o modelo de implantação do Azure de computadores de origem.
    - **Assinatura de origem**: a assinatura à qual suas máquinas virtuais de origem pertencem. Pode ser qualquer assinatura dentro do mesmo locatário do Azure Active Directory na qual existe o cofre dos serviços de recuperação.
    - **Grupo de recursos**: o grupo de recursos ao qual pertencem as máquinas virtuais de origem. Todas as máquinas virtuais do grupo de recursos selecionado são listadas para a proteção na próxima etapa.

    ![Habilitar a replicação](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. Em **Máquinas Virtuais > Selecionar máquinas virtuais**, clique e selecione cada VM que você deseja replicar. Você só pode selecionar computadores para os quais a replicação pode ser habilitada. Em seguida, clique em **OK**.
    ![Habilitar a replicação](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. Em **Configurações**, você também pode definir configurações do site de destino:

    - **Local de destino**: o local onde seus dados de máquina virtual de origem serão replicados. Dependendo de seu local de máquinas selecionadas, a recuperação de Site fornece a lista de regiões de destino adequado. É recomendável que você mantenha o local de destino o mesmo do local do cofre dos Serviços de Recuperação.
    - **Assinatura de destino**: a assinatura de destino usada para recuperação de desastres. Por padrão, a assinatura de destino será o mesma que a assinatura de origem.
    - **Grupo de recursos de destino**: o grupo de recursos ao qual pertencem todas as máquinas virtuais replicadas. Por padrão, o Azure Site Recovery cria um novo grupo de recursos na região de destino com um sufixo "asr". Caso o grupo de recursos criado pelo Azure Site Recovery já exista, ele é reutilizado. Você também poderá escolher personalizá-lo conforme mostrado na seção abaixo. O local do grupo de recursos pode ser qualquer região do Azure exceto a região em que as máquinas virtuais de origem estão hospedadas.
    - **Rede Virtual de Destino**: por padrão, o Site Recovery cria uma nova rede virtual na região de destino com o nome com um sufixo "asr". Isso é mapeado para sua rede de origem e usado para qualquer proteção futura. [Saiba mais](site-recovery-network-mapping-azure-to-azure.md) sobre o mapeamento de rede.
    - **Contas de armazenamento de destino (se a VM de origem não usar discos gerenciados)**: por padrão, o Site Recovery cria uma nova conta de armazenamento de destino imitando sua configuração de armazenamento da VM de origem. Caso a conta de armazenamento já exista, ela é reutilizada.
    - **Discos gerenciados de réplica (se a VM de origem usar discos gerenciados)**: o Site Recovery cria novos discos gerenciados de réplica na região de destino para espelhar os discos gerenciados da VM de origem com o mesmo tipo de armazenamento (Standard ou premium) do disco gerenciado da VM de origem.
    - **Contas de armazenamento em cache**: o Site Recovery precisa de uma conta de armazenamento extra, chamada armazenamento em cache na região de origem. Todas as alterações ocorrendo nas máquinas virtuais de origem são controladas e enviadas para a conta de armazenamento do cache antes de replicar para o local de destino.
    - **Conjunto de disponibilidade**: por padrão, o Azure Site Recovery cria uma nova configuração de disponibilidade na região de destino com o sufixo "asr". Caso o conjunto de disponibilidade criado pelo Azure Site Recovery já exista, ele é reutilizado.
    - **Política de replicação**: define as configurações para histórico de retenção de ponto de recuperação e frequência de instantâneo consistente do aplicativo. Por padrão, o Azure Site Recovery cria uma nova política de replicação com configurações padrão de “24 horas” para retenção de pontos de recuperação e “60 minutos” para a frequência de instantâneo consistente do aplicativo.

    ![Habilitar a replicação](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

## <a name="customize-target-resources"></a>Personalizar os recursos de destino

Você pode modificar as configurações de destino padrão usadas pelo Site Recovery.

1. Clique em **Personalizar:** ao lado de "Assinatura de destino" para modificar a assinatura de destino padrão. Selecione a assinatura na lista de todas as assinaturas disponíveis no mesmo locatário do AAD (Azure Active Directory).

2. Clique em **Personalizar:** para modificar as configurações padrão:
    - Em **Grupo de recursos de destino**, selecione o grupo de recursos da lista de todos os grupos de recursos no local de destino da assinatura.
    - Em **Rede virtual de destino**, selecione a rede em uma lista de todas as redes virtuais no local de destino.
    - Em **Conjunto de disponibilidade**, você pode adicionar configurações do conjunto de disponibilidade à VM, caso elas façam parte de um conjunto de disponibilidade na região de origem.
    - Em **Contas de armazenamento de destino**, selecione a conta que você deseja usar.

        ![Habilitar a replicação](./media/site-recovery-replicate-azure-to-azure/customize.PNG)

2. Clique em **Criar o recurso de destino** > **Habilitar Replicação**.
3. Depois que as VMs forem habilitadas para replicação, você poderá verificar o status da integridade da VM em **Itens replicados**

>[!NOTE]
>Durante a replicação inicial, o status pode levar algum tempo para atualizar, sem andamento. Clique no botão **Atualizar** para obter o status mais recente.
>

# <a name="next-steps"></a>Próximas etapas

[Saiba mais](site-recovery-test-failover-to-azure.md) sobre a execução de failovers de teste.
