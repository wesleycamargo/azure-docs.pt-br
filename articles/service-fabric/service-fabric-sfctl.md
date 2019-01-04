---
title: CLI do Azure Service Fabric - sfctl | Microsoft Docs
description: Descreve os comandos do sfctl da CLI do Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: cb48cff488c4f0bcd3c4a742f5d9922ec59a36b6
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269848"
---
# <a name="sfctl"></a>sfctl
Comandos para gerenciar clusters e entidades do Service Fabric. Esta versão é compatível com o tempo de execução do Service Fabric 6.4.

Os comandos seguem o padrão de substantivo-verbo. Confira subgrupos para saber mais.

## <a name="subgroups"></a>Subgrupos
|Subgrupo|DESCRIÇÃO|
| --- | --- |
| [application](service-fabric-sfctl-application.md) | Criar, excluir e gerenciar aplicativos e tipos de aplicativo. |
| [chaos](service-fabric-sfctl-chaos.md) | Iniciar, parar e emitir relatórios sobre o serviço de teste de caos. |
| [cluster](service-fabric-sfctl-cluster.md) | Selecionar, gerenciar e operar clusters do Service Fabric. |
| [compose](service-fabric-sfctl-compose.md) | Criar, excluir e gerenciar aplicativos do Docker Compose. |
| [contêiner](service-fabric-sfctl-container.md) | Execute comandos relacionados ao contêiner em um nó de cluster. |
| [is](service-fabric-sfctl-is.md) | Consultar e enviar comandos para o serviço de infraestrutura. |
| [malha](service-fabric-sfctl-mesh.md) | Excluir e gerenciar aplicativos de Malha do Service Fabric. |
| [node](service-fabric-sfctl-node.md) | Gerenciar os nós que formam um cluster. |
| [partition](service-fabric-sfctl-partition.md) | Consultar e gerenciar partições para qualquer serviço. |
| [property](service-fabric-sfctl-property.md) | Propriedades de armazenamento e a consulta em nomes do Service Fabric. |
| [replica](service-fabric-sfctl-replica.md) | Gerenciar as réplicas que pertencem a partições de serviço. |
| [rpm](service-fabric-sfctl-rpm.md) | Consultar e enviar comandos para o serviço de gerenciador de reparo. |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | Gerencie clusters autônomos do Service Fabric. |
| [service](service-fabric-sfctl-service.md) | Crie, exclua e gerencie o serviço, os tipos de serviço e os pacotes de serviço. |
| [configurações](service-fabric-sfctl-settings.md) | Defina as configurações locais para essa instância do sfctl. |
| [store](service-fabric-sfctl-store.md) | Execute operações de arquivo de nível básico no repositório de imagens do cluster. |

## <a name="next-steps"></a>Próximas etapas
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).