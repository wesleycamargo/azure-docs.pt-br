---
title: "Gerar certificados de infraestrutura de chave pública do Azure pilha para implantação de sistemas de pilha do Azure integradas | Microsoft Docs"
description: "Descreve os sistemas de pilha do Azure integradas processfor implantação do Azure pilha PKI certificado."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: e7edbc1c4aa6e3cb1026d493886ef7ca704b9131
ms.sourcegitcommit: 5108f637c457a276fffcf2b8b332a67774b05981
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2018
---
# <a name="generate-pki-certificates-for-azure-stack-deployment"></a>Gerar certificados PKI para implantação de pilha do Azure
Agora que você sabe [os requisitos de certificado PKI](azure-stack-pki-certs.md) para implantações de pilha do Azure, você precisa obter os certificados da autoridade de certificação (CA) de sua escolha. 

## <a name="request-certificates-using-an-inf-file"></a>Solicitar certificados usando um arquivo INF
Uma maneira para solicitar certificados de uma autoridade de certificação pública ou de uma CA interna é usando um arquivo INF. O utilitário de certreq.exe interno do Windows pode usar um arquivo INF especificando os detalhes do certificado, para gerar um arquivo de solicitação, conforme descrito nesta seção. 

### <a name="sample-inf-file"></a>Exemplo de arquivo INF 
O arquivo INF de solicitação de certificado de exemplo pode ser usado para criar um arquivo de solicitação de certificado offline para envio a uma autoridade de certificação (interno ou público). O arquivo INF abrange todos os pontos de extremidade necessários (inclusive os serviços de PaaS opcionais) um certificado curinga. 

O exemplo de arquivo INF assume essa região é igual a **sea** e o valor FQDN externo é **sea &46;contoso 46; & com**. Alterar esses valores para coincidir com seu ambiente antes de gerar um. Arquivo INF para sua implantação. 

    
    [Version] 
    Signature="$Windows NT$"

    [NewRequest] 
    Subject = "C=US, O=Microsoft, L=Redmond, ST=Washington, CN=portal.sea.contoso.com"

    Exportable = TRUE                   ; Private key is not exportable 
    KeyLength = 2048                    ; Common key sizes: 512, 1024, 2048, 4096, 8192, 16384 
    KeySpec = 1                         ; AT_KEYEXCHANGE 
    KeyUsage = 0xA0                     ; Digital Signature, Key Encipherment 
    MachineKeySet = True                ; The key belongs to the local computer account 
    ProviderName = "Microsoft RSA SChannel Cryptographic Provider" 
    ProviderType = 12 
    SMIME = FALSE 
    RequestType = PKCS10
    HashAlgorithm = SHA256

    ; At least certreq.exe shipping with Windows Vista/Server 2008 is required to interpret the [Strings] and [Extensions] sections below

    [Strings] 
    szOID_SUBJECT_ALT_NAME2 = "2.5.29.17" 
    szOID_ENHANCED_KEY_USAGE = "2.5.29.37" 
    szOID_PKIX_KP_SERVER_AUTH = "1.3.6.1.5.5.7.3.1" 
    szOID_PKIX_KP_CLIENT_AUTH = "1.3.6.1.5.5.7.3.2"

    [Extensions] 
    %szOID_SUBJECT_ALT_NAME2% = "{text}dns=*.sea.contoso.com&dns=*.blob.sea.contoso.com&dns=*.queue.sea.contoso.com&dns=*.table.sea.contoso.com&dns=*.vault.sea.contoso.com&dns=*.adminvault.sea.contoso.com&dns=*.dbadapter.sea.contoso.com&dns=*.appservice.sea.contoso.com&dns=*.scm.appservice.sea.contoso.com&dns=api.appservice.sea.contoso.com&dns=ftp.appservice.sea.contoso.com&dns=sso.appservice.sea.contoso.com&dns=adminportal.sea.contoso.com&dns=management.sea.contoso.com&dns=adminmanagement.sea.contoso.com" 
    %szOID_ENHANCED_KEY_USAGE% = "{text}%szOID_PKIX_KP_SERVER_AUTH%,%szOID_PKIX_KP_CLIENT_AUTH%"

    [RequestAttributes]
    

## <a name="generate-and-submit-request-to-the-ca"></a>Gerar e enviar a solicitação para a autoridade de certificação
O fluxo de trabalho a seguir descreve como você pode personalizar e usar o arquivo INF de exemplo gerado anteriormente para solicitar um certificado de uma autoridade de certificação:

1. **Editar e salvar o arquivo INF**. Copie o exemplo fornecido e salvá-lo em um novo arquivo de texto. Substitua o nome da entidade e o FQDN externo com os valores que correspondem a sua implantação e salve o arquivo como um. Arquivo INF.
2. **Gerar uma solicitação usando certreq**. Usando um computador com Windows, inicie um prompt de comando como administrador e execute o seguinte comando para gerar um arquivo de solicitação (. req): `certreq -new <yourinffile>.inf <yourreqfilename>.req`.
3. **Enviar à autoridade de certificação**. Enviar o. Arquivo de sol gerado para a autoridade de certificação (pode ser público ou interno).
4. **Importe. CER**. A autoridade de certificação retornará um. Arquivo CER. Usando o mesmo computador do Windows do qual você gerou o arquivo de solicitação, importar o. Arquivo CER retornado para o computador/repositório pessoal. 
5. **Exportar e copiar. PFX para pastas de implantação**. Exportar o certificado (incluindo a chave privada) como um. PFX do arquivo e, em seguida, copie o. O arquivo PFX para as pastas de implantação descrito no [requisitos de PKI de implantação do Azure pilha](azure-stack-pki-certs.md).

