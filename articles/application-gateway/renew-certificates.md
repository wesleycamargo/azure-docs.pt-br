---
title: Renovar um certificado do Gateway do Aplicativo do Azure
description: Saiba como renovar um certificado associado a um ouvinte de gateway do aplicativo.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 8/15/2018
ms.author: victorh
ms.openlocfilehash: 48bd548ec977d2dc4dd3b5b2f34df04562a6e918
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2018
ms.locfileid: "42145455"
---
# <a name="renew-application-gateway-certificates"></a>Renovar certificados de Gateway do Aplicativo

Em algum momento, você precisará renovar seus certificados se você tiver configurado o gateway de aplicativo para criptografia SSL.

Você pode renovar um certificado associado a um ouvinte usando o portal do Azure, o Azure PowerShell ou a CLI do Azure:

## <a name="azure-portal"></a>Portal do Azure

Para renovar um certificado de ouvinte do portal, navegue para os ouvinte de gateway do aplicativo. Clique no ouvinte que tem um certificado que precisa ser renovado e, em seguida, clique em **Renovar ou editar o certificado selecionado**.

![Renovar certificado](media/renew-certificate/ssl-cert.png)

Carregue o novo certificado PFX, atribua um nome, digite a senha e, em seguida, clique em **Salvar**.

## <a name="azure-powershell"></a>Azure PowerShell

Para renovar seu certificado usando o Azure PowerShell, use o seguinte script:

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-azureRmApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password

Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```
## <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
az network application-gateway ssl-cert update \
  -n "<CertName>" \
  --gateway-name "<AppGatewayName>" \
  -g "ResourceGroupName>" \
  --cert-file <PathToCerFile> \
  --cert-password "<password>"
```

## <a name="next-steps"></a>Próximas etapas

Para saber como configurar o descarregamento SSL com o Gateway de Aplicativo do Azure, veja [Configurar descarregamento SSL](application-gateway-ssl-portal.md)
