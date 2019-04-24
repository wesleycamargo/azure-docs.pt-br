---
title: Habilitar a visualização pública do Firewall do Azure
description: Usar o Azure PowerShell para habilitar a visualização pública do Firewall do Azure
author: vhorne
ms.service: firewall
services: firewall
ms.topic: article
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: fe1b8f9d56b0f4faa0baa25463b2aa29a59715cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60193044"
---
# <a name="enable-the-azure-firewall-public-preview"></a>Habilitar a visualização pública do Firewall do Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

## <a name="enable-using-azure-powershell"></a>Habilitar usando o Azure PowerShell

Para habilitar a visualização pública do Firewall do Azure, use os seguintes comandos do Azure PowerShell:

```PowerShell
Register-AzProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```

Demora até 30 minutos para a conclusão do registro de recursos. É possível conferir o status do seu registro executando os seguintes comandos do Azure PowerShell:

```powershell

Get-AzProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network

Get-AzProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```
Após a conclusão do registro, execute o seguinte comando:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="next-steps"></a>Próximos passos

- [Tutorial: Implantar e configurar o Firewall do Azure usando o portal do Azure](tutorial-firewall-deploy-portal.md)

