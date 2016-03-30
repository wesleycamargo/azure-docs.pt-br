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
   ms.date="03/10/2016"
   ms.author="masnider"/>

# Visão geral da arquitetura do Gerenciador de Recursos de Cluster
A fim de gerenciar os recursos no cluster, o Gerenciador de Recursos de Cluster deve ter vários tipos de informações. Ele precisa saber quais serviços existem no momento e a quantidade de recursos atual (ou padrão) que está sendo consumida por um serviço. Ele precisa saber a capacidade real dos nós no cluster e, portanto, a quantidade de recursos disponíveis no cluster como um todo e restantes em um nó específico. Teremos de lidar com o fato de que o consumo de recursos de determinado serviço pode mudar com o tempo, bem como com o fato de que os serviços, na realidade, normalmente envolvem mais de um recurso. Em vários serviços diferentes, pode haver recursos “reais” como consumo de memória e de disco que são geralmente usados em vários tipos diferentes de serviços, bem como recursos que envolvem apenas determinado serviço.

## Outras considerações
Para complicar ainda mais as coisas, há a questão de que os proprietários e operadores do cluster são ocasionalmente diferentes dos autores do serviço ou, pelo menos, são as mesmas pessoas desempenhando funções diferentes; por exemplo, ao desenvolver seu serviço, você sabe algumas coisas sobre o que é necessário em termos de recursos e como os diferentes componentes devem ser implantados de forma ideal, mas sendo a pessoa que lida com um incidente de site ativo para esse serviço em produção, você tem um trabalho diferente a fazer e que exige ferramentas diferentes. Além disso, nem o cluster nem os próprios serviços são configurados estaticamente: o número de nós no cluster pode ser aumentado ou reduzido, nós de tamanhos diferentes podem sair e entrar, e os serviços podem alterar a alocação de recurso, além de ser criados e removidos. Atualizações ou outras operações de gerenciamento podem passar pelo cluster e – é claro – as coisas podem dar errado a qualquer momento.

## Componentes e fluxo de dados do Gerenciador de Recursos de Cluster
Nosso gerenciador de recursos precisará saber muitas coisas sobre o próprio cluster geral, bem como os requisitos de serviços específicos. Para fazer isso, no Service Fabric, temos os dois agentes do Gerenciador de Recursos que funcionam em nós individuais para agregar informações sobre o consumo de recursos local, e um serviço do Gerenciador de Recursos centralizado de tolerância a falhas que agrega todas as informações sobre os serviços e o cluster, além de reagir a alterações de acordo com a configuração de estado desejado do cluster e do serviço. A tolerância a falhas é alcançada exatamente pelo mesmo mecanismo que seguimos para seus serviços, ou seja, a replicação de estado do serviço para um número de réplicas (geralmente 7).

![Arquitetura do Balanceador de Recursos][Image1]

Vamos observar este diagrama acima como um exemplo. Há uma série de alterações que poderia acontecer durante o tempo de execução: por exemplo, digamos que há algumas alterações na quantidade de recursos consumidos pelos serviços, algumas falhas de serviço, alguns nós que entram e saem do cluster, etc. Todas as alterações em um nó específico são agregadas e enviadas periodicamente ao serviço central do Gerenciador de Recursos (1 e 2), em que são agregadas novamente, analisadas e armazenadas. Em intervalo de segundos, o serviço central analisa todas as alterações e determina se há quaisquer ações necessárias (3). Por exemplo, ele poderia observar que foram adicionados nós ao cluster e que eles estão vazios, e decidir mover alguns serviços para esses nós. Ele também pode observar que um nó específico está sobrecarregado ou que determinados serviços falharam (ou foram excluídos), liberando recursos em outros nós.

Vamos observar o próximo diagrama e ver o que acontece neste exemplo. Digamos que o Gerenciador de Recursos determine que é necessário fazer alterações. Ele se organiza com outros serviços do sistema (em especial, o Gerenciador de Failover) para fazer as alterações necessárias. Em seguida, as solicitações de alteração são enviadas para os nós apropriados (4). Nesse caso, supomos que o Gerenciador de Recursos observou que o Nó 5 estava sobrecarregado e então decidiu mover o serviço B do N5 para o N4. No final da reconfiguração (5), o cluster terá esta aparência:

![Arquitetura do Balanceador de Recursos][Image2]

## Próximas etapas
- O Gerenciador de Recursos de Cluster tem muitas opções para descrever o cluster. Para saber mais sobre elas, confira este artigo sobre a [descrição de um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]: ./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png

<!---HONumber=AcomDC_0316_2016-->