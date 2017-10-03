---
title: "Habilitar a replicação para VMs do Azure para outra região do Azure com o Azure Site Recovery | Microsoft Docs"
description: "Resume as etapas necessárias para habilitar a replicação para outra região do Azure para VMs do Azure, utilizando o serviço do Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a309644f-d36b-4188-bba7-ad45a2d9bede
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 8/01/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: f426ba4341e61d3c7da820d7d5097b217e94ca0e
ms.contentlocale: pt-br
ms.lasthandoff: 08/02/2017

---


# <a name="step-5-enable-replication-for-azure-vms"></a>Etapa 5: habilitar replicação para VMs do Azure


Após configurar um [Cofre de Serviços de Recuperação](azure-to-azure-walkthrough-vault.md), utilize este artigo para habilitar a replicação de VMs (Máquinas Virtuais) para outra região do Azure, com o [Azure Site Recovery](site-recovery-overview.md). Para habilitar a replicação, você define as configurações de origem e destino, verifica a política de replicação e seleciona as VMs que deseja replicar.

- Ao concluir o artigo, suas VMs do Azure deverão ser replicadas para a região do Azure secundária.
- Publique eventuais comentários no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

>[!NOTE]
>
> Atualmente, a replicação de VM do Azure está em versão prévia.


## <a name="select-the-source"></a>Selecione a origem

1. Em cofres dos Serviços de Recuperação, clique no nome do cofre > **+Replicar**.
2. Em **fonte**, selecione **Azure - VISUALIZAÇÃO**.
2. Em **Local de origem**, selecione a fonte de região do Azure em que suas VMs estão sendo executados.
3. Selecione o **modelo de implantação de máquina virtual do Azure** para VMs: **Gerenciador de Recursos** ou **Clássico**.
4. Selecione o **Grupo de recursos de origem** para VMs do Gerenciador de Recursos ou **serviço de nuvem** para VMs clássicas.
5. Clique em **OK** para salvar as configurações.

    ![Configure a origem](./media/azure-to-azure-walkthrough-enable-replication/source.png)

## <a name="select-the-vms"></a>Selecione as VMs

O Site Recovery recupera uma lista das VMs associadas ao serviço de nuvem/grupo de recursos e assinatura.

1. Em **Máquinas Virtuais**, selecione as VMs que deseja replicar.
2. Clique em **OK**.

    ![Selecione as máquinas virtuais](./media/azure-to-azure-walkthrough-enable-replication/vms.png)


## <a name="configure-settings"></a>Configurar definições

O Site Recovery provisiona configurações padrão para a região de destino (baseado nas configurações da região de origem) e a política de replicação:

   - **Localização de destino**: a região de destino que você deseja utilizar para recuperação de desastre. É recomendável que a localização de destino corresponda à localização do cofre do Site Recovery.
   - **Grupo de recursos de destino**: grupo de recursos para o qual as VMs do Azure na região de destino pertencerão após o failover. Por padrão, o Site Recovery cria um novo grupo de recursos na região de destino com um sufixo "asr".
   - **Rede virtual de destino**: a rede na qual as VMs Azure na região alvo serão localizadas após o failover. Por padrão, o Site Recovery cria uma nova rede virtual (e sub-redes) na região de destino com um sufixo "asr". Essa rede é mapeada para sua rede de origem. Observe que é possível atribuir um endereço IP específico após o failover de uma VM, se você precisar reter o mesmo endereço IP nas localizações de origem e destino.
   - **Contas de armazenamento de cache**: o Site Recovery utiliza uma conta de armazenamento na região de origem. As alterações nas VMs de origem são enviadas para essa conta, antes da replicação para a localização de destino.
   - **Contas de armazenamento de destino**: por padrão, o Site Recovery cria uma nova conta de armazenamento na região de destino, para espelhar a conta de armazenamento da VM de origem.
   -  **Conjuntos de disponibilidade de destino**: por padrão, o Site Recovery cria um novo conjunto de disponibilidade na região de destino, com o sufixo "asr".
   - **Nome da política de replicação**: nome da política.
   - **Retenção de ponto de recuperação**: por padrão, o Site Recovery mantém os pontos de recuperação por 24 horas. É possível configurar um valor entre 1 e 72 horas.
   - **Frequência de instantâneo consistente com o aplicativo**: por padrão, o Site Recovery obtém um instantâneo consistente com o aplicativo a cada 4 horas. É possível configurar qualquer valor entre 1 e 12 horas. Os dados são replicados continuamente:
    - Os pontos de recuperação consistentes com falha mantêm uma ordem de gravação de dados consistente quando criados. Este tipo de ponto de recuperação geralmente é suficiente se o seu aplicativo for projetado para recuperar de uma falha sem inconsistências de dados
    - Os pontos de recuperação consistentes com falha são gerados a cada poucos minutos. O uso desses pontos de recuperação para fazer failover e recuperar suas VMs fornece um RPO (Objetivo do Ponto de Recuperação) na ordem dos minutos.
    - Pontos de recuperação consistente com o aplicativo (além da consistência de ordem de gravação) garantem que a execução de aplicativos conclua todas as operações e libere os buffers para disco (fechando o aplicativo). É recomendável utilizar esses pontos de recuperação para aplicativos de banco de dados, como SQL Server, Oracle e Exchange.

    ![Configurar definições](./media/azure-to-azure-walkthrough-enable-replication/settings.png)


### <a name="modify-settings"></a>Modificar configurações

Se deseja modificar as configurações de política de replicação e destino, faça o seguinte:

1. Para exibir ou modificar as configurações de destino, clique em **Configurações**.
2. Para substituir as configurações de destino padrão, clique em **Personalizar**. É possível especificar um grupo de recursos de destino, uma rede virtual, um conjunto de disponibilidade e uma conta de armazenamento de destino. Somente será possível adicionar conjuntos de disponibilidade se as VMs forem parte de um conjunto na região de origem.

    ![Configurar definições](./media/azure-to-azure-walkthrough-enable-replication/customize-target.png)

3. Para substituir as configurações de replicação para pontos de recuperação e snapshots consistentes com o aplicativo, clique em **Personalizar** próximo à **Política de Replicação**.

    ![Configurar definições](./media/azure-to-azure-walkthrough-enable-replication/customize-policy.png)

4. Para iniciar o provisionamento de recursos de destino, clique em **Criar recursos de destino**. O provisionamento demora alguns minutos. Não feche a folha durante o provisionamento ou será necessário começar tudo novamente.




## <a name="enable-replication"></a>Habilitar a replicação

1. Em **Configurações**, clique em **Habilitar replicação**. Isso permite a replicação inicial das VMs selecionadas. O status da replicação inicial pode levar algum tempo para atualizar. Clique em **Atualizar** para obter o status mais recente.

2. Você pode acompanhar o progresso do trabalho **Habilitar Proteção** em **Configurações** > **Trabalhos** > **Trabalhos de Recuperação de Site**.

3. Em **Configurações** > **Itens Replicados**, você pode exibir o status das máquinas virtuais e o andamento da replicação inicial. Clique em VM para fazer uma busca detalhada em suas configurações.



## <a name="next-steps"></a>Próximas etapas

Acesse a [Etapa 6: executar um failover de teste](azure-to-azure-walkthrough-test-failover.md)

