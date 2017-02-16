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
ms.date: 1/05/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: d7aa8568dd6fdd806d8ad70e408f108c722ec1ce
ms.openlocfilehash: b56d75d49e0b95025dd1a0bae532f677958eac8d


---
# <a name="service-fabric-programming-model-overview"></a>Visão geral do modelo de programação do Service Fabric
O Service Fabric oferece várias maneiras de escrever e gerenciar seus serviços. Os serviços podem escolher usar as APIs do Service Fabric para se beneficiarem dos recursos e estruturas de aplicativo da plataforma ou podem simplesmente ser um programa executável compilado escrito em qualquer linguagem e simplesmente hospedado em um cluster do Service Fabric.

## <a name="guest-executable"></a>Executável Convidado
Um executável convidado é um executável arbitrário escrito em qualquer linguagem e, portanto, você pode hospedar seus aplicativos existentes em um cluster do Service Fabric. Um executável convidado pode ser empacotado em um aplicativo e hospedado juntamente com outros serviços. O Service Fabric trata da coordenação e do gerenciamento da execução simples do executável, garantindo que ele permaneça ativo e em execução de acordo com a descrição do serviço. No entanto, como os executáveis convidados não se integram diretamente com as APIs do Service Fabric, eles não se beneficiam do conjunto completo de recursos que a plataforma oferece, como a integridade personalizada e relatórios de carga, registro do ponto de extremidade de serviço e computação com estado.

Comece a usar executáveis convidados implantando seu primeiro [aplicativo executável convidado](service-fabric-deploy-existing-app.md).

## <a name="reliable-services"></a>Reliable Services
O Reliable Services é uma estrutura leve para o desenvolvimento de serviços que se integram com a plataforma do Service Fabric e aproveitam o conjunto completo de recursos de plataforma. O Reliable Services fornece um conjunto mínimo de APIs que permite que o tempo de execução do Service Fabric gerencie o ciclo de vida de seus serviços e que seus serviços interajam com o tempo de execução. A estrutura de aplicativo é mínima, dando a você total controle sobre as opções de design e implementação e pode ser usada para hospedar qualquer outra estrutura de aplicativo, como ASP.NET MVC ou Web API.

O Reliable Services pode ser sem estado, semelhante à maioria das plataformas de serviço, como servidores Web ou Funções de Trabalho nos Serviços de Nuvem do Azure, nas quais todas as instâncias do serviço são iguais e o estado é persistido em uma solução externa, como Banco de Dados do Azure ou Armazenamento de Tabelas do Azure.

O Reliable Services também pode ser com estado, exclusivo do Service Fabric, em que o estado é persistido diretamente no próprio serviço usando Coleções Confiáveis. O estado fica altamente disponível por meio de replicação e é distribuído por meio de particionamento, tudo gerenciado automaticamente pelo Service Fabric.

[Saiba mais sobre os Reliable Services](service-fabric-reliable-services-introduction.md) ou comece [escrevendo o seu primeiro Reliable Service](service-fabric-reliable-services-quick-start.md).

## <a name="reliable-actors"></a>Reliable Actors
Criada com base no Reliable Services, a estrutura Reliable Actor é uma estrutura de aplicativo que implementa o padrão Ator Virtual baseado no padrão de design de ator. A estrutura Reliable Actor usa unidades independentes de computação e de estado com execução single-threaded chamadas atores. A estrutura Reliable Actor fornece comunicação interna para atores e configurações de expansão e persistência de estado predefinido.

Como o Reliable Actors em si é uma estrutura de aplicativo baseada no Reliable Services, ele é totalmente integrado com a plataforma Service Fabric e se beneficia do conjunto completo de recursos oferecidos pela plataforma.

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre Reliable Actors](service-fabric-reliable-actors-introduction.md) ou comece [escrevendo seu primeiro serviço de Reliable Actors](service-fabric-reliable-actors-get-started.md)
[Saiba mais sobre como colocar seus serviços em contêiner no Windows ou Linux](service-fabric-deploy-container.md)




<!--HONumber=Jan17_HO2-->


