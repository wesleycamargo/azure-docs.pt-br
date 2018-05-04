---
title: Exibir logs de contêineres no Azure Service Fabric | Microsoft Docs
description: Descreve como exibir os logs de contêineres para serviços de contêiner do Service Fabric em execução usando o Service Fabric Explorer.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/09/2018
ms.author: ryanwi
ms.openlocfilehash: 48ee54460454368deef44c8f84624e32856efafa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Exibir logs para um serviço de contêiner do Service Fabric
O Microsoft Azure Service Fabric é um orquestrador de contêineres e dá suporte a [contêineres de Linux e Windows](service-fabric-containers-overview.md).  Este artigo descreve como exibir logs de contêiner de um serviço de contêiner em execução para que você possa diagnosticar e solucionar problemas.

## <a name="access-container-logs"></a>Acessar logs do contêiner
Logs de contêiner podem ser acessados usando [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  Em um navegador da web, abra o Service Fabric Explorer do ponto de extremidade de gerenciamento do cluster navegando até [http://mycluster.region.cloudapp.azure.com:19080/Explorer](http://mycluster.region.cloudapp.azure.com:19080/Explorer).  

Logs de contêiner estão localizados no nó de cluster que está executando a instância de serviço de contêiner. Por exemplo, obtenha os logs do contêiner do front-end da Web do [aplicativo de exemplo do Linux Voting](service-fabric-quickstart-containers-linux.md). No modo de exibição de árvore, expanda **Cluster**>**Aplicativos**>**VotingType**>**fabric:/Voting/azurevotefront**.  Em seguida, expanda a partição (d1aa737e-f22a-e347-be16-eec90be24bc1, neste exemplo) e veja que o contêiner está em execução no nó de cluster *_lnxvm_0*.

No modo de exibição de árvore, localize o pacote de código no nó *_lnxvm_0* expandindo **Nós**>**_lnxvm_0**>**fabric:/Voting** > **azurevotfrontPkg**>**Pacotes de códigos**>**código**.  Selecione a opção **Logs do Contêiner** para exibir os logs do contêiner.

![Plataforma Service Fabric][Image1]


## <a name="next-steps"></a>Próximas etapas
- Navegue pelo [tutorial Criar um aplicativo de contêiner do Linux](service-fabric-tutorial-create-container-images.md).
- Saiba mais sobre o [Service Fabric e contêineres](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
