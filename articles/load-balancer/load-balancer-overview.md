---
title: Visão geral do Azure Load Balancer | Microsoft Docs
description: Visão geral dos recursos do Balanceador de Carga do Azure, arquitetura e implementação. Saiba como o balanceador de carga funciona e como aproveitá-lo na nuvem.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: kumud
ms.openlocfilehash: 3a5d1e897d8ffe063ecf9277bef346c8b7c5092b
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-load-balancer-overview"></a>Visão geral do Balanceador de Carga do Azure

O Azure Load Balancer permite dimensionar seus aplicativos e criar alta disponibilidade para seus serviços. O Azure Load Balancer é compatível tanto com cenários de entrada como de saída e fornece latência baixa, taxa de transferência alta e escala verticalmente a milhões de fluxos para todos os aplicativos TCP e UDP.   

O Azure Load Balancer distribuirá novos fluxos de entrada que chegam no front-end do Azure Load Balancer para instâncias de pool de back-end de acordo com as regras e investigações de integridade.  

Além disso, um Azure Load Balancer público também pode fornecer conexões de saída para máquinas virtuais dentro de sua rede virtual convertendo seus endereços IP privados para endereços IP públicos.

O Azure Load Balancer está disponível em dois SKUs diferetes: Basic e Standard.  Há diferenças em dimensionamento, recursos e preço.  Qualquer cenário possível com o Load Balancer Basic também pode ser criado com o Azure Load Balancer Standard, embora a abordagem possa ser um pouco diferente.  Como você sabe, é importante familiarizar-se com os fundamentos e diferenças específicas de SKU do Load Balancer.

## <a name="why-use-load-balancer"></a>Por que usar o Load Balancer? 

O Azure Load Balancer pode ser usado para:

