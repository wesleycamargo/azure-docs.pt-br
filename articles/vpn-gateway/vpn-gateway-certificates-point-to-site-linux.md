---
title: 'Gerar e exportar certificados para o Ponto a Site: Linux: Azure | Microsoft Docs'
description: Criar um certificado raiz autoassinado, exportar a chave pública e gerar certificados do cliente usando a CLI do Linux (strongSwan).
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/06/2018
ms.author: cherylmc
ms.openlocfilehash: 8647b3b3eda980dbd5d5ec368b6b4b13949ecaf1
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44305718"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-linux-strongswan-cli"></a>Gerar e exportar certificados para Ponto a Site usando a CLI strongSwan do Linux

Conexões ponto a site usam certificados para se autenticar. Este artigo mostra como criar um certificado raiz autoassinado e gerar certificados do cliente usando a CLI do Linux e o strongSwan. Se você estiver procurando por instruções de certificado diferentes, confira os artigos [PowerShell](vpn-gateway-certificates-point-to-site.md) ou [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md).

> [!NOTE]
> As etapas neste artigo exigem o strongSwan.
>

A configuração do computador usada para obter as etapas neste artigo foi a seguinte:

| | |
|---|---|
|**Computador**| Ubuntu Server 16.04<br>ID_LIKE=debian<br>PRETTY_NAME="Ubuntu 16.04.4 LTS"<br>VERSION_ID="16.04" |
|**Dependências**| apt-get install strongswan-ikev2 strongswan-plugin-eap-tls<br>apt-get install libstrongswan-standard-plugins |

## <a name="install-strongswan"></a>Instalar o strongSwan

1. `apt-get install strongswan-ikev2 strongswan-plugin-eap-tls`
2. `apt-get install libstrongswan-standard-plugins`

Para obter informações sobre como instalar o strongSwan usando a GUI, veja as etapas no artigo [Configuração do cliente](point-to-site-vpn-client-configuration-azure-cert.md#install).

## <a name="generate-keys-and-certificate"></a>Gerar chaves e certificado

1. Gere o Certificado de Autoridade de Certificação.

  ```
  ipsec pki --gen --outform pem > caKey.pem
  ipsec pki --self --in caKey.pem --dn "CN=VPN CA" --ca --outform pem > caCert.pem
  ```
2. Imprima o Certificado de Autoridade de Certificação no formato base64. Este é o formato compatível com o Azure. Você posteriormente fará upload disso para o Azure como parte da sua configuração de P2S.

  ```
  openssl x509 -in caCert.pem -outform der | base64 -w0 ; echo
  ```
3. Gere o certificado do usuário.

  ```
  export PASSWORD="password"
  export USERNAME="client"

  ipsec pki --gen --outform pem > "${USERNAME}Key.pem"
  ipsec pki --pub --in "${USERNAME}Key.pem" | ipsec pki --issue --cacert caCert.pem --cakey caKey.pem --dn "CN=${USERNAME}" --san "${USERNAME}" --flag clientAuth --outform pem > "${USERNAME}Cert.pem"
  ```
4. Gere um pacote p12 contendo o certificado de usuário. Este pacote será usado nas próximas etapas ao trabalhar com os [Arquivos de configuração de cliente](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).

  ```
  openssl pkcs12 -in "${USERNAME}Cert.pem" -inkey "${USERNAME}Key.pem" -certfile caCert.pem -export -out "${USERNAME}.p12" -password "pass:${PASSWORD}"
  ```

## <a name="next-steps"></a>Próximas etapas

Continue com a configuração de ponto a site para [Criar e instalar arquivos de configuração de cliente VPN](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).