---
title: "Verificar uma conexão de Gateway de VPN | Microsoft Docs"
description: "Este artigo mostra como verificar a conexão de Gateway de VPN de uma rede virtual."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 7e3d1043-caa9-4472-96d3-832f4e2c91ee
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/24/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 0e9fa1b1397c60985de9d2e60b3f01146036801f
ms.lasthandoff: 04/26/2017


---
# <a name="verify-a-vpn-gateway-connection"></a>Verificar uma conexão de Gateway de VPN

Este artigo mostra como verificar sua conexão de gateway de VPN para os modelos de implantação do Resource Manager e clássico.

## <a name="verify-using-the-azure-portal"></a>Verificar usando o Portal do Azure

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="verify-using-powershell"></a>Verificar usando o PowerShell

Para verificar usando o PowerShell, instale a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Confira [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para saber mais sobre como instalar os cmdlets do PowerShell. Para saber mais sobre como usar cmdlets do Resource Manager, confira [Usando o Windows PowerShell com o Resource Manager](../powershell-azure-resource-manager.md).

### <a name="log-in-to-your-azure-account"></a>Fazer logon na sua conta do Azure
1. Abra o console do PowerShell com privilégios elevados e conecte-se à sua conta.

  ```powershell
  Login-AzureRmAccount
  ```
2. Verificar as assinaturas da conta.

  ```powershell
  Get-AzureRmSubscription
  ``` 
3. Especifique a assinatura que você quer usar.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```

### <a name="verify-your-connection"></a>Verificar a conexão

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="verify-using-the-azure-cli"></a>Verificar usando a CLI do Azure

Para verificar usando a CLI do Azure, instale a versão mais recente dos comandos da CLI (2.0 ou posterior). Para saber mais sobre como instalar os comandos do CLI, veja [Instalar a CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="log-in-to-your-azure-account"></a>Fazer logon na sua conta do Azure

1. Faça logon na sua assinatura do Azure com o comando [az login](/cli/azure/#login) e siga as instruções na tela.

  ```azurecli
  az login
  ```
2. Se você tiver mais de uma assinatura do Azure, liste as assinaturas para a conta.

  ```azurecli
  Az account list --all
  ```
3. Especifique a assinatura que você quer usar.

  ```azurecli
  Az account set --subscription
  <replace_with_your_subscription_id>
  ```

### <a name="verify-your-connection"></a>Verificar a conexão

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="verify-using-the-azure-portal-classic"></a>Verificar usando o Portal do Azure (clássico)
[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


## <a name="verify-using-powershell-classic"></a>Verificar usando o PowerShell (clássico)
Para verificar usando o PowerShell, instale as versões mais recente dos cmdlets do Azure PowerShell. Baixe e instale as versões do Resource Manager e do Gerenciamento de Serviço (SM). Confira [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para saber mais sobre como instalar os cmdlets do PowerShell. 

### <a name="log-in-to-your-azure-account"></a>Fazer logon na sua conta do Azure
1. Abra o console do PowerShell com privilégios elevados e conecte-se à sua conta.

  ```powershell
  Login-AzureRmAccount
  ```
2. Verificar as assinaturas da conta.

  ```powershell
  Get-AzureRmSubscription 
  ```
3. Especifique a assinatura que você quer usar.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
4. Faça logon para usar os cmdlets do Gerenciamento de Serviço para o modelo de implantação clássica.

  ```powershell
  Add-AzureAccount
  ```

### <a name="verify-your-connection"></a>Verificar a conexão
[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Próximas etapas
* Você pode adicionar máquinas virtuais às suas redes virtuais. Veja [Criar uma máquina virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter as etapas.


