<properties
   pageTitle="Configurar serviços com o Gerenciador de Recursos de Cluster do Service Fabric | Microsoft Azure"
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
   ms.date="03/10/2016"
   ms.author="masnider"/>


# Configurando as definições do Gerenciador de Recursos de Cluster para serviços do Service Fabric
O Gerenciador de Recursos de Cluster do Service Fabric permite obter um controle muito refinado sobre as regras que regem cada serviço nomeado individual. Cada instância do Serviço pode especificar regras específicas de como ela deve ser alocada no cluster e pode definir o conjunto de métricas que deseja relatar, incluindo seu grau de importância para esse serviço. Em geral, a configuração de serviços é dividida em três tarefas diferentes:

1. Configurando restrições de posicionamento
2. Configurando métricas
3. Configurando regras de posicionamento avançado (menos comum)

Vamos abordar todas elas, uma por vez:

## Restrições de posicionamento
As restrições de posicionamento são usadas para controlar em quais nós no cluster um serviço pode, na verdade, ser executado. Normalmente, você verá uma instância específica de serviço nomeado ou todos os serviços de um tipo restrito ser executados em determinado tipo de nó, mas as restrições de posicionamento são extensíveis – é possível definir qualquer conjunto de propriedades por tipo de nó e, em seguida, selecionar para elas com restrições quando o serviço é criado. As restrições de posicionamento também são atualizadas dinamicamente durante o tempo de vida do serviço, permitindo que você responda às alterações no cluster.

## Métricas
As métricas são a lista de recursos nos quais esse serviço deve ser balanceado, incluindo informações sobre a quantidade do recurso consumido, por padrão, por cada réplica ou instância desse serviço. As métricas também incluem um peso que indica até que ponto essa métrica é importante para o serviço, caso as compensações sejam necessárias.

## Outras regras de posicionamento
Há outros tipos de regras de posicionamento que são principalmente úteis em clusters distribuídos geograficamente ou em outros cenários menos comuns. Elas são configuradas por meio de Correlações ou Políticas. Embora não sejam usadas em muitos cenários, vamos descrevê-las para fins de integridade.

## Próximas etapas
- As métricas são como o Gerenciador de Recursos de Cluster do Service Fabric gerencia o consumo e a capacidade no cluster. Para saber mais sobre elas e como configurá-las, confira [este artigo](service-fabric-cluster-resource-manager-metrics.md)
- A afinidade é um modo de configurar seus serviços. Ela não é comum, mas se precisar dela, você pode saber mais [aqui](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md).
- Há muitas regras de posicionamento diferentes que podem ser configuradas em seu serviço para lidar com cenários adicionais. Você pode obter informações sobre essas diferentes políticas de posicionamento [aqui](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- Comece do princípio e [veja uma introdução ao Gerenciador de Recursos de Cluster do Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
- Para descobrir como o Gerenciador de Recursos de Cluster gerencia e balanceia carga no cluster, confira o artigo sobre [como balancear carga](service-fabric-cluster-resource-manager-balancing.md).
- O Gerenciador de Recursos de Cluster tem muitas opções para descrever o cluster. Para saber mais sobre elas, confira este artigo sobre a [descrição de um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

<!---HONumber=AcomDC_0316_2016-->