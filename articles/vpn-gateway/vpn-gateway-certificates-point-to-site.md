---
title: 'Criar e exportar certificados para o Ponto a Site: PowerShell: Azure | Microsoft Docs'
description: "Este artigo contém etapas para criar um certificado raiz autoassinado, exportar a chave pública e gerar os certificados do cliente usando o PowerShell no Windows 10."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 27b99f7c-50dc-4f88-8a6e-d60080819a43
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 1bfcb8683669770d6dd927cbde53a683bbc1d56e
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-powershell"></a>Gerar e exportar certificados para conexões Ponto a Site usando o PowerShell

Este artigo mostra como criar um certificado raiz autoassinado e gerar certificados do cliente. Este artigo contém instruções de configuração do Site de ponto ou as perguntas frequentes sobre o Point-to-Site. Encontre essas informações selecionando um dos artigos “Configurar Ponto a Site” da seguinte lista:

> [!div class="op_single_selector"]
> * [Criar certificados autoassinados – PowerShell](vpn-gateway-certificates-point-to-site.md)
> * [Criar certificados autoassinados – Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
> * [Configurar Ponto a Site – Resource Manager – portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Configurar o Point-to-Site - Gerenciador de recursos - Portal do Azure](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Configurar Ponto a Site – Clássico – portal do Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Conexões ponto a site usam certificados para se autenticar. Quando você configura uma conexão ponto a site, você precisa carregar a chave pública (arquivo .cer) de um certificado raiz no Azure. Além disso, os certificados de cliente devem ser gerados com base no certificado raiz e instalados em todos os computadores cliente que se conectam à VNet. O certificado de cliente permite que o cliente seja autenticado.


> [!NOTE]
> Execute as etapas deste artigo em um computador que executa o Windows 10. Os cmdlets do PowerShell necessários para gerar certificados fazem parte do sistema operacional Windows 10 e não funcionam em outras versões do Windows. Você só precisa desses cmdlets para gerar os certificados. Depois que os certificados forem gerados, você poderá instalá-los em qualquer sistema operacional cliente com suporte. Se você não tiver acesso a um computador Windows 10, poderá usar o Makecert para gerar certificados. No entanto, os certificados gerados com o Makecert, embora sejam compatíveis com o Ponto a Site, não são com o SHA-2. Para obter instruções sobre o Makecert, consulte [Criar certificados usando o Makecert](vpn-gateway-certificates-point-to-site-makecert.md). Os certificados criados, usando o PowerShell ou o Makecert, podem ser instalados em qualquer [sistema operacional cliente com suporte](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq), não apenas no sistema operacional usado para criá-los.
> 
>

## <a name="rootcert"></a>Criar um certificado raiz autoassinado

As etapas a seguir mostram como criar um certificado raiz autoassinado usando o PowerShell no Windows 10. Os cmdlets e parâmetros que são usados nessas etapas fazem parte do sistema operacional Windows 10, não são parte de uma versão do PowerShell. Isso não significa que os certificados criados por você só podem ser instalados no Windows 10. Eles podem ser instalados em qualquer cliente com suporte. Para obter informações sobre clientes com suporte, consulte [Point-to-Site perguntas frequentes sobre](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq).

1. Em um computador com Windows 10, abra um console do Windows PowerShell com privilégios elevados.
2. Use o exemplo a seguir para criar o certificado raiz autoassinado. O exemplo a seguir cria um certificado raiz autoassinado chamado 'P2SRootCert' que é instalado automaticamente em 'Certificates-Current User\Personal\Certificates'. Exiba o certificado abrindo *certmgr.msc* ou *Gerenciar Certificados de Usuário*.

  ```powershell
  $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
  -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
  ```

### <a name="cer"></a>Exportar a chave pública (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

O arquivo exported.cer deve ser carregado no Azure. Para obter instruções, consulte [Configurar uma conexão ponto a site](vpn-gateway-howto-point-to-site-rm-ps.md#upload).

### <a name="export-the-self-signed-root-certificate-and-public-key-to-store-it-optional"></a>Exportar o certificado raiz autoassinado e a chave pública para armazená-lo (opcional)

Convém exportar o certificado raiz autoassinado e armazená-lo com segurança. Se precisar, você pode instalá-lo depois em outro computador e gerar mais certificados de cliente, ou exportar outro arquivo .cer. Para exportar o certificado raiz autoassinado como um .pfx, selecione o certificado raiz e use as mesmas etapas, conforme descrito em [Exportar um certificado do cliente](#clientexport) para exportar.

## <a name="clientcert"></a>Gerar um certificado do cliente

Cada computador cliente que se conecta a uma rede virtual usando ponto a site deve ter um certificado do cliente instalado. Você gera um certificado do cliente com base no certificado raiz autoassinado e, em seguida, a exporta e instala o certificado do cliente. Se o certificado do cliente não estiver instalado, a autenticação falhará. 

As etapas abaixo lhe guiarão pela geração de um certificado do cliente por meio de um certificado raiz autoassinado. Você pode gerar vários certificados de cliente a partir do mesmo certificado raiz. Quando você gerar certificados do cliente usando as etapas a seguir, o certificado do cliente será instalado automaticamente no computador que você tiver usado para gerar o certificado. Se você quiser instalar um certificado do cliente em outro computador cliente, você poderá exportar o certificado.

Windows 10 é necessária para gerar certificados de cliente usando as seguintes etapas do PowerShell. Os cmdlets e parâmetros que são usados nessas etapas fazem parte do sistema operacional Windows 10, não são parte de uma versão do PowerShell. Isso não significa que os certificados criados por você só podem ser instalados no Windows 10. Para obter informações sobre clientes com suporte, consulte [Point-to-Site perguntas frequentes sobre](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq).

### <a name="example-1"></a>Exemplo 1

Este exemplo usa a variável '$cert' declarada da seção anterior. Se você fechou o console do PowerShell após criar o certificado raiz autoassinado ou está criando certificados do cliente adicionais em uma nova sessão de console do PowerShell, use as etapas no [Examplo 2](#ex2).

Modifique e execute o exemplo para gerar um certificado do cliente. Se você executar o exemplo a seguir sem modificá-lo, o resultado será um certificado do cliente chamado 'P2SChildCert'.  Se você deseja nomear o certificado filho diferentemente, modifique o valor de CN. Não altere a TextExtension ao executar esse exemplo. O certificado do cliente que você gera é instalado automaticamente em 'Certificates - Current User\Personal\Certificates' em seu computador.

```powershell
New-SelfSignedCertificate -Type Custom -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="ex2"></a>Exemplo 2

Se você estiver criando certificados do cliente adicionais ou não estiver usando a mesma sessão do PowerShell usada para criar o certificado raiz autoassinado, use as seguintes etapas:

1. Identifique o certificado raiz autoassinado instalado no computador. Esse cmdlet retorna uma lista de certificados instalados no seu computador.

  ```powershell
  Get-ChildItem -Path “Cert:\CurrentUser\My”
  ```
2. Localize o nome da entidade da lista retornada e copie a impressão digital localizada ao lado dela para um arquivo de texto. No exemplo a seguir, há dois certificados. O nome CN é o nome do certificado raiz autoassinado do qual você deseja gerar um certificado filho. Nesse caso, 'P2SRootCert'.

        Thumbprint                                Subject

        AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
        7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert


3. Declare uma variável para o certificado raiz usando a impressão digital da etapa anterior. Substitua THUMBPRINT pela impressão digital do certificado raiz do qual você deseja gerar um certificado filho.

  ```powershell
  $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
  ```

  Por exemplo, usando a impressão digital para P2SRootCert na etapa anterior, a variável teria esta aparência:

  ```powershell
  $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
  ```    

4.  Modifique e execute o exemplo para gerar um certificado do cliente. Se você executar o exemplo a seguir sem modificá-lo, o resultado será um certificado do cliente chamado 'P2SChildCert'. Se você deseja nomear o certificado filho diferentemente, modifique o valor de CN. Não altere a TextExtension ao executar esse exemplo. O certificado do cliente que você gera é instalado automaticamente em 'Certificates - Current User\Personal\Certificates' em seu computador.

  ```powershell
  New-SelfSignedCertificate -Type Custom -KeySpec Signature `
  -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" `
  -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
  ```

## <a name="clientexport"></a>Exportar um certificado do cliente   

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]
    

## <a name="install"></a>Instalar um certificado do cliente exportado

[!INCLUDE [Install client certificate](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="next-steps"></a>Próximas etapas

Continue com a configuração de Ponto a Site. 

* Para as etapas do modelo de implantação do **Resource Manager**, veja [Configurar uma conexão Ponto a Site a uma VNet](vpn-gateway-howto-point-to-site-resource-manager-portal.md). 
* Para as etapas do modelo de implantação **clássico**, veja [Configurar uma conexão VPN Ponto a Site para uma VNet (clássico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).
