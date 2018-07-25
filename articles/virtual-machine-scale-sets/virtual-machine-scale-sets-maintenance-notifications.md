---
title: Manipulação de notificações de manutenção para Conjuntos de Dimensionamento de Máquinas Virtuais no Azure | Microsoft Docs
description: Exiba notificações de manutenção para Conjuntos de Dimensionamento de Máquinas Virtuais no Azure e inicie a manutenção de autoatendimento.
services: virtual-machine-scale-sets
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2018
ms.author: shants
ms.openlocfilehash: 4ce984686c2bb320d5d32771d31b81cdec1153af
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38506253"
---
# <a name="handling-planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Lidar com notificações de manutenção planejadas para Conjuntos de Dimensionamento de Máquinas Virtuais

O Azure realiza atualizações periodicamente para aumentar a confiabilidade, o desempenho e a segurança da infraestrutura de host para máquinas virtuais. As atualizações são as alterações como, por exemplo, aplicação de patches no ambiente de hospedagem ou atualização e desativação de hardware. A maioria dessas atualizações é realizada sem nenhum impacto nas máquinas virtuais hospedadas. No entanto, há casos em que as atualizações possuem um impacto:

- Se a manutenção não exigir uma reinicialização, o Azure usa migração in-loco para pausar a máquina virtual enquanto o host está atualizado. Essas operações de manutenção não reiniciáveis são o domínio de falha aplicado pelo domínio de falha e o andamento é interrompido quando algum sinal de aviso de integridade é recebido.

- Se a manutenção requer uma reinicialização, você receberá um aviso informando para quando a manutenção está planejada. Nesses casos, você tem uma janela de tempo, na qual você pode iniciar a manutenção, quando for mais oportuno para você.


A manutenção planejada que requer um reinício é agendada em ondas. Cada onda tem um escopo diferente (regiões).

- Uma onda começa com uma notificação para os clientes. Por padrão, a notificação é enviada para o proprietário e os coproprietários da assinatura. Você pode adicionar mais destinatários e opções de mensagem como email, SMS e Webhooks, às notificações usando os [alertas de log de atividades](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) do Azure.  
- No momento da notificação, uma *janela de autoatendimento* é disponibilizada. Durante essa janela, você pode encontrar quais máquinas virtuais estão incluídas nessa onda e iniciar proativamente a manutenção de acordo com suas necessidades de agendamento.
- Após a janela de autoatendimento, *janela de manutenção agendada* inicia. Em algum momento durante esta janela, o Azure agenda e aplica a manutenção necessária à sua máquina virtual. 

O objetivo de ter duas janelas é dar a você tempo suficiente para iniciar a manutenção e reiniciar sua máquina virtual sabendo quando o Azure iniciará automaticamente a manutenção.


Você pode usar o portal do Azure, o PowerShell, a API REST e a CLI para consultar as janelas de manutenção para suas VMs do conjunto de dimensionamento de máquina virtual e iniciar a manutenção de autoatendimento.

  
## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Você deve iniciar a manutenção durante a janela de autoatendimento?  

As diretrizes a seguir devem ajudá-lo a decidir se você deseja usar essa funcionalidade e a iniciar a manutenção no seu próprio tempo.

> [!NOTE] 
> A manutenção de autoatendimento pode não estar disponível para todas as suas VMs. Para determinar se a reimplantação pró-ativa está disponível para sua VM, procure o status de manutenção **Iniciar agora**. No momento, a manutenção de autoatendimento não está disponível para os serviços de nuvem (função Web/de trabalho) nem para o Service Fabric.


A manutenção de autoatendimento não é recomendada para implantações usando **conjuntos de disponibilidade**, pois elas são configurações altamente disponíveis, em que apenas um domínio de atualização é afetado a qualquer momento. 

- Permita que o Azure dispare a manutenção. Para a manutenção que exige reinício, lembre-se de que a manutenção será feita de domínio de atualização em domínio de atualização, que os domínios de atualização não necessariamente recebem a manutenção em sequência e que há uma pausa de 30 minutos entre os domínios de atualização.
- Se a perda temporária de parte da capacidade (1/contagem de domínios de atualização) é uma preocupação, é possível compensar isso facilmente alocando instâncias de adição durante o período de manutenção.
- Para a manutenção que não exige reinicialização, as atualizações são aplicadas no nível do domínio de falha. 
    
**Não** use o autoatendimento de manutenção nos seguintes cenários: 

