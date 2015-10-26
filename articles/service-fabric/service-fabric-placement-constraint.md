<properties
   pageTitle="Restrições de Posicionamento para administração de cluster da Malha do Serviço"
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
   ms.date="09/03/2015"
   ms.author="abhic"/>

# Visão geral das Restrições de Posicionamento

A Malha do Serviço permite aos desenvolvedores restringir o posicionamento de réplicas de serviço em nós que atendem a condições específicas. Essas condições são expressas por meio de uma expressão booliana que é avaliada com valores específicos de contexto de serviço apropriados.


## Funcionalidades
Ao usar as Restrições de Posicionamento, você pode:

- Restringir diferentes tipos de serviço em diferentes tipos de nó definindo NodeProperties nos nós.

- Aplicar determinadas restrições para réplicas primárias, mas não para réplicas secundárias


## Principais conceitos
NodeProperty — um mapa definido pelo usuário ou sistema de uma cadeia de caracteres para um valor, que pode variar a cada nó, isto é, NodeName.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

Para obter mais informações: [Cenários de aplicativo](../service-fabric-application-scenarios).
 

<!---HONumber=Oct15_HO3-->