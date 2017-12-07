---
title: "Exemplo de script do Azure PowerShell - Adicionar uma regra de grupo de segurança de rede | Microsoft Docs"
description: "Exemplo de Script do Microsoft Azure PowerShell - Adiciona um grupo de segurança de rede para permitir o tráfego de entrada em uma porta específica."
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
ms.topic: sample
ms.date: 11/28/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: fd3c648ee63c45bef305658832a4d31dfdb213be
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/29/2017
---
# <a name="add-an-inbound-network-security-group-rule"></a>Adicionar uma regra de grupo de segurança de rede de entrada

Esse exemplo de script cria uma regra de grupo de segurança de rede para permitir o tráfego de entrada na porta 8081.  O script obtém o recurso `Microsoft.Network/networkSecurityGroups` em que o cluster está localizado, cria uma nova regra de configuração de segurança de rede e atualiza o grupo de segurança de rede. Personalize os parâmetros conforme necessário.

Se necessário, instale o Azure PowerShell usando as instruções no [Guia do Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Obtém o recurso `Microsoft.Network/networkSecurityGroups`. |
|[Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)| Obtém o grupo de segurança de rede por nome.|
|[Add-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig)| Adiciona uma configuração de regra de segurança de rede a um grupo de segurança de rede. |
|[Set-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/set-azurermnetworksecuritygroup)| Define o estado de meta para um grupo de segurança de rede.|

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).
