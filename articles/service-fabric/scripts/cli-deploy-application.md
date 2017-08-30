---
title: "Exemplo de implantação de script CLI do Azure Service Fabric"
description: Implantar um aplicativo em um cluster do Azure Service Fabric usando a CLI do Azure Service Fabric
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
ms.date: 08/22/2017
ms.author: adegeo
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 74d54b314e81dc260e800e32d6c3edd5215c82b4
ms.contentlocale: pt-br
ms.lasthandoff: 08/24/2017

---

# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Implantar um aplicativo em um cluster do Service Fabric

Esse script de exemplo copia um pacote de aplicativos em um repositório de imagens de cluster, registra o tipo de aplicativo no cluster e cria uma instância do aplicativo com base no tipo de aplicativo. Todos os serviços padrão também são criados nesse momento.

Se necessário, instale a [CLI do Service Fabric](../service-fabric-cli.md).

## <a name="sample-script"></a>Script de exemplo

[!code-sh[principal](../../../cli_scripts/service-fabric/deploy-application/deploy-application.sh "Implantar um aplicativo em um cluster")]

## <a name="clean-up-deployment"></a>Limpar implantação

Quando terminar, o script de [remoção](cli-remove-application.md) poderá ser usado para remover o aplicativo. O script de remoção exclui a instância do aplicativo, cancela o registro do tipo de aplicativo e exclui o pacote de aplicativos do repositório de imagens.

## <a name="next-steps"></a>Próximas etapas

Para saber mais, confira a [documentação da CLI do Service Fabric](../service-fabric-cli.md).

Mais exemplos de CLI do Service Fabric para o Azure Service Fabric podem ser encontrados em [Exemplos de CLI do Service Fabric](../samples-cli.md).

