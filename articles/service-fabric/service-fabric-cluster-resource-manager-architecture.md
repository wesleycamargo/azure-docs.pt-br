<properties
   pageTitle="Arquitetura do Gerenciador de Recursos | Microsoft Azure"
   description="Uma visão geral da arquitetura do Gerenciador de Recursos de Cluster do Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="masnider"/>

# Visão geral da arquitetura do Gerenciador de Recursos de Cluster
A fim de gerenciar os recursos no cluster, o Gerenciador de Recursos de Cluster do Service Fabric deve ter vários tipos de informações. Ele precisa saber quais serviços existem no momento e a quantidade de recursos atual (ou padrão) que esses serviços estão consumindo. Ele precisa saber a capacidade real dos nós no cluster e, portanto, a quantidade de recursos disponíveis no cluster como um todo e restantes em um nó específico. O consumo de recursos de um determinado serviço pode mudar com o tempo, bem como com o fato de que os serviços, na realidade, normalmente envolvem mais de um recurso. Em vários serviços diferentes pode haver dois recursos físicos reais que estão sendo medidos e reportados como métricas, como consumo de memória e disco, bem como (e, na realidade, mais frequentemente) métricas lógicas – coisas como "WorkQueueDepth" ou "TotalRequests". As métricas lógicas e físicas podem ser usadas em muitos tipos diferentes de serviço ou podem ser específicas a apenas alguns serviços.

## Outras considerações
Os proprietários e operadores do cluster ocasionalmente são diferentes dos autores do serviço ou, pelo menos, são as mesmas pessoas desempenhando funções diferentes; por exemplo, ao desenvolver seu serviço, você sabe algumas coisas sobre o que é necessário em termos de recursos e como os diferentes componentes devem ser implantados de forma ideal, mas sendo a pessoa que lida com um incidente de site ativo para esse serviço em produção, você tem um trabalho diferente a fazer e que exige ferramentas diferentes. Além disso, nem o cluster nem os próprios serviços são configurados estaticamente: o número de nós no cluster pode ser aumentado ou reduzido, nós de tamanhos diferentes podem sair e entrar, e os serviços podem ser criados, removidos e alterar as alocações de recurso desejado dinamicamente. Atualizações ou outras operações de gerenciamento podem passar pelo cluster e – é claro – as coisas podem dar errado a qualquer momento.

## Componentes e fluxo de dados do Gerenciador de Recursos de Cluster
O Gerenciador de Recursos de Cluster precisará saber muitas coisas sobre o cluster geral, bem como os requisitos de serviços individuais e a instâncias sem estado ou réplicas com estado que o constituem. Para fazer isso, temos agentes do Gerenciador de Recursos de Cluster que são executados em nós individuais para agregar informações sobre o consumo de recursos local, e um serviço do Gerenciador de Recursos de Cluster centralizado e tolerante a falhas que agrega todas as informações sobre os serviços e o cluster, além de reagir de acordo com sua configuração atual. A tolerância a falhas do serviço do Gerenciador de Recursos de Cluster (e todos os outros serviços do sistema) é atingida exatamente pelos mesmos mecanismos que usamos para seus serviços, em outras palavras, replicação do estado do serviço em quóruns de algumas réplicas no cluster (geralmente sete).

![Arquitetura do Balanceador de Recursos][Image1]

Vamos observar este diagrama acima como um exemplo. Há uma série de alterações que poderia acontecer durante o tempo de execução: por exemplo, digamos que há algumas alterações na quantidade de recursos consumidos pelos serviços, algumas falhas de serviço, alguns nós que entram e saem do cluster, etc. Todas as alterações em um nó específico são agregadas e enviadas periodicamente ao serviço do Gerenciador de Recursos de Cluster (1 e 2), onde são agregadas novamente, analisadas e armazenadas. Em intervalo de segundos, o serviço analisa todas as alterações e determina se há quaisquer ações necessárias (3). Por exemplo, ele poderia observar que foram adicionados nós ao cluster e que eles estão vazios, e decidir mover alguns serviços para esses nós. Ele também pode observar que um nó específico está sobrecarregado ou que determinados serviços falharam (ou foram excluídos), liberando recursos em outros nós.

Vamos observar o próximo diagrama e ver o que acontece em seguida. Digamos que o Gerenciador de Recursos de Cluster determine que é necessário fazer alterações. Ele se organiza com outros serviços do sistema (em especial, o Gerenciador de Failover) para fazer as alterações necessárias. Em seguida, as solicitações de alteração são enviadas para os nós apropriados (4). Nesse caso, supomos que o Gerenciador de Recursos observou que o Nó 5 estava sobrecarregado e então decidiu mover o serviço B do N5 para o N4. No final da reconfiguração (5), o cluster terá esta aparência:

![Arquitetura do Balanceador de Recursos][Image2]

## Próximas etapas
- O Gerenciador de Recursos de Cluster tem muitas opções para descrever o cluster. Para saber mais sobre elas, confira este artigo sobre a [descrição de um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]: ./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png

<!---HONumber=AcomDC_0824_2016-->