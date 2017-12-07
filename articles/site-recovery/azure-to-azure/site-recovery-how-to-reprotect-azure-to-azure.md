---
title: "Como criar uma nova proteção de failover para máquinas virtuais do Azure para a região primária do Azure | Microsoft Docs"
description: "Após o failover das VMs de uma região do Azure para outra, você pode usar o Azure Site Recovery para proteger as máquinas na direção inversa. Aprenda as etapas para criar uma nova proteção antes de realizar um failback novamente."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/11/2017
ms.author: ruturajd
ms.openlocfilehash: 0004323fee44c1433cdd3c39fc1fa7dad965dbf7
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2017
---
# <a name="reprotect-azure-vms-back-to-the-primary-region"></a>Proteja novamente as VMs do Azure para a região principal



>[!NOTE]
>
> A replicação do Site Recovery para as máquinas virtuais (VMs) do Azure está atualmente na versão prévia.


Quando você faz [failover](../site-recovery-failover.md) em VMs de uma região do Azure para outra, as VMs com failover estão em um estado desprotegido. Se você quiser colocá-las de volta na região primária, primeiro será necessário iniciar a replicação das VMs e então realizar failover novamente. Não há nenhuma diferença no failover em uma direção ou em outra. Da mesma forma, depois que você habilitar a replicação de VM, não há nenhuma diferença entre a nova proteção pós-failover ou pós-failback.

Para explicar o processo de nova proteção, como um exemplo, vamos supor que o site primário das VMs protegidas seja a região da Ásia Oriental, e o site de recuperação é o Sudeste Asiático. Durante o failover, você deve fazer failover das VMs para a região do Sudeste Asiático. Antes do failback, você precisa replicar as máquinas virtuais do Sudeste Asiático novamente para a Ásia Oriental. Este artigo descreve as etapas necessárias para nova proteção.

> [!WARNING]
> Se você tiver concluído a migração e movido a máquina virtual para outro grupo de recursos ou excluídos a máquina virtual do Azure, não será possível aplicar failback.

Depois de terminada a nova proteção, e quando as VMs estiverem replicando, você poderá iniciar um failover nas VMs para trazê-las de volta à região da Ásia Oriental.

## <a name="prerequisites"></a>Pré-requisitos
1. A máquina virtual deve ter sido confirmada.
2. O local de destino (Azure Ásia Oriental) deve estar disponível e ser capaz de acessar/criar novos recursos nessa região.

## <a name="reprotect"></a>Proteger novamente

Siga estas etapas para proteger novamente uma VM usando as configurações padrão.

