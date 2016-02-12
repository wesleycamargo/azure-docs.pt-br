<properties
   pageTitle="Restrições de posicionamento para administração de cluster no Service Fabric | Microsoft Azure"
   description="Uma visão geral conceitual das restrições de posicionamento na Malha do Serviço"
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
   ms.date="02/03/2016"
   ms.author="masnider"/>

# Visão geral das restrições de posicionamento

O Service Fabric do Azure permite aos desenvolvedores restringir o posicionamento de réplicas de serviço em nós que atendem a condições específicas. Essas condições são expressas por meio de uma expressão booliana que é avaliada com valores específicos de contexto de serviço apropriados.


## Funcionalidades
Ao usar as restrições de posicionamento, você pode:

- Restringir diferentes tipos de serviço em diferentes tipos de nó por meio da definição de NodeProperties nos nós.

- Direcione as restrições a fim de aplicar às réplicas primárias e secundários de um serviço, ou direcione as restrições a fim de aplicar somente nas réplicas primárias.


## Principais conceitos
NodeProperty — um mapa definido pelo usuário ou pelo sistema de uma cadeia de caracteres para um valor, que pode variar a cada nó, isto é, NodeName.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

Para saber mais: [Cenários de aplicativo](../service-fabric-application-scenarios).

<!---HONumber=AcomDC_0204_2016-->