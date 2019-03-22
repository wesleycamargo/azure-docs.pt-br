---
title: Saiba mais sobre as versões de cluster do Service Fabric do Azure | Microsoft Docs
description: Versões de cluster com suporte do Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/24/2018
ms.author: aljo
ms.openlocfilehash: de5522e68d1329ce2b80a4d3c7045d38c13169e5
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/01/2019
ms.locfileid: "57191640"
---
# <a name="supported-service-fabric-versions"></a>Versões do Service Fabric com suporte

Verifique se o cluster sempre executa uma versão do Service Fabric com suporte. Quando anunciamos o lançamento de uma nova versão do Service Fabric, a versão anterior é marcada para o fim do suporte após um mínimo de 60 dias a partir da data do comunicado. As novas versões são anunciadas [no blog da equipe do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).

Consulte os documentos a seguir para obter detalhes sobre como manter o cluster executando uma versão do Service Fabric com suporte.

- [Atualizar a versão do Service Fabric em um cluster do Azure](service-fabric-cluster-upgrade.md)
- [Atualizar a versão do Service Fabric em um cluster do windows server autônomo](service-fabric-cluster-upgrade-windows-server.md)

Veja a seguir a lista das versões do Service Fabric com suporte e as datas de fim de suporte.

| **Tempo de execução do Service Fabric no cluster** | **Permite a atualização diretamente da versão do cluster** |**Compatível com SDK/versões do pacote NuGet** | **Data de fim do suporte** |
| --- | --- |--- | --- |
| Todas as versões de cluster anteriores a 5.3.121 | 5.1.158* |Menor ou igual à versão 2.3 |20 de janeiro de 2017 |
| 5.3.* | 5.1.158.* |Menor ou igual à versão 2.3 |24 de fevereiro de 2017 |
| 5.4.* | 5.1.158.* |Menor ou igual à versão 2.4 |10 de maio de 2017       |
| 5.5.* | 5.4.164.* |Menor ou igual à versão 2.5 |10 de agosto de 2017    |
| 5.6.* | 5.4.164.* |Menor ou igual à versão 2.6 |13 de outubro de 2017   |
| 5.7.* | 5.4.164.* |Menor ou igual à versão 2.7 |15 de dezembro de 2017  |
| 6.0.* | 5.6.205.* |Menor ou igual à versão 2.8 |30 de março de 2018     |
| 6.1.* | 5.7.221.* |Menor ou igual à versão 3.0 |15 de julho de 2018      |
| 6.2.* | 6.0.232.* |Menor ou igual à versão 3.1 |26 de outubro de 2018   |
| 6.3.* | 6.1.480.* |Menor ou igual à versão 3.2 |Março de 31,2019  |
| 6.4.* | 6.2.301.* |Menor ou igual a versão 3.3 |Versão atual e, portanto, sem data de término |