---
title: Mover as VMs IaaS do Azure para outra região do Azure usando o serviço Azure Site Recovery | Microsoft Docs
description: Use o Azure Site Recovery para mover as VMs IaaS do Azure de uma região do Azure para outra.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 5d844692b6199d93fa835da1021c9753311e17de
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824433"
---
# <a name="move-azure-vms-to-another-region"></a>Mover VMs do Azure para outra região

O Azure está crescendo extensivamente junto com a base de clientes e está adicionando suporte para novas regiões com aumento de demandas. Também há recursos mais recentes que são adicionados mensalmente aos serviços. Por conta disso, há ocasiões em que você desejará mover as VMs para outra região ou para Zonas de Disponibilidade, a fim de aumentar a disponibilidade.

Este documento explica os vários cenários em que você desejará mover as VMs e um guia de como a arquitetura deve ser configurada no destino para obter disponibilidade mais alta. 
> [!div class="checklist"]
> * [Por que mover as VMs do Azure](#why-would-you-move-azure-vms)
> * [Como mover as VMs do Azure](#how-to-move-azure-vms)
> * [Arquiteturas típicas](#typical-architectures-for-a-multi-tier-deployment)
> * [Mover as VMs no estado em que se encontram para uma região de destino](#move-azure-vms-to-another-region)
> * [Mover as VMs para aumentar a disponibilidade](#move-vms-to-increase-availability)


## <a name="why-would-you-move-azure-vms"></a>Por que mover as VMs do Azure

Os clientes movem as VMs pelos seguintes motivos:

- Se você já tiver feito a implantação em uma região e for adicionado o suporte a uma nova região que seja mais próxima aos usuários finais do aplicativo ou do serviço, você desejará **mover as VMs no estado em que se encontram para a nova região**, a fim de reduzir a latência. A mesma abordagem será adotada se você desejar consolidar assinaturas ou se houver regras de governança/da organização que exigem a movimentação. 
- Se a VM foi implantada como uma VM de instância única ou como parte de um conjunto de disponibilidade e você deseja aumentar o SLA de disponibilidade, **mova as VMs para um conjunto de disponibilidade**. 

## <a name="how-to-move-azure-vms"></a>Como mover as VMs do Azure
A movimentação das VMs envolve as seguintes etapas:

1. Verificar pré-requisitos 
2. Preparar as VMs de origem 
3. Preparar a região de destino 
4. Copiar os dados para a região de destino – use a tecnologia de replicação do Azure Site Recovery para copiar os dados da VM de origem para a região de destino
5. Testar a configuração: Depois que a replicação for concluída, teste a configuração executando um failover de teste em uma rede de não produção.
6. Realizar a movimentação 
7. Descartar os recursos na região de origem 


> [!IMPORTANT]
> Atualmente, o Azure Site Recovery dá suporte à movimentação de VMs de uma região para outra, mas não dá suporte à movimentação dentro de uma região. 

> [!NOTE]
> Diretrizes detalhadas sobre essas etapas são fornecidas na documentação de cada cenário, conforme mencionado aqui

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Arquiteturas típicas para uma implantação de várias camadas
A seção abaixo descreve as arquiteturas de implantação mais comuns adotadas pelos clientes, para um aplicativo de várias camadas no Azure. O exemplo que estamos usando aqui é de um aplicativo de três camadas com um IP público. Cada uma das camadas – Web, Aplicativo e Banco de Dados – tem 2 VMs e é conectada por um Balanceador de Carga às outras camadas. A camada de Banco de Dados tem a replicação do SQL AlwaysOn entre as VMs para HA (alta disponibilidade).

1.  **VMs de instância única implantadas em várias camadas** – cada VM em uma camada é configurada como uma VM de instância única, conectada por balanceadores de carga às outras camadas. Essa é a configuração mais simples adotada pelos clientes.

       ![single-VMs](media/move-vm-overview/regular-deployment.PNG)

2. **VMs em cada camada implantadas em um conjunto de disponibilidade** – cada VM em uma camada é configurada em um conjunto de disponibilidade. Os [conjuntos de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) garantem que as VMs implantadas no Azure sejam distribuídas entre vários nós de hardware isolados em um cluster. Isso garante que, se ocorrer uma falha de hardware ou de software no Azure, apenas um subconjunto de suas VMs será afetado e a solução geral permanecerá disponível e operacional. 
   
      ![avset](media/move-vm-overview/AVset.PNG)

3. **VMs em cada camada implantadas em um conjunto de disponibilidade** – cada VM em uma camada é configurada em [Zonas de Disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Uma Zona de Disponibilidade em uma região do Azure é uma combinação de um domínio de falha e um domínio de atualização. Por exemplo, se você criar três ou mais VMs em três zonas em uma região do Azure, as VMs serão efetivamente distribuídas em três domínios de falha e três domínios de atualização. A plataforma do Azure reconhece essa distribuição nos domínios de atualização para garantir que as VMs em diferentes zonas não sejam atualizadas ao mesmo tempo.

      ![zone-deploymnt](media/move-vm-overview/zone.PNG)



 ## <a name="move-vms-as-is-to-a-target-region"></a>Mover as VMs no estado em que se encontram para uma região de destino

Com base nas [arquiteturas](#typical-architectures-for-a-multi-tier-deployment) mencionadas acima, veja a seguir como será a aparência das implantações depois que você realizar a movimentação das VMs no estado em que se encontram para a região de destino.


1. **VMs de instância única implantadas em várias camadas** 

     ![single-zone.PNG](media/move-vm-overview/single-zone.PNG)

2. **VMs em cada camada implantadas em um conjunto de disponibilidade**

     ![crossregionAset.PNG](media/move-vm-overview/crossregionAset.PNG)


3. **VMs em cada camada implantadas em uma zona de disponibilidade**
      

     ![AzoneCross.PNG](media/move-vm-overview/AzoneCross.PNG)

## <a name="move-vms-to-increase-availability"></a>Mover as VMs para aumentar a disponibilidade

1. **VMs de instância única implantadas em várias camadas** 

     ![single-zone.PNG](media/move-vm-overview/single-zone.PNG)

2. **VMs em cada camada implantadas em um conjunto de disponibilidade** – você poderá optar por configurar e colocar as VMs de um conjunto de disponibilidade em Zonas de Disponibilidade separadas quando optar por habilitar a replicação na VM usando o Azure Site Recovery. O SLA de disponibilidade será de 99,9% depois que a operação de movimentação for concluída.

      ![aset-Azone.PNG](media/move-vm-overview/aset-Azone.PNG)


## <a name="next-steps"></a>Próximas etapas

Neste documento, você lerá as diretrizes gerais para a movimentação de VMs. Para conhecer a execução passo a passo disso, leia mais:


> [!div class="nextstepaction"]
> * [Mover as VMs do Azure para outra região](azure-to-azure-tutorial-migrate.md)

> * [Mover as VMs do Azure para Zonas de Disponibilidade](move-azure-VMs-AVset-Azone.md)

