---
title: Configurar a recuperação de desastre para uma VM IaaS do Azure para uma região secundária do Azure
description: Este início rápido fornece as etapas necessárias para recuperação de desastre de uma VM de IaaS do Azure entre regiões do Azure, usando o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: quickstart
ms.date: 10/28/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: e5f6cbec75a9f652cf05c9608c412e00489288b9
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212923"
---
# <a name="set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Configurar a recuperação de desastre para uma região secundária do Azure para uma VM do Azure 

O serviço [Azure Site Recovery](site-recovery-overview.md) contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) mantendo seus aplicativos de negócios em execução durante interrupções planejadas e não planejadas. O Site Recovery gerencia e orquestra a recuperação de desastre de máquinas locais e de VMs (máquinas virtuais) do Azure, incluindo replicação, failover e recuperação.

Este guia de início rápido descreve como replicar uma VM do Azure para uma região do Azure diferente.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
> Este artigo tem como objetivo orientar um novo usuário por meio da experiência do Azure Site Recovery com opções padrão e personalização mínima. Se você quiser saber mais sobre as várias configurações que podem ser personalizadas, consulte [o tutorial para habilitar a replicação para VMs do Azure](azure-to-azure-tutorial-enable-replication.md).

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no Portal do Azure em http://portal.azure.com.

## <a name="enable-replication-for-the-azure-vm"></a>Habilitar a replicação para a VM do Azure

1. No Portal do Azure, clique em **Máquinas virtuais** e selecione a VM que você deseja replicar.
2. Em **Operações**, clique em **Recuperação de desastre**.
3. Em **Configurar a recuperação de desastre** > **Região de destino**, selecione a região de destino para a qual você replicará.
4. Para este guia de início rápido, aceite as outras configurações padrão.
5. Clique em **Habilitar a replicação**. Isso inicia um trabalho para habilitar a replicação para a VM.

    ![habilitar a replicação](media/azure-to-azure-quickstart/enable-replication1.png)

## <a name="verify-settings"></a>Verificar as configurações

Após o trabalho de replicação, você poderá verificar o status de replicação, modificar as configurações de replicação e testar a implantação.

1. No menu da VM, clique em **Recuperação de desastre**.
2. Você pode verificar a integridade da replicação, os pontos de recuperação que foram criados e as regiões de origem e destino no mapa.

   ![Status de replicação](media/azure-to-azure-quickstart/replication-status.png)

## <a name="clean-up-resources"></a>Limpar recursos

A VM na região primária deixa de replicar quando você desabilita a replicação para ela:

- As configurações de replicação de origem são limpas automaticamente.
- A cobrança do Site Recovery para a VM também é interrompida.

Interrompa a replicação da seguinte maneira:

1. Selecione a VM.
2. Em **Recuperação de desastres**, clique em **Desabilitar Replicação**.

   ![Desabilitar a replicação](media/azure-to-azure-quickstart/disable2-replication.png)

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você replicou uma única VM para uma região secundária. Agora você pode explorar mais opções e tentar a replicação de um conjunto de VMs do Azure usando um plano de recuperação.

> [!div class="nextstepaction"]
> [Configurar a recuperação de desastre para VMs do Azure](azure-to-azure-tutorial-enable-replication.md)
