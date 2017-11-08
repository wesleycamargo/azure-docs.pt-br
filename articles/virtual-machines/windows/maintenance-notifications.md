---
title: "Manipulação de notificações de manutenção para VMs do Windows no Azure | Microsoft Docs"
description: "Exiba notificações de manutenção para máquinas virtuais do Windows em execução no Azure e inicie a manutenção de autoatendimento."
services: virtual-machines-windows
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: zivr
ms.openlocfilehash: fec64b3c499577af6b1d6eddb1c761ee0af73772
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2017
---
# <a name="handling-planned-maintenance-notifications-for-windows-virtual-machines"></a>Administração de notificações de manutenção planejada para máquinas virtuais do Windows

O Azure realiza atualizações periodicamente para aumentar a confiabilidade, o desempenho e a segurança da infraestrutura de host para máquinas virtuais. As atualizações são as alterações como, por exemplo, aplicação de patches no ambiente de hospedagem ou atualização e desativação de hardware. A maioria dessas atualizações é realizada sem nenhum impacto nas máquinas virtuais hospedadas. No entanto, há casos em que as atualizações possuem um impacto:

- Se a manutenção não exigir uma reinicialização, o Azure usa migração in-loco para pausar a máquina virtual enquanto o host está atualizado.

- Se a manutenção requer uma reinicialização, você receberá um aviso informando para quando a manutenção está planejada. Nesses casos, você tem uma janela de tempo, na qual você pode iniciar a manutenção, quando for mais oportuno para você.


A manutenção planejada que requer um reinício é agendada em ondas. Cada onda tem um escopo diferente (regiões).

- Uma onda começa com uma notificação para os clientes. Por padrão, a notificação é enviada para o proprietário e os coproprietários da assinatura. Você pode adicionar mais destinatários e opções de mensagem como email, SMS e Webhooks, às notificações.  
- Logo após a notificação, uma janela de autoatendimento é definida. Durante essa janela, você pode ver qual das suas máquinas virtuais está incluída nessa onda e iniciar a manutenção usando reimplantação pró-ativa. 
- Após a janela de autoatendimento, uma janela de manutenção agendada começa. Neste momento, o Azure agenda e aplica a manutenção necessária à sua máquina virtual. 

O objetivo de ter duas janelas é dar a você tempo suficiente para iniciar a manutenção e reiniciar sua máquina virtual sabendo quando o Azure iniciará automaticamente a manutenção.


Você pode usar o portal do Azure, o PowerShell, a API REST e a CLI para consultar as janelas de manutenção para suas VMs e iniciar a manutenção de autoatendimento.

 > [!NOTE]
 > Se você tentar iniciar a manutenção e falhar, o Azure marcará sua VM como **ignorada** e ela não é reiniciada durante a janela de manutenção agendada. Em vez disso, você é contatado em um momento posterior com uma nova agenda. 


