---
title: "Administrando notificações de manutenção para VMs do Linux no Azure | Microsoft Docs"
description: "Exiba notificações de manutenção para máquinas virtuais do Linux em execução no Azure e inicie a manutenção de autoatendimento."
services: virtual-machines-linux
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: zivr
ms.openlocfilehash: be062ce9cfbe7486ef500dd9d27418cbf245d6e0
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2017
---
# <a name="handling-planned-maintenance-notifications-for-linux-virtual-machines"></a>Administrando notificações de manutenção planejada para máquinas virtuais do Linux

O Azure realiza atualizações periodicamente para aumentar a confiabilidade, o desempenho e a segurança da infraestrutura de host para máquinas virtuais. As atualizações são as alterações como, por exemplo, aplicação de patches no ambiente de hospedagem ou atualização e desativação de hardware. A maioria dessas atualizações é realizada sem nenhum impacto nas máquinas virtuais hospedadas. No entanto, há casos em que as atualizações possuem um impacto:

- Se a manutenção não exigir uma reinicialização, o Azure usa migração in-loco para pausar a máquina virtual enquanto o host está atualizado.

- Se a manutenção requer uma reinicialização, você receberá um aviso informando para quando a manutenção está planejada. Nesses casos, você tem uma janela de tempo, na qual você pode iniciar a manutenção, quando for mais oportuno para você.


A manutenção planejada que requer um reinício é agendada em ondas. Cada onda tem um escopo diferente (regiões).

- Uma onda começa com uma notificação para os clientes. Por padrão, a notificação é enviada para o proprietário e os coproprietários da assinatura. Você pode adicionar mais destinatários e opções de mensagem como email, SMS e Webhooks, às notificações. 
- Logo após a notificação, uma janela de autoatendimento é definida. Durante essa janela, você pode ver qual das suas máquinas virtuais está incluída nessa onda e iniciar a manutenção usando reimplantação pró-ativa. 
- Após a janela de autoatendimento, uma janela de manutenção agendada começa. Neste momento, o Azure agenda e aplica a manutenção necessária à sua máquina virtual.  

O objetivo de ter duas janelas é dar a você tempo suficiente para iniciar a manutenção e reiniciar sua máquina virtual sabendo quando o Azure iniciará automaticamente a manutenção.

Você pode usar a CLI do Azure, PowerShell, API REST e o portal do Azure para consultar as janelas de manutenção para suas VMs e iniciar a manutenção de autoatendimento.

 > [!NOTE]
 > Se você tentar iniciar a manutenção e falhar, o Azure marcará sua VM como **ignorada** e ela não é reinicializada durante a janela de manutenção agendada. Em vez disso, você é contatado em um momento posterior com uma nova agenda. 

## <a name="find-vms-scheduled-for-maintenance-using-cli"></a>Encontrar VMs agendadas para manutenção usando a CLI

Informações de manutenção planejada podem ser vistas usando [azure vm get-instance-view](/cli/azure/vm?view=azure-cli-latest#az_vm_get_instance_view).
 
Informações de manutenção são retornadas somente se houver manutenção planejada. Se não houver que nenhuma manutenção agendada que afete a VM, o comando não retornará nenhuma informação de manutenção. 

```azure-cli
az vm get-instance-view  - g rgName  -n vmName 
```

Os seguintes valores são retornados em MaintenanceRedeployStatus: 

| Valor | Descrição   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica se você pode iniciar a manutenção na máquina virtual neste momento ||
| PreMaintenanceWindowStartTime         | O início da janela de autoatendimento de manutenção quando você pode iniciar a manutenção na sua VM ||
| PreMaintenanceWindowEndTime           | O fim da janela de autoatendimento de manutenção quando você pode iniciar manutenção na sua VM ||
| MaintenanceWindowStartTime            | O início da janela de manutenção agendada quando você pode iniciar manutenção na sua VM ||
| MaintenanceWindowEndTime              | O fim da manutenção agendada janela quando você pode iniciar manutenção na sua VM ||
| LastOperationResultCode               | O resultado da última tentativa de iniciar a manutenção na VM ||


## <a name="start-maintenance-on-your-vm-using-cli"></a>Iniciar manutenção na sua VM usando a CLI

A chamada a seguir iniciará a manutenção em uma VM se `IsCustomerInitiatedMaintenanceAllowed` for definido como true.

```azure-cli
az vm perform-maintenance rgName vmName 
```

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]


## <a name="faq"></a>Perguntas frequentes


**P: por que você precisa reinicializar minhas máquinas virtuais agora?**

**R:** enquanto a maioria das atualizações para a plataforma do Azure não afete a disponibilidade da máquina virtual, há casos em que não podemos evitar o reinício de máquinas virtuais hospedadas no Azure. Nós acumulamos várias alterações que exigem que nossos servidores sejam reiniciados, o que resultará no reinício das máquinas virtuais.

**P: se eu seguir suas recomendações de alta disponibilidade usando um Conjunto de Disponibilidade, estou seguro?**

**R:**as máquinas virtuais implantadas em um conjunto de disponibilidade ou conjuntos de dimensionamento de máquinas virtuais têm a noção de UD (domínios de atualização). Ao fazer a manutenção, o Azure respeita a restrição de UD e não reinicia máquinas virtuais de UDs diferentes (dentro do mesmo conjunto de disponibilidade).  O Azure também espera pelo menos 30 minutos antes de passar para o próximo grupo de máquinas virtuais. 

Para saber mais sobre alta disponibilidade, consulte Gerenciar a disponibilidade de máquinas virtuais Windows no Azure ou Gerenciar a disponibilidade de máquinas virtuais Linux no Azure.

