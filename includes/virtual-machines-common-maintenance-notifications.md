---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 07/02/2018
ms.author: shants
ms.custom: include file
ms.openlocfilehash: 782c855ff6d28f2cd96a87893ebf74023472badc
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38945338"
---
## <a name="view-vms-scheduled-for-maintenance-in-the-portal"></a>Exibir VMs agendadas para manutenção no portal

Após agendar uma onda de manutenção planejada, observe a lista de máquinas virtuais que serão afetadas pela próxima onda de manutenção. 

É possível usar o portal do Azure e procurar as VMs agendadas para manutenção.

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Na navegação à esquerda, clique em **Máquinas virtuais**.

3. No painel de máquinas virtuais, clique no botão **Colunas**para abrir a lista de colunas disponíveis.

4. Selecione e adicione as seguintes colunas:

   **Manutenção**: mostra o status de manutenção da VM. Estes são os valores possíveis:
      
      | Valor | DESCRIÇÃO |
      |-------|-------------|
      | Comece agora | A VM está na janela de manutenção de autoatendimento, na qual você pode iniciar a manutenção por conta própria. Veja a seguir como iniciar a manutenção na VM. | 
      | Agendado | A VM está programada para manutenção sem a opção para iniciar a manutenção. Para saber mais sobre a janela de manutenção, selecione a janela de manutenção agendada neste modo de exibição ou clique na VM. | 
      | Já atualizado | A VM já está atualizada e nenhuma ação adicional é necessária neste momento. | 
      | Tente novamente mais tarde | Você iniciou a manutenção, mas ela apresentou falha. Você poderá usar a opção de manutenção de autoatendimento posteriormente. | 
      | Tente agora | Tente realizar a manutenção que foi iniciada automaticamente sem êxito. | 

   **Janela de manutenção de autoatendimento**: mostra a janela de tempo, quando é possível iniciar automaticamente a manutenção nas VMs.
   
   **Janela de manutenção agendada**: mostra a janela de tempo, quando o Azure fará a manutenção da VM. 



## <a name="notification-and-alerts-in-the-portal"></a>Notificação e alertas no portal

O Azure comunica uma agenda para manutenção planejada, enviando um email para o grupo de proprietário e os coadministradores de assinatura. Você pode adicionar outros destinatários e canais para essa comunicação com a criação de alertas de log de atividades do Azure. Para saber mais, confira [Monitorar a atividade de assinatura com o Log de Atividades do Azure] (../articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu à esquerda, selecione **Monitor**. 
3. No painel **Monitor – Alertas (clássico)**, clique em **+Adicionar alerta do log de atividades**.
5. Preencha as informações na página **Adicionar alerta do log de atividades** e defina os seguintes itens nos **Critérios**:
   - **Categoria de eventos**: Integridade do Serviço
   - **Serviços**: Máquinas Virtuais e Conjuntos de Dimensionamento de Máquinas Virtuais
   - **Tipo**: manutenção planejada 
    
Para saber mais sobre como configurar alertas do log de atividades, confira o artigo [Criar alertas de log de atividade](../articles/monitoring-and-diagnostics/monitoring-activity-log-alerts.md).
    
    
## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Iniciar Manutenção na sua VM do portal

Ao examinar os detalhes da VM, você poderá ver mais detalhes relacionados à manutenção.  
Na parte superior do modo de exibição de detalhes VM, uma nova faixa de opções de notificação será adicionada se sua VM for incluída em uma onda de manutenção planejada. Além disso, uma nova opção é adicionada ao iniciar manutenção quando possível. 


Clique na notificação de manutenção para ver a página de manutenção com mais detalhes sobre a manutenção planejada. Nesse local, você pode **iniciar a manutenção** da VM.

Quando você inicia a manutenção, a máquina virtual passa pelo processo de manutenção e o respectivo status é atualizado para refletir o resultado dentro de alguns minutos.

Caso perca a janela de autoatendimento, você ainda poderá exibi-la quando a VM passar pela manutenção do Microsoft Azure. 
