---
title: CLI do Azure Service Fabric - sfctl | Microsoft Docs
description: Descreve os comandos do sfctl da CLI do Service Fabric.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/22/2017
ms.author: ryanwi
ms.openlocfilehash: 23adc7fa6ee53d1c5edd10b2772f8825dbcef2a8
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2018
---
# <a name="sfctl"></a>sfctl 
Comandos para gerenciar clusters e entidades do Service Fabric. Esta versão é compatível com o tempo de execução do Service Fabric 6.0. Os comandos seguem o padrão de substantivo-verbo. Consulte os seguintes subgrupos para saber mais.

## <a name="subgroups"></a>Subgrupos
|Subgrupo|DESCRIÇÃO|
| --- | --- |
| [application](service-fabric-sfctl-application.md)| Criar, excluir e gerenciar aplicativos e tipos de aplicativo.|
| [chaos](service-fabric-sfctl-chaos.md)   | Iniciar, parar e emitir relatórios sobre o serviço de teste de caos.|
| [cluster](service-fabric-sfctl-cluster.md) | Selecionar, gerenciar e operar clusters do Service Fabric.|
| [compose](service-fabric-sfctl-compose.md) | Criar, excluir e gerenciar aplicativos do Docker Compose.|
| [is](service-fabric-sfctl-is.md)      | Consultar e enviar comandos para o serviço de infraestrutura.|
| [node](service-fabric-sfctl-node.md)    | Gerenciar os nós que formam um cluster.|
| [partition](service-fabric-sfctl-partition.md)  | Consultar e gerenciar partições para qualquer serviço.|
| [rpm](service-fabric-sfctl-rpm.md)        | Consultar e enviar comandos para o serviço de gerenciador de reparo.|
| [replica](service-fabric-sfctl-replica.md) | Gerenciar as réplicas que pertencem a partições de serviço.|
| [service](service-fabric-sfctl-service.md) | Criar, excluir e gerenciar o serviço, os tipos de serviço e os pacotes de serviço.|
| [store](service-fabric-sfctl-store.md)   | Execute operações de arquivo de nível básico no repositório de imagens do cluster.|

## <a name="next-steps"></a>Próximas etapas
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).