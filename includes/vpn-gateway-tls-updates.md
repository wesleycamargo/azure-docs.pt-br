---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9a17f34333503436d3da340670abdde154e45ef6
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38727516"
---
Começando em 1 de julho de 2018, o suporte está sendo removido para TLS 1.0 e 1.1 do Gateway de VPN do Azure. O Gateway de VPN oferecerá suporte somente ao protocolo TLS 1.2. Para manter a conectividade e suporte do TLS para seus clientes ponto a site Windows 7 e Windows 8 que usam o TLS, recomendamos que instalar as seguintes atualizações:

•   [Atualização para a implementação EAP da Microsoft que permite o uso de TLS](https://support.microsoft.com/help/2977292/microsoft-security-advisory-update-for-microsoft-eap-implementation-th)

•   [Atualizar a habilitação do TLS 1.1 e o TLS 1.2 como os protocolos de segurança padrão no WinHTTP](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-a-default-secure-protocols-in)

Os seguintes algoritmos herdados também serão substituídos para TLS em 1 de julho de 2018:

* RC4 (Rivest Cipher 4)
* DES (Algoritmo de criptografia de dados)
* 3DES (Algoritmo triplo de criptografia de dados)
* MD5 (Message Digest 5)
