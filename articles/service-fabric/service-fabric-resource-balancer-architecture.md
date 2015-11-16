<properties
   pageTitle="Arquitetura do Balanceador de Recursos | Microsoft Azure"
   description="Uma visão geral da arquitetura do Balanceador de Recursos do Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="GaugeField"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/03/2015"
   ms.author="masnider"/>

# Visão geral da arquitetura do Balanceador de Recursos

![Arquitetura do Balanceador de Recursos][Image1]

O Balanceador de Recursos da Malha de Serviço consiste em um único serviço centralizado de Balanceamento de Recursos e um componente que existe em cada nó, que são conceitualmente colocados com o Gerenciador de Failover da Malha de Serviço e com o nó da Malha de Serviço local, respectivamente.

O agente local é responsável por coletar e armazenar em buffer os relatórios de carga dos serviços que estão em execução no nó local, para enviá-los ao serviço de Balanceador de Recursos, assim como por relatar falhas e outros eventos do Gerenciador de Failover e Balanceador de Recursos (1 e 2 acima). O serviço de Balanceador de Recursos e o Gerenciador de Failover colaboram para responder a eventos de carga e outros eventos no sistema, como falhas de réplica ou nó, relatórios de carga, e serviços e aplicativos que são criados e excluídos. Por exemplo, quando uma réplica falha, o Gerenciador de Failover solicita que o Balanceador de Recursos da Malha de Serviço sugira um local para a substituição que se baseie nos dados de carga de diferentes nós. Da mesma forma, quando uma nova solicitação de serviço é recebida, o Gerenciador de Failover solicita recomendações do Balanceador de Recursos sobre onde colocar esse serviço. Em todos esses casos, o Balanceador de Recursos responde às alterações nas diversas configurações de serviço (3), que são então colocadas em prática pelo Gerenciador de Failover. No exemplo anterior, o Gerenciador de Failover cria uma nova réplica no nó, que resulta em melhor equilíbrio geral (4).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

Recursos do Balanceamento de Recursos:

- [Descrever o cluster](service-fabric-resource-balancer-cluster-description.md)
- [Descrever serviços](service-fabric-resource-balancer-service-description.md)
- [Relatar carga dinâmica](service-fabric-resource-balancer-dynamic-load-reporting.md)
- [Empacotamento de métricas proativas](service-fabric-resource-balancer-proactive-metric-packing.md)
- [Porcentagem de buffer do nó](service-fabric-resource-balancer-node-buffer-percentage.md)

[Image1]: media/service-fabric-resource-balancer-architecture/Service-Fabric-Resource-Balancer-Architecture.png
 

<!---HONumber=Nov15_HO2-->