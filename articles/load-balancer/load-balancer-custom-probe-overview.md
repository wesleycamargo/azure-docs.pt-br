---
title: "Usar investigações personalizadas do Load Balancer para monitorar o status da integridade | Microsoft Docs"
description: "Saiba como usar investigações personalizadas para o Balanceador de Carga do Azure a fim de monitorar instâncias por trás de um Balanceador de Carga"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 266132d8cbb6f9922ce7b49759981132c2c17f47
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="understand-load-balancer-probes"></a>Compreender as investigações do Load Balancer

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

O Balanceador de Carga do Azure oferece a capacidade de monitorar a integridade das instâncias do servidor usando investigações. Quando uma investigação não responde, o Balanceador de Carga interrompe o envio de novas conexões para as instâncias não íntegras. As conexões existentes não são afetadas e novas conexões são enviadas para instâncias íntegras.

Funções do serviço de nuvem (funções de trabalho e da Web) usam um agente convidado para o monitoramento de investigação. As investigações personalizadas TCP ou HTTP devem ser configuradas quando você usa VMs atrás do Load Balancer.

## <a name="understand-probe-count-and-timeout"></a>Noções básicas sobre contagem da investigação e tempo limite

O comportamento de investigação depende do seguinte:

* Do número de investigações bem-sucedidas que permitem a uma instância ser rotulada como em execução.
* Do número de investigações com falha que fazem com que uma instância seja rotulada como inoperante.

Os valores de tempo limite e frequência definidos em SuccessFailCount determinam se uma instância foi confirmada como em execução ou não. Para o Portal do Azure, o tempo limite é definido como duas vezes o valor da frequência.

A configuração da investigação de todas as instâncias com balanceamento de carga para um ponto de extremidade (ou seja, um conjunto com balanceamento de carga) deve ser a mesma. Você não pode ter uma configuração de investigação diferente para cada instância de função ou VM no mesmo serviço hospedado para uma combinação de ponto de extremidade específica. Por exemplo, cada instância deve ter portas locais e tempos limite idênticos.

> [!IMPORTANT]
> A investigação de um balanceador de carga usa o endereço IP 168.63.129.16. Esse endereço IP público facilita a comunicação com recursos internos de plataforma para o cenário de Rede Virtual do Azure com seu próprio IP. O endereço IP público virtual 168.63.129.16 é usado em todas as regiões e não muda. Recomendamos que você permita esse endereço IP em todas as políticas de firewall local. Ele não deve ser considerado um risco de segurança, pois somente a plataforma interna do Azure pode originar uma mensagem a partir dele. Se você não permitir esse endereço IP nas políticas de firewall, um comportamento inesperado ocorrerá em uma grande variedade de cenários. O comportamento inclui a configuração do mesmo intervalo de endereços IP de 168.63.129.16 e a duplicação de endereços IP.

## <a name="learn-about-the-types-of-probes"></a>Saiba mais sobre os tipos de investigações

### <a name="guest-agent-probe"></a>Investigação do agente convidado

Uma investigação de agente convidado só está disponível para os Serviços de Nuvem do Azure. O Load Balancer utiliza o agente convidado dentro da VM. Em seguida, ele escuta e responde com uma resposta HTTP 200 OK somente quando a instância está no estado Pronto. (Outros estados são Ocupado, Reciclando ou Parando.)

