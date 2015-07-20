<properties
   pageTitle="Relatório dinâmico de carga"
   description="Uma visão geral do Relatório Dinâmico de Carga para o Balanceador de Recursos"
   services="service-fabric"
   documentationCenter=".net"
   authors="abhic"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/27/2015"
   ms.author="abhic"/>

# Visão geral do Relatório Dinâmico de Carga

Durante o tempo de execução, objetos de serviço sem e com estado podem relatar carga por meio do método ReportLoad (membro das interfaces IStatefulServicePartition e IStatelessServicePartition). Relatar os valores de carga do tempo de execução é importante porque permite empacotamento preciso de serviços em nós e ajuda a garantir que a utilização de recursos seja rastreada de forma precisa pelo Balanceador de Recursos da Malha de Serviço central, uma vez que os serviços estão passando por essa experiência nos nós.

Observe que, quando uma réplica relata carga, ela é dividida em lotes com outros relatórios de carga localmente e, em seguida, um único relatório é enviado ao Balanceador de Recursos. Esse processo significa que, se um serviço relatar carga repetidamente e de maneira muito rápida, apenas o último relatório será de fato enviado ao Balanceador de Recursos.

Quando o Balanceador de Recursos da Malha de Serviço é executado, ele examina todas as informações de carga que são agregadas de todos os nós e executa algumas verificações. Essas verificações incluem o limite de balanceamento e os limites de atividade para as métricas, conforme definido no manifesto do cluster. Elas determinam se o cluster está desequilibrado o suficiente para executar o Balanceador de Recursos e se algum nó específico está acima da capacidade para alguma das métricas para a qual ele tenha uma capacidade definida. No caso de excesso de capacidade, o Balanceador de Recursos considera primeiro somente serviços no(s) nó(s) acima da capacidade que compartilham a métrica, que está acima da capacidade. No desequilíbrio de um cluster, o Balanceador de Recursos considera todos os serviços relacionados por métricas para qualquer serviço que reporte a métrica desequilibrada. Se o Balanceador de Recursos da Malha de Serviço determinar que alguma dessas situações ocorreu, ele executará uma simulação que tentará encontrar uma disposição melhor dos serviços.

Os serviços devem relatar carga sempre que a carga mudar e não executar nenhuma agregação ou suavização de relatórios de carga por conta própria.

Observe que quando um serviço relata carga, esses relatórios de carga substituem os valores de carga padrão Primários e Secundários que foram definidos quando o serviço foi criado, tornando-se os novos valores de carga que são usados quando o Balanceador de Carga da Malha de Serviço precisa tomar decisões de balanceamento ou posicionamento desse ponto em diante.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

Para obter mais informações: [Arquitetura do Balanceador de Recursos](service-fabric-resource-balancer-architecture.md)
 

<!---HONumber=July15_HO2-->