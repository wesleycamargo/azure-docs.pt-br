---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f2d7aba05fc01c5a4dcdb123f25242c4e4a72578
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "43426414"
---
>[!NOTE]
>Começando em 1 de julho de 2018, o suporte está sendo removido para TLS 1.0 e 1.1 do Gateway de VPN do Azure. O Gateway de VPN oferecerá suporte somente ao protocolo TLS 1.2. Para manter o suporte, consulte as [ atualizações para habilitar o suporte para TLS1.2](#tls1).

Além disso, os seguintes algoritmos herdados também serão preteridos para TLS em 1 de julho de 2018:

* RC4 (Rivest Cipher 4)
* DES (Algoritmo de criptografia de dados)
* 3DES (Algoritmo triplo de criptografia de dados)
* MD5 (Message Digest 5)

### <a name="tls1"></a>Como habilitar o suporte para o TLS 1.2 no Windows 7 e no Windows 8.1?

[!INCLUDE [tls 1.2](vpn-gateway-tls-include.md)]
