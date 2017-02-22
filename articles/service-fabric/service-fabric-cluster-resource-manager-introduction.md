---
title: "Introdução ao Resource Manager de Cluster do Service Fabric | Microsoft Docs"
description: "Uma introdução ao Gerenciador de Recursos de Cluster do Service Fabric."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: cfab735b-923d-4246-a2a8-220d4f4e0c64
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: ec470466f006265af5c4ccfddeeba975e6e648b5


---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Introdução ao Gerenciador de Recursos de Cluster do Service Fabric
Tradicionalmente, o gerenciamento de sistemas de TI ou de um conjunto de serviços significava ter alguns computadores ou máquinas virtuais dedicados a esses serviços ou sistemas específicos. Muitos serviços principais foram divididos em uma camada "Web" e em uma camada de "dados" ou de "armazenamento", talvez com alguns outros componentes especializados, como um cache. Outros tipos de aplicativos teria uma camada de mensagens na qual as solicitações entrariam e sairiam. Esta camada estaria conectada a uma camada de trabalho para qualquer análise ou transformação necessária como parte da mensagem. Cada tipo de carga de trabalho tem computadores específicos dedicados: o banco de dados tem dois computadores dedicados a ele, enquanto os servidores Web contam com alguns. Se um tipo específico de carga de trabalho fez com que os computadores presentes nele tivessem utilização alta demais, adicione mais computadores à mesma configuração. Na maioria das vezes, você substituiu alguns dos computadores por outros maiores. Fácil. Se uma máquina falhava, essa parte do aplicativo geral era executada com menor capacidade até que o computador pudesse ser restaurado. Ainda assim, relativamente fácil (mesmo que não necessariamente divertido).

No entanto, vamos dizer que você sentiu a necessidade de escalar horizontalmente e resolveu se arriscar com os contêineres e/ou o microsserviço. De repente, você verá que tem dezenas, centenas ou milhares de computadores. Você tem dezenas de tipos de serviços diferentes (nenhum deles consumindo todos os recursos de um computador), talvez centenas de instâncias diferentes desses serviços. Cada instância nomeada tem uma ou mais instâncias ou réplicas para HA (Alta Disponibilidade).

Inesperadamente, já não era tão simples gerenciar seu ambiente com poucos computadores dedicados a tipos de carga de trabalho únicos. Os servidores são virtuais e não têm mais nomes (sua mentalidade *passou* a valorizar a [coletividade](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) ). A configuração tem menos a ver com os computadores e mais a ver com os próprios serviços. O hardware dedicado é coisa do passado e os próprios serviços tornaram-se pequenos sistemas distribuídos, abrangendo várias partes menores do hardware de mercadoria.

Como consequência dessa divisão do seu antigo aplicativo monolítico em camadas em serviços separados executados no hardware de uso geral, agora você tem muitas combinações com as quais lidar. Quem decide quais tipos de carga de trabalho podem ser executados em qual hardware, ou quantos? Quais cargas de trabalho funcionam bem no mesmo hardware e quais entram em conflito? Quando um computador falha… como saber o que ele estava executando? Quem é o responsável por garantir que a carga de trabalho comece a execução novamente? Você espera que a máquina (virtual) volte ou suas cargas de trabalho fazem failover automaticamente para outras máquinas e continuam em execução? Há necessidade de intervenção humana? E quanto às atualizações nesse ambiente?

Como desenvolvedores e operadores que lidam com isso, vamos precisar de certa ajuda para gerenciar essa complexidade. Você percebe que uma onda de contratações para tentar esconder a complexidade não é a resposta certa.

O que fazer?

## <a name="introducing-orchestrators"></a>Introdução aos orquestradores
Um "Orquestrador" é o termo geral para um componente de software que ajuda os administradores a gerenciar esses tipos de ambiente. Os Orchestrators são os componentes que recebem solicitações como “Eu gostaria de ter cinco cópias desse serviço em execução no meu ambiente”. Eles tentam corresponder o ambiente ao estado desejado, independente do que venha a ocorrer.

Os orquestradores (não humanos) são os que entram em ação quando um computador falha ou quando uma carga de trabalho é encerrada por algum motivo inesperado. A maioria dos Orchestrators fazem mais do que apenas lidar com falhas. Outros recursos que eles oferecem são o gerenciamento de novas implantações, manipulação de atualizações e tratamento do consumo de recursos. Todos os Orchestrators destinam-se fundamentalmente a manter algum estado desejado da configuração no ambiente. Você quer poder dizer a um Orquestrador o que deseja para que ele faça o trabalho pesado. Chronos ou Marathon sobre Mesos, Fleet, Docker Datacenter/Docker Swarm, Kubernetes e Service Fabric são exemplos de Orchestrators (ou os têm internamente). Mais estão sendo criados o tempo todo, já que as complexidades de gerenciar as implantações reais em diferentes tipos de ambientes e de condições crescem e mudam.

