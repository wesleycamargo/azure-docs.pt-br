---
title: Instalar um certificado de cliente P2S | Azure
description: "Este artigo ajuda você a instalar um certificado de cliente para autenticação de certificado P2S."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 4f77c7a615aaf5f87c0b260321f45a4e7129f339
ms.openlocfilehash: d3e373adeaa2ae01c8e3cbaffccf0ddb5310126d
ms.contentlocale: pt-br
ms.lasthandoff: 09/22/2017

---
# <a name="install-a-client-certificate-for-point-to-site-azure-certificate-authentication-connections"></a>Instalar um certificado de cliente para conexões de autenticação de certificado do Azure Ponto a Site

Todos os clientes que se conectam a uma rede virtual usando a autenticação de certificado do Azure Ponto a Site exigem um certificado de cliente. Este artigo ajuda você a instalar um certificado de cliente que é usado para autenticação ao se conectar a uma VNet usando P2S.

## <a name="generate"></a>Gerar e exportar um certificado de cliente

Você pode gerar um certificado de cliente de um certificado raiz que foi gerado usando uma solução de AC corporativa, ou um certificado raiz autoassinado. Consulte as instruções sobre [PowerShell](vpn-gateway-certificates-point-to-site.md) ou [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) para conferir as etapas. Depois de gerar os certificados de cliente, exporte-os como arquivos .pfx. Lembre-se de incluir toda a cadeia de certificado na exportação.

## <a name="installwin"></a>Instalar um certificado em clientes do Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>Instalar um certificado em clientes do Mac

> [!NOTE]
> Os clientes de VPN de MAC têm suporte somente para o modelo de implantação do Gerenciador de Recursos. Não há suporte para eles no modelo de implantação clássico.
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="next-steps"></a>Próximas etapas

Continue com as etapas de configuração Ponto a Site.

* [Portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
* [Portal do Azure (clássico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
