---
title: "Entendendo as conexões de saída no Azure | Microsoft Docs"
description: "Este artigo explica como o Azure permite que as VMs se comuniquem com serviços de Internet públicos."
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
ms.assetid: 5f666f2a-3a63-405a-abcd-b2e34d40e001
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: b8e225ba4374c73dbabac3dddab9ba37fa798a5a
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2018
---
# <a name="understanding-outbound-connections-in-azure"></a>Entendendo as conexões de saída no Azure

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Uma VM (Máquina Virtual) no Azure pode se comunicar com pontos de extremidade fora do Azure no espaço de endereço IP público. Quando uma VM inicia um fluxo de saída para um destino no espaço de endereço IP público, o Azure mapeia um endereço IP privado da VM para um endereço IP público e permite que o tráfego de retorno chegue à VM.

O Azure fornece três métodos diferentes para obter conectividade de saída. Cada método tem seus próprios recursos e restrições. Analise cada método cuidadosamente para escolher qual deles atende às suas necessidades.

| Cenário | Método | Observação |
| --- | --- | --- |
| VM autônoma (nenhum balanceador de carga, nenhum endereço IP público em nível de instância) |SNAT padrão |O Azure associa um endereço IP público para o SNAT |
| VM com balanceamento de carga (nenhum endereço IP público em nível de instância na VM) |SNAT usando o balanceador de carga |O Azure usa um endereço IP público do balanceador de carga para o SNAT |
| VM com endereço IP público em nível de instância (com ou sem o balanceador de carga) |SNAT não é usado |O Azure usa o IP público atribuído à VM |

