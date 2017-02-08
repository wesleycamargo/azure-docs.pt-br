---
title: "Definir e gerenciar o estado nos microsserviços do Azure | Microsoft Docs"
description: "Como definir e gerenciar o estado do serviço na malha de serviço"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: f5e618a5-3ea3-4404-94af-122278f91652
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: 7033955fa9c18b2fa1a28d488ad5268d598de287
ms.openlocfilehash: d03bd6a4c317da67a4e6d0e8cdb0cbd3f07d5a1f


---
# <a name="service-state"></a>Estado do serviço
**Estado do serviço** refere-se aos dados que o serviço requer para funcionar. São as estruturas de dados e variáveis que o serviço lê e grava para realizar trabalhos.

Considere um serviço de calculadora, por exemplo. Esse serviço usa dois números e retorna a soma. Este é um serviço puramente sem monitoração de estado que não possui dados associados a ele.

Agora, considere a mesma calculadora, mas além da soma de computação, ela também conta com um método para retornar a última soma computada. Esse serviço agora é com monitoração de estado - contém algum estado que é gravado (quando calcula uma nova soma) e lido (quando retorna a última soma calculada).

No Service Fabric do Azure, o primeiro serviço é chamado de serviço sem estado. O segundo serviço é chamado de serviço com estado.

## <a name="storing-service-state"></a>Armazenando o estado do serviço
O estado pode ser externalizado ou localizado em conjunto com o código que está manipulando o estado. A externalização de estado normalmente é feita por meio de um armazenamento ou banco de dados externo. Em nosso exemplo de calculadora, isso pode ser um Banco de Dados SQL no qual o resultado atual é armazenado em uma tabela. Todas as solicitações para calcular a soma executa uma atualização nesta linha.

O estado também pode ser localizado com o código que manipula esse código. Serviços com monitoração de estado no Service Fabric são criados usando esse modelo. O Service Fabric fornece a infraestrutura para garantir que esse estado seja altamente disponível e tolerante a falhas em caso de uma falha.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os conceitos do Service Fabric, confira os seguintes artigos:

* [Disponibilidade dos serviços de malha do serviço](service-fabric-availability-services.md)
* [Escalabilidade de serviços da Malha do Serviço](service-fabric-concepts-scalability.md)
* [Particionando serviços da Malha do Serviço](service-fabric-concepts-partitioning.md)
* [Reliable Services do Service Fabric](service-fabric-reliable-services-introduction.md)



<!--HONumber=Jan17_HO4-->


