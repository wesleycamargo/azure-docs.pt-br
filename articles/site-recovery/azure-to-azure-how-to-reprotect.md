---
title: "Proteger novamente VMs do Azure que sofreram failover de volta para a região do Azure primária com o Azure Site Recovery | Microsoft Docs"
description: "Descreve como proteger novamente VMs do Azure em uma região secundária, após o failover de uma região primária, usando o Azure Site Recovery."
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: rajanaki
ms.openlocfilehash: d24376c57c468a562fc6d6dd52b4e9b01b53c3da
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2018
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Proteger novamente VMs do Azure que sofreram failover para a região primária


>[!NOTE]
>
> A replicação do Site Recovery para as VMs do Azure está na versão prévia atualmente.



Quando você [faz failover](site-recovery-failover.md) de VMs do Azure de uma região para outra usando o [Azure Site Recovery](site-recovery-overview.md), as VMs inicializam na região secundária, em um estado desprotegido. Se você fizer failback das VMs de volta para a região primária, faça o seguinte:

- Proteja novamente as VMs na região secundária, para que elas iniciem a replicação para a região primária. 
- Depois que a nova proteção é concluída e as VMs estão replicando, você pode fazer failover delas da região secundária para a primária.

> [!WARNING]
> Se você [migrar](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration) máquinas da região primária para a secundária, mover a VM para outro grupo de recursos ou excluir a VM do Azure, você não poderá protegê-la novamente nem fazer failback dela.


## <a name="prerequisites"></a>pré-requisitos
1. O failover de VM da região primária para a secundária deve ser confirmado.
2. O local de destino primário deve estar disponível e você deve ser capaz de acessar ou criar recursos nessa região.

## <a name="reprotect-a-vm"></a>Proteger novamente uma VM

1. Em **Cofre** > **Itens replicados**, clique com o botão direito do mouse na VM em que o failover foi executado e, em seguida, selecione **Proteger Novamente**. A direção de nova proteção deve mostrar da secundária para a primária. 

  ![Proteger novamente](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Examine o grupo de recursos, a rede, o armazenamento e os conjuntos de disponibilidade. Em seguida, clique em **OK**. Se houver algum recurso marcado como novo, ele será criado como parte do processo de nova proteção.
3. O trabalho de nova proteção propaga os dados mais recentes para o site de destino. Depois que isso termina, ocorre a replicação delta. Em seguida, você pode fazer failover de volta para o site primário. Você pode selecionar a conta de armazenamento ou a rede que você deseja usar ao proteger novamente, usando a opção personalizar.

  ![Opção de personalização](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Personalizar as configurações de nova proteção

Você pode personalizar as propriedades a seguir da VM de destino ao proteger novamente.

![Personalizar](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Propriedade |Observações  |
|---------|---------|
|Grupo de recursos de destino     | Modifique o grupo de recursos de destino no qual a VM é criada. Como parte da nova proteção, a VM de destino é excluída. Você pode escolher um novo grupo de recursos sob o qual criar a VM após o failover.        |
|Rede virtual de destino     | A rede de destino não pode ser alterada durante o trabalho de nova proteção. Para alterar a rede, refaça o mapeamento de rede.         |
|Armazenamento de destino     | Você pode alterar a conta de armazenamento que a VM usa após o failover.         |
|Armazenamento de cache     | Você pode especificar uma conta de armazenamento de cache a ser usada durante a replicação. Por padrão, uma nova conta de armazenamento de cache será criada se ela não existir.         |
|Conjunto de disponibilidade     |Se a VM na região secundária for parte de um conjunto de disponibilidade, você poderá escolher um conjunto de disponibilidade para a VM de destino na região primária. Por padrão, o Site Recovery tenta localizar o conjunto de disponibilidade existente na região primária e usá-lo. Durante a personalização, você pode especificar um novo conjunto de disponibilidade.         |


### <a name="what-happens-during-reprotection"></a>O que acontece ao proteger novamente?

Por padrão, acontece o seguinte:

1. Uma conta de armazenamento de cache é criada na região primária
2. Se a conta de armazenamento de destino (a conta de armazenamento original na região primária) não existir, uma nova será criada. O nome da conta de armazenamento atribuído é o nome da conta de armazenamento usada pela VM secundária, com o sufixo "asr".
3. Se o conjunto de disponibilidade de destino não existir, um novo será criado como parte do trabalho de nova proteção, se necessário. Se você personalizar as configurações de nova proteção, o conjunto selecionado será usado.

Quando você dispara um trabalho de nova proteção e a VM de destino existe, ocorre o seguinte:

1. Os componentes necessários são criados como parte da nova proteção. Se já existirem, eles serão reutilizados.
2. A VM do lado de destino é desligada quando ela está em execução.
3. O disco da VM do lado de destino é copiado pelo Azure Site Recovery em um contêiner como um blob de semente.
4. A VM do lado de destino é então excluída.
5. O blob de semente é usado pela VM do lado de origem (secundária) atual para replicar. Isso garante que apenas deltas sejam replicadas.
6. As principais alterações entre o disco de origem e o blob de semente são sincronizadas. Elas podem levar algum tempo para ser concluída.
7. Após a conclusão do trabalho de nova proteção, a replicação delta começa e cria um ponto de recuperação de acordo com a política de replicação.
8. Depois que o trabalho de nova proteção for bem-sucedido, a VM entrará em um estado protegido.

## <a name="next-steps"></a>Próximas etapas

Depois que a VM estiver protegida, você poderá iniciar um failover. O failover desliga a VM na região secundária e cria e inicializa a VM na região primária, com um pequeno tempo de inatividade. Recomendamos que você escolha um horário adequadamente e que execute um failover de teste, mas iniciando um failover completo para o site primário. [Saiba mais](site-recovery-failover.md) sobre failover.

