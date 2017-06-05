---
title: "Reinicialização de manutenção da VM para VMs do Linux no Azure | Microsoft Docs"
description: "Reinicialização de manutenção da VM para máquinas virtuais do Linux."
services: virtual-machines-linux
documentationcenter: 
author: zivr
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: eb4b92d8-be0f-44f6-a6c3-f8f7efab09fe
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: zivr
ms.translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: c02b2878462afb107c81317c9ea0a317cb9ce838
ms.contentlocale: pt-br
ms.lasthandoff: 04/18/2017


---

# <a name="vm-restarting-maintenance"></a>Reinicialização de manutenção da VM

Há alguns casos em que as VMs são reinicializadas devido à manutenção planejada na infraestrutura subjacente. Tendo um impacto na disponibilidade das VMs hospedadas no Azure, os seguintes itens agora estão disponíveis para uso:

-   Notificação enviada com, no mínimo, 30 dias de antecedência do impacto.

-   Visibilidade das janelas de manutenção por cada VM.

-   Flexibilidade e controle na definição do tempo exato da manutenção que afeta as VMs.

A manutenção no Microsoft Azure é agendada em iterações. As iterações iniciais têm escopo menor, a fim de reduzir o risco envolvido na distribuição de novas correções e funcionalidades. As iterações posteriores podem abranger várias regiões (nunca do mesmo par de regiões). Uma VM está incluída em uma única iteração de manutenção. Se uma iteração for anulada, as VMs restantes serão incluídas em outra iteração futura.

A iteração de manutenção planejada tem duas fases: uma Janela de Manutenção Preemptiva e uma Janela de Manutenção Agendada.

A **Janela de Manutenção Preemptiva** fornece a flexibilidade para iniciar a manutenção nas VMs. Ao fazer isso, você pode determinar quando as VMs são afetadas, a sequência da atualização e o tempo de manutenção entre cada VM. Você pode consultar cada VM para ver se ela está planejada para manutenção e verificar o resultado da última solicitação de manutenção iniciada.

A **Janela de Manutenção Agendada** ocorre quando o Azure agenda as VMs para manutenção. Durante essa janela de tempo, que segue a janela de manutenção preemptiva, você pode ainda consultar a janela de manutenção, mas não será mais possível orquestrar a manutenção.

## <a name="availability-considerations-during-planned-maintenance"></a>Considerações sobre disponibilidade durante a manutenção planejada 

### <a name="paired-regions"></a>Regiões emparelhadas

