---
title: "Exemplo de script da CLI do Azure – implantar aplicativo em um cluster"
description: "Exemplo de script da CLI do Azure – implantar um aplicativo em um cluster do Service Fabric."
services: service-fabric
documentationcenter: 
author: Thraka
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
ms.openlocfilehash: de6ec4378a05656ecefefa7d5994a4f5dad404ba
ms.contentlocale: pt-br
ms.lasthandoff: 07/25/2017

---

# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Implantar um aplicativo em um cluster do Service Fabric

Esse script de exemplo copia um pacote de aplicativos em um repositório de imagens de cluster, registra o tipo de aplicativo no cluster e cria uma instância do aplicativo com base no tipo de aplicativo.  Se algum serviço padrão tiver sido definido no manifesto do aplicativo do tipo de aplicativo de destino, ele também será criado nesse momento.

Se necessário, instale a [CLI do Azure](../service-fabric-azure-cli-2-0.md).

## <a name="sample-script"></a>Script de exemplo

[!code-sh[principal](../../../cli_scripts/service-fabric/deploy-application/deploy-application.sh "Implantar um aplicativo em um cluster")]

## <a name="clean-up-deployment"></a>Limpar implantação 

Depois que o exemplo de script foi executado, o script em [Remover um aplicativo](cli-remove-application.md) pode ser usado para remover a instância do aplicativo, cancelar o registro do tipo de aplicativo e excluir o pacote de aplicativos do repositório de imagens.

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.


| Command | Observações |
|---|---|
| [sf cluster select](/cli/azure/sf/cluster#select) | Seleciona o cluster com o qual trabalhar. |
| [sf application upload](/cli/azure/sf/application#upload) | Carrega os arquivos de aplicativo e os manifestos. |
| [sf application provision](/cli/azure/sf/application#provision) | Registra o aplicativo no cluster.|
| [sf application create](/cli/azure/sf/application#create) | Cria uma instância do aplicativo e implanta todos os serviços definidos nos nós. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais, confira a [documentação da CLI do Azure](../service-fabric-azure-cli-2-0.md).

Mais exemplos da CLI do Azure para o Azure Service Fabric podem ser encontrados nos [exemplos da CLI do Azure](../samples-cli.md).

