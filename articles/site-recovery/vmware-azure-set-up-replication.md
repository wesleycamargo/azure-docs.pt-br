---
title: Configurar e gerenciar políticas de replicação para replicação do VMware com o Azure Site Recovery | Microsoft Docs
description: Descreve como definir as configurações de replicação para replicação do VMware para o Azure com o Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: sutalasi
ms.openlocfilehash: 115f4e76630a0ecd659fdd3aec0c6aa67d503817
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2018
ms.locfileid: "37920480"
---
# <a name="configure-and-manage-replication-policies-for-vmware-replication"></a>Configurar e gerenciar políticas de replicação para replicação do VMware
Este artigo descreve como configurar uma política de replicação quando você replicar VMs do VMware no Azure, usando o [Azure Site Recovery](site-recovery-overview.md).


## <a name="create-a-policy"></a>Criar uma política

1. Selecione **Gerenciar** > **Infraestrutura do Site Recovery**.
2. Em **Para o VMware e Computadores físicos**, selecione **Políticas de replicação**. 
3. Clique em **+Política de replicação** e especifique o nome da política.
5. Em **Limite de RPO**, especifique o limite de RPO. Os alertas serão gerados quando a replicação contínua excede esse limite.
6. Em **Retenção do ponto de recuperação**, especifique (em horas) a duração da janela de retenção para cada ponto de recuperação. Os computadores protegidos podem ser recuperados para qualquer ponto em uma janela de retenção. Há suporte para até 24 horas de retenção para computadores replicados no armazenamento premium. Há suporte para até 72 horas no armazenamento standard.
7. Em **Frequência de instantâneos consistente com o aplicativo**, especifique com que frequência (em minutos) serão criados os pontos de recuperação que contém instantâneos consistentes com aplicativos.
8. Clique em **OK**. A política deve ser criada em 30 a 60 segundos.

Quando você cria uma política de replicação, uma política de replicação de failback correspondente é criada automaticamente, com o sufixo "failback". Depois de criar a política, você pode editá-la selecionando > **Editar Configurações**.

## <a name="associate-a-configuration-server"></a>Associar um servidor de configuração 

Associe a política de replicação ao servidor de configuração local.

1. Clique em **Associar** e selecione o servidor de configuração.

    ![Associar o servidor de configuração](./media/vmware-azure-set-up-replication/associate1.png)

2. Clique em **OK**. O servidor de configuração deve ser associado em um ou dois minutos.

    ![Associação de servidor de configuração](./media/vmware-azure-set-up-replication/associate2.png)


## <a name="disassociate-or-delete-a-replication-policy"></a>Desassociar ou excluir uma política de replicação
1. Escolha a política de replicação.
    a. Para desassociar a política do servidor de configuração, verifique se nenhum computador replicado está usando a política. Em seguida, clique em **Desassociar**.
    b. Para excluir a política, verifique se ela não está associada a um servidor de configuração. Em seguida, clique em **Excluir**. A exclusão deve levar de 30 a 60 segundos.
2. Clique em **OK**.
