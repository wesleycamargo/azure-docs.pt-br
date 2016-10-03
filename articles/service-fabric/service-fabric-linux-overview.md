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
   ms.date="09/14/2016"
   ms.author="SubramaR"/>

# Service Fabric no Linux

A visualização será disponibilizada para o público geral em 26 de setembro conforme anunciado [na postagem deste blog](https://azure.microsoft.com/blog/service-fabric-on-linux-support-available-this-month/). A visualização do Service Fabric no Linux permite criar, implantar e gerenciar aplicativos altamente disponíveis e altamente escalonáveis no Linux, assim como você faria no Windows. Além disso, as estruturas de alto nível do Service Fabric (Reliable Services e Reliable Actors) agora estão disponíveis em Java no Linux.

> [AZURE.VIDEO service-fabric-linux-preview]

## Sistemas operacionais e linguagens de programação com suporte

A visualização limitada oferece suporte à criação de clusters de desenvolvimento de uma caixa, além de clusters de vários computadores no Azure executando o Ubuntu Server 16.04.

Você pode criar [serviços executáveis convidados](service-fabric-deploy-existing-app.md) com qualquer linguagem ou estrutura. Também é possível usar Java ou C# para criar serviços com base nas estruturas Reliable Services e Reliable Actor, além de orquestrar contêineres do Docker.

>[AZURE.NOTE] O Linux ainda não dá suporte às Reliable Collections.

O Service Fabric no Linux é conceitualmente equivalente ao Service Fabric no Windows (exceto pelas especificações de sistema operacional e pelo suporte à linguagem de programação). Portanto, a maior parte da nossa [documentação existente](http://aka.ms/servicefabricdocs) destina-se a ajudar você a se familiarizar com a tecnologia.

## Próximas etapas

Conheça as estruturas de programação [Reliable Actors](service-fabric-reliable-actors-introduction.md) e [Reliable Services](service-fabric-reliable-services-introduction.md).

<!---HONumber=AcomDC_0921_2016-->