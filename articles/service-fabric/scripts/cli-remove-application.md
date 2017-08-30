---
title: "Script da CLI do Azure Service Fabric – exemplo de remoção"
description: Remova um aplicativo de um cluster do Azure Service Fabric usando a CLI do Azure Service Fabric
services: service-fabric
documentationcenter: 
author: thraka
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: adegeo
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 6ea52da6698647bb21d8f4c9a47a73bd5eef2a29
ms.contentlocale: pt-br
ms.lasthandoff: 08/24/2017

---

# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Remover um aplicativo de um cluster do Service Fabric

Esse script de exemplo exclui uma instância de aplicativo do Service Fabric em execução e cancela o registro do tipo e da versão de um aplicativo do cluster.  A exclusão da instância do aplicativo também exclui todas as instâncias de serviço em execução associadas a esse aplicativo. Em seguida, os arquivos de aplicativo são excluídos do repositório de imagens. 

Se necessário, instale a [CLI do Service Fabric](../service-fabric-cli.md).

## <a name="sample-script"></a>Script de exemplo

[!code-sh[principal](../../../cli_scripts/service-fabric/remove-application/remove-application.sh "Remover um aplicativo de um cluster")]

## <a name="next-steps"></a>Próximas etapas

Para saber mais, confira a [Documentação da CLI do Service Fabric](../service-fabric-cli.md).

Mais exemplos da CLI do Service Fabric para o Azure Service Fabric podem ser encontrados em [Exemplos da CLI do Service Fabric](../samples-cli.md).