Se não quiser que uma VM se comunique com pontos de extremidade fora do Azure no espaço de endereço IP público, você pode usar NSG (Grupos de Segurança de Rede) para bloquear o acesso. Usar NSGs é abordado em mais detalhes em [Impedindo a conectividade pública](#preventing-public-connectivity).

## <a name="standalone-vm-with-no-instance-level-public-ip-address"></a>VM autônoma sem endereço IP público em nível de instância

Nesse cenário, a VM não faz parte de um pool do Azure Load Balancer e não tem um endereço ILPIP (IP Público em Nível de Instância) atribuído a ele. Quando a VM cria um fluxo de saída, o Azure converte o endereço IP de origem particular do fluxo de saída para um endereço IP de origem pública. O endereço IP público usado para esse fluxo de saída não é configurável e não conta para o limite de recursos IP públicos da assinatura. O Azure usa SNAT (Conversão de Endereço de Rede de Origem) para executar essa função. Portas efêmeras do endereço IP público são usadas para distinguir os fluxos individuais originados pela VM. O SNAT aloca dinamicamente portas efêmeras quando os fluxos são criados. Nesse contexto, as portas efêmeras usadas para o SNAT são chamadas de portas SNAT.

As portas SNAT são um recurso finito que pode ser esgotado. É importante entender como elas são consumidas. Uma porta SNAT é consumida por fluxo para um IP de destino único. Para vários fluxos para a mesma porta e endereço IP de destino, cada fluxo consome uma única porta SNAT. Isso garante que os fluxos sejam exclusivos quando originados do mesmo endereço IP público para a mesma porta e endereço IP de destino. Vários fluxos, cada um para uma porta e endereço IP de destino diferente, compartilham uma única porta SNAT. A porta e o endereço IP de destino tornam os fluxos exclusivos.

Você pode usar [Log Analytics para o Azure Load Balancer](load-balancer-monitor-log.md) e [Logs de evento de alerta para monitorar as mensagens de esgotamento da porta SNAT](load-balancer-monitor-log.md#alert-event-log) para monitorar a integridade das conexões de saída. Quando os recursos da porta SNAT acabam, os fluxos de saída falham até as portas SNAT serem lançadas por fluxos existentes. O Balanceador de Carga usa um tempo limite de ociosidade de 4 minutos para recuperar portas SNAT.  Examine [VM com um endereço IP público de nível de instância (com ou sem o Azure Load Balancer)](#vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer) a seguinte seção, bem como [Gerenciamento de esgotamento de SNAT](#snatexhaust).

## <a name="load-balanced-vm-with-no-instance-level-public-ip-address"></a>VM com balanceamento de carga com nenhum endereço IP público em nível de instância

Nesse cenário, a VM faz parte de um pool do Azure Load Balancer.  A VM não tem um endereço IP público atribuído a ela. O recurso do Load Balancer deve ser configurado com uma regra de balanceador de carga para criar um link entre o front-end de IP público e o pool de back-end. Se você não concluir esta configuração, o comportamento será como descrito na seção anterior para [VM Autônoma sem IP Público de Nível de Instância](load-balancer-outbound-connections.md#standalone-vm-with-no-instance-level-public-ip-address).

Quando a VM com balanceamento de carga cria um fluxo de saída, o Azure converte o endereço IP de origem particular do fluxo de saída para um endereço IP público do frontend do Balanceador de Carga público. O Azure usa SNAT (Conversão de Endereço de Rede de Origem) para executar essa função. Portas efêmeras do endereço IP público do Balanceador de Carga são usadas para distinguir os fluxos individuais originados pela VM. O SNAT aloca dinamicamente portas efêmeras quando os fluxos de saída são criados. Nesse contexto, as portas efêmeras usadas para o SNAT são chamadas de portas SNAT.

As portas SNAT são um recurso finito que pode ser esgotado. É importante entender como elas são consumidas. Uma porta SNAT é consumida por fluxo para uma porta e endereço IP de destino único. Para vários fluxos para a mesma porta e endereço IP de destino, cada fluxo consome uma única porta SNAT. Isso garante que os fluxos sejam exclusivos quando originados do mesmo endereço IP público para a mesma porta e endereço IP de destino. Vários fluxos, cada um para uma porta e endereço IP de destino diferente, compartilham uma única porta SNAT. A porta e o endereço IP de destino tornam os fluxos exclusivos.

Você pode usar [Log Analytics para o Azure Load Balancer](load-balancer-monitor-log.md) e [Logs de evento de alerta para monitorar as mensagens de esgotamento da porta SNAT](load-balancer-monitor-log.md#alert-event-log) para monitorar a integridade das conexões de saída. Quando os recursos da porta SNAT acabam, os fluxos de saída falham até as portas SNAT serem lançadas por fluxos existentes. O Balanceador de Carga usa um tempo limite de ociosidade de 4 minutos para recuperar portas SNAT.  Examine a seguinte seção, bem como [Gerenciamento de esgotamento de SNAT](#snatexhaust).

## <a name="vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer"></a>VM com endereço IP público em nível de instância (com ou sem o Balanceador de Carga)

Nesse cenário, a VM tem um ILPIP (IP Público em Nível de Instância) atribuído a ela. Não importa se a VM é ou não de balanceamento de carga. Quando é usado um ILPIP, o SNAT (Conversão do Endereço de Rede de Origem) não é usado. A VM usa o ILPIP para todos os fluxos de saída. Se o aplicativo iniciar vários fluxos de saída e for observado um esgotamento de SNAT, você deverá considerar atribuir um ILPIP para mitigar as limitações de SNAT.

## <a name="discovering-the-public-ip-used-by-a-given-vm"></a>Descobrindo o IP público usado por uma determinada VM

Há várias maneiras de determinar o endereço IP público de uma conexão de saída. O OpenDNS fornece um serviço que pode mostrar o endereço IP público de sua VM. Usando o comando nslookup, você pode enviar uma consulta DNS para o nome myip.opendns.com para o resolvedor do OpenDNS. O serviço retornará o endereço IP de origem que foi usado para enviar a consulta. Quando você executar a consulta a seguir de sua VM, a resposta será o IP público usado para essa VM.

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-public-connectivity"></a>Impedindo a conectividade pública

Às vezes, não é recomendável para uma VM poder criar um fluxo de saída ou pode haver um requisito para gerenciar quais destinos podem ser alcançados com fluxos de saída ou quais destinos podem iniciar fluxos de entrada. Nesse caso, você usa os [NSG (Grupos de Segurança de Rede)](../virtual-network/virtual-networks-nsg.md) para gerenciar os destinos que a VM pode acessar, além de qual destino público pode iniciar fluxos de entrada. Quando você aplica um NSG a uma VM com balanceamento de carga, é necessário prestar atenção nas [marcações padrão](../virtual-network/virtual-networks-nsg.md#default-tags) e nas [regras padrão](../virtual-network/virtual-networks-nsg.md#default-rules).

Certifique-se de que a VM possa receber solicitações de investigação de integridade do Azure Load Balancer. Se um NSG bloquear solicitações de investigação de integridade da marcação padrão AZURE_LOADBALANCER, o teste de integridade da VM falhará e a VM será reduzida. O Balanceador de Carga interrompe o envio de novos fluxos para a VM.

## <a name="snatexhaust"></a>Gerenciamento de esgotamento de SNAT

Portas efêmeras usadas para SNAT são um recurso limitado conforme descrito em [VM autônoma sem endereço IP público de nível de instância](#standalone-vm-with-no-instance-level-public-ip-address) e [VM com balanceamento de carga sem endereço IP público de nível de instância](#standalone-vm-with-no-instance-level-public-ip-address).

Se você souber que está iniciando muitas conexões UDP ou TCP de saída para a mesma porta e endereço IP de destino, observe as conexões de saída com falha ou, se for avisado pelo suporte de que as portas de SNAT estão se esgotando, você terá várias opções gerais de mitigação.  Examine essas opções e decida qual é a melhor para seu cenário.  É possível que uma ou mais possam ajudar a gerenciar esse cenário.

### <a name="modify-application-to-reuse-connections"></a>Modificar o aplicativo para reutilizar conexões 
Você pode reduzir a demanda por portas efêmeras usadas para SNAT, reutilizando as conexões em sua aplicação.  Isto é especialmente verdadeiro para protocolos como HTTP/1.1, onde a reutilização de conexão é a padrão.  E outros protocolos usando o HTTP como seu transporte (ou seja, REST) podem se beneficiar por sua vez.  A reutilização é sempre melhor que as conexões TCP individuais e atômicas para cada solicitação e resulta em transações TCP mais eficazes e muito eficientes.

### <a name="modify-application-to-use-connection-pooling"></a>Modificar o aplicativo para usar o pool de conexão
Você pode empregar um esquema de pooling de conexão em seu aplicativo, onde as solicitações são distribuídas internamente em um conjunto de conexões fixo (cada reutilização sempre que possível).  Isso restringe o número de portas efêmeras em uso e cria um ambiente mais previsível.  Isso também pode aumentar a taxa de transferência de solicitações, permitindo múltiplas operações simultâneas quando uma única conexão está bloqueando na resposta de uma operação.  O pooling de conexão já pode existir dentro da estrutura que você está utilizando para desenvolver seu aplicativo ou os conjuntos de configurações para o aplicativo.  Você pode combinar isso com a reutilização de conexão e suas múltiplas solicitações consomem um número fixo e previsível de portas para a mesma porta e endereço IP de destino, ao mesmo tempo em que se beneficia do uso muito eficiente das transações TCP, reduzindo a latência e a utilização de recursos.

### <a name="modify-application-to-use-less-aggressive-retry-logic"></a>Modificar o aplicativo para usar uma lógica de repetição menos agressiva
Quando as portas efêmeras utilizadas para SNAT estão esgotadas ou se ocorrem falhas no aplicativo, repetições de força bruta ou agressivas sem lógica de declínio e retirada fazem com que o esgotamento ocorra ou persista. Você pode reduzir a demanda de portas efêmeras usando uma lógica de repetição menos agressiva.   Portas efêmeras têm um tempo limite de ociosidade de 4 minutos (não ajustável) e, se as repetições forem muito agressivas, o esgotamento não terá a oportunidade de limpar por conta própria.  Portanto, considerando como e com que frequência seu aplicativo tenta transações é uma consideração crítica do design.

### <a name="assign-an-instance-level-public-ip-to-each-vm"></a>Atribua um IP público em nível de instância a cada VM
Isso altera seu cenário para [IP público em nível de instância para uma VM](#vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer).  Todas as portas efêmeras do IP público usado para cada VM estão disponíveis para a VM (diferentemente de cenários em que as portas efêmeras de um IP público são compartilhadas com todas as VMs associadas ao respectivo pool de back-end).  Há compromissos a considerar, tais como o custo adicional dos endereços IP e o potencial impacto da listagem de permissões de um grande número de endereços IP individuais.

## <a name="limitations"></a>Limitações

Se [vários endereços IP (públicos) estão associados com um balanceador de carga](load-balancer-multivip-overview.md), qualquer desses endereços IP públicos são um candidato para fluxos de saída.

O Azure usa um algoritmo para determinar o número de portas SNAT disponíveis com base no tamanho do pool.  Isso não é configurável no momento.

Conexões de saída têm um tempo limite de ociosidade de 4 minutos.  Isso não pode ser ajustado.

É importante lembrar que o número de portas SNAT disponíveis não se converte diretamente no número de conexões. Consulte as seções anteriores para informações sobre quando e como as portas SNAT são alocadas e [como gerenciar esse recurso esgotável](#snatexhaust).
