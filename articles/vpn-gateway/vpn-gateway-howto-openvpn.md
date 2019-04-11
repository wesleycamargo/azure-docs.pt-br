---
title: 'Como configurar o OpenVPN para Gateway de VPN do Azure: PowerShell | Microsoft Docs'
description: Etapas para configurar o OpenVPN para Gateway de VPN do Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: cherylmc
ms.openlocfilehash: 8cc2a6d4ad06bf4a55d4ef078970823df1e0d910
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281957"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway-preview"></a>Configurar OpenVPN para o Gateway de VPN ponto a site do Azure (versão prévia)

Este artigo ajuda você a configurar **OpenVPN® protocolo** no Gateway de VPN do Azure. O artigo supõe que você já tem um ambiente de trabalho de ponto a site. Se você não tiver, use as instruções na etapa 1 para criar uma VPN ponto a site.

> [!IMPORTANT]
> Essa versão prévia pública é fornecida sem um contrato de nível de serviço e não deve ser usada para cargas de trabalho de produção. Determinados recursos podem não ter suporte, podem ter restrição ou podem não estar disponíveis em todos os locais do Azure. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="register"></a>Registrar este recurso

Clique em **TryIt** nestas etapas para registrar esse recurso com facilidade usando o Azure Cloud Shell.

>[!NOTE]
>Se você não registrar esse recurso, não poderá usá-lo.
>

Depois de clicar em **TryIt** para abrir o Azure Cloud Shell, copie e cole os seguintes comandos:

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```
 
```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

Depois que o recurso for mostrado como registrado, registre novamente a assinatura no namespace Microsoft.Network.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="vnet"></a>1. Criar uma VPN ponto a site

Se você ainda não tiver um ambiente de ponto a site funcionando, siga as instruções para criar um. Veja [Criar uma VPN ponto a site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) para criar e configurar um gateway de VPN ponto a site com a autenticação de certificado nativa do Azure. 

> [!IMPORTANT]
> Não há suporte para o SKU Básico no OpenVPN.

## <a name="enable"></a>2. Habilitar OpenVPN no gateway

Habilite o OpenVPN no gateway. Verifique se o gateway já está configurado para ponto a site (IKEv2 ou SSTP) antes de executar os comandos a seguir:

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Próximas etapas

Para configurar clientes para OpenVPN, veja [Configurar clientes do OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" é uma marca registrada da Inc OpenVPN.**