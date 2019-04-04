---
title: Exemplo de script do Azure PowerShell - Adicionar uma regra de grupo de segurança de rede | Microsoft Docs
description: Exemplo de Script do Microsoft Azure PowerShell - Adiciona um grupo de segurança de rede para permitir o tráfego de entrada em uma porta específica.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 11/28/2017
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 526a568bbcd7249e4f8917e8cdd82a0de71bfb0a
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664991"
---
# <a name="add-an-inbound-network-security-group-rule"></a>Adicionar uma regra de grupo de segurança de rede de entrada

Esse exemplo de script cria uma regra de grupo de segurança de rede para permitir o tráfego de entrada na porta 8081.  O script obtém o recurso `Microsoft.Network/networkSecurityGroups` em que o cluster está localizado, cria uma nova regra de configuração de segurança de rede e atualiza o grupo de segurança de rede. Personalize os parâmetros conforme necessário.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se necessário, instale o Azure PowerShell usando as instruções no [Guia do Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Obtém o recurso `Microsoft.Network/networkSecurityGroups`. |
|[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)| Obtém o grupo de segurança de rede por nome.|
|[Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Adiciona uma configuração de regra de segurança de rede a um grupo de segurança de rede. |
|[Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)| Define o estado de meta para um grupo de segurança de rede.|

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).
