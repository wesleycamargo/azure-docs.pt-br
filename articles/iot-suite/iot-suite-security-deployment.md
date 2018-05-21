---
title: Proteger a implantação da Internet das Coisas | Microsoft Docs
description: Este artigo fornece detalhes sobre como proteger sua implantação IoT
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 95c23341-16b0-4954-b3f2-d2e82ab7b367
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/17/2018
ms.author: dobett
ms.openlocfilehash: 995759cf4831deedc642c0850603947b22ee1222
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
---
[!INCLUDE [iot-secure-your-deployment](../../includes/iot-secure-your-deployment.md)]

## <a name="iot-solution-accelerator-cipher-suites"></a>Conjuntos de codificação de acelerador de solução de IoT

Os aceleradores de solução de IoT oferecem suporte aos seguintes conjuntos de codificação, nessa ordem.

| Pacote de criptografia | Comprimento |
| --- | --- |
| TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA384 (0xc028) ECDH secp384r1 (eq. RSA de 7680 bits) FS |256 |
| TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA256 (0xc027) ECDH secp256r1 (eq. RSA de 3072 bits) FS |128 |
| TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA (0xc014) ECDH secp384r1 (eq. RSA de 7680 bits) FS |256 |
| TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA (0xc013) ECDH secp256r1 (eq. RSA de 3072 bits) FS |128 |
| TLS\_RSA\_WITH\_AES\_256\_GCM\_SHA384 (0x9d) |256 |
| TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256 (0x9c) |128 |
| TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA256 (0x3d) |256 |
| TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA256 (0x3c) |128 |
| TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA (0x35) |256 |
| TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA (0x2f) |128 |
| TLS\_RSA\_WITH\_3DES\_EDE\_CBC\_SHA (0xa) |112 |

## <a name="see-also"></a>Consulte também
Você também pode explorar alguns dos outros recursos dos aceleradores de solução de IoT:

* [Visão geral do acelerador da solução de manutenção preditiva][lnk-predictive-overview]
* [Perguntas frequentes sobre os aceleradores de solução do IoT][lnk-faq]

Leia sobre a segurança do Hub IoT em [Controlar o acesso ao Hub IoT][lnk-devguide-security] no guia do desenvolvedor do Hub IoT.

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]:../iot-accelerators/iot-accelerators-faq.md
[lnk-devguide-security]: ../iot-hub/iot-hub-devguide-security.md
