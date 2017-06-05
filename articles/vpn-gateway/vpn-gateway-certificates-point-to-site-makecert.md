---
title: 'Criar e exportar certificados para o Ponto a Site: Makecert: Azure | Microsoft Docs'
description: "Este artigo contém etapas para criar um certificado raiz autoassinado, exportar a chave pública e gerar certificados do cliente usando o makecert."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 0800a7754241eb409dbd86db82b586a3e19a29fc
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>Gerar e exportar certificados para conexões Ponto a Site usando o Makecert

> [!NOTE]
> Use estas instruções somente quando não tiver acesso a um computador Windows 10 para gerar certificados autoassinados para conexões Ponto a Site. O Makecert está sendo preterido. Além disso, o Makecert não pode criar um certificado SHA-2, apenas SHA-1 (que ainda é válido para o P2S). Por esses motivos, recomendamos que você use as [etapas do PowerShell](vpn-gateway-certificates-point-to-site.md), se possível. Os certificados criados, usando o PowerShell ou o Makecert, podem ser instalados em qualquer [sistema operacional cliente com suporte](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq), não apenas no sistema operacional usado para criá-los.
>
>


Este artigo mostra como criar um certificado autoassinado, exportar a chave pública e gerar certificados do cliente. Este artigo contém instruções de configuração do Site de ponto ou as perguntas frequentes sobre o Point-to-Site. Encontre essas informações selecionando um dos artigos “Configurar Ponto a Site” da seguinte lista:

> [!div class="op_single_selector"]
> * [Criar certificados autoassinados – PowerShell](vpn-gateway-certificates-point-to-site.md)
> * [Criar certificados autoassinados – Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
> * [Configurar Ponto a Site – Resource Manager – portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Configurar o Point-to-Site - Gerenciador de recursos - Portal do Azure](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Configurar Ponto a Site – Clássico – portal do Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Conexões ponto a site usam certificados para se autenticar. Quando você configura uma conexão ponto a site, você precisa carregar a chave pública (arquivo .cer) de um certificado raiz no Azure. Além disso, os certificados de cliente devem ser gerados com base no certificado raiz e instalados em todos os computadores cliente que se conectam à VNet. O certificado de cliente permite que o cliente seja autenticado.

## <a name="rootcert"></a>Criar um certificado raiz autoassinado

As etapas a seguir mostram como criar um certificado autoassinado usando o Makecert. Essas etapas não são específicas do modelo de implantação. Elas são válidas tanto para o Gerenciador de Recursos quanto para o clássico.

1. Baixe e instale o [makecert](https://msdn.microsoft.com/en-us/library/windows/desktop/aa386968(v=vs.85).aspx).
2. Após a instalação, localize o utilitário makecert.exe neste caminho: 'C:\Program Files (x86)\Windows Kits\10\bin\<arch>'. Abra um prompt de comando como administrador e navegue até o local do utilitário makecert. Você pode usar o exemplo a seguir:

  ```cmd
  cd C:\Program Files (x86)\Windows Kits\10\bin\x64
  ```
3. Em seguida, crie e instale um certificado no repositório de certificados Pessoal em seu computador. O exemplo a seguir cria um arquivo *.cer* correspondente que você carrega no Azure ao configurar P2S. Substitua “P2SRootCert” e “P2SRootCert.cer” pelo nome que você deseja usar para o certificado. O certificado estará localizado em seus 'Certificados – Usuário Atual\Pessoal\Certificados'.

  ```cmd
  makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha1 -len 2048 -ss My "P2SRootCert.cer"
  ```

## <a name="cer"></a>Exportar a chave pública (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

O arquivo exported.cer deve ser carregado no Azure. Para obter instruções, consulte [Configurar uma conexão ponto a site](vpn-gateway-howto-point-to-site-rm-ps.md#upload).

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>Exportar o certificado autoassinado e a chave privada para armazená-lo (opcional)

Convém exportar o certificado raiz autoassinado e armazená-lo com segurança. Se precisar, você pode instalá-lo depois em outro computador e gerar mais certificados de cliente, ou exportar outro arquivo .cer. Para exportar o certificado raiz autoassinado como um .pfx, selecione o certificado raiz e use as mesmas etapas, conforme descrito em [Exportar um certificado do cliente](#clientexport) para exportar.

## <a name="create-and-install-client-certificates"></a>Criar e instalar certificados de cliente

Não instale o certificado autoassinado diretamente no computador cliente. Você precisa gerar um certificado de cliente do certificado autoassinado. Em seguida, você exporta e instala o certificado de cliente no computador cliente. Essas etapas não são específicas ao modelo de implantação. Elas são válidas tanto para o Gerenciador de Recursos quanto para o clássico.

### <a name="clientcert"></a>Gerar um certificado do cliente

Cada computador cliente que se conecta a uma rede virtual usando ponto a site deve ter um certificado do cliente instalado. Você gera um certificado do cliente com base no certificado raiz autoassinado e, em seguida, a exporta e instala o certificado do cliente. Se o certificado do cliente não estiver instalado, a autenticação falhará. 

As etapas abaixo lhe guiarão pela geração de um certificado do cliente por meio de um certificado raiz autoassinado. Você pode gerar vários certificados de cliente a partir do mesmo certificado raiz. Quando você gerar certificados do cliente usando as etapas a seguir, o certificado do cliente será instalado automaticamente no computador que você tiver usado para gerar o certificado. Se você quiser instalar um certificado do cliente em outro computador cliente, você poderá exportar o certificado.
 
1. No mesmo computador usado para criar o certificado autoassinado, abra um prompt de comando como administrador.
2. Modifique e execute o exemplo para gerar um certificado de cliente.
  * Altere *“P2SRootCert”* para o nome da raiz autoassinada com base na qual você está gerando o certificado do cliente. Verifique se você está usando o nome do certificado raiz, que é equivalente ao valor 'CN=' que você especificou quando criou a raiz autoassinada.
  * Altere *P2SChildCert* para o nome que você deseja fornecer a um certificado do cliente gerado.

  Se você executar o exemplo a seguir sem modificá-lo, o resultado será um certificado do cliente chamado P2SChildcert em seu repositório de certificados Personal que foi gerado com base no certificado raiz P2SRootCert.

  ```cmd
  makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha1
  ```

### <a name="clientexport"></a>Exportar um certificado do cliente

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install"></a>Instalar um certificado do cliente exportado

[!INCLUDE [Install client certificate](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="next-steps"></a>Próximas etapas

Continue com a configuração de Ponto a Site. 

* Para as etapas do modelo de implantação do **Resource Manager**, veja [Configurar uma conexão Ponto a Site a uma VNet](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Para as etapas do modelo de implantação **clássico**, veja [Configurar uma conexão VPN Ponto a Site para uma VNet (clássico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).