[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="check-maintenance-status-using-powershell"></a>Verificar o status de manutenção usando o PowerShell

Você também pode usar o Azure Powershell para ver quando as VMs serão agendadas para manutenção. As informações de manutenção planejada estão disponíveis no cmdlet [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) quando você usa o parâmetro `-status`.
 
As informações de manutenção só serão retornadas se houver manutenção planejada. Se não houver que nenhuma manutenção agendada que afete a VM, o cmdlet não retornará nenhuma informação de manutenção. 

```powershell
Get-AzureRmVM -ResourceGroupName rgName -Name vmName -Status
```

As propriedades a seguir são retornadas em MaintenanceRedeployStatus: 
| Valor | Descrição   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica se você pode iniciar a manutenção na máquina virtual neste momento ||
| PreMaintenanceWindowStartTime         | O início da janela de autoatendimento de manutenção quando você pode iniciar a manutenção na sua VM ||
| PreMaintenanceWindowEndTime           | O fim da janela de autoatendimento de manutenção quando você pode iniciar manutenção na sua VM ||
| MaintenanceWindowStartTime            | O início da janela de manutenção agendada quando você pode iniciar manutenção na sua VM ||
| MaintenanceWindowEndTime              | O fim da manutenção agendada janela quando você pode iniciar manutenção na sua VM ||
| LastOperationResultCode               | O resultado da última tentativa de iniciar a manutenção na VM ||



Você também pode obter o status de manutenção para todas as VMs em um grupo de recursos usando [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) e não especificando uma máquina virtual.
 
```powershell
Get-AzureRmVM -ResourceGroupName rgName --Status
```

A função do PowerShell a seguir usa a sua ID de assinatura e imprime uma lista de máquinas virtuais que estão agendados para manutenção.

```powershell

function MaintenanceIterator
{
    Select-AzureRmSubscription -SubscriptionId $args[0]

    $rgList= Get-AzureRmResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]
        $vmList = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Iniciar manutenção na sua VM usando o PowerShell

Usando as informações da função na seção anterior, o seguinte iniciará a manutenção em uma VM se **IsCustomerInitiatedMaintenanceAllowed** for definido como true.

```powershell
Restart-AzureRmVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="faq"></a>Perguntas frequentes


**P: Por que você precisa reiniciar minhas máquinas virtuais agora?**

**R:** Enquanto a maioria das atualizações para a plataforma do Azure não afetará a disponibilidade da máquina virtual, há casos onde não podemos evitar o reinício de máquinas virtuais hospedadas no Azure. Nós acumulamos várias alterações que exigem que nossos servidores sejam reiniciados, o que resultará no reinício de máquinas virtuais.

**P: Se eu seguir suas recomendações para Alta Disponibilidade usando um Conjunto de Disponibilidade, estou seguro?**

**R:**As máquinas virtuais implantadas em um conjunto de disponibilidade ou em conjuntos de dimensionamento de máquina virtual têm a noção de Domínios de Atualização (UD). Ao fazer a manutenção, o Azure respeita a restrição de UD e não reiniciará as máquinas virtuais de um UD diferente (dentro do mesmo conjunto de disponibilidade).  O Azure também espera pelo menos 30 minutos antes de passar para o próximo grupo de máquinas virtuais. 

Para saber mais sobre a alta disponibilidade, consulte Gerenciar a disponibilidade de máquinas virtuais do Windows no Azure ou Gerenciar a disponibilidade de máquinas virtuais Linux no Azure.

**P: Tenho a recuperação de desastres definida em outra região. Estou seguro?**

**R:** Cada região do Azure é emparelhada com outra região na mesma área geográfica (como EUA, Europa ou Ásia). As atualizações planejadas do Azure são distribuídas para regiões emparelhadas uma por vez, de modo a minimizar o tempo de inatividade e o risco de interrupção dos aplicativos. Durante a manutenção planejada, o Azure pode agendar uma janela semelhante para que os usuários iniciem a manutenção. no entanto, a janela de manutenção agendada será diferente entre as regiões emparelhadas.  

Para saber mais sobre regiões do Azure, veja Regiões e disponibilidade para máquinas virtuais no Azure.  Você pode ver a lista completa de pares de regiões aqui.

**P: Como posso ser notificado sobre a manutenção planejada?**

**R:** Uma onda manutenção planejada começa pela definição de uma agenda para uma ou mais regiões do Azure. Assim, uma notificação por email é enviada para os proprietários de assinatura (um email por assinatura). Os canais adicionais e os destinatários dessa notificação podem ser configurados usando Alertas de Log de Atividades. No caso de você implantar uma máquina virtual em uma região em que a manutenção planejada já foi agendada, você não receberá a notificação, mas em vez disso, precisará verificar o estado de manutenção da VM.

**P: Não vejo nenhuma indicação de manutenção planejada no portal, no Powershell ou na CLI, o que há de errado?**

**R:** AS informações relacionadas à manutenção planejada estarão disponíveis durante uma onda de manutenção planejada apenas para as VMs que serão afetadas por ela. Em outras palavras, se você não vir os dados, pode ser que a onda de manutenção já tenha sido concluída (ou não iniciada) ou que sua máquina virtual já esteja hospedada em um servidor atualizado.

**P: Devo iniciar a manutenção em minha máquina virtual?**

**R:** Em geral, as cargas de trabalho implantadas em um serviço de nuvem, conjunto de disponibilidade ou conjunto de dimensionamento de máquinas virtuais são resistentes à manutenção planejada.  Durante a manutenção planejada, somente um único domínio de atualização é afetado em um período específico. Lembre-se de que a ordem dos domínios de atualização que são afetados não necessariamente acontece de forma sequencial.

Você talvez queira iniciar a manutenção nos seguintes casos:
- Seu aplicativo é executado em uma única máquina virtual e você precisa aplicar todas as manutenções fora do horário comercial
- Você precisa coordenar o tempo de manutenção como parte do seu SLA
- Você precisa de mais de 30 minutos entre cada reinício de VM até mesmo dentro de um conjunto de disponibilidade.
- Você deseja desativar o aplicativo inteiro (várias camadas, vários domínios de atualização) para concluir a manutenção mais rapidamente.

**P: Há uma maneira de saber exatamente quando minha máquina virtual será afetada?**

**R:** Ao definir a agenda, definimos uma janela de tempo de vários dias. No entanto, a sequência exata dos servidores (e de VMs) nessa janela é desconhecida. Os clientes que gostariam de saber a hora exata para suas VMs podem usar eventos agendados e consultar de dentro da máquina virtual e receber uma notificação de 10 minutos antes de um reinício de VM.
  
**P: Quanto tempo levará o reinício da minha máquina virtual?**

**R:** Dependendo do tamanho da VM, o reinício poderá levar vários minutos. Observe que no caso de você usar os serviços de nuvem, conjuntos de dimensionamento ou o conjunto de disponibilidade, terá 30 minutos entre cada grupo de VMs (UD). 

**P: Qual é a experiência no caso de serviços de nuvem, conjuntos de dimensionamento e o Service Fabric?**

**R:** Enquanto essas plataformas são afetadas pela manutenção planejada, considera-se que os clientes que usam essas plataformas estejam seguros, já que somente as VMs em um Domínio de Upgrade (UD) simples será afetado em um determinado momento.  

**P: Caso eu tenha recebido um email sobre o encerramento de hardware, isso é equivalente à manutenção planejada?**

**R:** Embora o encerramento de hardware seja um evento de manutenção planejada, nós ainda não integramos esse caso de uso à nova experiência.  Achamos que os clientes ficarão confusos caso recebam dois emails semelhantes sobre duas ondas de manutenção planejada diferentes.

**P: Não vejo quaisquer informações de manutenção em minhas VMs. O que deu errado?**

**R:** Há várias razões por que você não vê quaisquer informações de manutenção em suas VMs:
1.  Você está usando uma assinatura marcada como interna da Microsoft.
2.  Suas VMs não estão agendadas para manutenção. É possível que a onda de manutenção tenha sido concluída, cancelada ou modificada para que suas VMs não fossem afetadas por ela.
3.  Você não tem a coluna 'manutenção' adicionada ao modo de exibição de lista da VM. Embora tenhamos adicionado essa coluna à exibição padrão, os clientes que configuraram para ver as colunas padrão devem adicionar manualmente a coluna **manutenção** ao modo de exibição de lista de VM.

**P: Minha VM está agendada para manutenção pela segunda vez. Por quê?**

**R:** Há diversos casos de uso em que você verá sua VM agendado para manutenção depois que você já tiver concluído a reimplantação de manutenção:
1.  Nós cancelamos a onda de manutenção e a reiniciamos com uma carga diferente. É possível que tenhamos detectado uma carga com falha e precisamos simplesmente implantar uma carga adicional.
2.  A VM foi *recuperada* em outro nó devido a uma falha de hardware
3.  Você optou por parar (desalocar) e reiniciar a máquina virtual
4.  Você tem o **desligamento automático** ativado para a máquina virtual


## <a name="next-steps"></a>Próximas etapas

Saiba como você pode registrar-se para eventos de manutenção dentro da VM usando [Eventos agendados](scheduled-events.md).