<properties
   pageTitle="Azure Service Fabric no Linux | Microsoft Azure"
   description="Os clusters do Service Fabric dão suporte a Linux e ao Java, o que significa que você poderá implantar e hospedar aplicativos do Service Fabric escritos em Java no Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/29/2016"
   ms.author="SubramaR"/>

# Service Fabric no Linux

No momento, o Service Fabric está disponível como uma visualização limitada no Linux, permitindo que você compile, implante e gerencie aplicativos altamente disponíveis e escalonáveis naquele ambiente, como faria no Windows. Além disso, as estruturas de alto nível do Service Fabric (Reliable Services e Reliable Actors) agora podem ser compilados em Java.

## Sistemas operacionais e linguagens de programação com suporte

A visualização limitada oferece suporte à criação de clusters de desenvolvimento de uma caixa, bem como clusters de vários computadores no Azure executando o Ubuntu Server 15.10.

Você pode criar [serviços executáveis convidados](service-fabric-deploy-existing-app.md) com qualquer linguagem ou estrutura. Você também pode usar o Java para criar serviços com base nas estruturas Reliable Services e Reliable Actor.

>[AZURE.NOTE] O Java ainda não dá suporte às Reliable Collections.

## Participar da visualização

Se você estiver interessado em ser considerado para o programa de visualização limitada, [preencha a pesquisa](http://aka.ms/sflinuxsurvey) para que possamos entender seu cenário e seus requisitos. A visualização será muito pequena no início e aumentará ao longo do tempo.

Observe que o Service Fabric no Linux será conceitualmente equivalente ao que está disponível no Windows (exceto pelas informações específicas do sistema operacional e pelo suporte à linguagem de programação). Então, a maior parte da nossa [documentação existente](http://aka.ms/servicefabricdocs) se aplica e ajudará você a se familiarizar com a tecnologia.

## Próximas etapas

Conheça as estruturas de programação [Reliable Actors](service-fabric-reliable-actors-introduction.md) e [Reliable Services](service-fabric-reliable-services-introduction.md).

<!---HONumber=AcomDC_0406_2016-->