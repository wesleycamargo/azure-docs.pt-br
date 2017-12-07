---
title: "Replicar VMs do Azure para uma região secundária com o Azure Site Recovery | Microsoft Docs"
description: "Este artigo descreve como replicar VMs do Azure em execução em uma região do Azure para outra região usando o serviço Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/29/2017
ms.author: asgang
ms.openlocfilehash: 114390712f5e5bf62ec515db62469e09361b8f85
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2017
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Replicação de máquinas virtuais do Azure para outra região do Azure


Este artigo descreve como replicar máquinas virtuais (VMs) do Azure em uma região do Azure para uma região secundária do Azure, usando o serviço Azure Site Recovery.

>[!NOTE]
>
> Atualmente, a replicação de VM do Azure com o Site Recovery está em versão prévia.

## <a name="prerequisites"></a>Pré-requisitos

* Você deve ter um cofre de Serviços de Recuperação em vigor. É recomendável que você crie o cofre no local de destino para onde você deseja replicar suas VMs.
* Se você estiver usando regras de Grupos de Segurança de Rede (NSG) ou um proxy de firewall para controlar o acesso à conectividade de Internet de saída nas VMs do Azure, certifique-se de que você permita as URLs ou IPs necessários. [Saiba mais](./site-recovery-azure-to-azure-networking-guidance.md).
* Se você tiver uma conexão de ExpressRoute ou VPN entre locais e o local de origem no Azure, [saiba como configurá-las](site-recovery-azure-to-azure-networking-guidance.md#guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration).
* Sua conta de usuário do Azure precisa de [permissões específicas](../site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para habilitar a replicação de uma VM do Azure.
Sua assinatura do Azure deve ser habilitada para criar VMs, na localização de destino que deseja utilizar como uma região de recuperação de desastres. Contate o suporte para habilitar a cota necessária.

## <a name="enable-replication"></a>Habilitar a replicação

Neste procedimento, Ásia Oriental é usada como o local de origem e Sudeste Asiático, como o destino.

1. Clique em **+ Replicar** no cofre para habilitar a replicação para as máquinas virtuais.
2. Verifique se **Origem:** é definido como **Azure**.
3. Defina o **Local de origem** para Ásia Oriental.
4. Em **Modelo de implantação:** selecione **clássico** ou **Gerenciador de Recursos**.
5. Em **Grupo de Recursos**, selecione o grupo ao qual pertence suas VMs de origem. São listadas todas as VMs do grupo de recursos selecionado.

    ![Habilitar a replicação](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

6. Em **Máquinas Virtuais > Selecionar máquinas virtuais**, clique e selecione cada VM que você deseja replicar. Você só pode selecionar computadores para os quais a replicação pode ser habilitada. Em seguida, clique em **OK**.

    ![Habilitar a replicação](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)
    
7. Em **Configurações** > **Local de destino**, especifique para onde serão replicados os dados da VM de origem. O Site Recovery fornece uma lista de regiões de destino possíveis, dependendo da região das VMs selecionadas.
8. O Site Recovery define as configurações padrão de destino. Elas podem ser modificadas conforme necessário.

    - **Grupo de recursos de destino**. Por padrão, o Site Recovery cria um novo grupo de recursos na região de destino com o sufixo "asr". Se o grupo de recursos criados já existir, ele será reutilizado.
    - **Rede virtual de destino**. Por padrão, o Site Recovery cria uma nova rede virtual na região de destino com o sufixo "asr". Essa rede é mapeada para sua rede de origem. [Saiba mais](site-recovery-network-mapping-azure-to-azure.md) sobre o mapeamento de rede.
    - **Contas de armazenamento de destino**. Por padrão, o Site Recovery cria uma nova conta de armazenamento de destino que corresponde à configuração de armazenamento da VM de origem. Se a conta criada já existir, ela será reutilizado.
    - **Contas de armazenamento em cache**. O Azure Site Recovery cria uma conta de armazenamento em cache extra, a região de origem. Todas as alterações nas VMs de origem são controladas e enviadas para a conta de armazenamento em cache antes da replicação para o local de destino.
    - **Conjunto de disponibilidade**. Por padrão, o Site Recovery cria um novo conjunto de disponibilidade na região de destino com um sufixo "asr". Se o conjunto criada já existir, ele será reutilizado.
    - **Política de replicação**. O Site Recovery define as configurações para o histórico de retenção de ponto de recuperação e frequência de instantâneo consistente do aplicativo. Por padrão, o Site Recovery cria uma nova política de replicação com configurações padrão de 24 horas para retenção de pontos de recuperação e 60 minutos para a frequência de instantâneo consistente do aplicativo.

    ![Habilitar a replicação](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)
9. Clique em **Habilitar Replicação** para iniciar a proteção das VMs.

## <a name="customize-target-resources"></a>Personalizar os recursos de destino

1. Modifique qualquer um desses padrões de destino:

    - **Grupo de recursos de destino**. Selecione qualquer grupo de recursos da lista de todos os grupos de recursos no local de destino dentro da assinatura.
    - **Rede virtual de destino**. Selecione na lista de todas as redes virtuais no local de destino.
    - **Conjunto de disponibilidade** Você só pode adicionar configurações de conjuntos de disponibilidade para as VMs localizadas em um conjunto da região de origem.
    - **Contas de armazenamento de destino**: Adicione qualquer conta que esteja disponível.

        ![Habilitar a replicação](./media/site-recovery-replicate-azure-to-azure/customize.PNG)

2. Clique em **Criar o recurso de destino** > **Habilitar Replicação**. Durante a replicação inicial, o status da VM pode levar algum tempo para atualizar. Clique em **Atualizar** para obter o status mais recente.

    ![Habilitar a replicação](./media/site-recovery-replicate-azure-to-azure/replicateditems.PNG)
    
3. Depois que as VMs são protegidas, verifique a integridade da VM em **Itens replicados**.



## <a name="next-steps"></a>Próximas etapas
[Saiba](../azure-to-azure-tutorial-dr-drill.md) como executar um failover de teste.

