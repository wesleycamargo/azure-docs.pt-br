---
title: Instalar um certificado de cliente P2S | Azure
description: Instalar um certificado de cliente do Windows ou Mac para autenticação de certificado P2S.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager, azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: cherylmc
ms.openlocfilehash: bf2788fff64ab8b3a5ccf75b8a80f2bd5aba5151
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="install-a-client-certificate-for-point-to-site-azure-certificate-authentication-connections"></a>Instalar um certificado de cliente para conexões de autenticação de certificado do Azure Ponto a Site

Todos os clientes que se conectam a uma rede virtual usando a autenticação de certificado do Azure Ponto a Site exigem um certificado de cliente. Este artigo ajuda você a instalar um certificado de cliente que é usado para autenticação ao se conectar a uma VNet usando P2S.

## <a name="generate"></a>Gerar e exportar um certificado de cliente

Você pode gerar um certificado de cliente de um certificado raiz que foi gerado usando uma solução de AC corporativa, ou um certificado raiz autoassinado. Consulte as instruções sobre [PowerShell](vpn-gateway-certificates-point-to-site.md) ou [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) para conferir as etapas. Depois de gerar os certificados de cliente, exporte-os como arquivos .pfx. Lembre-se de incluir toda a cadeia de certificado na exportação.

## <a name="installwin"></a>Instalar certificado - Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>Instalar certificado - Mac

Os clientes de VPN de MAC têm suporte somente para o modelo de implantação do Gerenciador de Recursos. Não há suporte para eles no modelo de implantação clássico.

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="next-steps"></a>Próximas etapas

Continue com as etapas de configuração Ponto a Site.

* [Portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
* [Portal do Azure (clássico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
