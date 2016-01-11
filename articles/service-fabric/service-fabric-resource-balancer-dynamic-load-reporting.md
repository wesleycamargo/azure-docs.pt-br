<properties
   pageTitle="Relatório dinâmico de carga | Microsoft Azure"
   description="Uma visão geral do relatório dinâmico de carga para o Balanceador de Recursos"
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

# Visão geral do relatório dinâmico de carga

Durante o tempo de execução, objetos de serviço sem e com estado podem relatar carga por meio do método ReportLoad (membro das interfaces IStatefulServicePartition e IStatelessServicePartition). É importante reportar os valores de carga em tempo de execução. Isso permite o empacotamento preciso de serviços em nós e ajuda a garantir que a utilização de recursos seja rastreada de forma precisa pelo Balanceador de Recursos no Service Fabric do Azure, uma vez que os serviços estão passando por essa experiência nos nós.

Observe que, quando uma réplica reporta a carga, primeiro ela é agrupada localmente com outros relatórios de carga e, em seguida, um único relatório é enviado ao Balanceador de Recursos. Esse processo significa que, se um serviço reportar a carga repetidamente e de maneira muito rápida, apenas o último relatório será de fato enviado ao Balanceador de Recursos.

Quando o Balanceador de Recursos do Service Fabric é executado, ele examina todas as informações de carga agregadas de todos os nós e executa algumas verificações. Essas verificações incluem o limite de balanceamento e os limites de atividade para as métricas, conforme definido no manifesto do cluster. Elas determinam se o cluster está desequilibrado o suficiente para executar o Balanceador de Recursos e se algum nó específico está acima da capacidade para alguma das métricas com capacidade definida. No caso de excesso de capacidade, o Balanceador de Recursos considera primeiro somente serviços no(s) nó(s) acima da capacidade que compartilham a métrica, que está acima da capacidade. No desequilíbrio de um cluster, o Balanceador de Recursos considera todos os serviços relacionados por métricas para qualquer serviço que reporte a métrica desequilibrada. Se o Balanceador de Recursos determinar que alguma dessas situações ocorreu, ele executará uma simulação que tentará encontrar uma disposição melhor dos serviços.

Os serviços devem reportar a carga sempre que a carga for alterada. Eles não devem realizar qualquer agregação ou suavização de relatórios de carga por conta própria.

Observe que quando um serviço reporta a carga, esses relatórios de carga substituem o padrão de valores de carga primário e secundário que foram definidos na criação do serviço. Eles se tornam os novos valores de carga usados quando o Balanceador de Recursos do Service Fabric precisa tomar decisões de balanceamento ou posicionamento a partir daí.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

Para saber mais: [Arquitetura do Balanceador de Recursos](service-fabric-resource-balancer-architecture.md)

<!---HONumber=AcomDC_1223_2015-->