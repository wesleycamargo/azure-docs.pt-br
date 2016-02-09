<properties
   pageTitle="Modelos de programação da Service Fabric | Microsoft Azure"
   description="A Service Fabric oferece duas estruturas para criação de serviços: a estrutura de atores e a estrutura de serviços. Elas oferecem vantagens e desvantagens distintas com relação à simplicidade e ao controle."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/26/2016"
   ms.author="seanmck"/>

# Escolher uma estrutura para seu serviço

O Service Fabric do Azure oferece duas estruturas de alto nível para criar serviços: a API de Reliable Services e a API de Reliable Actors. Embora ambas baseiam-se no mesmo núcleo de malha do serviço, elas fazem diferentes compensações entre simplicidade e flexibilidade em termos de simultaneidade, particionamento e comunicação. É útil entender ambos os modelos para que você possa escolher a estrutura apropriada para um determinado serviço no seu aplicativo.

## Comparando as APIs de Reliable Actors e as APIs de Reliable Services

|**Quando escolher a API de Reliable Actors**|**Quando escolher a API de Reliable Services**|
|-----------------------|--------------------------|
|Seu problema de espaço envolve um grande número (+ 1.000) de pequenas unidades independentes de estado e lógica.|Você precisa manter a lógica em vários componentes.|
|Você deseja trabalhar com objetos de thread único que não exigem interação externa significativa.|Você deseja usar Coleções Confiáveis (como Dicionário do .NET e Fila Confiável) para armazenar e gerenciar o estado.|
|Você deseja que a plataforma gerencie a comunicação para você.|Você deseja gerenciar a comunicação e controlar o esquema de particionamento para o seu serviço.|

Lembre-se que é perfeitamente aceitável usar diferentes estruturas para serviços diferentes em seu aplicativo. Por exemplo, você pode ter um serviço com estado que agrega dados gerados por um número de atores.

## Próximas etapas

- [Saiba mais sobre as APIs de Reliable Actors](service-fabric-reliable-actors-introduction.md)
- [Saiba mais sobre as APIs de Reliable Services](../Service-Fabric/service-fabric-reliable-services-introduction.md)

<!---HONumber=AcomDC_0128_2016-->