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
ms.date: 02/05/2018
ms.author: kumud
ms.openlocfilehash: e1a2b4c281194ec4c70e4d9fe1bc97c5aa61436e
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="understanding-outbound-connections-in-azure"></a>Entendendo as conexões de saída no Azure

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]


O Azure fornece conectividade de saída para implantações de clientes através de vários mecanismos diferentes.  Este artigo descreve quais são os cenários, quando são aplicáveis, como funcionam e como gerenciá-los.

Uma implantação no Azure pode comunicar-se com os pontos de extremidade fora do Azure no espaço de endereços IP público. Quando uma instância inicia um fluxo de saída para um destino no espaço de endereços IP público, o Azure mapeia dinamicamente o endereço IP privado para um endereço IP público.  Quando esse mapeamento for criado, o tráfego de retorno para esse fluxo originado de saída também poderá alcançar o endereço IP privado onde o fluxo foi originado.

O Azure usa SNAT (conversão de endereço de rede de origem) para realizar essa função.  Quando vários endereços IP privados são disfarçados atrás de um único endereço IP público, o Azure usa a [PAT (conversão de endereços de porta)](#pat) para disfarçar endereços IP privados.  As portas efêmeras são usadas para PAT e são [pré-alocadas](#preallocatedports) com base no tamanho do pool.

Há vários [cenários de saída](#scenarios). Esses cenários podem ser combinados, conforme necessário. Revise-os cuidadosamente para entender os recursos, as restrições e os padrões, e como se aplicam ao seu modelo de implantação e cenário de aplicativo.  Revise as diretrizes para [gerenciar esses cenários](#snatexhaust).

## <a name="scenarios"></a>Visão geral do cenário

O Azure possui dois grandes modelos de implantação: Azure Resource Manager e Clássico. O balanceador de carga e os recursos relacionados são explicitamente definidos ao usar os [recursos do Azure Resource Manager](#arm).  As implantações Clássico abstraem o conceito de um balanceador de carga e expressam uma função semelhante através da definição de pontos de extremidade de um [serviço de nuvem](#classic). Os [cenários](#scenarios) aplicáveis à sua implantação dependem do modelo de implantação utilizado.

### <a name="arm"></a>Azure Resource Manager

Atualmente, o Azure fornece três métodos diferentes para alcançar a conectividade de saída para recursos do Azure Resource Manager.  O [Clássico](#classic) têm um subconjunto desses cenários.

| Cenário | Método | DESCRIÇÃO |
| --- | --- | --- |
| [1. VM com endereço IP Público em Nível de Instância (com ou sem balanceador de carga) ](#ilpip) | SNAT, disfarce de porta não usado |O Azure usa o IP público atribuído à configuração de IP do NIC da instância.  A instância possui todas as portas efêmeras disponíveis. |
| [2. Balanceador de carga público associado à VM (nenhum endereço IP Público em Nível de Instância na instância)](#lb) | SNAT com PAT (disfarce de porta) usando front-end do balanceador de carga |O Azure compartilha o endereço IP público do(s) front-end(s) do balanceador de carga público com vários endereços IP privados e usa portas efêmeras do(s) front-end(s) para PAT. |
| [3. VM autônomo (sem balanceador de carga, nenhum endereço IP Público em Nível de Instância) ](#defaultsnat) | SNAT com disfarce de porta (PAT) | O Azure designa automaticamente um endereço IP público para SNAT, compartilha esse endereço IP público com múltiplos endereços IP privados do Conjunto de Disponibilidade e usa portas efêmeras desse endereço IP público.  Esse cenário é um cenário de fallback para os cenários precedentes 1 e 2 e não recomendado se você precisar de visibilidade e controle. |

Se não quiser que uma VM comunique-se com pontos de extremidade fora do Azure no espaço de endereço IP público, você poderá usar NSG (Grupos de Segurança de Rede) para bloquear o acesso, conforme necessário.  O uso de NSGs é abordado mais detalhadamente em [impedindo a conectividade de saída](#preventoutbound).  A diretriz do projeto e os detalhes de implementação para como projetar e gerenciar uma Rede Virtual sem qualquer acesso de saída estão fora do escopo deste artigo.

### <a name="classic"></a>Clássico (serviços de nuvem)

Os cenários disponíveis para implantações Clássico são um subconjunto de cenários disponíveis para implantações do [Azure Resource Manager](#arm) e Load Balancer Basic.

Uma Máquina Virtual Clássica tem os mesmos três cenários fundamentais, conforme descrito para os recursos do Azure Resource Manager ([1](#ilpip), [2](#lb), [3](#defaultsnat)). Uma Função de Trabalho de Web Clássico possui somente dois cenários ([2](#lb), [3](#defaultsnat)).  [As estratégias de mitigação](#snatexhaust) também têm as mesmas diferenças.

O algoritmo utilizado para [pré-alocação de portas efêmeras](#ephemeralprots) para implantações PAT para Clássico é o mesmo que para implantações de recursos do Azure Resource Manager.  

### <a name="ilpip"></a>Cenário 1: VM com um endereço IP em Nível de Instância

Nesse cenário, a VM tem um ILPIP (IP Público em Nível de Instância) atribuído a ela. No que diz respeito às conexões de saída, não importa se a VM é com balanceamento de carga ou não.  Esse cenário tem precedência sobre os outros. Quando um ILPIP é usado, a VM usa o ILPIP para todos os fluxos de saída.  

A PAT (disfarce de porta) não é usada e a VM tem todas as portas efêmeras disponíveis para uso.

Se o aplicativo iniciar muitos fluxos de saída e for observado um esgotamento da porta SNAT, você poderá considerar atribuir um [ILPIP para mitigar as restrições SNAT](#assignilpip). Revise [Gerenciar esgotamento de SNAT](#snatexhaust) completamente.

### <a name="lb"></a>Cenário 2: VM com balanceamento de carga sem Endereço IP Público em Nível de Instância

Nesse cenário, a VM faz parte de um pool de back-end do balanceador de carga público.  A VM não tem um endereço IP público atribuído a ela. O recurso do Load Balancer deve ser configurado com uma regra de balanceador de carga para criar um link entre o front-end de IP público e o pool de back-end. Se você não concluir essa configuração da regra, o comportamento será conforme descrito no cenário para [VM autônoma sem IP em Nível de Instância](#defaultsnat).  Não é necessário que a regra tenha um ouvinte trabalhando no pool de back-end ou a investigação de integridade para ter êxito.

Quando a VM com balanceamento de carga cria um fluxo de saída, o Azure converte o endereço IP de origem particular do fluxo de saída para um endereço IP público do frontend do Balanceador de Carga público. O Azure usa SNAT (Conversão de Endereço de Rede de Origem) para executar essa função, bem como [PAT (conversão de endereços de porta)](#pat) para disfarçar vários endereços IP privados por trás de um endereço IP público. As portas efêmeras do front-end do endereço IP público do balanceador de carga são usadas para distinguir os fluxos individuais originados pela VM. A SNAT usa dinamicamente [ortas efêmeras pré-alocadas](#preallocatedports) quando os fluxos de saída são criados. Nesse contexto, as portas efêmeras usadas para o SNAT são chamadas de portas SNAT.

As portas SNAT são pré-alocadas conforme descrito na seção [Entendendo SNAT e PAT](#snat) e são um recurso finito que pode ser esgotado. É importante entender como elas são [consumidas](#pat). Revise [Gerenciar esgotamento de SNAT](#snatexhaust) para entender como projetar e mitigar, conforme necessário.

Quando [vários endereços IP (públicos) estão associados a um Load Balancer Basic](load-balancer-multivip-overview.md), qualquer um desses endereços IP públicos é um candidato [para fluxos de saída e um é selecionado](#multivipsnat).  

Você pode usar [Log Analytics para o Azure Load Balancer](load-balancer-monitor-log.md) e [Logs de evento de alerta para monitorar as mensagens de esgotamento da porta SNAT](load-balancer-monitor-log.md#alert-event-log) para monitorar a integridade das conexões de saída com Load Balancer Basic.

### <a name="defaultsnat"></a>Cenário 3: VM autônoma sem Endereço IP Público em Nível de Instância

A VM não faz parte de um pool do Azure Load Balancer e não tem um endereço ILPIP (IP Público em Nível de Instância) atribuído a ele. Quando a VM cria um fluxo de saída, o Azure converte o endereço IP de origem particular do fluxo de saída para um endereço IP de origem pública. O endereço IP público usado para esse fluxo de saída não é configurável e não conta para o limite de recursos IP públicos da assinatura. O Azure usa SNAT (Conversão de Endereço de Rede de Origem) com PAT ([disfarce de porta](#pat)) para executar essa função. Esse cenário é semelhante ao [cenário 2](#lb), exceto que não há controle sobre o endereço IP usado.  Este é um cenário de fallback para quando os cenários 1 e o cenário 2 não existirem.  Esse cenário não é recomendado, caso queira o controle sobre o endereço de saída.

As portas SNAT são pré-alocadas conforme descrito na seção [Entendendo SNAT e PAT](#snat) e são um recurso finito que pode ser esgotado. É importante entender como elas são [consumidas](#pat). Revise [Gerenciar esgotamento de SNAT](#snatexhaust) para entender como projetar e mitigar, conforme necessário.

### <a name="combinations"></a>Cenários combinados, vários

Os cenários descritos nas seções anteriores podem ser combinados para alcançar um resultado específico.  Quando vários cenários estão presentes, uma ordem de precedência é aplicável: o [cenário 1](#ilpip) tem precedência sobre o [cenário 2](#lb) e [3](#defaultsnat) (Azure Resource Manager, somente)e o [cenário 2](#lb) substitui o [cenário 3](#defaultsnat) (Azure Resource Manager e Clássico).

Um exemplo é uma implantação do Azure Resource Manager onde o aplicativo depende muito das conexões de saída para um número limitado de destinos, mas também recebe fluxos de entrada em um front-end do balanceador de carga. Nesse caso, você poderia combinar cenários 1 e 2 por segurança.  Revise [Gerenciar esgotamento de SNAT](#snatexhaust) para padrões adicionais.

### <a name="multivipsnat"></a> Vários front-ends para fluxos de saída

O Load Balancer Basic escolherá um único front-end para ser utilizado em fluxos de saída quando [vários front-ends de IP (público)](load-balancer-multivip-overview.md) forem candidatos para fluxos de saída.  Essa seleção não é configurável e o algoritmo de seleção deve ser considerado aleatório.  Você pode designar um endereço IP específico para a saída, conforme descrito em [cenários combinados](#combinations).

## <a name="snat"></a>Entendendo SNAT e PAT

### <a name="pat"></a>Disfarce de porta SNAT (PAT)

Quando um recurso público do Load Balancer estiver associado a instâncias VM, cada fonte de conexão de saída será reescrita. A origem foi reescrita do espaço do endereço IP privado da rede virtual para o endereço IP Público de front-end do balanceador de carga. No espaço de endereço IP público, as 5 tuplas do fluxo (endereço IP de origem, porta de origem, protocolo de transporte IP, endereço IP de destino, porta de destino) devem ser exclusivas.  As portas efêmeras são usadas para alcançar isso após a regravação do endereço IP de origem privado, já que vários fluxos originam-se de um único endereço IP público.  Essas portas efêmeras são chamadas de portas SNAT. 

Uma porta SNAT é consumida por fluxo para um único endereço IP de destino, porta e protocolo. Para vários fluxos para o mesmo endereço IP, porta e protocolo de destino, cada fluxo consome uma única porta SNAT. Isso garante que os fluxos sejam exclusivos quando originados do mesmo endereço IP público para o mesmo endereço IP, porta e protocolo de destino. 

Vários fluxos, cada um para um endereço IP, porta e protocolo de destino diferente, irão compartilhar uma única porta SNAT. O endereço IP de destino, porta e protocolo tornam os fluxos exclusivos sem a necessidade de portas de origem adicionais a serem usadas para distinguir fluxos no espaço de endereço IP público.

Quando os recursos da porta SNAT acabam, os fluxos de saída falham até as portas SNAT serem lançadas por fluxos existentes. O balanceador de carga recupera as portas SNAT quando o fluxo fecha e usa um [tempo limite de ociosidade de 4 minutos](#idletimeout) para recuperar as portas SNAT dos fluxos ociosos.

Revise a seção [Gerenciar SNAT](#snatexhaust) para padrões para mitigar as condições que geralmente levam ao esgotamento da porta SNAT.

### <a name="preallocatedports"></a>Pré-alocação de porta efêmera para a porta de disfarce SNAT (PAT)

O Azure usa um algoritmo para determinar o número de portas SNAT pré-alocadas disponíveis com base no tamanho do pool do back-end ao usar a porta de disfarce SNAT ([PAT](#pat)).  As portas SNAT são portas efêmeras disponíveis para um determinado endereço de origem de IP público.

O Azure pré-aloca as portas SNAT para a configuração IP do NIC de cada VM. Quando uma configuração IP é adicionada ao pool, as portas SNAT são pré-atribuídas para essa configuração de IP com base no tamanho do pool do back-end. Para Funções de Trabalho de Web Clássico, a alocação é por instância de função.  Quando os fluxos de saída são criados, a [PAT](#pat) consome dinamicamente (até o limite pré-alocado) e libera essas portas quando o fluxo fecha ou [tempo limite ocioso](#ideltimeout).

A tabela a seguir mostra as pré-alocações de porta SNAT para níveis de tamanhos de pool de back-end:

| Tamanho do pool (instâncias VM) | Portas SNAT pré-alocadas por configuração de IP|
| --- | --- |
| 1 – 50 | 1024 |
| 51 – 100 | 512 |
| 101 – 200 | 256 |
| 201 – 400 | 128 |
| 401 – 800 | 64 |
| 801 – 1.000 | 32 |

É importante lembrar que o número de portas SNAT disponíveis não se converte diretamente no número de conexões. Uma única porta SNAT pode ser reutilizada para vários destinos exclusivos. As porta somente serão consumidas, se for necessário fazer fluxos exclusivos. Consulte [como gerenciar esse recurso esgotável](#snatexhaust) para diretriz de projeto e mitigação, bem como a seção que descreve [PAT](#pat).

Alterar o tamanho do seu pool de back-end pode afetar alguns dos fluxos estabelecidos. Se o tamanho do pool de back-end aumenta e faz transições para a próxima camada, metade das suas portas SNAT pré-alocadas é recuperada durante a transição para a próxima camada maior de pool de back-end. Os fluxos associados a uma porta SNAT recuperada expirarão e deverão ser restabelecidos. As novas tentativas de conexão terão êxito, desde que as portas pré-alocadas estejam disponíveis.

Se o tamanho do pool de back-ends diminuir e fizer transição para uma camada mais baixa, o número de portas SNAT disponíveis aumentará. Nesse caso, as portas SNAT alocadas existentes e seus respectivos fluxos não são afetados.

## <a name="snatexhaust"></a>Gerenciar esgotamento da porta SNAT (PAT)

[As portas efêmeras](#preallocatedports) usadas para [PAT](#pat) são um recurso esgotável, conforme descrito em [VM autônoma sem Endereço IP Público em Nível de Instância](#defaultsnat) e [VM com balanceamento de carga sem Endereço IP Público em Nível de Instância](#lb).

Se você souber que está iniciando muitas conexões TCP ou UDP de saída para o mesmo endereço e porta IP de destino, observar conexões de saída com falha ou, se for avisado pelo suporte de que as portas SNAT ([portas efêmeras](#preallocatedports) pré-alocadas usadas pela [PAT](#pat)) estão se esgotando, você terá várias opções gerais de mitigação.  Avalie essas opções e decida o que está disponível e melhor para o seu cenário.  É possível que uma ou mais possam ajudar a gerenciar esse cenário.

Caso esteja tendo problemas para entender o comportamento da conexão de saída, você pode usar estatísticas da pilha de IP (netstat) ou pode ser útil observar comportamentos de conexão usando capturas de pacotes.  Você pode executar essas capturas de pacotes no sistema operacional convidado da sua instância ou usar o [Observador de Rede para captura de pacote](../network-watcher/network-watcher-packet-capture-manage-portal.md).

### <a name="connectionreuse"></a>Modificar o aplicativo para reutilizar conexões 
Você pode reduzir a demanda por portas efêmeras usadas para SNAT, reutilizando as conexões em sua aplicação.  Isto é especialmente verdadeiro para protocolos como HTTP/1.1, onde a reutilização de conexão é a padrão.  E outros protocolos usando o HTTP como seu transporte (ou seja, REST) podem se beneficiar por sua vez.  A reutilização é sempre melhor que as conexões TCP individuais e atômicas para cada solicitação e resulta em transações TCP mais eficazes e muito eficientes.

### <a name="connection pooling"></a>Modificar o aplicativo para usar o pool de conexão
Você pode empregar um esquema de pooling de conexão em seu aplicativo, onde as solicitações são distribuídas internamente em um conjunto de conexões fixo (cada reutilização sempre que possível).  Isso restringe o número de portas efêmeras em uso e cria um ambiente mais previsível.  Isso também pode aumentar a taxa de transferência de solicitações, permitindo múltiplas operações simultâneas quando uma única conexão está bloqueando na resposta de uma operação.  O pooling de conexão já pode existir dentro da estrutura que você está utilizando para desenvolver seu aplicativo ou os conjuntos de configurações para o aplicativo.  Você pode combinar isso com a reutilização de conexão e suas múltiplas solicitações consomem um número fixo e previsível de portas para a mesma porta e endereço IP de destino, ao mesmo tempo em que se beneficia do uso muito eficiente das transações TCP, reduzindo a latência e a utilização de recursos.  As transações UDP também podem beneficiar porque o gerenciamento do número de fluxos UDP pode, por sua vez, evitar condições de esgotamento e gerenciar a utilização da porta SNAT.

### <a name="retry logic"></a>Modificar o aplicativo para usar uma lógica de repetição menos agressiva
Quando as [portas efêmeras pré-alocadas](#preallocatedports) usadas para [PAT](#pat) estão esgotadas ou se ocorrem falhas no aplicativo, repetições de força bruta ou agressivas sem lógica de declínio e retirada fazem com que o esgotamento ocorra ou persista. Você pode reduzir a demanda de portas efêmeras usando uma lógica de repetição menos agressiva.   Portas efêmeras têm um tempo limite de ociosidade de 4 minutos (não ajustável) e, se as repetições forem muito agressivas, o esgotamento não terá a oportunidade de limpar por conta própria.  Portanto, considerando como e com que frequência seu aplicativo tenta transações é uma consideração crítica do design.

### <a name="assignilpip"></a>Atribua um IP público em nível de instância a cada VM
Isso altera seu cenário para [IP público em nível de instância para uma VM](#ilpip).  Todas as portas efêmeras do IP público usado para cada VM estão disponíveis para a VM (diferentemente de cenários em que as portas efêmeras de um IP público são compartilhadas com todas as VMs associadas ao respectivo pool de back-end).  Há compromissos a considerar, tais como o custo adicional dos endereços IP públicos e o potencial impacto da listagem de permissões de um grande número de endereços IP individuais.

>[!NOTE] 
>Essa opção não está disponível para as Funções de Trabalho de Web.

### <a name="idletimeout"></a>Use keepalives para redefinir o tempo limite ocioso de saída

Conexões de saída têm um tempo limite de ociosidade de 4 minutos. Isso não pode ser ajustado. No entanto, você pode usar o transporte (ou TCP Keep-alives) ou o keepalives da camada de aplicação para atualizar um fluxo ocioso e redefinir esse tempo limite ocioso, se necessário.

## <a name="discoveroutbound"></a>Descobrindo o IP público usado por uma determinada VM
Há várias maneiras de determinar o endereço IP público de uma conexão de saída. O OpenDNS fornece um serviço que pode mostrar o endereço IP público de sua VM. Usando o comando nslookup, você pode enviar uma consulta DNS para o nome myip.opendns.com para o resolvedor do OpenDNS. O serviço retornará o endereço IP de origem que foi usado para enviar a consulta. Quando você executar a consulta a seguir de sua VM, a resposta será o IP público usado para essa VM.

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventoutbound"></a>Impedir conectividade de saída
Às vezes, não é recomendável para uma VM poder criar um fluxo de saída ou pode haver um requisito para gerenciar quais destinos podem ser alcançados com fluxos de saída ou quais destinos podem iniciar fluxos de entrada. Nesse caso, você usa os [NSG (Grupos de Segurança de Rede)](../virtual-network/virtual-networks-nsg.md) para gerenciar os destinos que a VM pode acessar, além de qual destino público pode iniciar fluxos de entrada. Quando você aplica um NSG a uma VM com balanceamento de carga, é necessário prestar atenção nas [marcações padrão](../virtual-network/virtual-networks-nsg.md#default-tags) e nas [regras padrão](../virtual-network/virtual-networks-nsg.md#default-rules).

Certifique-se de que a VM possa receber solicitações de investigação de integridade do Azure Load Balancer. Se um NSG bloquear solicitações de investigação de integridade da marcação padrão AZURE_LOADBALANCER, o teste de integridade da VM falhará e a VM será reduzida. O Balanceador de Carga interrompe o envio de novos fluxos para a VM.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Load Balancer Básico](load-balancer-overview.md).
- Saiba mais sobre [Grupos de Segurança de Rede](../virtual-network/virtual-networks-nsg.md).
- Saiba mais sobre alguns dos outros principais [recursos de rede](../networking/networking-overview.md) no Azure.
