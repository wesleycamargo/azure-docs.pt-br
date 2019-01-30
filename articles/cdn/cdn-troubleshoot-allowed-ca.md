---
title: Autoridades de certificação permitidas para habilitar HTTPS personalizado no CDN do Azure | Microsoft Docs
description: Se você estiver usando seu próprio certificado para habilitar HTTPS em um domínio personalizado, deverá usar uma AC (autoridade de certificação) permitida para criá-lo.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 11651c2721756a4f750a5a5e78f86fdbd363fb9d
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54462582"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Autoridades de certificação permitidas para habilitar HTTPS personalizado no CDN do Azure

Para um domínio personalizado da CDN (Rede de Distribuição de Conteúdo) do Azure em um ponto de extremidade **CDN do Azure Standard da Microsoft**, quando [habilitar o recurso HTTPS usando seu próprio certificado](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates), deverá usar uma AC (autoridade de certificação) permitida para criar o certificado SSL. Caso contrário, se você usar uma AC não permitida ou um certificado autoassinado, sua solicitação será rejeitada.

> [!NOTE]
> A opção de usar seu próprio certificado para habilitar HTTPS personalizado está disponível apenas com perfis **CDN do Azure Standard da Microsoft**. 
>

As ACs a seguir são permitidas quando você criar seu próprio certificado:

- AddTrust External CA Root
- AC de raiz AlphaSSL
- AME Infra CA 01
- AME Infra CA 02
- Ameroot
- APCA-DM3P
- Autopilot Root CA
- Baltimore CyberTrust Root
- Class 3 Public Primary Certification Authority
- COMODO RSA Certification Authority
- COMODO RSA Domain Validation Secure Server CA
- D-TRUST Root Class 3 CA 2 2009
- DigiCert Cloud Services CA-1
- DigiCert Global Root CA
- DigiCert High Assurance CA-3
- DigiCert High Assurance EV Root CA
- DigiCert SHA2 CA do Servidor de Validação Estendido
- DigiCert SHA2 High Assurance Server CA
- DigiCert SHA2 Secure Server CA
- DST Raiz CA X3
- D-TRUST Classe de Raiz 3 CA 2 2009
- Em todos os lugares criptografia CA DE TLS DV
- Autoridade de certificação raiz da Entrust
- Autoridade de certificação raiz da Entrust - G2
- Autoridade de certificação raiz da Entrust (2048)
- GeoTrust Global CA
- GeoTrust Primary Certification Authority
- GeoTrust Primary Certification Authority – G2
- Geotrust RSA CA 2018
- GlobalSign
- GlobalSign Extended Validation CA – SHA256 – G2
- GlobalSign Organization Validation CA – G2
- GlobalSign Root CA
- Go Daddy Root Certificate Authority – G2
- Go Daddy Secure Certificate Authority - G2
- Raiz QuoVadis CA2 G3
- RapidSSL RSA CA 2018
- Symantec Class 3 EV SSL CA – G3
- Symantec Class 3 Secure Server CA – G4
- Symantec Enterprise Mobile Root for Microsoft
- Thawte Primary Root CA
- Thawte Primary Root CA – G2
- Thawte Primary Root CA – G3
- Thawte RSA CA 2018
- Thawte Timestamping CA
- TrustAsia TLS RSA CA
- VeriSign Class 3 Extended Validation SSL CA
- VeriSign Class 3 Extended Validation SSL SGC CA
- VeriSign Class 3 Public Primary Certification Authority – G5
- VeriSign International Server CA – Class 3
- VeriSign Time Stamping Service Root
- VeriSign Universal Root Certification Authority


