---
title: 'Como configurar o OpenVPN para Gateway de VPN do Azure: PowerShell | Microsoft Docs'
description: Etapas para configurar o OpenVPN para Gateway de VPN do Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: cherylmc
ms.openlocfilehash: 50a8c30831ba806d0ea02d4f67b4e672e71e6325
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415078"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway-preview"></a>Configurar OpenVPN para o Gateway de VPN ponto a site do Azure (versão prévia)

Este artigo ajuda você a configurar o OpenVPN no Gateway de VPN do Azure. O artigo supõe que você já tem um ambiente de trabalho de ponto a site. Se você não tiver, use as instruções na etapa 1 para criar uma VPN ponto a site.

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

## <a name="cmdlets"></a>2. Instalar cmdlets do PowerShell

Instale a versão mais recente dos cmdlets do PowerShell do Gerenciador de Recursos. Para saber mais sobre como instalar cmdlets do PowerShell, confira [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview). Isso é importante porque as versões anteriores dos cmdlets não contêm os valores atuais que são necessários para este exercício.

## <a name="enable"></a>3. Habilitar OpenVPN no gateway

Habilite o OpenVPN no gateway. Verifique se o gateway já está configurado para ponto a site (IKEv2 ou SSTP) antes de executar os comandos a seguir:

```powershell
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Próximas etapas

Para configurar clientes para OpenVPN, veja [Configurar clientes do OpenVPN](vpn-gateway-howto-openvpn-clients.md).