- Se você desliga as VMs com frequência, seja manualmente, usando o DevTest Labs, usando o desligamento automático ou seguindo um agendamento, é possível reverter o status de manutenção e, portanto, aumentar o tempo de inatividade.
- Em VMs de curta duração que você sabe que serão excluídas antes do final da onda de manutenção. 
- Para cargas de trabalho com um estado grande armazenado no disco local (efêmero) e que se deseja manter após a atualização. 
- Para casos em que você redimensiona a VM com frequência, pois isso pode reverter o status de manutenção. 
- Se você adotou eventos agendados que permitem um failover proativo ou o desligamento normal da carga de trabalho, 15 minutos antes do início do desligamento da manutenção.

**Use** a manutenção de autoatendimento se você estiver planejando executar sua VM ininterrupta durante a fase de manutenção agendada e nenhuma das indicações de contadores mencionadas acima são aplicáveis. 

É melhor usar a manutenção de autoatendimento nos seguintes casos:

- Você precisa comunicar uma janela de manutenção exata para o gerenciamento ou o cliente final. 
- Você precisa concluir a manutenção em uma determinada data. 
- Você precisa controlar a sequência de manutenção, por exemplo, o aplicativo de várias camadas para garantir uma recuperação segura.
- Você precisa de mais de 30 minutos de tempo de recuperação de máquina virtual entre dois domínios de atualização (UDs). Para controlar o tempo entre domínios de atualização, você deve disparar a manutenção em suas VMs um domínio de atualização (UD) por vez.

 
## <a name="view-virtual-machine-scale-sets-impacted-by-maintenance-in-the-portal"></a>Exibir conjuntos de dimensionamento de máquina virtual afetados pela manutenção no portal

Após o agendamento de uma fase de manutenção planejada, veja a lista de conjuntos de dimensionamento de máquina virtual que são afetados pela próxima fase de manutenção usando o portal do Azure. 

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** na barra de navegação à esquerda e escolha **Conjuntos de dimensionamento de máquinas virtuais**.
3. Na página **Conjuntos de dimensionamento de máquina virtual**, escolha a opção **Editar colunas** na parte superior para abrir a lista de colunas disponíveis.
4. Na **seção Colunas disponíveis**, selecione o item **Manutenção de autoatendimento** e use os botões de seta para movê-lo para a lista **Colunas selecionadas**. Você pode alternar a lista suspensa na seção **Colunas disponíveis** de **Todos** para **Propriedades** para facilitar a localização do item **Manutenção de autoatendimento**. Quando você tiver o item **Manutenção de autoatendimento** na seção **Colunas selecionadas**, selecione o botão **Aplicar** na parte inferior da página. 

Depois de seguir as etapas acima, a coluna **Manutenção de autoatendimento** aparecerá na lista de conjuntos de dimensionamento de máquina virtual. Cada conjunto de dimensionamento de máquina virtual pode ter um dos seguintes valores para a coluna de manutenção de autoatendimento:

| Valor | DESCRIÇÃO |
|-------|-------------|
| sim | Pelo menos uma máquina virtual em seu conjunto de dimensionamento de máquina virtual está em uma janela de autoatendimento. Você pode iniciar a manutenção a qualquer momento durante essa janela de autoatendimento. | 
| Não  | Não há máquinas virtuais que estão em uma janela de autoatendimento no conjunto de dimensionamento de máquinas virtuais afetado. | 
| - | Seus conjuntos de dimensionamento de máquinas virtuais não fazem parte de uma fase de manutenção planejada.| 

## <a name="notification-and-alerts-in-the-portal"></a>Notificação e alertas no portal

