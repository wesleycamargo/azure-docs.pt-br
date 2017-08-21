---
title: "Exemplo de script do Azure PowerShell – adicionar certificado de aplicativo a um cluster | Microsoft Docs"
description: "Exemplo de script do Azure PowerShell – adicionar um certificado de aplicativo a um cluster do Service Fabric."
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: ryanwi
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: 3212944a8bd882ced86e841944ac60bd1845e3d1
ms.contentlocale: pt-br
ms.lasthandoff: 07/15/2017

---

# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Adicionar um certificado de aplicativo a um cluster do Service Fabric

Esse script de exemplo cria um certificado autoassinado no Azure Key Vault especificado e o instala em todos os nós do cluster do Service Fabric. O certificado também é baixado em uma pasta local. O nome do certificado baixado é igual ao nome do certificado no cofre de chaves. Personalize os parâmetros conforme necessário.

Se necessário, instale o Azure PowerShell usando as instruções encontradas no [Guia do Azure PowerShell](/powershell/azure/overview) e, em seguida, execute `Login-AzureRmAccount` para criar uma conexão com o Azure. 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[principal](../../../powershell_scripts/service-fabric/add-application-certificate/add-new-application-certificate.ps1 "Adicionar um certificado de aplicativo a um cluster")]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos: cada comando na tabela contém links para a respectiva documentação específica.

| Command | Observações |
|---|---|
| [Add-AzureRmServiceFabricApplicationCertificate](/powershell/module/azurerm.servicefabric/Add-AzureRmServiceFabricApplicationCertificate) | Adiciona um novo certificado de aplicativo ao conjunto de dimensionamento de máquinas virtuais que compõem o cluster.  |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Mais exemplos do Azure PowerShell para o Azure Service Fabric podem ser encontrados nos [exemplos do Azure PowerShell](../service-fabric-powershell-samples.md).