Cada região do Azure é emparelhada com outra região na mesma área geográfica, formando juntas um par regional. Ao executar a manutenção, o Azure atualizará apenas as instâncias de Máquina Virtual em uma única região de seu par. Por exemplo, ao atualizar as Máquinas Virtuais no Centro-Norte dos EUA, o Azure não atualizará qualquer Máquina Virtual no Centro-Sul dos EUA ao mesmo tempo. Isso será agendado em outro momento, permitindo o failover ou o balanceamento de carga entre regiões. No entanto, outras regiões, como o Norte da Europa, podem estar em manutenção simultaneamente com Leste dos EUA.
Leia mais sobre os [pares de regiões do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

### <a name="single-instance-vms-vs-availability-set-or-vm-scale-set"></a>VMs de instância única versus conjunto de disponibilidade ou conjunto de dimensionamento de VMs

Ao implantar uma carga de trabalho usando máquinas virtuais no Azure, é possível criar as VMs em um conjunto de disponibilidade para fornecer alta disponibilidade para o aplicativo. Essa configuração garante que, durante uma interrupção ou eventos de manutenção, pelo menos uma máquina virtual estará disponível.

Em um conjunto de disponibilidade, VMs individuais são distribuídas em até 20 domínios de atualização. Durante a manutenção planejada, somente um único domínio de atualização é afetado em um período específico. A ordem dos domínios de atualização afetados pode não continuar sequencialmente durante a manutenção planejada. Para VMs de instância única (que não fazem parte do conjunto de disponibilidade), não é possível prever nem determinar quais e quantas VMs são afetadas em conjunto.

Os conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que permite implantar e gerenciar um conjunto de VMs idênticas como um único recurso.
O conjunto de dimensionamento fornece garantias semelhantes a um conjunto de disponibilidade na forma de domínios de atualização. 

Para obter mais informações sobre como configurar as máquinas virtuais para alta disponibilidade, consulte [*Gerenciar a disponibilidade das máquinas virtuais do Windows*](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="scheduled-events"></a>Eventos agendados

O Serviço de Metadados do Azure permite descobrir informações sobre sua Máquina Virtual hospedada no Azure. Eventos Agendados, uma das categorias expostas, revela informações sobre eventos futuros (por exemplo, de reinicialização) para que seu aplicativo possa se preparar para eles e limitar interrupções.

Para obter mais informações sobre eventos agendados, consulte [Serviço de Metadados do Azure – eventos agendados](../virtual-machines-scheduled-events.md).

## <a name="maintenance-discovery-and-notifications"></a>Descoberta de manutenção e notificações

O agendamento de manutenção é visível para os clientes nas VMs individuais. Você pode usar o portal do Azure, a API, o PowerShell ou a CLI para consultar as janelas de manutenção preemptiva e agendada. Além disso, espere receber uma notificação (email) quando uma (ou mais) VMs forem afetadas durante o processo.

As fases de manutenção preemptiva e agendada começam com uma notificação. Espere receber uma única notificação para cada assinatura do Azure. Por padrão, a notificação é enviada ao administrador e coadministrador da assinatura. Você também pode configurar o público-alvo da notificação de manutenção.

### <a name="view-the-maintenance-window-in-the-portal"></a>Exibir a Janela de Manutenção no portal 

É possível usar o portal do Azure e procurar as VMs agendadas para manutenção.

1.  Entre no Portal do Azure.

2.  Clique e abra a folha **Máquinas Virtuais**.

3.  Clique no botão **Colunas** para abrir a lista de colunas disponíveis para seleção

4.  Selecione e adicione as colunas **Janela de Manutenção**. As VMs agendadas para manutenção têm as janelas de manutenção exibidas. Depois que a manutenção é concluída ou anulada para uma, a janela de manutenção não é mais apresentada.

### <a name="query-maintenance-details-using-the-azure-api"></a>Consultar detalhes da manutenção usando a API do Azure

Use a [API Obter Informações da VM](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-get) e procure a exibição da instância para descobrir os detalhes de manutenção de uma VM individual. A resposta inclui os seguintes elementos:

  - isCustomerInitiatedMaintenanceAllowed: indica se agora você pode iniciar a reimplantação preemptiva na VM.

  - preMaintenanceWindowStartTime: a hora de início da janela de manutenção preemptiva.

  - preMaintenanceWindowEndTime: a hora de término da janela de manutenção preemptiva. Após esse período, você não poderá mais iniciar a manutenção nessa VM.
    
  - maintenanceWindowStartTime: a hora de início da janela de manutenção agendada quando a VM é afetada.

  - maintenanceWindowEndTime: a hora de término da janela de manutenção agendada.
  
  - lastOperationResultCode: o resultado da última operação de Manutenção-Reimplantação.
 
  - lastOperationMessage: uma mensagem que descreve o resultado da última operação de Manutenção-Reimplantação.


## <a name="pre-emptive-redeploy"></a>Reimplantação preemptiva

A ação de reimplantação preemptiva fornece a flexibilidade para controlar o período em que a manutenção é aplicada às VMs no Azure. A manutenção planejada começa com uma janela de manutenção preemptiva, em que é possível decidir sobre o tempo exato de reinicialização de cada uma das VMs. Estes são os casos de uso em que uma funcionalidade desse tipo é útil:

-   A manutenção precisa ser coordenada com o cliente final.

-   A janela de manutenção agendada oferecida pelo Azure não é suficiente.
    É possível que a janela esteja no período mais ocupado da semana ou seja muito longa.

-   Para aplicativos de várias instâncias ou camadas, você precisa ter tempo suficiente entre duas VMs ou determinada sequência a ser seguida.

Ao chamar a reimplantação preemptiva em uma VM, ela move a VM para um nó já atualizado e, em seguida, a liga novamente, mantendo todas as opções de configuração e os recursos associados. Ao fazer isso, o disco temporário é perdido e os endereços IP dinâmicos associados ao adaptador de rede virtual são atualizados.

A reimplantação preemptiva é habilitada uma vez por VM. Em caso de erro durante o processo, a operação é anulada, a VM não é afetada e ela é excluída da iteração da manutenção planejada. Você será contatado em um momento posterior com um novo agendamento e receberá uma nova oportunidade para agendar e sequenciar o impacto nas VMs.