O Azure comunica uma agenda para manutenção planejada, enviando um email para o grupo de proprietário e os coadministradores de assinatura. Você pode adicionar outros destinatários e canais para essa comunicação com a criação de alertas de log de atividades do Azure. Para saber mais, veja [Monitorar a atividade de assinatura com o Log de Atividades do Azure] (../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu à esquerda, selecione **Monitor**. 
3. No painel **Monitor – Alertas (clássico)**, clique em **+Adicionar alerta do log de atividades**.
4. Preencha as informações na página **Adicionar alerta do log de atividades** e defina os seguintes itens nos **Critérios**:
   - **Categoria de eventos**: Integridade do Serviço
   - **Serviços**: Máquinas Virtuais e Conjuntos de Dimensionamento de Máquinas Virtuais
   - **Tipo**: manutenção planejada 
    
Para saber mais sobre como configurar alertas de Log de Atividade, veja [Criar alertas de log de atividade](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Iniciar a manutenção em seu conjunto de dimensionamento de máquina virtual no portal

Ao examinar a visão geral dos conjuntos de dimensionamento de máquinas virtuais, será possível ver mais detalhes relacionados à manutenção. Se houver pelo menos uma máquina virtual no conjunto de dimensionamento de máquina virtual incluída na fase de manutenção planejada, uma nova faixa de opções de notificação será adicionada à parte superior da página. Você pode clicar na faixa de opções de notificação para acessar a página **Manutenção** para ver qual instância de máquina virtual foi afetada pela manutenção planejada. 

A partir daí, você poderá iniciar a manutenção marcando a caixa correspondente à máquina virtual afetada e, em seguida, clicar na opção **Iniciar a manutenção**.

Depois que você iniciar a manutenção, as máquinas virtuais afetadas em seu conjunto de dimensionamento de máquina virtual passarão pela manutenção e ficarão temporariamente indisponíveis. Caso perca a janela de autoatendimento, você ainda poderá exibi-la quando seu conjunto de dimensionamento de máquinas virtuais passar pela manutenção do Azure.
 
## <a name="check-maintenance-status-using-powershell"></a>Verificar o status de manutenção usando o PowerShell

Você pode usar o Azure Powershell para ver quando as VMs em seus conjuntos de dimensionamento de máquinas virtuais serão agendadas para manutenção. As informações de manutenção planejada estão disponíveis no cmdlet [Get-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss) quando você usa o parâmetro `-InstanceView`.
 
As informações de manutenção só serão retornadas se houver manutenção planejada. Se não houver nenhuma manutenção agendada que afete a instância da VM, o cmdlet não retornará nenhuma informação de manutenção. 

```powershell
Get-AzureRmVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

As propriedades a seguir são retornadas em MaintenanceRedeployStatus: 
| Valor | DESCRIÇÃO   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica se você pode iniciar a manutenção na máquina virtual neste momento ||
| PreMaintenanceWindowStartTime         | O início da janela de autoatendimento de manutenção quando você pode iniciar a manutenção na sua VM ||
| PreMaintenanceWindowEndTime           | O fim da janela de autoatendimento de manutenção quando você pode iniciar manutenção na sua VM ||
| MaintenanceWindowStartTime            | O início da manutenção agendada na qual o Azure inicia a manutenção na sua VM ||
| MaintenanceWindowEndTime              | O término da janela de manutenção agendada na qual o Azure inicia a manutenção na sua VM ||
| LastOperationResultCode               | O resultado da última tentativa de iniciar a manutenção na VM ||



### <a name="start-maintenance-on-your-vm-instance-using-powershell"></a>Iniciar manutenção em sua instância de VM usando o PowerShell

Você pode iniciar a manutenção em uma VM se **IsCustomerInitiatedMaintenanceAllowed** estiver definido como verdadeiro usando o cmdlet [Set-AzureRmVmss](/powershell/module/azurerm.compute/set-azurermvmss) com o parâmetro `-PerformMaintenance`.

```powershell
Set-AzureRmVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-using-cli"></a>Verificar o status de manutenção usando a CLI

As informações de manutenção planejada podem ser vistas usando [az vmss list-instances](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances).
 
As informações de manutenção só serão retornadas se houver manutenção planejada. Se não houver que nenhuma manutenção agendada que afete a instância da VM, o comando não retornará nenhuma informação de manutenção. 

```azure-cli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

As propriedades a seguir retornarão em MaintenanceRedeployStatus para cada instância de VM: 
| Valor | DESCRIÇÃO   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica se você pode iniciar a manutenção na máquina virtual neste momento ||
| PreMaintenanceWindowStartTime         | O início da janela de autoatendimento de manutenção quando você pode iniciar a manutenção na sua VM ||
| PreMaintenanceWindowEndTime           | O fim da janela de autoatendimento de manutenção quando você pode iniciar manutenção na sua VM ||
| MaintenanceWindowStartTime            | O início da manutenção agendada na qual o Azure inicia a manutenção na sua VM ||
| MaintenanceWindowEndTime              | O término da janela de manutenção agendada na qual o Azure inicia a manutenção na sua VM ||
| LastOperationResultCode               | O resultado da última tentativa de iniciar a manutenção na VM ||


### <a name="start-maintenance-on-your-vm-instance-using-cli"></a>Iniciar a manutenção em sua instância de VM usando a CLI

A chamada a seguir iniciará a manutenção em uma instância de VM se `IsCustomerInitiatedMaintenanceAllowed` for definido como true.

```azure-cli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>Perguntas frequentes


**P: por que você precisa reinicializar minhas máquinas virtuais agora?**

**R:** enquanto a maioria das atualizações para a plataforma do Azure não afete a disponibilidade da máquina virtual, há casos em que não podemos evitar o reinício de máquinas virtuais hospedadas no Azure. Nós acumulamos várias alterações que exigem que nossos servidores sejam reiniciados, o que resultará no reinício das máquinas virtuais.

**P: se eu seguir suas recomendações de alta disponibilidade usando um Conjunto de Disponibilidade, estou seguro?**

**R:** as máquinas virtuais implantadas em um conjunto de disponibilidade ou conjuntos de dimensionamento de máquinas virtuais têm a noção de UD (domínios de atualização). Ao fazer a manutenção, o Azure respeita a restrição de UD e não reinicia máquinas virtuais de UDs diferentes (dentro do mesmo conjunto de disponibilidade).  O Azure também espera pelo menos 30 minutos antes de passar para o próximo grupo de máquinas virtuais. 

Para obter mais informações sobre alta disponibilidade, consulte [Regiões e disponibilidade para máquinas virtuais no Azure](../virtual-machines/windows/regions-and-availability.md).

**P: como fazer para ser notificado sobre manutenção planejada?**

**R:** uma onda manutenção planejada começa pela definição de uma agenda para uma ou mais regiões do Azure. Assim, uma notificação por email é enviada para proprietários de assinaturas (um email por assinatura). Os canais adicionais e os destinatários dessa notificação podem ser configurados usando Alertas de Log de Atividades. Caso você implante uma máquina virtual em uma região em que a manutenção planejada já foi agendada, não receberá a notificação e precisará verificar o estado de manutenção da VM.

**P: Não vejo nenhuma indicação de manutenção planejada no portal, no Powershell e na CLI. Qual é o problema?**

**R:** As informações relacionadas à manutenção planejada ficam disponíveis durante uma onda de manutenção planejada apenas para as VMs que serão afetadas por ela. Em outras palavras, se você não vir os dados, pode ser que a fase de manutenção já tenha sido concluída (ou não iniciada) ou que sua máquina virtual já esteja hospedada em um servidor atualizado.

**P: há uma maneira de saber exatamente quando minha máquina virtual será afetada?**

**R:** ao definir a agenda, definiremos um período de tempo de vários dias. No entanto, a sequência exata dos servidores (e de VMs) nesse período é desconhecido. Os clientes que querem saber a hora exata para suas VMs podem usar [eventos agendados](../virtual-machines/windows/scheduled-events.md) e consultar na máquina virtual para receber uma notificação de 15 minutos antes do reinício de uma VM.

**P: quanto tempo levará o reinício da minha máquina virtual?**

**R:** dependendo do tamanho da VM, o reinício poderá levar vários minutos durante a janela de manutenção por autoatendimento. Durante as reinicializações iniciadas pelo Azure na janela de manutenção agendada, a reinicialização geralmente levará cerca de 25 minutos. Observe que, caso você use Serviços de Nuvem (função Web/de trabalho), Conjuntos de Dimensionamento de Máquinas Virtuais ou conjuntos de disponibilidade, você terá 30 minutos entre cada grupo de VMs (UD) durante a janela de manutenção agendada. 

**P: não vejo informações sobre manutenção nas minhas VMs. O que deu errado?**

**R:** existem várias razões para não se ver informações de manutenção em suas VMs:
   - Você está usando uma assinatura marcada como Microsoft interna.
   - Suas VMs não estão agendadas para manutenção. É possível que a onda de manutenção tenha sido concluída, cancelada ou modificada de modo que suas VMs não são afetadas por ela.
   - Você não tem a coluna **Manutenção** adicionada ao modo de exibição de lista da VM. Embora tenhamos adicionado essa coluna à exibição padrão, os clientes que configuraram para ver colunas não padrão devem adicionar manualmente a coluna **Manutenção** ao modo de exibição de lista da VM.

**P: minha VM está agendada para manutenção pela segunda vez. Por quê?**

**R:** há diversos casos de uso em que você verá sua VM agendada para manutenção depois de ter concluído a reimplantação da manutenção:
   - Nós cancelamos a fase de manutenção e a reiniciamos com uma carga diferente. É possível que tenhamos detectado uma carga com falha e seja necessário simplesmente implantar uma carga adicional.
   - Sua máquina virtual teve o *serviço autorrestabelecido* para outro nó devido a uma falha de hardware.
   - Você optou por parar (desalocar) e reiniciar a VM.
   - O **desligamento automático** está ativado para a VM.

## <a name="next-steps"></a>Próximas etapas

Saiba como você pode registrar-se para eventos de manutenção dentro da VM usando [Eventos agendados](../virtual-machines/windows/scheduled-events.md).
