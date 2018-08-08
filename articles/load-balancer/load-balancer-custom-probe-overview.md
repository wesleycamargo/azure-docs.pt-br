---
title: Usar as investigações de integridade do Load Balancer para proteger o serviço|Microsoft Docs
description: Saiba como usar as investigações de integridade para monitorar instâncias atrás do Load Balancer
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2018
ms.author: kumud
ms.openlocfilehash: 7366273e30132daf7dc5ea15072c574180d1bc8b
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397272"
---
# <a name="load-balancer-health-probes"></a>Investigações de integridade do Load Balancer

O Azure Load Balancer usa investigações de integridade para determinar qual instância de pool de back-end receberá novos fluxos. É possível usar investigações de integridade para detectar a falha de um aplicativo em uma instância de back-end. Também é possível gerar uma resposta personalizada a uma investigação de integridade e usar a investigação de integridade para controle de fluxo e sinal para que o Load Balancer continue a enviar ou pare de enviar novos fluxos a uma instância de back-end. Isso pode ser usado para gerenciar o tempo de inatividade planejado ou de carga.

Quando uma investigação de integridade falha, o Load Balancer para de enviar novos fluxos à respectiva instância não íntegra. O comportamento dos fluxos novos e existentes depende se um fluxo é TCP ou UDP, bem como qual SKU do Load Balancer que você está usando.  Revisar o [comportamento inoperante da investigação para obter detalhes](#probedown).

> [!IMPORTANT]
> As investigações de integridade do Load Balancer são originadas do endereço IP 168.63.129.16 e não devem ser bloqueadas para que as investigações marquem a instância como operante.  Revisar o [endereço IP de origem da investigação](#probesource) para obter detalhes.

## <a name="health-probe-types"></a>Tipos de investigações de integridade

As investigações de integridade podem observar qualquer porta em uma instância de back-end, incluindo a porta na qual o serviço real é fornecido. A investigação de integridade dá suporte a ouvintes TCP ou pontos de extremidade HTTP. 

Para o balanceamento de carga UDP, é necessário gerar um sinal de investigação de integridade personalizado para a instância de back-end usando uma investigação de integridade TCP ou HTTP.

Ao usar as [regras de balanceamento de carga das Portas HA](load-balancer-ha-ports-overview.md) com o [Standard Load Balancer](load-balancer-standard-overview.md), todas as cargas das portas serão balanceadas e uma única resposta da investigação de integridade deverá refletir o status da instância inteira.  

Você não deve prover NAT ou proxy em uma investigação de integridade por meio da instância que recebe a investigação de integridade para outra instância na VNet, pois isso pode resultar em falhas em cascata no cenário.

Caso queira testar uma falha de investigação de integridade ou marcar uma instância individual, você pode usar um Grupo de Segurança para bloquear explicitamente a investigação de integridade (destino ou [origem](#probesource)).

### <a name="tcp-probe"></a>Investigação TCP

As investigações TCP iniciam uma conexão executando um handshake TCP aberto de três vias com a porta definida.  Isso é seguido por um handshake TCP fechado de quatro vias.

O intervalo mínimo de investigação é de 5 segundos e o número mínimo de respostas não íntegras é 2.  A duração total não pode exceder 120 segundos.

Uma investigação TCP falha quando:
* O ouvinte TCP na instância não responde durante o período de tempo limite.  Uma investigação é marcada como inoperante dependendo do número de solicitações com falha, as quais foram configuradas para passar sem resposta antes da marcação de investigação inoperante.
* A investigação recebe uma redefinição de TCP da instância.

### <a name="http-probe"></a>Investigação HTTP

As investigações HTTP estabelecem uma conexão TCP e emitem um HTTP GET com o caminho especificado. As investigações HTTP dão suporte a caminhos relativos para o HTTP GET. A investigação de integridade é marcada como operante quando a instância responde com um status HTTP 200 dentro do período de tempo limite.  Por padrão, as investigações de integridade HTTP tentam verificar a porta de investigação de integridade configurada a cada 15 segundos. O intervalo mínimo de investigação é de 5 segundos. A duração total não pode exceder 120 segundos. 


As investigações HTTP podem ser úteis se você quiser implementar sua própria lógica para remover instâncias da rotação do balanceador de carga. Por exemplo, é recomendável remover uma instância caso ela esteja usando mais de 90% da CPU e retorne um status HTTP diferente de 200. 

Se você usar o Serviços de Nuvem e tiver funções web que usem w3wp.exe, também é possível obter o monitoramento automático do site. Falhas no código do site retornam um status não 200 para a investigação do balanceador de carga.  A investigação HTTP substitui a investigação de agente convidado padrão. 

Uma investigação HTTP falha quando:
* O ponto de extremidade da investigação HTTP retorna um código de resposta HTTP diferente de 200 (por exemplo, 403, 404 ou 500). Isso marcará a investigação de integridade inoperante imediatamente. 
* O ponto de extremidade de investigação HTTP não responde durante um período de tempo limite de 31 segundos. Dependendo do valor de tempo limite definido, várias solicitações de investigação podem ficar sem resposta antes de a investigação ser marcada como não estando em execução (ou seja, antes das investigações SuccessFailCount serem enviadas).
* O ponto de extremidade de investigação HTTP fecha a conexão por meio de uma redefinição de TCP.

### <a name="guest-agent-probe-classic-only"></a>Investigação de agente convidado (somente clássico)

Por padrão, as funções do serviço de nuvem (funções de trabalho e funções web) usam um agente convidado para o monitoramento de investigação.   Você deve considerar isso uma opção de último recurso.  Sempre é necessário definir uma investigação de integridade explicitamente com uma investigação TCP ou HTTP. Uma investigação de agente convidado não é tão eficiente quanto a investigações definidas explicitamente para a maioria dos cenários de aplicativo.  

Uma investigação de agente convidado é uma verificação do agente convidado dentro da VM. Em seguida, ele escuta e responde com uma resposta HTTP 200 OK somente quando a instância está no estado Pronto. (Outros estados são Ocupado, Reciclando ou Parando.)

Para obter mais informações, consulte [Configurar o arquivo de definição de serviço (csdef) para investigações de integridade](https://msdn.microsoft.com/library/azure/ee758710.aspx) ou [Introdução à criação de um balanceador de carga público para serviços de nuvem](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

Se o agente convidado não responder com HTTP 200 OK, o balanceador de carga marcará a instância como sem resposta. Em seguida, ele para de enviar fluxos a essa instância. O balanceador de carga continua a verificar a instância. 

Se o agente convidado responder com um HTTP 200, o balanceador de carga enviará o tráfego para essa instância novamente.

Quando você usa uma função Web, o código do site geralmente é executado em w3wp.exe, que não é monitorado nem pela malha do Azure nem pelo agente convidado. Falhas em w3wp.exe (por exemplo, respostas HTTP 500) não são relatadas para o agente convidado. Consequentemente, o balanceador de carga não perde essa instância.

## <a name="probe-health"></a>Integridade da investigação

Investigações de integridade TCP e HTTP são consideradas íntegras e marcam a instância de função como íntegra quando:

* A investigação de integridade é bem-sucedida na primeira vez em que a VM é inicializada.
* O número SuccessFailCount (descrito acima) define o valor de investigações bem-sucedidas necessárias para marcar a instância da função como íntegra. Se uma instância de função tiver sido removida, o número de investigações bem-sucedidas e sucessivas deverá ser igual ou exceder o valor de SuccessFailCount a fim de marcar a instância de função como em execução.

> [!NOTE]
> Se a integridade de uma instância de função for flutuante, o balanceador de carga aguardará até recolocar a instância de função no estado íntegro. Esse tempo de espera extra protege o usuário e a infraestrutura, e é uma política intencional.

## <a name="probe-count-and-timeout"></a>Contagem e tempo limite de investigação

O comportamento de investigação depende do seguinte:

* Número de investigações bem-sucedidas que permitem a uma instância ser marcada como operante.
* Número de investigações com falha que fazem com que uma instância seja marcada como inoperante.

Os valores de tempo limite e frequência definidos em SuccessFailCount determinam se uma instância foi confirmada como em execução ou não. Para o Portal do Azure, o tempo limite é definido como duas vezes o valor da frequência.

Uma regra de balanceamento de carga tem uma única investigação de integridade definida no respectivo pool de back-end.

## <a name="probedown"></a>Comportamento de investigação inoperante

### <a name="tcp-connections"></a>Conexões TCP

Novas conexões TCP terão êxito na instância de back-end que esteja íntegra e tenha um SO convidado capaz de aceitar um novo fluxo.

Se a investigação de integridade de uma instância de back-end falhar, as conexões TCP estabelecidas para essa instância de back-end continuarão.

Se todas as investigações para todas as instâncias em um pool de back-end falharem, nenhum novo fluxo será enviado ao pool de back-end. O Load Balancer Standard permitirá a continuação dos fluxos TCP estabelecidos.  O Basic Load Balancer terminará todos os fluxos TCP existentes para o pool de back-end.
 
Como o fluxo é sempre entre o cliente e o SO convidado da VM, um pool com todas as investigações inoperantes fará com que um front-end não responda às tentativas abertas de conexão TCP, pois não há nenhuma instância de back-end íntegra para receber o fluxo.

### <a name="udp-datagrams"></a>Datagramas UDP

Datagramas UDP serão entregues para as instâncias de back-end íntegras.

UDP é sem conexão e não há estado de fluxo controlado para UDP. Se a investigação de integridade de qualquer instância de back-end falhar, os fluxos UDP existentes podem ser movidos para outra instância íntegra no pool de back-end.

Se todas as investigações para todas as instâncias em um pool de back-end falharem, os fluxos UDP existentes serão encerrados para os Basic e Standard Load Balancers.


## <a name="probesource"></a>Endereço IP de origem da investigação

Todas as investigações de integridade do Load Balancer originam-se do endereço IP 168.63.129.16 como sua fonte.  Quando você traz seus próprios endereços IP para Rede Virtual do Microsoft Azure, esse endereço IP de origem da investigação de integridade é garantido que seja exclusivo porque está reservado globalmente para a Microsoft.  Esse endereço é o mesmo em todas as regiões e não é alterado. Isso não deve ser considerado um risco de segurança, pois somente a plataforma interna do Azure pode originar um pacote a partir desse endereço IP. 

Para que a investigação de integridade do Load Balancer marque a instância como operante, você **deve** permitir esse endereço IP em quaisquer [Grupos de Segurança](../virtual-network/security-overview.md) do Azure e políticas de firewall local.

Se você não permitir esse endereço IP nas políticas de firewall, a investigação de integridade falhará, pois ela não é capaz de acessar a instância.  Por sua vez, o Load Balancer marcará para a instância como inoperante devido à falha da investigação de integridade.  Isso pode causar a falha do serviço com balanceamento de carga. 

Você também não deve configurar a VNet com o intervalo de endereços IP de propriedade da Microsoft que contém 168.63.129.16.  Isso coincidirá com o endereço IP da investigação de integridade.

Se você tiver várias interfaces na VM, será necessário certificar-se de responder à investigação na interface em que a investigação foi recebida.  Isso pode exigir prover exclusivamente NAT de origem nesse endereço na VM em um regime por interface.

## <a name="monitoring"></a>Monitoramento

Todos os [Standard Load Balancer](load-balancer-standard-overview.md) expõem o status da investigação de integridade como métricas multidimensionais por instância através do Azure Monitor.

O Basic Load Balancer expõe o status da investigação de integridade por pool de back-end através do Log Analytics.  Isso só está disponível para Basic Load Balancers públicos e não está disponível para Basic Load Balancers internos.  É possível usar a [log analytics](load-balancer-monitor-log.md) para verificar o status de integridade da investigação do balanceador de carga público e a contagem de investigação. O registro em log pode ser usado com o Power BI ou com o Azure Operational Insights para fornecer estatísticas sobre o status da integridade do balanceador de carga.


## <a name="limitations"></a>Limitações

-  A investigação de integridade HTTP não dá suporte a TLS (HTTPS).  Em vez disso, use uma investigação TCP na porta 443.

## <a name="next-steps"></a>Próximas etapas

- [Introdução à criação de um balanceador de carga público no Gerenciador de Recursos usando PowerShell](load-balancer-get-started-internet-arm-ps.md)
- [API REST para investigações de integridade](https://docs.microsoft.com/en-us/rest/api/load-balancer/loadbalancerprobes/get)

