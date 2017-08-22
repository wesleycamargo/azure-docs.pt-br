---
title: "Exemplo de script da CLI do Azure – remover aplicativo de um cluster"
description: "Exemplo de script da CLI do Azure – remover um aplicativo de um cluster do Service Fabric."
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
ms.date: 07/21/2017
ms.author: adegeo
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 141270c353d3fe7341dfad890162ed74495d48ac
ms.openlocfilehash: 77fa78199854d10c4897d56da12c8e359ae491f3
ms.contentlocale: pt-br
ms.lasthandoff: 07/25/2017

---

# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Remover um aplicativo de um cluster do Service Fabric

Esse script de exemplo exclui uma instância de aplicativo do Service Fabric em execução e cancela o registro do tipo e da versão de um aplicativo do cluster.  A exclusão da instância do aplicativo também exclui todas as instâncias de serviço em execução associadas a esse aplicativo. Em seguida, os arquivos de aplicativo são excluídos do repositório de imagens. 

Se necessário, instale a [CLI do Azure](../service-fabric-azure-cli-2-0.md).

## <a name="sample-script"></a>Script de exemplo

[!code-sh[principal](../../../cli_scripts/service-fabric/remove-application/remove-application.sh "Remover um aplicativo de um cluster")]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [sf cluster select](/cli/azure/sf/cluster#select) | Seleciona o cluster com o qual trabalhar. |
| [sf application delete](/cli/azure/sf/application#delete) | Exclui a instância do aplicativo do cluster. |
| [sf application unprovision](/cli/azure/sf/application#unprovision) | Cancela o registro de um tipo e uma versão de aplicativo do Service Fabric do cluster.|
| [sf application package-delete](/cli/azure/sf/application#package-delete) | Remove do repositório de imagens um pacote de aplicativos do Service Fabric. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais, confira a [documentação da CLI do Azure](../service-fabric-azure-cli-2-0.md).

Mais exemplos da CLI do Azure para o Azure Service Fabric podem ser encontrados nos [exemplos da CLI do Azure](../samples-cli.md).

