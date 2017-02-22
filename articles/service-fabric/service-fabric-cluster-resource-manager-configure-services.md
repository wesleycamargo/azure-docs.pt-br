---
title: "Especificar configurações de métricas e posicionamento no Azure microsserviços | Microsoft Docs"
description: "Descrição de um serviço do Service Fabric especificando métricas, restrições de posicionamento e outras políticas de posicionamento."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 16e135c1-a00a-4c6f-9302-6651a090571a
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: bb27d279396aa7b670187560cebe2ed074576bad
ms.openlocfilehash: c1df3b77f3fa621a60d6ab73f6dc2c24abbc3366


---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Configurando as definições do gerenciador de recursos de cluster para serviços do Service Fabric
O Gerenciador de Recursos de Cluster do Service Fabric permite obter um controle muito refinado sobre as regras que regem cada serviço nomeado individual. Cada instância de serviço nomeado pode especificar regras para como deve ser alocado no cluster. Cada serviço nomeado também pode definir o conjunto de métricas que ele deseja para relatório, incluindo importantes como eles são a esse serviço. A configuração de serviços é dividida em três tarefas diferentes:

1. Configurando restrições de posicionamento
2. Configurando métricas
3. Configuração de políticas de posicionamento avançado e outras regras (menos comuns)

## <a name="placement-constraints"></a>Restrições de posicionamento
As restrições de posicionamento são usadas para controlar em quais nós no cluster um serviço pode, na verdade, ser executado. Normalmente, uma instância de serviço nomeada específica ou todos os serviços de um determinado tipo restrito para ser executado em um determinado tipo de nó. Dito isso, as restrições de posicionamento são extensíveis – você pode definir qualquer conjunto de propriedades em uma base de tipo de nó e, em seguida, selecionar para ele com restrições quando o serviço for criado. As restrições de posicionamento também são atualizadas dinamicamente durante o tempo de vida do serviço, permitindo que você responda às alterações no cluster. As propriedades de um determinado nó também podem ser atualizadas dinamicamente no cluster. Para obter mais informações sobre as restrições de posicionamento e como configurá-los podem ser encontradas [neste artigo](service-fabric-cluster-resource-manager-cluster-description.md#placement-constraints-and-node-properties)

## <a name="metrics"></a>Métricas
As métricas são o conjunto de recursos que precisa de uma instância de determinado serviço nomeado. Uma configuração do serviço métrica inclui a quantidade do recurso consome cada instância sem monitoração de estado de serviço ou réplica com monitoração de estado por padrão. As métricas também incluem um peso que indica até que ponto o balanceamento dessa métrica é importante para o serviço, caso as compensações sejam necessárias.

## <a name="other-placement-rules"></a>Outras regras de posicionamento
Há outros tipos de regras de posicionamento que são úteis em clusters distribuídos geograficamente ou em outros cenários menos comuns. Outras regras de posicionamento são configuradas por meio de Correlações ou Políticas.

## <a name="next-steps"></a>Próximas etapas
* As métricas são como o Gerenciador de Recursos de Cluster do Service Fabric gerencia o consumo e a capacidade no cluster. Para saber mais sobre eles e como configurá-los, confira [este artigo](service-fabric-cluster-resource-manager-metrics.md)
* A afinidade é um modo de configurar seus serviços. Ela não é comum, mas se precisar dela, você poderá saber mais [aqui](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* Há muitas regras de posicionamento diferentes que podem ser configuradas em seu serviço para lidar com cenários adicionais. Você pode obter informações sobre essas diferentes políticas de posicionamento [aqui](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
* Comece do princípio e [veja uma introdução ao Resource Manager de Cluster do Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
* Para descobrir como o Gerenciador de Recursos de Cluster gerencia e balanceia carga no cluster, confira o artigo sobre [como balancear carga](service-fabric-cluster-resource-manager-balancing.md)
* O Cluster Resource Manager tem muitas opções para descrever o cluster. Para saber mais sobre elas, confira este artigo sobre a [descrição de um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)



<!--HONumber=Jan17_HO4-->