## <a name="orchestration-as-a-service"></a>Orquestração como um serviço
O trabalho do Orquestrador em um cluster do Service Fabric é tratado principalmente pelo Gerenciador de Recursos de Cluster. O Gerenciador de Recursos de Cluster do Service Fabric é um dos Serviços do Sistema no Service Fabric e é iniciado automaticamente em cada cluster. Geralmente, o trabalho do Gerenciador de Recursos de Cluster é dividido em três partes:

1. Imposição de regras
2. Otimização do seu ambiente
3. Ajudando com outros processos

Para ver como o Cluster Resource Manager funciona, assista ao seguinte vídeo do Microsoft Virtual Academy:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=d4tka66yC_5706218965">
<img src="./media/service-fabric-cluster-resource-manager-introduction/ConceptsAndDemoVid.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="what-it-isnt"></a>O que não é
Em aplicativos tradicionais de camada N, sempre existiu uma certa noção de “Balanceador de Carga”. Geralmente, esse era um NLB (Balanceador de Carga de Rede) ou um ALB (Balanceador de Carga de Aplicativo), dependendo de onde ele estava na pilha da rede. Alguns balanceadores de carga se baseiam em Hardware, como a oferta BigIP da F5 e outros em software, como o NLB da Microsoft. Em outros ambientes, você poderá ver algo como HAProxy ou nginx nessa função. Nessas arquiteturas, o trabalho de balanceamento de carga é garantir que as cargas de trabalho sem estado recebam (aproximadamente) a mesma quantidade de trabalho. Estratégias de balanceamento de carga variadas. Alguns balanceadores enviavam cada chamada para um servidor diferente. Outros forneciam fixação/adesão à sessão. Balanceadores mais avançados usam estimativa real ou relatórios para rotear chamadas com base em seu custo esperado e a carga atual do computador.

Balanceadores de rede ou roteadores de mensagem tentavam garantir que uma camada Web/de trabalho permaneça razoavelmente equilibrada. As estratégias para balancear a camada de dados eram completamente diferentes e dependiam do mecanismo de armazenamento de dados, normalmente centralizado na fragmentação de dados, cache, exibições gerenciadas, procedimentos armazenados e demais mecanismos específicos de repositório.

Embora algumas dessas estratégias sejam interessantes, o Gerenciador de Recursos de Cluster do Service Fabric não é um balanceador de carga de rede ou um cache. Um Balanceador de Carga de Rede garante que os front-ends são balanceados movendo o tráfego para onde os serviços estão em execução. O Service Fabric Cluster Resource Manager assuma uma estratégia diferente. Fundamentalmente, o Service Fabric move os *serviços* para onde eles fazem mais sentido, esperando que o tráfego ou a carga os siga. Por exemplo, ele pode mover serviços para nós que são pouco utilizados no momento, pois os serviços que estão lá não estão trabalhando muito. Os nós podem ser pouco utilizados porque os serviços existentes foram excluídos ou movidos para outro lugar. Como outro exemplo, o Cluster Resource Manager também poderia retirar um serviço de um computador. Talvez o computador esteja prestes a ser atualizado ou está sobrecarregado devido a um aumento no consumo de serviços em execução nele.

Como o Cluster Resource Manager é responsável por mover os serviços (e não fornecer tráfego de rede para onde os serviços já estão), ele contém um conjunto de recursos consideravelmente diferente em comparação com o que você encontraria em um balanceador de carga de rede. Como veremos mais detalhadamente, descobriremos que ele utiliza estratégias fundamentalmente diferentes para garantir que os recursos de hardware do cluster sejam utilizados com eficiência.

## <a name="next-steps"></a>Próximas etapas
* Para obter informações sobre arquitetura e o fluxo de informações no Cluster Resource Manager, confira [este artigo ](service-fabric-cluster-resource-manager-architecture.md)
* O Cluster Resource Manager tem muitas opções para descrever o cluster. Para saber mais sobre elas, confira este artigo sobre a [descrição de um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
* Para saber mais sobre outras opções disponíveis para a configuração de serviços, confira o tópico sobre outras configurações disponíveis no Cluster Resource Manager em [Saiba mais sobre o a configuração dos serviços](service-fabric-cluster-resource-manager-configure-services.md)
* As métricas são como o Gerenciador de Recursos de Cluster do Service Fabric gerencia o consumo e a capacidade no cluster. Para saber mais sobre eles e como configurá-los, confira [este artigo](service-fabric-cluster-resource-manager-metrics.md)
* O Gerenciador de Recursos de Cluster funciona com recursos de gerenciamento do Service Fabric. Para saber mais sobre essa integração, leia [este artigo](service-fabric-cluster-resource-manager-management-integration.md)
* Para descobrir como o Gerenciador de Recursos de Cluster gerencia e balanceia carga no cluster, confira o artigo sobre [como balancear carga](service-fabric-cluster-resource-manager-balancing.md)



<!--HONumber=Jan17_HO1-->