* Balancear carga de tráfego de entrada na Internet para máquinas virtuais. Essa configuração é conhecida como [Load Balancer público](#publicloadbalancer).
* Tráfego de balanço de carga entre as máquinas virtuais dentro de uma rede virtual. Você também pode acessar um front-end do Azure Load Balancer na rede local em um cenário híbrido. Ambos os cenários usam uma configuração que é conhecida como um [Load Balancer interno](#internalloadbalancer).
* Tráfego de encaminhamento de porta para uma porta específica em máquinas vrituais específicas com regras NAT de entrada.
* Forneça [conectividade de saída](load-balancer-outbound-connections.md) para máquinas virtuais dentro de sua rede virtual usando um Load Balancer público.


>[!NOTE]
> O Azure fornece um conjunto de soluções de balanceamento de cargo para os seus cenários.  Se você estiver procurando por encerramento de TLS ("descarregamento de SSL") ou por processamento de camada de aplicativo de solicitação HTTP/HTTPS, revise [Gateway de Aplicativo](../application-gateway/application-gateway-introduction.md).  Se você estiver procurando por balanceamento de carga DNS global, veja [Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md).  Os cenários de ponta a ponta podem se beneficiar da combinação dessas soluções, conforme necessário.

## <a name="what-is-load-balancer"></a>O que é o Balanceador de Carga?

Um recurso do Azure Load Balancer pode existir como Load Balancer público ou Load Balancer interno. As funções de um recurso do Load Balancer são expressas como um front-end, uma regra, uma investigação de integridade e uma definição de pool de back-end.  As máquinas virtuais são colocadas no pool de back-end, especificando o pool de back-end da máquina virtual.

Os recursos do Load Balancer são objetos nos quais é possível expressar como o Azure deve programar sua infraestrutura multilocatária para alcançar o cenário que você deseja criar.  Não há relação direta entre as capacidades do Load Balancer e a infraestrutura real; a criação de um Azure Load Balancer não cria uma instância e o recurso está sempre disponível. 

## <a name="fundamental-load-balancer-features"></a>Recursos do Azure Load Balancer fundamental

O Azure Load Balancer fornece os seguintes recursos fundamentais para aplicativos de TCP e UDP:

* **Balanceamento de carga**

    O Azure Load Balancer permite que você crie uma regra para distribuir o tráfego que chega em um front-end para instâncias do pool de back-end de balanceamento de carga.  Ele usa um algoritmo de hash para distribuição de fluxos de entrada e reconfigura os cabeçalhos de fluxos para instâncias do pool de back-end adequadamente. Um servidor está disponível para receber novos fluxos quando o teste de integridade indicar um ponto de extremidade de back-end íntegro.
    
    Por padrão, ele usa um hash de 5 tuplas composto por endereço IP de origem, porta de origem, IP de destino, porta de destino e tipo de protocolo para mapear o tráfego até os servidores disponíveis.  Você pode optar por criar afinidade a um endereço IP de origem específico optando por hash de 2 ou 3 tuplas para uma determinada regra.  Todos os pacotes do mesmo fluxo de pacote chegam na mesma instância por trás de front-end com balanceamento de carga.  Quando o cliente inicia um novo fluxo do mesmo IP de origem, a porta de origem muda. As 5 tuplas resultantes podem fazer com que o tráfego vá para um ponto de extremidade de back-end diferente como resultado.

    Para obter mais detalhes, consulte [Modo de distribuição do Azure Load Balancer](load-balancer-distribution-mode.md). O gráfico a seguir mostra a distribuição baseada em hash:

    ![Distribuição baseada em hash](./media/load-balancer-overview/load-balancer-distribution.png)

    *Figura – distribuição baseada em hash*

* **Encaminhamento de porta**

    Azure Load Balancer permite que você crie uma regra NAT de entrada para o tráfego de encaminhamento de porta de uma porta específica de um endereço IP de front-end específico para uma porta específica de uma instância de back-end específica dentro da Rede Virtual. Isso é também é realizado pela mesma distribuição baseada em hash mesmo como o balanceamento de carga.  Cenários comuns para esse recurso são as sessões Desktop Protocol (RDP) ou Secure Shell (SSH) para instâncias de máquina virtual dentro da Rede Virtual.  Você pode mapear vários pontos de extremidade internos nas portas diferentes no mesmo endereço IP de front-end. Você pode usá-las para administrar remotamente suas máquinas virtuais pela internet, sem a necessidade de uma caixa de salto adicional.

* **Aplicativo independente e transparente**

    O zure Load Balancer não interage diretamente com o TCP ou UDP ou com a camada de aplicativo e qualquer TCP ou o cenário de aplicativo com base em UDP pode ter suporte.  Por exemplo, enquanto o Azure Load Balancer não encerra o TLS em si, você pode criar e dimensionar aplicativos TLS usando um Azure Load Balancer e encerrar a conexão TLS na própria máquina virtual. O zure Load Balancer não encerra um fluxo e os handshakes de protocolo estão sempre diretamente entre o cliente e a instância de pool de back-end selecionada de hash. Por exemplo, um handshake TCP está sempre entre o cliente e a máquina virtual de back-end selecionada.  E uma resposta a uma solicitação de um front-end é uma resposta gerada a partir da máquina virtual de back-end.  O desempenho de rede de saída do Azure Load Balancer é limitado somente pelo SKU da máquina virtual que você escolher e os fluxos permanecerão ativos por longos períodos de tempo se o tempo limite de ociosidade nunca for alcançado.

* **Reconfiguração automática**

    O Balanceador de Carga do Azure reconfigura-se instantaneamente quando você aumenta ou diminui as instâncias. Adicionar ou remover máquinas virtuais do pool de back-end reconfigura o Azure Load Balancer sem operações adicionais no recurso de Azure Load Balancer.

* **Investigações de integridade**

    O Azure Load Balancer usa as investigações de integridade definidas para determinar a integridade das instâncias no pool de back-end. Quando uma investigação não responde, o balanceador de carga interrompe o envio de novas conexões para as instâncias não íntegras. As conexões existentes não são afetadas e continuarão até que o aplicativo encerre o fluxo, ocorra um tempo limite de ociosidade, ou a máquina virtual seja desligada.

    Existem três tipos de investigação com suporte:

    - **Investigação personalizada de HTTP:** você pode usá-la para criar sua própria lógica personalizada para determinar a integridade da instância de pool de back-end. O balanceador de carga investigará regularmente seu ponto de extremidade (a cada 15 segundos, por padrão). A instância é considerada íntegra se responder com um HTTP 200 dentro do período de tempo limite (padrão de 31 segundos). Qualquer status diferente de HTTP 200 faz com que esse teste falhe.  Isso é útil para implementar sua própria lógica para remover as instâncias d rotação do Azure Load Balancer. Por exemplo, você pode configurar a instância para retornar um status não 200 se a instância estiver acima de 90% da CPU.   Essa investigação substitui a investigação de agente convidado padrão.

    - **Investigação TCP personalizada:** essa investigação depende do estabelecimento bem-sucedido da sessão de TCP em uma porta de investigação definida.  Como o ouvinte especificado na máquina virtual existe, esse teste será bem-sucedido. Se a conexão for recusada, o teste falhará. Essa investigação substitui a investigação de agente convidado padrão.

    - **Investigação de agente convidado (somente em máquinas virtuais de plataforma como serviço):** o Azure Load Balancer também pode utilizar o agente convidado dentro da máquina virtual. O agente convidado escuta e responde com uma resposta HTTP 200 OK somente quando a instância está no estado Pronto. Se o agente não responder com HTTP 200 OK, o balanceador de carga marcará a instância como sem resposta e interromperá o envio de tráfego para essa instância. O Azure Load Balancer continuará tentando alcançar a instância. Se o agente convidado responder com um HTTP 200, o balanceador de carga enviará o tráfego para essa instância novamente.  As investigações do agente convidado são um último recurso e não devem ser usadas quando as configurações de investigação personalizada de HTTP ou TCP forem possíveis. 
    
* **Conexões de saída (NAT de origem)**

    Todos os fluxos de saída de endereços IP privados dentro da sua Rede Virtual para os endereços IP públicos na Internet podem ser convertidos para um endereço IP de front-end do Azure Load Balancer. Quando um front-end público é vinculado a uma máquina virtual de back-end por meio de uma regra de balanceamento de carga, as conexões de saída dos programas do Azure são traduzidas automaticamente para o endereço IP de front-end. Isso também é chamado de NAT de origem (SNAT). A SNAT fornece benefícios importantes:

    + Permite uma fácil atualização e recuperação de desastres dos serviços, já que o front-end pode ser mapeado dinamicamente para outra instância do serviço.
    + Facilita o gerenciamento de ACL (lista de controle de acesso). ACLs expressadas em termos de front-end não mudam à medida que os serviços são escalados verticalmente ou reimplantados.

    Consulte o artigo [conexões de saída](load-balancer-outbound-connections.md) para uma discussão detalhada sobre esse recurso.

O Load Balancer Standard tem capacidades de específico de SKU adicional, além esses conceitos básicos.  Revise o restante deste artigo para obter detalhes.

## <a name="skus"></a> Comparação de SKU do Azure Load Balancer

O Azure Load Balancer oferece suporte a dois SKUs diferentes: Basic e Standard.  Há diferenças no cenário de dimensionamento, recursos e preço.  Qualquer cenário possível com o Azure Load Balancer Basic também pode ser criado com o Load Balancer Standard.  Na verdade, as APIs para ambos os SKUs são semelhantes e invocadas através da especificação de um SKU.  A API para dar suporte a SKUs para o Azure Load Balancer e o IP público está disponível começando com a API de 2017-08-01.  Ambos os SKUs têm a mesma API geral e estrutura.

No entanto, dependendo de qual SKU for escolhido, os detalhes de configuração do cenário completo podem ser um pouco diferentes. A documentação do Azure Load Balancer chama quando um artigo é aplicável a um SKU específico. Examine a tabela a seguir para comparar e entender as diferenças.  Revisão da [Visão Geral Padrão do Azure Load Balancer](load-balancer-standard-overview.md) para obter mais detalhes.

>[!NOTE]
> Novos designs devem considerar usar o Load Balancer Standard. 

Máquinas virtuais autônomas, conjuntos de disponibilidade e conjuntos de escala de máquina virtual podem estar conectados apenas a um SKU, nunca ambos. Quando usado com endereços IP públicos, o Azure Load Balancer e o endereço IP público SKU deve corresponder. O Azure Load Balancer e os SKUs do IP público não são mutáveis.

_É uma prática recomendada especificar os SKUs explicitamente, mesmo que ainda não seja obrigatório._  Neste momento, as alterações necessárias estão sendo mantidas em um mínimo. Se um SKU não for especificado, será interpretado como a intenção de usar SKUS Basic na versão da API de 2017-08-01.

>[!IMPORTANT]
>O Azure Load Balancer Standard é um novo produto de Azure Load Balancere amplamente um superconjunto Load Balancer Basic.  Há diferenças importantes e deliberadas entre os dois produtos.  Qualquer cenário de ponta a ponta possível com o Azure Load Balancer Basic também pode ser criado com o Load Balancer Standard.  Se você já usava o Azure Load Balancer Basic, você deve se familiarizar com o Load Balancer Standard para entender as últimas alterações no comportamento entre Standard e Basic e seu impacto. Revise esta seção com cuidado.

| | [SKU padrão](load-balancer-standard-overview.md) | SKU Básico |
| --- | --- | --- |
| Tamanho do pool de back-end | até 1000 instâncias | até 100 instâncias |
| Pontos de extremidade de pool de back-end | qualquer máquina virtual em uma rede virtual única, incluindo a mistura de máquinas virtuais, conjuntos de disponibilidade, conjuntos de dimensionamento de máquina virtual. | máquinas virtuais em um conjunto de disponibilidade ou conjunto de dimensionamento da máquina virtual |
| Zonas de Disponibilidades | front-ends com redundância de zona e por zona para entrada e saída, o mapeamento de fluxos de saída sobrevivem à falha de zona, balanceamento de carga entre zonas | / |
| Diagnostics | Azure Monitor, métricas multidimensionais incluindo contadores byte e pacote, status de investigação de integridade, tentativas de conexão (TCP SYN), integridade de conexão de saída (SNAT bem sucedido e fluxos com falha), medidas de plano de dados ativo | Log Analytics do Azure apenas para o Azure Load Balancer público, alerta de exaustão SNAT, conta de integridade de pool back-end |
| Portas de alta disponibilidade | Azure Load Balancer interno | / |
| Segurança por padrão | padrão fechado para IP público e pontos de extremidade de Azure Load Balancer e um grupo de segurança de rede deve ser usado para indicar explicitamente a permissão para o tráfego fluir | padrão aberto, grupo de segurança de rede opcional |
| Conexões de saída | Vários front-ends com regra recusar. Um cenário de saída _deve_ ser explicitamente criado para a máquina virtual poder usar a conectividade de saída.  [Pontos de Extremidade de Serviço de VNet](../virtual-network/virtual-network-service-endpoints-overview.md) podem ser acessados sem conectividade de saída e não são considerados dados processados.  Quaisquer endereços de IP públicos, incluindo serviços de PaaS do Azure não estão disponíveis como pontos de extremidade de VNet, devem ser acessados por meio de conectividade de saída e contam para dados processados. Quando apenas um Azure Load Balancer interno estiver atendendo uma máquina virtual, as conexões de saída via SNAT padrão não estão disponíveis. A programação de saída SNAT é o protocolo de transporte específicos com base no protocolo da regra de balanceamento de carga de entrada. | Único front-end, selecionado aleatoriamente quando vários front-ends estiverem presentes.  Quando apenas o Azure Load Balancer interno estiver atendendo a uma máquina virtual, o padrão SNAT é usado. |
| Vários front-ends | Entrada e saída | Somente entrada |
| Operações de Gerenciamento | Maioria das operações < 30 segundos | 60-90+ segundos típicos |
| Contrato de Nível de Serviço | 99,99% para o caminho de dados com duas máquinas virtuais íntegras | Implícito no SLA de VM | 
| Preços | Cobrado com base no número de regras, dados processados de entrada ou saída associados ao recurso  | Sem encargos |

Revisão [dos limites de serviço para o Azure Load Balancer](https://aka.ms/lblimits).  Para o Azure Load Balancer Standard também analise uma [visão geral](load-balancer-standard-overview.md), [preços](https://aka.ms/lbpricing) e [SLA](https://aka.ms/lbsla) mais detalhados.

## <a name="concepts"></a>Conceitos

### <a name = "publicloadbalancer"></a>Azure Load Balancer Público

O Azure Load Balancer Público mapeia o endereço IP público e o número da porta do tráfego de entrada até o endereço IP privado e o número da porta da máquina virtual e vice-versa no tráfego de resposta da máquina virtual. As regras de balanceamento de carga permitem que você distribua tipos específicos de tráfego entre várias máquinas virtuais ou serviços. Por exemplo, você pode difundir a carga de tráfego de solicitação da web em vários servidores web.

A figura a seguir mostra um ponto de extremidade de balanceamento de carga para tráfego na Web compartilhado entre três máquinas virtuais para a porta TCP pública e privada de número 80. Essas três máquinas virtuais estão em um conjunto de balanceamento de carga.

![exemplo de balanceador de carga público](./media/load-balancer-overview/IC727496.png)

**Figura 1: tráfego web de balanceamento de carga usando um Azure Load Balancer público**

Quando os clientes da Internet enviam solicitações de página da Web para o endereço IP público do aplicativo web na porta TCP 80, o Azure Load Balancer distribui as solicitações entre as três máquinas virtuais no conjunto de balanceamento de carga. Para obter mais informações sobre os algoritmos do balanceador de carga, consulte a [load balancer overview page (página de visão geral do balanceador de carga)](load-balancer-overview.md#load-balancer-features).

Por padrão, Azure Load Balancer distribui o tráfego de rede igualmente entre várias instâncias da máquina virtual. Você também pode configurar a afinidade de sessão. Para obter mais detalhes, consulte [modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a>Azure Load Balancer Interno

O Balanceador de Carga interno apenas direciona o tráfego a recursos que estão dentro de uma rede virtual ou que usam uma VPN para acessar a infraestrutura do Azure. Nesse sentido, o Azure Load Balancer Interno difere de um Azure Load Balancer público. A infraestrutura do Azure restringe o acesso aos endereços IP de front-end de carga balanceada de uma rede virtual. Os endereços de front-ed e as redes virtuais nunca são expostos diretamente a um ponto de extremidade de Internet. Os aplicativos de linha de negócios interno são executados no Azure e acessados de dentro do Azure ou de recursos locais.

O Azure Load Balancer interno habilita os seguintes tipos de balanceamento de carga:

* Dentro de uma rede virtual: balanceamento de carga de VMs na rede virtual para um conjunto de VMs que residem dentro da mesma rede virtual.
* Para uma rede virtual entre instalações: balanceamento de carga de computadores locais para um conjunto de VMs que residem dentro da mesma rede virtual. 
* Para aplicativos de várias camadas: balanceamento de carga para aplicativos de várias camadas para a Internet em que as camadas de back-end não são para a Internet. As camadas de back-end requerem balanceamento de carga de tráfego da camada para a internet (veja a figura 2).
* Para aplicativos de linha de negócios: balanceamento de carga para aplicativos de linha de negócios hospedados no Azure sem hardware ou software de balanceador de carga adicional. Esse cenário inclui servidores locais que estão no conjunto de computadores cujo tráfego tem balanceamento de carga.

![exemplo do Azure Load Balancer Interno](./media/load-balancer-overview/IC744147.png)

**Figura 2 – aplicativos de várias camadas do balanceamento de carga usando Azure Load Balancer interno e público**

## <a name="pricing"></a>Preços
O Load Balancer Standard é um produto cobrado com base no número de regras de balanceamento de carga configurados e todos os dados de entrada e saída processados. Para obter informações de preço do Load Balancer Standard, visite a página [Preços do Balanceador de carga](https://azure.microsoft.com/pricing/details/load-balancer/).

O Azure Load Balancer Basic é oferecido gratuitamente.

## <a name="sla"></a>Contrato de Nível de Serviço

Para obter informações sobre o SLA do Azure Load Balancer Standard, visite a página do [SLA do Azure Load Balancer](https://aka.ms/lbsla). 

## <a name="next-steps"></a>Próximas etapas

- Revisão [Azure Load Balancer Standard em mais detalhes](load-balancer-standard-overview.md)
- Saiba mais sobre usar o [Load Balancer Standard e Zonas de Disponibilidade](load-balancer-standard-availability-zones.md)
- Saiba mais sobre como usar o [BAzure Load Balancer para conexões de saída](load-balancer-outbound-connections.md)
- Saiba mais sobre as [Portas de Alta Disponibilidade do Azure Load Balancer](load-balancer-ha-ports-overview.md)
- Saiba mais sobre como usar o [Azure Load Balancer com vários Front-ends](load-balancer-multivip-overview.md)
- Saiba mais sobre [Pontos de Extremidade de Serviço de VNET](../virtual-network/virtual-network-service-endpoints-overview.md)
- Saiba como criar um [Azure Load Balancer público básico](load-balancer-get-started-internet-portal.md)

