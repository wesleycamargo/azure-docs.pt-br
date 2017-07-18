---
title: "Visão geral do modelo de programação do Service Fabric | Microsoft Docs"
description: "A Service Fabric oferece duas estruturas para criação de serviços: a estrutura de atores e a estrutura de serviços. Elas oferecem vantagens e desvantagens distintas com relação à simplicidade e ao controle."
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: vturecek
ms.assetid: 974b2614-014e-4587-a947-28fcef28b382
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/02/2017
ms.author: vturecek
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 102ede8d2aafaf485a5212faad47de6781d84578
ms.contentlocale: pt-br
ms.lasthandoff: 07/06/2017


---
# <a name="service-fabric-programming-model-overview"></a>Visão geral do modelo de programação do Service Fabric
O Service Fabric oferece várias maneiras de escrever e gerenciar seus serviços. Os serviços podem escolher usar as APIs do Service Fabric para se beneficiarem dos recursos e estruturas de aplicativo da plataforma. Os serviços também podem ser um programa executável compilado, escrito em qualquer linguagem ou código executado em um contêiner simplesmente hospedado em um cluster do Service Fabric.

### <a name="guest-executables"></a>Executáveis de convidado
Um [executável convidado](service-fabric-deploy-existing-app.md) é um executável existente e arbitrário (escrito em qualquer linguagem), hospedado em um cluster do Service Fabric junto com outros serviços. Os executáveis convidados não são integrados diretamente às APIs do Service Fabric. No entanto, eles ainda se beneficiam dos recursos que a plataforma oferece, como relatórios personalizados de integridade e carregamento e a capacidade de descoberta de serviço com a chamada às APIs REST. Eles também têm suporte completo do ciclo de vida do aplicativo.

Comece a usar executáveis convidados implantando seu primeiro [aplicativo executável convidado](service-fabric-deploy-existing-app.md).

### <a name="containers"></a>Contêineres
Por padrão, o Service Fabric implanta e ativa esses serviços como processos. O Service Fabric também pode implantar serviços em [contêineres](service-fabric-containers-overview.md). O Service Fabric dá suporte à implantação de contêineres do Linux e do Windows no Windows Server 2016. Implante aplicativos existentes, serviços sem estado ou serviços com estado em contêineres e combine serviços em processos e serviços em contêineres no mesmo aplicativo.

## <a name="reliable-services"></a>Reliable Services
O Reliable Services é uma estrutura leve para o desenvolvimento de serviços que se integram com a plataforma do Service Fabric e aproveitam o conjunto completo de recursos de plataforma. O Reliable Services fornece um conjunto mínimo de APIs que permite que o tempo de execução do Service Fabric gerencie o ciclo de vida de seus serviços e que seus serviços interajam com o tempo de execução. A estrutura do aplicativo é mínima, dando a você total controle sobre as opções de design e implementação e pode ser usada para hospedar qualquer outra estrutura de aplicativo, como ASP.NET Core.

Os Reliable Services podem ser sem estado, semelhantes à maioria das plataformas de serviço, como servidores Web, nas quais todas as instâncias do serviço são criadas de forma igual e o estado é persistido em uma solução externa, como o BD do Azure ou o Armazenamento de Tabelas do Azure.

O Reliable Services também pode ser com estado, exclusivo do Service Fabric, em que o estado é persistido diretamente no próprio serviço usando Coleções Confiáveis. O estado fica altamente disponível por meio de replicação e é distribuído por meio de particionamento, tudo gerenciado automaticamente pelo Service Fabric.

[Saiba mais sobre os Reliable Services](service-fabric-reliable-services-introduction.md) ou comece [escrevendo o seu primeiro Reliable Service](service-fabric-reliable-services-quick-start.md).

## <a name="reliable-actors"></a>Reliable Actors
Criada com base no Reliable Services, a estrutura Reliable Actor é uma estrutura de aplicativo que implementa o padrão Ator Virtual baseado no padrão de design de ator. A estrutura Reliable Actor usa unidades independentes de computação e de estado com execução single-threaded chamadas atores. A estrutura Reliable Actor fornece comunicação interna para atores e configurações de expansão e persistência de estado predefinido.

Como o Reliable Actors em si é uma estrutura de aplicativo baseada no Reliable Services, ele é totalmente integrado com a plataforma Service Fabric e se beneficia do conjunto completo de recursos oferecidos pela plataforma.

### <a name="aspnet-core"></a>ASP.NET Core
O Service Fabric é integrado ao [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) para a criação de aplicativos Web e de API. 

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre os Reliable Actors](service-fabric-reliable-actors-introduction.md) ou comece [escrevendo seu primeiro serviço dos Reliable Actors](service-fabric-reliable-actors-get-started.md)
[Saiba mais sobre como colocar seus serviços em contêineres no Windows ou no Linux](service-fabric-deploy-container.md)
[Criar um serviço de front-end usando o ASP.NET Core](service-fabric-add-a-web-frontend.md)



