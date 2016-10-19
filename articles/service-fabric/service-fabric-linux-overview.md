<properties
   pageTitle="Azure Service Fabric no Linux | Microsoft Azure"
   description="Os clusters do Service Fabric dão suporte para o Linux e para o Java, o que significa que você poderá implantar e hospedar aplicativos do Service Fabric escritos em Java e em C# no Linux."
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
   ms.date="09/26/2016"
   ms.author="SubramaR"/>

# Service Fabric no Linux

A visualização do Service Fabric no Linux permite criar, implantar e gerenciar aplicativos altamente disponíveis e altamente escalonáveis no Linux, assim como você faria no Windows. As estruturas do Service Fabric (Reliable Services e Reliable Actors) estão disponíveis em Java no Linux, além de em C# (.NET Core). Você também pode compilar os [serviços executáveis convidados](service-fabric-deploy-existing-app.md) com qualquer linguagem ou estrutura. Além disso, a preview também dá suporte à orquestração de contêineres de Docker. Os contêineres de Docker podem executar arquivos executáveis de convidado ou serviços nativos do Service Fabric, que usam as estruturas do Service Fabric.

O Service Fabric no Linux é conceitualmente equivalente ao Service Fabric no Windows (exceto pelas especificações de sistema operacional e pelo suporte à linguagem de programação). Portanto, a maior parte da nossa [documentação existente](http://aka.ms/servicefabricdocs) destina-se a ajudar você a se familiarizar com a tecnologia.

> [AZURE.VIDEO service-fabric-linux-preview]

## Sistemas operacionais e linguagens de programação com suporte

A visualização limitada oferece suporte à criação de clusters de desenvolvimento de uma caixa, além de clusters de vários computadores no Azure executando o Ubuntu Server 16.04. A preview dá suporte para Reliable Actors e para as estruturas de serviços sem monitoração de estado confiáveis em Java e em C#, além dos executáveis de convidado e da orquestração de contêineres de Docker.

>[AZURE.NOTE] O Linux ainda não dá suporte às Reliable Collections. Também não há suporte para clusters autônomos – somente os clusters de uma caixa e de vários computadores Linux do Azure têm suporte na preview.

## Ferramentas com suporte

A preview dá suporte à interação com o cluster por meio da CLI do Azure. Para desenvolvedores Java, a integração com o Eclipse e o Yeoman é fornecida com o Eclipse com suporte no Linux e no OSX. A integração OSX usa uma VM do Linux nos bastidores por meio do Vagrant. Para desenvolvedores C#, a integração com o Yeoman é fornecida para gerar modelos de aplicativos.

## Próximas etapas


1. Conheça as estruturas de programação [Reliable Actors](service-fabric-reliable-actors-introduction.md) e [Reliable Services](service-fabric-reliable-services-introduction.md).

2. [Preparar seu ambiente de desenvolvimento no Linux](service-fabric-get-started-linux.md)

3. [Preparar seu ambiente de desenvolvimento no OSX](service-fabric-get-started-mac.md)

4. [Criar seu primeiro aplicativo do Java do Service Fabric no Linux](service-fabric-create-your-first-linux-application-with-java.md)

<!---HONumber=AcomDC_0928_2016-->