**P: tenho a recuperação de desastre definida em outra região. Estou seguro?**

**R:** cada região do Azure é emparelhada com outra região na mesma área geográfica (como EUA, Europa ou Ásia). As atualizações planejadas do Azure são distribuídas para regiões emparelhadas uma por vez, de modo a minimizar o tempo de inatividade e o risco de interrupção dos aplicativos. Durante a manutenção planejada, o Azure pode agendar um período semelhante para que os usuários iniciem a manutenção. No entanto, o período de manutenção agendada será diferente entre as regiões emparelhadas.  

Para saber mais sobre regiões do Azure, consulte Regiões e disponibilidade para máquinas virtuais no Azure.  Você pode ver a lista completa de pares de regiões aqui.

**P: como fazer para ser notificado sobre manutenção planejada?**

**R:** uma onda manutenção planejada começa pela definição de uma agenda para uma ou mais regiões do Azure. Assim, uma notificação por email é enviada para proprietários de assinaturas (um email por assinatura). Os canais adicionais e os destinatários dessa notificação podem ser configurados usando Alertas de Log de Atividades. Caso você implante uma máquina virtual em uma região em que a manutenção planejada já foi agendada, não receberá a notificação e precisará verificar o estado de manutenção da VM.

**P: não vejo nenhuma indicação de manutenção planejada no portal, no Powershell ou na CLI. O que há de errado?**

**R:** as informações relacionadas à manutenção planejada ficam disponíveis durante uma fase de manutenção planejada apenas para as VMs que serão afetadas por ela. Em outras palavras, se você não vir os dados, pode ser que a fase de manutenção já tenha sido concluída (ou não iniciada) ou que sua máquina virtual já esteja hospedada em um servidor atualizado.

**P: devo iniciar a manutenção em minha máquina virtual?**

**R:** em geral, as cargas de trabalho implantadas em um serviço de nuvem, conjunto de disponibilidade ou conjunto de dimensionamento de máquinas virtuais são resistentes à manutenção planejada.  Durante a manutenção planejada, somente um único domínio de atualização é afetado em um período específico. Lembre-se de que a ordem dos domínios de atualização que são afetados não necessariamente acontece de forma sequencial.

Talvez seja melhor iniciar a manutenção nos seguintes casos:
- Seu aplicativo é executado em uma única máquina virtual e você precisa aplicar todas as manutenções fora do horário comercial
- Você precisa coordenar o tempo de manutenção como parte do seu SLA
- Você precisa de mais de 30 minutos entre cada reinício de VM até mesmo dentro de um conjunto de disponibilidade.
- Você deseja desativar o aplicativo inteiro (várias camadas, vários domínios de atualização) para concluir a manutenção mais rapidamente.

**P: há uma maneira de saber exatamente quando minha máquina virtual será afetada?**

**R:** ao definir a agenda, definiremos um período de tempo de vários dias. No entanto, a sequência exata dos servidores (e de VMs) nesse período é desconhecido. Os clientes que queiram saber a hora exata para suas VMs podem usar eventos agendados e consultar na máquina virtual para receber uma notificação 10 minutos antes da reinicialização de uma VM.
  
**P: quanto tempo levará o reinício da minha máquina virtual?**

**R:** dependendo do tamanho da VM, o reinício poderá levar vários minutos. Observe que, caso você use serviços de nuvem, conjuntos de dimensionamento ou conjunto de disponibilidade, terá 30 minutos entre cada grupo de VMs (UD). 

**P: qual é a experiência em caso de serviços de nuvem, conjuntos de dimensionamento e Service Fabric?**

**R:** embora essas plataformas sejam afetadas pela manutenção planejada, considera-se que os clientes que usam essas plataformas estejam seguros, já que somente as VMs em um UD (domínio de atualização) simples serão afetadas em determinado momento.  

**P: caso eu tenha recebido um email sobre o encerramento de hardware, isso é equivalente à manutenção planejada?**

**R:** embora o encerramento de hardware seja um evento de manutenção planejada, nós ainda não integramos esse caso de uso à nova experiência.  Achamos que os clientes ficarão confusos caso recebam dois emails semelhantes sobre duas fases de manutenção planejada diferentes.

**P: não vejo informações sobre manutenção nas minhas VMs. O que deu errado?**

**R:** existem várias razões para não se ver informações de manutenção em suas VMs:
1.  Você está usando uma assinatura marcada como Microsoft interna.
2.  Suas VMs não estão agendadas para manutenção. É possível que a fase de manutenção tenha sido concluída, cancelada ou modificada e, portanto, suas VMs não estão sendo mais afetadas por ela.
3.  Você não tem a coluna 'manutenção' adicionada ao modo de exibição de lista da VM. Embora tenhamos adicionado essa coluna à exibição padrão, os clientes que configuraram para ver colunas não padrão devem adicionar manualmente a coluna **Manutenção** ao modo de exibição de lista da VM.

**P: minha VM está agendada para manutenção pela segunda vez. Por quê?**

**R:** há diversos casos de uso em que você verá sua VM agendada para manutenção depois de ter concluído a reimplantação da manutenção:
1.  Nós cancelamos a fase de manutenção e a reiniciamos com uma carga diferente. É possível que tenhamos detectado uma carga com falha e seja necessário simplesmente implantar uma carga adicional.
2.  A VM foi *recuperada* em outro nó devido a uma falha de hardware
3.  Você optou por parar (desalocar) e reiniciar a máquina virtual
4.  Você tem o **desligamento automático** ligado para a VM


## <a name="next-steps"></a>Próximas etapas

Saiba como você pode registrar-se para eventos de manutenção dentro da VM usando [Eventos agendados](scheduled-events.md).