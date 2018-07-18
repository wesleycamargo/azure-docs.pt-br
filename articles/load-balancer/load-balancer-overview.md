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
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 080a4e670b06544d84e3d34a0b04bdb91a95aff1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202434"
---
# <a name="what-is-azure-load-balancer"></a>O que é o Azure Load Balancer?

Com o Azure Load Balancer, você pode dimensionar seus aplicativos e criar alta disponibilidade para seus serviços. O Load Balancer é compatível com cenários de entrada e saída, fornece baixa latência e alta taxa de produtividade e pode ser escalado verticalmente em milhões de fluxos para aplicativos TCP e UDP.  

O Load Balancer distribui novos fluxos de entrada que chegam ao front-end do balanceador de carga para instâncias de pool de back-end, de acordo com as regras e as investigações de integridade. 

Além disso, um balanceador de carga público pode fornecer conexões de saída para VMs (máquinas virtuais) dentro de sua rede virtual convertendo endereços IP privados em públicos.

O Azure Load Balancer está disponível em dois SKUs: Básico e Standard. Há diferenças em dimensionamento, recursos e preço. Qualquer cenário possível com o Load Balancer Básico também pode ser criado com o Load Balancer Standard, embora as abordagens possam ser um pouco diferentes. Como você sabe, é importante familiarizar-se com os fundamentos e diferenças específicas de SKU do Load Balancer.

## <a name="why-use-load-balancer"></a>Por que usar o Load Balancer? 

Use o Azure Load Balancer para:

