---
title: Renovar um certificado do Gateway do Aplicativo do Azure
description: Saiba como renovar um certificado associado a um ouvinte de gateway do aplicativo.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 05/17/2018
ms.author: victorh
ms.openlocfilehash: b125f707e8de17764701e981736a53492e5e756c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34356929"
---
# <a name="renew-application-gateway-certificates"></a>Renovar certificados de Gateway do Aplicativo

Em algum momento, você precisará renovar seus certificados se você tiver configurado o gateway de aplicativo para criptografia SSL.

Você pode renovar um certificado associado a um ouvinte usando o portal do Azure ou o PowerShell do Azure:

## <a name="azure-portal"></a>Portal do Azure

Para renovar um certificado de ouvinte do portal, navegue para os ouvinte de gateway do aplicativo. Clique no ouvinte que tem um certificado que precisa ser renovado e, em seguida, clique em **Renovar ou editar o certificado selecionado**.

![Renovar certificado](media/renew-certificate/ssl-cert.png)

Carregue o novo certificado PFX, atribua um nome, digite a senha e, em seguida, clique em **Salvar**.

## <a name="azure-powershell"></a>Azure PowerShell

Para renovar seu certificado usando o Microsoft Azure PowerShell, use o seguinte cmdlet:

```PowerShell
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-azureRmApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password
```

## <a name="next-steps"></a>Próximas etapas

Para saber como configurar o descarregamento SSL com o Gateway de Aplicativo do Azure, veja [Configurar descarregamento SSL](application-gateway-ssl-portal.md)
