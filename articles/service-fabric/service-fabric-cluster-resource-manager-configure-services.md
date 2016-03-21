<properties
   pageTitle="Configurar serviços com o Gerenciador de Recursos de Cluster do Service Fabric"
   description="Descrevendo um Serviço do Service Fabric especificando métricas, restrições de posicionamento e outras políticas de posicionamento."
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
   ms.date="03/03/2016"
   ms.author="masnider"/>


# Configurando serviços
O Gerenciador de Recursos de Cluster do Service Fabric permite obter um controle muito refinado sobre as regras que regem cada serviço nomeado individual. Cada instância do Serviço pode especificar regras específicas de como ela deve ser alocada no cluster e pode definir o conjunto de métricas que deseja relatar, incluindo seu grau de importância para esse serviço. Em geral, a configuração de serviços é dividida em três tarefas diferentes:

1. Configurando restrições de posicionamento
2. Configurando métricas
3. Configurando regras de posicionamento avançado (menos comum)

Vamos abordar todas elas, uma por vez:

## Restrições de Posicionamento
As restrições de posicionamento são usadas para controlar em quais nós no cluster um serviço pode, na verdade, ser executado. Normalmente, você verá uma instância específica de serviço nomeado ou todos os serviços de um tipo restrito ser executados em determinado tipo de nó, mas as restrições de posicionamento são extensíveis – é possível definir qualquer conjunto de propriedades por tipo de nó e, em seguida, selecionar para elas com restrições quando o serviço é criado. As restrições de posicionamento também são atualizadas dinamicamente durante o tempo de vida do serviço, permitindo que você responda às alterações no cluster.

## Métricas
As métricas são a lista de recursos nos quais esse serviço deve ser balanceado, incluindo informações sobre a quantidade do recurso consumido, por padrão, por cada réplica ou instância desse serviço. As métricas também incluem um peso que indica até que ponto essa métrica é importante para o serviço, caso as compensações sejam necessárias.

## Outras regras de posicionamento
Há outros tipos de regras de posicionamento que são principalmente úteis em clusters distribuídos geograficamente ou em outros cenários menos comuns. Elas são configuradas por meio de Correlações ou Políticas. Embora não sejam usadas em muitos cenários, vamos descrevê-las para fins de integridade.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
- [Saber mais sobre Métricas](service-fabric-cluster-resource-manager-metrics.md)
- [Saber mais sobre a Afinidade de Serviço](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- [Saber mais sobre Políticas de Posicionamento de Serviço](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- [Obter uma introdução ao Gerenciador de Recursos de Cluster do Service Fabric](service-fabric-cluster-resource-manager-introduction.md)

<!---HONumber=AcomDC_0309_2016-->