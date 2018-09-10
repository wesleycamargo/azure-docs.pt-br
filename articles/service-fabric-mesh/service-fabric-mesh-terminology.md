---
title: Terminologia para Malha do Microsoft Azure Service Fabric | Microsoft Docs
description: Saiba mais sobre os termos comuns para Malha do Microsoft Azure Service Fabric.
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 07/12/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 7c3ce5571c54d6c613114ea49999e450934c8ff4
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136191"
---
# <a name="service-fabric-mesh-terminology"></a>Terminologia da Malha do Service Fabric

A Malha do Microsoft Azure Service Fabric é um serviço totalmente gerenciado que permite aos desenvolvedores implantar aplicativos de microsserviços sem gerenciar máquinas virtuais, armazenamento ou rede. Este artigo detalha a terminologia usada pela Malha do Microsoft Azure Service Fabric para compreender os termos usados na documentação.

## <a name="service-fabric"></a>Service Fabric

O Service Fabric é uma plataforma de sistemas distribuídos de software livre que facilita o empacotamento, a implantação e o gerenciamento de microsserviços escalonáveis e confiáveis. O Service Fabric é o orquestrador que fornece energia à Malha do Service Fabric. O Service Fabric fornece opções de como você pode compilar e executar os aplicativos de microsserviços. É possível usar qualquer estrutura para gravar os serviços e escolher onde executar o aplicativo a partir de várias opções de ambiente.

## <a name="deployment-and-application-models"></a>Implantação e modelos de aplicativos 

Para implantar os serviços será necessário descrever como deverão ser executados. O Service Fabric dá suporte a três diferentes modelos de implantação:

### <a name="resource-model"></a>Modelo de recursos
Os recursos são tudo o que pode ser implantado individualmente no Service Fabric, incluindo aplicativos, serviços, redes e volumes. Os recursos são definidos usando um arquivo YAML ou JSON usando o esquema de Modelo do Azure Resource Manager.

O modelo de recursos é a maneira mais simples de descrever os aplicativos do Service Fabric. O foco principal é a implantação e o gerenciamento simples de serviços de contêiner.

Os recursos podem ser implantados em qualquer lugar em que o Service Fabric execute.

### <a name="native-model"></a>Modelo nativo
O modelo de aplicativo nativo fornece aos aplicativos acesso de nível baixo completo ao Service Fabric. Os aplicativos e serviços são definidos como tipos registrados em arquivos de manifesto XML.

O modelo nativo dá suporte à estrutura de Reliable Services que fornece acesso às APIs de tempo de execução do Service Fabric e APIs de gerenciamento de cluster em C# e Java. O modelo nativo também dá suporte a executáveis e contêineres arbitrários.

O modelo nativo não tem suporte no ambiente de Malha do Service Fabric.

### <a name="docker-compose"></a>Docker Compose 
O [Docker Compose](https://docs.docker.com/compose/) é parte do projeto Docker. O Service Fabric dá suporte limitado para implantar aplicativos usando o modelo do Docker Compose.

## <a name="environments"></a>Ambientes

O Service Fabric é uma tecnologia de plataforma de software livre em que vários serviços e produtos diferentes são baseados. A Microsoft fornece as seguintes opções:

 - **Malha do Service Fabric**: um serviço totalmente gerenciado para executar aplicativos do Service Fabric no Microsoft Azure.
 - **Microsoft Azure Service Fabric**: a oferta de cluster do Service Fabric hospedada no Azure. Fornece integração entre o Service Fabric e a infraestrutura do Azure, juntamente com gerenciamento de configuração e upgrade de clusters do Service Fabric.
 - **Service Fabric independente**: um conjunto de ferramentas de configuração e instalação para [implantar clusters do Service Fabric em qualquer lugar](/azure/service-fabric/service-fabric-deploy-anywhere) (no local ou em qualquer provedor de nuvem). Não gerenciado pelo Azure.
 - **Cluster de desenvolvimento do Service Fabric**: fornece uma experiência de desenvolvimento local no Windows, Linux ou Mac para o desenvolvimento de aplicativos do Service Fabric.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Matriz de suporte do modelo de implantação, estrutura e ambiente
Diferentes ambientes possuem diferentes níveis de suporte para modelos de implantação e estrutura. A tabela a seguir descreve as combinações de modelo de implantação e estrutura com suporte.

| Tipo de aplicativo | Descrito por | Malha do Microsoft Azure Service Fabric | Clusters do Azure Service Fabric (qualquer SO)| Cluster local - Windows | Cluster local - Linux | Cluster local - Mac | Cluster independente (Windows)
|---|---|---|---|---|---|---|---|---|---|
| Aplicativos de Malha do Service Fabric | Modelo de recurso (YAML & JSON) | Com suporte |Sem suporte | Com suporte |Sem suporte | Sem suporte | Sem suporte |
|Aplicativos nativos do Service Fabric | Modelo de aplicativo nativo (XML) | Sem suporte| Com suporte|Com suporte|Com suporte|Com suporte|Com suporte|

A tabela a seguir descreve os diferentes modelos de aplicativos e as ferramentas existentes no Service Fabric.

| Tipo de aplicativo | Descrito por | Visual Studio 2017 | Visual Studio 2015 | Eclipse | Código VS | SFCTL | AZ CLI | Powershell
|---|---|---|---|---|---|---|---|---|---|
| Aplicativos de Malha do Service Fabric | Modelo de recurso (YAML & JSON) | Com suporte |Sem suporte |Sem suporte |Sem suporte |Sem suporte | Com suporte - apenas ambiente de Malha | Sem suporte
|Aplicativos nativos do Service Fabric | Modelo de aplicativo nativo (XML) | Com suporte| Com suporte|Com suporte|Com suporte|Com suporte|Com suporte|Com suporte|

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre Malha do Service Fabric, leia a visão geral:
- [Visão geral da Malha do Service Fabric](service-fabric-mesh-overview.md)
