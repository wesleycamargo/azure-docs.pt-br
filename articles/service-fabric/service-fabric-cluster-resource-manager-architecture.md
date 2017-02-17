---
title: Arquitetura do Gerenciador de Recursos | Microsoft Docs
description: "Uma visão geral da arquitetura do Gerenciador de Recursos de Cluster do Service Fabric."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 6c4421f9-834b-450c-939f-1cb4ff456b9b
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 67f07bad8f6f89d9e5e68326f0cc194d920e841b


---
# <a name="cluster-resource-manager-architecture-overview"></a>Visão geral da arquitetura do Gerenciador de Recursos de Cluster
Para gerenciar os recursos no cluster, o Gerenciador de Recursos de Cluster do Service Fabric deve ter vários tipos de informação. Ele precisa saber quais serviços existem no momento e a quantidade de recursos atual (ou padrão) que esses serviços estão consumindo. Para controlar os recursos disponíveis no cluster, ele precisa saber a capacidade dos nós no cluster e a quantidade de recursos consumidos em cada um. O consumo de recursos de um determinado serviço pode mudar com o tempo e, geralmente, os serviços consideram mais de um tipo de recurso. Em serviços diferentes, pode haver recursos físicos e físicos reais que estão sendo medidos. Os serviços podem rastrear métricas físicas, como consumo de disco e memória. Com mais frequência, os serviços podem considerar métricas lógicas — como "WorkQueueDepth" ou "TotalRequests". As métricas lógicas e físicas podem ser usadas em muitos tipos diferentes de serviço ou podem ser específicas a apenas alguns serviços.

## <a name="other-considerations"></a>Outras considerações
Às vezes, os proprietários e operadores do cluster são diferentes dos autores do serviço ou, no mínimo, são a mesma pessoa usando acessórios diferentes. Por exemplo, ao desenvolver seu serviço, você sabe por exemplo o que ele exige em termos de recursos e como os diferentes componentes devem, de maneira ideal, ser implantados. No entanto, a pessoa que manipula um incidente de site ao vivo para esse serviço em produção tem um trabalho diferente a fazer, exigindo ferramentas diferentes. Além disso, nem o cluster, nem os serviços são estaticamente configurados:

* O número de nós no cluster pode aumentar e diminuir
* Nós de diferentes tamanhos e tipos podem entrar e sair
* Serviços podem ser criados, removidos e alterar as respectivas alocações do recurso desejado
* Atualizações ou outras operações de gerenciamento podem passar pelo cluster, e as coisas podem dar errado a qualquer momento.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Componentes e fluxo de dados do Gerenciador de Recursos de Cluster
O Gerenciador de Recursos de Cluster precisa controlar os requisitos de serviços individuais e o consumo de recursos pelos objetos do serviço individual que compõem esses serviços. Para isso, o Gerenciador de Recursos de Cluster tem duas partes conceituais: agentes que são executados em cada nó e um serviço tolerante a falhas. Os agentes em cada nó rastreiam os relatórios de carga dos serviços, agregando-os e reportando-os periodicamente. O serviço do Gerenciador de Recursos de Cluster agrega todas as informações dos agentes locais e reage com base em sua configuração atual.

Vamos observar o seguinte diagrama:

<center>
![Arquitetura do Balanceador de Recursos][Image1]
</center>

No tempo de execução, muitas alterações poderiam acontecer. Por exemplo, digamos que a quantidade de recursos que alguns recursos consomem muda, alguns serviços falham e alguns nós entram e saem do cluster. Todas as alterações em um nó são agregadas e enviadas periodicamente ao serviço do Gerenciador de Recursos de Cluster (1 e&2;), onde são agregadas novamente, analisadas e armazenadas. Em intervalo de segundos, o serviço analisa todas as alterações e determina se alguma ação é necessária (3). Por exemplo, ele poderia observar que alguns nós vazios foram adicionados ao cluster e decidir mover alguns serviços para esses nós. O Gerenciador de Recursos de Cluster também poderia observar que um nó específico está sobrecarregado ou que determinados serviços falharam (ou foram excluídos), liberando recursos em outro lugar.

Vamos observar o próximo diagrama e ver o que acontece em seguida. Digamos que o Gerenciador de Recursos de Cluster determine que é necessário fazer alterações. Ele se organiza com outros serviços do sistema (em especial, o Gerenciador de Failover) para fazer as alterações necessárias. Em seguida, os comandos necessários são enviados aos nós apropriados (4). Nesse caso, supomos que o Gerenciador de Recursos observou que o Nó 5 estava sobrecarregado e então decidiu mover o serviço B do N5 para o N4. No final da reconfiguração (5), o cluster terá esta aparência:

<center>
![Arquitetura do Balanceador de Recursos][Image2]
</center>

## <a name="next-steps"></a>Próximas etapas
* O Gerenciador de Recursos de Cluster tem muitas opções para descrever o cluster. Para saber mais sobre elas, confira este artigo sobre a [descrição de um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png



<!--HONumber=Feb17_HO3-->


