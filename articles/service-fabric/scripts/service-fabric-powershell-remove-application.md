---
title: Exemplo de script do Azure PowerShell – remover aplicativo de um cluster | Microsoft Docs
description: Exemplo de script do Azure PowerShell – remover um aplicativo de um cluster do Service Fabric.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 05edc6bce6744acd14dea2358663c4097922f2ce
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667448"
---
# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Remover um aplicativo de um cluster do Service Fabric

Esse script de exemplo exclui uma instância de aplicativo do Service Fabric em execução e cancela o registro do tipo e da versão de um aplicativo do cluster.  A exclusão da instância do aplicativo também exclui todas as instâncias de serviço em execução associadas a esse aplicativo. Personalize os parâmetros conforme necessário. 

Se necessário, instale o módulo Service Fabric do PowerShell com o [SDK do Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | Remove do cluster uma instância de aplicativo do Service Fabric em execução.  |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Cancela o registro de um tipo e uma versão de aplicativo do Service Fabric do cluster. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do PowerShell do Service Fabric, confira [Documentação do Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Mais exemplos do PowerShell para o Azure Service Fabric podem ser encontrados nos [exemplos do Azure PowerShell](../service-fabric-powershell-samples.md).
