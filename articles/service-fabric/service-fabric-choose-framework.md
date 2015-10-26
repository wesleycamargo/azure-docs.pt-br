<properties
   pageTitle="Escolhendo uma estrutura do Service Fabric | Microsoft Azure"
   description="A malha de serviço oferece duas estruturas de alto nível para criar serviços: as APIs de atores e as APIs de serviços. Entender o valor de cada ajudará você a tomar as decisões corretas em relação à arquitetura do seu aplicativo."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/13/2015"
   ms.author="seanmck"/>

# Escolher uma estrutura para seu serviço

A malha de serviço oferece duas estruturas de alto nível para criar serviços: as APIs de atores confiáveis e as APIs de serviços confiáveis. Embora ambas baseiam-se no mesmo núcleo de malha do serviço, elas fazem diferentes compensações entre simplicidade e flexibilidade em termos de simultaneidade, particionamento e comunicação. É útil entender os dois modelos para que você possa escolher a estrutura apropriada para um serviço específico dentro de seu aplicativo.

## Comparando as APIs de atores confiáveis e as APIs de serviços confiáveis

|**APIs de atores confiáveis**|**APIs de serviços confiáveis**|
|-----------------------|--------------------------|
|Problema de espaço envolve muitas pequenas unidades independentes de estado e lógica|Você precisa manter a lógica em vários componentes|
|Você deseja trabalhar com objetos de thread único e, ainda assim, poder dimensionar e manter a consistência|Você deseja usar coleções confiáveis (como dicionário do .NET e fila) para armazenar e gerenciar seu estado|
|Você deseja que a estrutura gerencie a simultaneidade e a granularidade do estado|Você deseja controlar a granularidade e simultaneidade do estado|
|Você deseja que a plataforma gerencie a comunicação para você|Você deseja gerenciar a comunicação e controlar o esquema de particionamento para o seu serviço|

Lembre-se que é perfeitamente aceitável usar diferentes estruturas para serviços diferentes em seu aplicativo. Por exemplo, você pode ter um serviço com monitoração de estado que agrega dados gerados por um número de atores.

## Próximas etapas

- [Saiba mais sobre as APIs de atores confiáveis](service-fabric-reliable-actors-introduction.md)
- [Saiba mais sobre as APIs de serviços confiáveis](../Service-Fabric/service-fabric-reliable-services-introduction.md)

<!---HONumber=Oct15_HO3-->