1. Em **Cofre** > **Itens replicados**, clique com o botão direito do mouse na VM em que o failover foi executado e, em seguida, selecione **Proteger Novamente**. Também é possível clicar no computador e selecionar **Proteger Novamente** nos botões de comando.

  ![Nova proteção](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Verifique se a direção da replicação **Sudeste Asiático para Ásia Oriental** está selecionada.

  ![Proteger novamente](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotectblade.png)

3. Examine as informações de **Grupo de recursos, rede, conjuntos de armazenamento e a disponibilidade** e clique em **OK**. Se houver quaisquer recursos marcados (novos), eles serão criados durante a nova proteção.

Isso dispara um trabalho de nova proteção que propaga o site de destino com os dados mais recentes e depois de concluído, replica os deltas antes de fazer failback para o Sudeste Asiático.

### <a name="reprotect-customization"></a>Personalização da nova proteção
Se quiser escolher a conta de armazenamento de extração ou a rede durante a nova proteção, você pode fazer isso usando a opção personalizar fornecida na página de proteger novamente.

![Opção de personalização](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

Você pode personalizar as propriedades a seguir da máquina virtual de destino durante o proteger novamente.

![Personalizar](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Propriedade |Observações  |
|---------|---------|
|Grupo de recursos de destino     | Você pode optar por alterar o grupo de recursos de destino no qual a máquina virtual será criada. Como parte da nova proteção, a máquina virtual de destino será excluída, portanto, você pode escolher um novo grupo de recursos no qual você pode criar a VM depois do failover         |
|Rede virtual de destino     | A rede não pode ser alterada durante o proteger novamente. Para alterar a rede, refaça o mapeamento de rede.         |
|Armazenamento de destino     | Você pode alterar a conta de armazenamento à qual a máquina virtual será criada depois do failover.         |
|Armazenamento de cache     | Você pode especificar uma conta de armazenamento de cache que será usada durante a replicação. Se você usar os padrões, uma nova conta de armazenamento de cache será criada, se ela ainda não existir.         |
|Conjunto de disponibilidade     |Se a máquina virtual na Ásia Oriental fizer parte de um conjunto de disponibilidade, você pode escolher um conjunto de disponibilidade para a máquina virtual de destino do Sudeste Asiático. Os padrões encontrarão o conjunto de disponibilidade SEA existente e tentarão usá-lo. Durante a personalização, você pode especificar um conjunto AV totalmente novo.         |


### <a name="what-happens-during-reprotect"></a>O que acontece durante a nova proteção?

Após habilitar a primeira proteção, a seguir estão os artefatos que são gerados se você usar os padrões.
1. Uma conta de armazenamento de cache é criada na região Ásia Oriental.
2. Se a conta de armazenamento de destino (a conta de armazenamento original da VM do Sudeste Asiático) não existir, uma nova será criada. O nome é a conta de armazenamento da máquina virtual da Ásia Oriental com o sufixo "asr".
3. Se o conjunto de destino AV não existir e os padrões detectarem que precisa criar um novo conjunto AV, então ele será criado como parte do trabalho de proteger novamente. Se você tiver personalizado a nova proteção, o conjunto AV selecionado será usado.


A seguir estão a lista de etapas que ocorrem quando você disparar um trabalho de proteger novamente. Este é o caso no qual a máquina virtual lateral de destino existe.

1. Os artefatos necessários são criados como parte de proteger novamente. Se já existirem, eles serão reutilizados.
2. A máquina virtual lateral (Sudeste Asiático) de destino é primeiro desativada, se ela estiver em execução.
3. O disco da máquina virtual lateral de destino é copiado pelo Azure Site Recovery em um contêiner como um blob de semeadura.
4. A máquina virtual lateral de destino é excluída.
5. O blob de semeadura é usado pela máquina virtual lateral (Ásia Oriental) de origem atual para replicar. Isso garante que apenas deltas sejam replicadas.
6. As principais alterações entre o disco de origem e o blob de semeadura são sincronizadas. Elas podem levar algum tempo para ser concluída.
7. Depois de concluído o trabalho de proteger novamente, a replicação delta começa a criar um ponto de recuperação de acordo com a política.

> [!NOTE]
> Não é possível proteger em um nível do plano de recuperação. Você só pode proteger novamente em um nível de VM.

Depois que nova proteção for bem-sucedida, a máquina virtual entrará em um estado protegido.

## <a name="next-steps"></a>Próximas etapas

Depois que a máquina virtual entrou em um estado protegido, você poderá iniciar um failover. O failover desligará a máquina virtual na região do Azure Ásia Oriental e, em seguida, criará e inicializará a máquina virtual da região Sudeste Asiático. Portanto, há um pequeno tempo de inatividade do aplicativo. Sendo assim, faça o failover quando seu aplicativo puder passar por um tempo de inatividade. É recomendável que você execute um failover de teste da máquina virtual primeiro para certificar-se de que ele está chegando corretamente, antes de iniciar um failover.

-   [Etapas para iniciar o failover de teste da máquina virtual](../site-recovery-test-failover-to-azure.md)

-   [Etapas para iniciar o failover da máquina virtual](../site-recovery-failover.md)
