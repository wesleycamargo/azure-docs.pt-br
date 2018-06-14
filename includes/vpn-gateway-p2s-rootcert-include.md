---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b2a96aad793d956b3ea3de06fba74bcf68e50786
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197011"
---
Você pode usar um certificado raiz que foi gerado usando uma solução corporativa (recomendado) ou você pode gerar um certificado autoassinado. Depois de criar o certificado raiz, exporte os dados de certificado público (não a chave privada) como o arquivo .cer X.509 codificado em Base 64 e carregue os dados do certificado público no Azure.

* **Certificado corporativo:** Se você estiver usando uma solução corporativa, você poderá usar a cadeia de certificados existente. Obtenha o arquivo .cer do certificado raiz que você deseja usar.
* **Certificado raiz autoassinado:** se você não estiver usando uma solução de certificado corporativo, será necessário criar um certificado raiz autoassinado. É importante que você execute as etapas em um dos artigos de certificado de P2S abaixo. Caso contrário, os certificados criados não serão compatíveis com conexões P2S, e os clientes receberão um erro de conexão ao tentar se conectar. Você pode usar o Azure PowerShell, MakeCert ou OpenSSL. As etapas dos artigos fornecidos geram um certificado compatível:

  * [Instruções do Windows 10 PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): essas instruções exigem o Windows 10 e o PowerShell para gerar certificados. Os certificados de cliente gerados a partir do certificado raiz podem ser instalados em qualquer cliente de P2S com suporte.
  * [Instruções de MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): use MakeCert se você não tiver acesso a um computador com Windows 10 para gerar certificados. MakeCert foi preterido, mas você ainda pode usar o MakeCert para gerar certificados. Os certificados de cliente gerados a partir do certificado raiz podem ser instalados em qualquer cliente de P2S com suporte.