Para obter mais informações, consulte [Configurar o arquivo de definição de serviço (csdef) para investigações de integridade](https://msdn.microsoft.com/library/azure/ee758710.aspx) ou [Introdução à criação de um balanceador de carga público para serviços de nuvem](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>O que faz uma investigação de agente convidado marcar uma instância como não íntegra?

Se o agente convidado não responder com HTTP 200 OK, o balanceador de carga marcará a instância como sem resposta. Em seguida, ele para de enviar tráfego para essa instância. O balanceador de carga continuará executando o ping nessa instância. Se o agente convidado responder com um HTTP 200, o Balanceador de Carga enviará o tráfego para essa instância novamente.

Quando você usa uma função Web, o código do site geralmente é executado em w3wp.exe, que não é monitorado nem pela malha do Azure nem pelo agente convidado. Falhas em w3wp.exe (por exemplo, respostas HTTP 500) não são relatadas para o agente convidado. Consequentemente, o balanceador de carga não perde essa instância.

### <a name="http-custom-probe"></a>Investigação personalizada do HTTP

A investigação personalizada HTTP substitui a investigação de agente convidado padrão. Você pode criar a própria lógica personalizada para determinar a integridade da instância da função. Por padrão, o balanceador de carga investiga seu ponto de extremidade a cada 15 segundos. A instância será considerada em rotação do balanceador de carga se responder com um HTTP 200 dentro do período de tempo limite. O período de tempo limite é 31 segundos por padrão.

Uma investigação personalizada HTTP poderá ser útil ser você quiser implementar a própria lógica para remover instâncias da rotação do balanceador de carga. Por exemplo, convém optar por remover uma instância caso ela esteja usando mais de 90% da CPU e retorne um status diferente de 200. Se tiver funções web que usem w3wp.exe, você também receberá o monitoramento automático do site. Falhas no código do site retornam um status não 200 para a investigação do balanceador de carga.

> [!NOTE]
> A investigação personalizada HTTP só oferece suporte aos caminhos relativos e ao protocolo HTTP. HTTPS não é compatível.

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>O que faz uma investigação personalizada HTTP marcar uma instância como não íntegra?

* O aplicativo HTTP retorna um código de resposta HTTP diferente de 200 (por exemplo, 403, 404 ou 500). Essa é uma confirmação positiva alerta você para tirar a instância do aplicativo de serviço imediatamente.
* O servidor HTTP não responderá depois do período de tempo limite. Dependendo do valor de tempo limite definido, várias solicitações de investigação podem ficar sem resposta antes de a investigação ser marcada como não estando em execução (ou seja, antes das investigações SuccessFailCount serem enviadas).
* O servidor fecha a conexão por meio de uma redefinição TCP.

### <a name="tcp-custom-probe"></a>Investigação personalizada do TCP

As investigações personalizadas TCP iniciam uma conexão executando um handshake de três vias com a porta definida.

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>O que faz uma investigação personalizada TCP marcar uma instância como não íntegra?

* O servidor TCP não responderá depois do período de tempo limite. A marcação da investigação como não estando em execução depende da configuração da quantidade de solicitações de investigação sem resposta.
* A investigação recebe uma redefinição de TCP da instância de função.

Para obter mais informações sobre como configurar uma investigação de integridade HTTP ou uma investigação TCP, consulte [Introdução à criação de um balanceador de carga no Gerenciador de Recursos usando o PowerShell](load-balancer-get-started-internet-arm-ps.md).

## <a name="add-healthy-instances-back-into-the-load-balancer-rotation"></a>Readicionar instâncias íntegras à rotação do balanceador de carga

As investigações de TCP e HTTP são consideradas íntegras e marcam a instância de função como íntegra quando:

* O balanceador de carga obtém uma investigação positiva na primeira vez em que a VM é iniciada.
* O número SuccessFailCount (descrito acima) define o valor de investigações bem-sucedidas necessárias para marcar a instância da função como íntegra. Se uma instância de função tiver sido removida, o número de investigações bem-sucedidas e sucessivas deverá ser igual ou exceder o valor de SuccessFailCount a fim de marcar a instância de função como em execução.

> [!NOTE]
> Se a integridade de uma instância de função for flutuante, o balanceador de carga aguardará até recolocar a instância de função no estado íntegro. Esse tempo de espera extra protege o usuário e a infraestrutura, e é uma política intencional.

## <a name="use-log-analytics-for-a-load-balancer"></a>Usar análise de log para um balanceador de carga

Você pode usar a [análise de log](load-balancer-monitor-log.md) para verificar o status de integridade da investigação do balanceador de carga e a contagem da investigações. O registro em log pode ser usado com o Power BI ou com o Azure Operational Insights para fornecer estatísticas sobre o status da integridade do balanceador de carga.