* Balancear a carga do tráfego de Internet de entrada para suas VMs. Essa configuração é conhecida como [balanceador de carga público](#publicloadbalancer).
* Balancear a carga do tráfego entre VMs dentro de uma rede virtual. Também é possível acessar o front-end de um balanceador de carga de uma rede local em um cenário híbrido. Os dois cenários usam uma configuração conhecida como [balanceador de carga interno](#internalloadbalancer).
* Encaminhar o tráfego para uma porta específica em VMs específicas com regras de NAT (conversão de endereços de rede) de entrada.
* Fornecer [conectividade de saída](load-balancer-outbound-connections.md) para VMs dentro de sua rede virtual usando um balanceador de carga público.


>[!NOTE]
> O Azure fornece um conjunto de soluções de balanceamento de carga totalmente gerenciadas para seus cenários. Se estiver em busca de informações sobre o encerramento de protocolo TLS ("descarregamento de SSL") ou sobre o processamento de camada de aplicativo por solicitação HTTP/HTTPS, consulte [Gateway de Aplicativo](../application-gateway/application-gateway-introduction.md). Se você estiver procurando por balanceamento de carga DNS global, veja [Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md). Cenários de ponta a ponta podem se beneficiar da combinação dessas soluções conforme for necessário.

## <a name="what-are-load-balancer-resources"></a>O que são recursos de balanceador de carga?

Um recurso de balanceador de carga pode ser um balanceador de carga público ou interno. As funções de um recurso de balanceador de carga são expressas como um front-end, uma regra, uma investigação de integridade e uma definição de pool de back-end. Você coloca VMs no pool de back-end especificando o pool de back-end da VM.

Recursos de balanceador de carga são objetos nos quais você pode expressar como o Azure deve programar sua infraestrutura de multilocatário para alcançar o cenário que deseja criar. Não há relação direta entre os recursos do balanceador de carga e a infraestrutura real. A criação de um balanceador de carga não cria uma instância e a capacidade sempre está disponível. 

## <a name="fundamental-load-balancer-features"></a>Recursos do Azure Load Balancer fundamental

O Azure Load Balancer fornece os seguintes recursos fundamentais para aplicativos de TCP e UDP:

* **Balanceamento de carga**

    Com o Azure Load Balancer, é possível criar uma regra de balanceamento de carga para distribuir o tráfego que chega em um front-end para instâncias do pool de back-end. O Load Balancer usa um algoritmo baseado em hash para distribuição de fluxos de entrada e regenera os cabeçalhos dos fluxos para instâncias do pool de back-end adequadamente. Um servidor fica disponível para receber novos fluxos quando a investigação de integridade indica que o ponto de extremidade de back-end é íntegro.
    
    Por padrão, o Load Balancer usa um hash de 5 tuplas composto por endereço IP de origem, porta de origem, endereço IP de destino, porta de destino e número do protocolo IP para mapear os fluxos para os servidores disponíveis. Você pode optar por criar afinidade a um endereço IP de origem específico optando por hash de 2 ou 3 tuplas para uma determinada regra. Todos os pacotes do mesmo fluxo de pacote chegam na mesma instância por trás do front-end com balanceamento de carga. Quando o cliente inicia um novo fluxo do mesmo IP de origem, a porta de origem muda. Dessa forma, as 5 tuplas podem fazer com que o tráfego vá para um ponto de extremidade de back-end diferente.

    Para obter mais detalhes, consulte [Modo de distribuição do Azure Load Balancer](load-balancer-distribution-mode.md). A imagem a seguir mostra a distribuição baseada em hash:

    ![Distribuição baseada em hash](./media/load-balancer-overview/load-balancer-distribution.png)

    *Figura: distribuição baseada em hash*

* **Encaminhamento de porta**

    Com o Load Balancer, é possível criar uma regra de NAT de entrada para encaminhar o tráfego de uma porta específica de um endereço IP de front-end específico para uma porta específica de uma instância de back-end específica dentro da rede virtual. Isso é também é realizado pela mesma distribuição baseada em hash mesmo como o balanceamento de carga. Cenários comuns de uso dessa funcionalidade são a distribuição de sessões de protocolo RDP ou SSH (Secure Shell) para instâncias de VM individuais dentro da Rede Virtual do Azure. É possível mapear vários pontos de extremidade internos para portas diferentes no mesmo endereço IP de front-end. Use-os para administrar remotamente suas VMs pela Internet sem precisar de um jumpbox adicional.

* **Aplicativo independente e transparente**

    O Load Balancer não interage diretamente com TCP ou UDP ou com a camada de aplicativo, e qualquer cenário de aplicativo TCP ou UDP pode ser compatível.  O Load Balancer não encerra nem origina fluxos, não interage com o conteúdo do fluxo, não fornece nenhuma função de gateway de camada de aplicativo e os handshakes de protocolo sempre ocorrem diretamente entre o cliente e a instância do pool de back-end.  Uma resposta para um fluxo de entrada sempre é uma resposta de uma máquina virtual.  Quando o fluxo chega na máquina virtual, o endereço IP de origem original também é preservado.  Alguns exemplos que ilustram melhor a transparência:
    - Cada ponto de extremidade é respondido somente por uma VM.  Por exemplo, um handshake TCP sempre ocorre entre o cliente e a VM de back-end selecionada.  Uma resposta a uma solicitação feita a um front-end é uma resposta gerada pela VM de back-end. Quando valida com êxito a conectividade com um front-end, você está validando a conectividade de ponta a ponta com pelo menos uma máquina virtual de back-end.
    - Cargas de aplicativo são transparentes para o Load Balancer e qualquer aplicativo UDP ou TCP pode ser compatível. Para cargas de trabalho que exigem processamento por solicitação HTTP ou manipulação de cargas de camada de aplicativo (por exemplo, análise de URLs HTTP), use um balanceador de carga de camada 7, como o [Gateway de Aplicativo](https://azure.microsoft.com/services/application-gateway).
    - Como o Load Balancer é independente da carga de TCP e o descarregamento de TLS ("SSL") não é fornecido, você pode criar cenários criptografados de ponta a ponta usando o Load Balancer e obter uma grande expansão para aplicativos TLS encerrando a conexão TLS na própria VM.  Por exemplo, a capacidade de chaveamento de sua sessão TLS é limitada apenas pelo tipo e pelo número de VMs adicionadas ao pool de back-end.  Se você precisa de "descarregamento de SSL" ou de tratamento de camada de aplicativo ou deseja delegar o gerenciamento de certificados ao Azure, use o [Gateway de Aplicativo](https://azure.microsoft.com/services/application-gateway) do balanceador de carga de camada 7 do Azure.
        

* **Reconfiguração automática**

    O Load Balancer reconfigura-se instantaneamente quando você escala ou reduz verticalmente as instâncias. Adicionar ou remover VMs do pool de back-end reconfigura o balanceador de carga sem operações adicionais no recurso de balanceador de carga.

* **Investigações de integridade**

     Para determinar a integridade das instâncias no pool de back-end, o Load Balancer usa investigações de integridade definidas por você. Quando uma investigação não responde, o balanceador de carga interrompe o envio de novas conexões para as instâncias não íntegras. Conexões existentes não são afetadas e continuam até que o aplicativo encerre o fluxo, que o tempo limite de ociosidade seja atingido ou que a VM seja desligada.

    Existem três tipos de investigação com suporte:

    - **Investigação personalizada de HTTP**: use esta investigação para criar sua própria lógica personalizada para determinar a integridade de uma instância do pool de back-end. O balanceador de carga investiga regularmente seu ponto de extremidade (por padrão, a cada 15 segundos). A instância será considerada íntegra se responder com um HTTP 200 dentro do tempo limite (padrão de 31 segundos). Qualquer status diferente de HTTP 200 faz com que esse teste falhe. Essa investigação é útil para implementar sua própria lógica para remover instâncias da rotação do balanceador de carga. Por exemplo, é possível configurar a instância para retornar um status diferente de 200 caso esteja acima de 90% da CPU.  Essa investigação substitui a investigação de agente convidado padrão.

    - **Investigação personalizada de TCP**: esta investigação depende do estabelecimento de uma sessão de TCP bem-sucedida em uma porta de investigação definida. Enquanto o ouvinte especificado na VM existir, a investigação será bem-sucedida. Se a conexão for recusada, a investigação falhará. Essa investigação substitui a investigação de agente convidado padrão.

    - **Investigação de agente convidado (somente em VMs de PaaS [plataforma como serviço])**: o balanceador de carga também pode utilizar o agente convidado dentro da VM. O agente convidado escuta e responde com uma resposta HTTP 200 OK somente quando a instância está no estado Pronto. Se o agente não responder com HTTP 200 OK, o balanceador de carga marcará a instância como sem resposta e interromperá o envio de tráfego para essa instância. O Azure Load Balancer continuará tentando alcançar a instância. Se o agente convidado responder com um HTTP 200, o Balanceador de Carga enviará o tráfego para essa instância novamente. As investigações de agente convidado são um último recurso e não são recomendadas quando configurações de investigação personalizada de HTTP ou TCP são possíveis. 
    
* **Conexões de saída (SNAT)**

    Todos os fluxos de saída de endereços IP privados dentro da sua rede virtual para os endereços IP públicos na Internet podem ser convertidos para um endereço IP de front-end do balanceador de carga. Quando um front-end público é vinculado a uma VM de back-end por meio de uma regra de balanceamento de carga, o Azure programa as conexões de saída para serem convertidas automaticamente no endereço IP do front-end público.

    * Facilite a atualização e a recuperação de desastre dos serviços, uma vez que o front-end pode ser mapeado dinamicamente para outra instância do serviço.
    * Gerenciamento mais fácil da ACL (lista de controle de acesso). ACLs expressadas em termos de front-end não mudam à medida que os serviços são escalados ou reduzidos verticalmente ou reimplantados.  Converter conexões de saída em um número de endereços IP menor que o de máquinas pode reduzir a carga da lista de permissões.

    Para obter mais informações, confira [conexões de saída](load-balancer-outbound-connections.md).

O Load Balancer Standard tem funcionalidades adicionais específicas do SKU além dessas básicas. Revise o restante deste artigo para obter detalhes.

## <a name="skus"></a> Comparação de SKU do Azure Load Balancer

O Load Balancer é compatível com os SKUs Básico e Standard, cada um com diferenças de preços, recursos e escala de cenário. Qualquer cenário possível com o Load Balancer Básico também pode ser criado com o Load Balancer Standard. Na verdade, as APIs para ambos os SKUs são semelhantes e invocadas através da especificação de um SKU. A API para dar suporte a SKUs do Load Balancer e ao IP público está disponível da API de 01-08-2017 em diante. Ambos os SKUs têm a mesma API geral e estrutura.

No entanto, dependendo do SKU escolhido, a configuração do cenário completo pode ser um pouco diferente. A documentação do Load Balancer indica quando um artigo se aplica somente a um SKU específico. Veja a tabela a seguir para comparar e entender as diferenças. Para obter mais informações, confira [Visão geral do Load Balancer Standard](load-balancer-standard-overview.md).

>[!NOTE]
> Se estiver usando um cenário com design mais recente, considere o uso do Load Balancer Standard. 

VMs autônomas, conjuntos de disponibilidade e conjuntos de dimensionamento de máquina virtual podem estar conectados apenas a um SKU, nunca a ambos. Quando usados com endereços IP públicos, os SKUs do Load Balancer e do endereço IP público devem ser correspondentes. Os SKUs do Load Balancer e de IPs públicos não são mutáveis.

_É uma prática recomendada especificar os SKUs explicitamente, mesmo que ainda não seja obrigatório._  Neste momento, as alterações necessárias estão sendo mantidas em um mínimo. Deixar de especificar um SKU é interpretado como a intenção de usar a versão da API 2017-01/08 do SKU Básico.

>[!IMPORTANT]
>O Azure Load Balancer Standard é um novo produto de Azure Load Balancere amplamente um superconjunto Load Balancer Basic. Há diferenças importantes e deliberadas entre os dois produtos. Qualquer cenário de ponta a ponta possível com o Load Balancer Básico também pode ser criado com o Load Balancer Standard. Se já estiver habituado a usar o Load Balancer Básico, você deverá se familiarizar com o Load Balancer Standard para entender as últimas alterações no comportamento entre o Standard e o Básico, bem como seu impacto. Revise esta seção com cuidado.

| | [SKU padrão](load-balancer-standard-overview.md) | SKU Básico |
| --- | --- | --- |
| tamanho do pool de back-end | Até 1000 instâncias. | Até 100 instâncias. |
| pontos de extremidade de pool de back-end | Qualquer VM em uma única rede virtual, incluindo uma combinação de VMs, conjuntos de disponibilidade e conjuntos de dimensionamento de máquinas virtuais. | VMs em um único conjunto de disponibilidade ou conjunto de dimensionamento de máquinas virtuais. |
| Zonas de Disponibilidade do Azure | Front-ends com redundância de zona e por zona para entrada e saída, mapeamentos de fluxos de saída sobrevivem à falha de zona, balanceamento de carga entre zonas. | / |
| Diagnostics | Azure Monitor, métricas multidimensionais incluindo contadores byte e pacote, status de investigação de integridade, tentativas de conexão (TCP SYN), integridade da conexão de saída (fluxos de SNAT bem-sucedidos e com falha), medidas do plano de dados ativo. | Log Analytics do Azure apenas para o balanceador de carga público, alerta de exaustão SNAT, conta de integridade de pool back-end. |
| Portas de alta disponibilidade | Balanceador de carga interno. | / |
| Segurança por padrão | Por padrão, fechado para pontos de extremidade de balanceador de carga e de IP públicos. Para o tráfego fluir, um Grupo de Segurança de Rede deve ser usado para colocar explicitamente as entidades na lista de permissões. | Aberto por padrão, Grupo de Segurança de Rede opcional. |
| Conexões de saída | Vários front-ends com opção de recusa de acordo com a regra. Um cenário de saída _precisa_ ser criado explicitamente para que a VM possa usar a conectividade de saída. [Pontos de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md) podem ser acessados sem conectividade de saída e não são considerados dados processados. Qualquer endereço IP público, incluindo serviços de PaaS do Azure que não estão disponíveis como pontos de extremidade de serviço de rede virtual, devem ser acessados por meio da conectividade de saída e são considerados dados processados. Quando apenas um balanceador de carga interno está atendendo uma VM, as conexões de saída via SNAT padrão não ficam disponíveis. A programação de SNAT de saída é específica do protocolo de transporte, com base no protocolo da regra de balanceamento de carga de entrada. | Front-end único, selecionado aleatoriamente quando vários front-ends estão presentes. Quando apenas um balanceador de carga interno está atendendo uma VM, o SNAT padrão é usado. |
| Vários front-ends | Entrada e saída. | Somente entrada. |
| Operações de Gerenciamento | Maioria das operações < 30 segundos. | Normalmente, 60 a 90 segundos ou mais. |
| Contrato de Nível de Serviço | 99,99% para um caminho de dados com duas VMs íntegras. | Implícito no SLA da VM. | 
| Preços | Os encargos se baseiam no número de regras e nos dados processados de entrada ou de saída associados ao recurso.  | Sem encargos. |

Para obter mais informações, confira [Limites de serviço do Load Balancer](https://aka.ms/lblimits). Para obter detalhes sobre o Load Balancer Standard, confira [visão geral](load-balancer-standard-overview.md), [preços](https://aka.ms/lbpricing) e [SLA](https://aka.ms/lbsla).

## <a name="concepts"></a>Conceitos

### <a name = "publicloadbalancer"></a>Balanceador de carga público

Um balanceador de carga público mapeia o endereço IP público e o número da porta do tráfego de entrada até o endereço IP privado e o número da porta da VM, e vice-versa no caso do tráfego de resposta da VM. Aplicando regras de balanceamento de carga, você pode distribuir tipos de tráfego específicos entre várias VMs ou serviços. Por exemplo, você pode difundir a carga de tráfego de solicitação da web em vários servidores web.

A figura a seguir mostra um ponto de extremidade com balanceamento de carga para tráfego da Web compartilhado entre três VMs para a porta TCP pública e privada de número 80. Essas três VMs estão em um conjunto com balanceamento de carga.

![Exemplo de balanceador de carga público](./media/load-balancer-overview/IC727496.png)

*Figura: balanceamento de carga de tráfego da Web usando um balanceador de carga público*

Quando clientes de Internet enviam solicitações de página da Web para o endereço IP público de um aplicativo Web na porta TCP 80, o Azure Load Balancer distribui as solicitações entre as três VMs no conjunto com balanceamento de carga. Para obter mais informações sobre os algoritmos do balanceador de carga, consulte a seção [recursos do balanceador de carga](load-balancer-overview.md##fundamental-load-balancer-features) deste artigo.

Por padrão, o Azure Load Balancer distribui o tráfego de rede igualmente entre várias instâncias da VM. Você também pode configurar a afinidade de sessão. Para obter mais detalhes, consulte [modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a> Balanceador de carga interno

Um balanceador de carga interno direciona o tráfego apenas para recursos que estão dentro de uma rede virtual ou que usam uma VPN para acessar a infraestrutura do Azure. Nesse sentido, um balanceador de carga interno é diferente de um balanceador de carga público. A infraestrutura do Azure restringe o acesso aos endereços IP de front-end de carga balanceada de uma rede virtual. Os endereços de front-end e as redes virtuais nunca são expostos diretamente a um ponto de extremidade de Internet. Os aplicativos de linha de negócios interno são executados no Azure e acessados de dentro do Azure ou de recursos locais.

Um balanceador de carga interno habilita os seguintes tipos de balanceamento de carga:

* **Dentro de uma rede virtual**: balanceamento de carga de VMs na rede virtual para um conjunto de VMs dentro da mesma rede virtual.
* **Para uma rede virtual entre instalações**: balanceamento de carga de computadores locais para um conjunto de VMs dentro da mesma rede virtual. 
* **Para aplicativos de várias camadas**: balanceamento de carga para aplicativos de várias camadas voltados para a Internet em que as camadas de back-end não são voltadas para a Internet. As camadas de back-end requerem balanceamento de carga de tráfego da camada voltada para a Internet (veja a figura a seguir).
* **Para aplicativos de linha de negócios**: balanceamento de carga para aplicativos de linha de negócios hospedados no Azure sem hardware ou software de balanceador de carga adicional. Esse cenário inclui servidores locais que estão no conjunto de computadores cujo tráfego tem balanceamento de carga.

![Exemplo de balanceador de carga interno](./media/load-balancer-overview/IC744147.png)

*Figura: balanceamento de carga de aplicativos de várias camadas usando balanceadores de carga internos e públicos*

## <a name="pricing"></a>Preços
Os encargos do Load Balancer Standard são cobrados com base no número de regras de balanceamento de carga configuradas e na quantidade de dados de entrada e de saída processados. Para obter informações sobre os preços do Load Balancer Standard, visite a página [Preços do Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

O Azure Load Balancer Basic é oferecido gratuitamente.

## <a name="sla"></a>Contrato de Nível de Serviço

Para obter informações sobre o SLA do Load Balancer Standard, visite a página [SLA do Load Balancer](https://aka.ms/lbsla). 

## <a name="limitations"></a>Limitações

- O Load Balancer é um produto de TCP ou UDP para balanceamento de carga e encaminhamento de porta para esses protocolos IP específicos.  As regras de balanceamento de carga e as regras de NAT de entrada são compatíveis com TCP e UDP, mas não com outros protocolos IP, incluindo ICMP. O Load Balancer não encerra, responde ou, de outra forma, interage com a carga de um fluxo UDP ou TCP. Ele não é um proxy. A validação bem-sucedida da conectividade com um front-end deve ocorrer na banda, com o mesmo protocolo usado em uma regra de NAT de entrada ou de balanceamento de carga (TCP ou UDP) _e_ pelo menos uma de suas máquinas virtuais deve gerar uma resposta para um cliente para ver uma resposta de um front-end.  Não receber uma resposta na banda do front-end do Load Balancer indica que nenhuma máquina virtual foi capaz de responder.  Não é possível interagir com um front-end do Load Balancer sem que uma máquina virtual possa responder.  Isso também se aplica a conexões de saída em que o [SNAT de representação de porta](load-balancer-outbound-connections.md#snat) é compatível apenas com TCP e UDP; outros protocolos IP, incluindo ICMP, também falharão.  Atribua um endereço IP público em nível da instância para mitigar esse problema.
- Diferente dos Load Balancers públicos que fornecem [conexões de saída](load-balancer-outbound-connections.md) durante a transição de endereços IP privados dentro da rede virtual para endereços IP públicos, os Load Balancers internos não convertem conexões originárias da saída para o front-end de um Load Balancer interno, uma vez que ambos estão em um espaço de endereços IP privado.  Isso evita o potencial de esgotamento de SNAT dentro de espaços de endereço IP internos exclusivos, em que a conversão não é necessária.  O efeito colateral é que, se um fluxo de saída de uma VM no pool de back-end tentar estabelecer um fluxo para o front-end do Load Balancer interno no pool em que reside _e_ for mapeado de volta para si mesmo, os dois lados do fluxo não serão correspondentes e o fluxo falhará.  Se o fluxo não tiver sido mapeado de volta para a mesma VM no pool de back-end que criou o fluxo para o front-end, ele será bem-sucedido.   Quando o fluxo é mapeado de volta para si mesmo, o fluxo de saída parece ser originado da VM para o front-end e o fluxo de entrada correspondente parece ser originado da VM para si mesmo. Do ponto de vista do SO convidado, as partes de entrada e de saída do mesmo fluxo não são correspondentes dentro da máquina virtual. A pilha TCP não reconhece essas metades do mesmo fluxo como parte do mesmo fluxo, pois a origem e o destino não são correspondentes.  Quando o fluxo é mapeado para qualquer outra VM no pool de back-end, as metades do fluxo são correspondentes e a VM pode responder ao fluxo com êxito.  O sintoma desse cenário são tempos limite de conexão intermitentes. Há várias soluções alternativas comuns para alcançar esse cenário de forma confiável (originar fluxos de um pool de back-end para seu respectivo front-end de Load Balancer interno), que incluem a inserção de um proxy de terceiros por trás do balanceador de carga interno ou o [uso de regras de estilo de DSR](load-balancer-multivip-overview.md).  Embora você possa usar um balanceador de carga público para mitigar esse problema, o cenário resultante será propenso ao [esgotamento de SNAT](load-balancer-outbound-connections.md#snat) e deverá ser evitado, a menos que seja gerenciado atentamente.

## <a name="next-steps"></a>Próximas etapas

Agora, você tem uma visão geral do Azure Load Balancer. Para começar a usar um balanceador de carga, crie um, crie VMs com uma extensão de IIS personalizada e faça o balanceamento de carga do aplicativo Web entre as VMs. Para saber mais, consulte o início rápido [Criar um Load Balancer Básico](quickstart-create-basic-load-balancer-portal.md).
