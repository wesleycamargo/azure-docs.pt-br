---
title: Requisitos de certificado de infraestrutura de chave pública da pilha do Azure para pilha Azure integrado sistemas | Microsoft Docs
description: Descreve os requisitos de implantação de certificado PKI de pilha do Azure para sistemas de pilha do Azure integradas.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 9a43179998e8377dfbbb1a41ba7d46936d63aedd
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030148"
---
# <a name="azure-stack-public-key-infrastructure-certificate-requirements"></a>Requisitos de certificado de infraestrutura de chave pública de pilha do Azure

A pilha do Azure tem uma rede de infraestrutura pública usando externamente acessíveis endereços IP públicos atribuídos a um pequeno conjunto de serviços de pilha do Azure e, possivelmente, máquinas virtuais do locatário. Certificados PKI com os nomes DNS apropriados para esses pontos de extremidade do Azure pilha infraestrutura pública são necessários durante a implantação da pilha do Azure. Este artigo fornece informações sobre:

- Quais certificados são necessários para implantar a pilha do Azure
- O processo de obtenção de certificados, as especificações de correspondência
- Como preparar, validar e usar esses certificados durante a implantação

> [!Note]  
> Durante a implantação, você deve copiar certificados para a pasta de implantação que coincide com o provedor de identidade que você está implantando contra (Azure AD ou AD FS). Se você usar um único certificado para todos os pontos de extremidade, você deve copiar esse arquivo de certificado para cada pasta de implantação, conforme descrito nas tabelas a seguir. A estrutura de pasta predefinida na máquina virtual de implantação e podem ser encontrada em: C:\CloudDeployment\Setup\Certificates. 

## <a name="certificate-requirements"></a>Requisitos de certificado
A lista a seguir descreve os requisitos de certificado que são necessários para implantar o Azure pilha: 
- Certificados devem ser emitidos por uma autoridade de certificação interna ou uma autoridade de certificação pública. Se uma autoridade de certificação pública for usada, ela deve ser incluída na imagem base do sistema operacional como parte do programa de autoridade de raiz confiável do Microsoft. Você pode encontrar a lista completa aqui: https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca 
- Sua infraestrutura de pilha do Azure deve ter acesso à rede local de lista de revogação de certificados (CRL) da autoridade de certificação publicada no certificado. Essa CRL deve ser um ponto de extremidade de http
- Quando a rotação de certificados, certificados devem ser emitidos por qualquer uma da mesma autoridade de certificado interno usada para assinar certificados fornecidos na implantação ou qualquer autoridade de certificação pública acima
- Não há suporte para o uso de certificados autoassinados
- O certificado pode ser um certificado curinga único que abrangem todos os espaços de nome no campo nome alternativo da entidade (SAN). Como alternativa, você pode usar certificados individuais usando caracteres curinga para pontos de extremidade como **acs** e chave de cofre onde eles são necessários. 
- O algoritmo de assinatura de certificado não pode ser SHA1, ele deve ser mais forte. 
- O formato do certificado deve ser PFX, como as chaves públicas e privadas são necessárias para a instalação da pilha do Azure. 
- O arquivo pfx de certificado deve ter um valor de "Assinatura Digital" e "KeyEncipherment" em seu campo "Key Usage".
- O arquivo pfx de certificado deve ter os valores "Autenticação do servidor (1.3.6.1.5.5.7.3.1)" e "Autenticação de cliente (1.3.6.1.5.5.7.3.2)" no campo "Uso avançado de chave".
- O certificado "emitido para:" campo não deve ser o mesmo que seu "emitido por:" campo.
- As senhas para todos os arquivos pfx de certificado devem ser o mesmo no momento da implantação
- Senha para o pfx de certificado deve ser uma senha complexa.
- Certifique-se de que os nomes de entidade e os nomes alternativos da entidade na correspondência de (x509v3_config) de extensão de nome alternativo de assunto. O campo de nome alternativo da entidade permite que você especifique nomes de host adicionais (sites, endereços IP, nomes comuns) a ser protegido por um único certificado de SSL.

> [!NOTE]  
> Não há suporte para certificados de autoatendimento assinado.

> [!NOTE]  
> Suporte para a presença de autoridades de certificação intermediária no IS de cadeia de relações de confiança do certificado. 

## <a name="mandatory-certificates"></a>Certificados obrigatórios
A tabela nesta seção descreve os certificados PKI de ponto de extremidade público de pilha do Azure que são necessários para o AD do Azure e implantações de pilha do AD FS do Azure. Requisitos de certificado são agrupados por área, bem como os namespaces usados e os certificados que são necessários para cada namespace. A tabela também descreve a pasta na qual o provedor de soluções copia os certificados diferentes por ponto de extremidade público. 

São necessários certificados com os nomes DNS apropriados para cada ponto de extremidade de infraestrutura pública de pilha do Azure. Nome DNS de cada ponto de extremidade é expresso no formato:  *&lt;prefixo >.&lt; região >. &lt;fqdn >*. 

Para sua implantação, [Região] e [externalfqdn] valores devem corresponder a região e nomes de domínio externo que você escolheu para o seu sistema de pilha do Azure. Por exemplo, se o nome da região foi *Redmond* e o nome de domínio externo foi *contoso.com*, os nomes DNS deve ter o formato *&lt;prefixo >. redmond.contoso.com*. O  *&lt;prefixo >* valores são predesignated pela Microsoft para descrever o ponto de extremidade protegido pelo certificado. Além disso, o  *&lt;prefixo >* valores dos pontos de extremidade externos infraestrutura dependem do serviço de pilha do Azure que usa o ponto de extremidade específico. 

> [!note]  
> Certificados podem ser fornecidos como um certificado curinga único que abrangem todos os namespaces nos campos de assunto e nome alternativo da entidade (SAN) copiados em todos os diretórios ou certificados individuais para cada ponto de extremidade copiado para o diretório correspondente. Lembre-se de que ambas as opções exigem que você usar certificados com caracteres curinga para pontos de extremidade como **acs** e chave de cofre onde eles são necessários. 

| Pasta de implantação | Entidade do certificado necessária e nomes alternativos da entidade (SAN) | Escopo (por região) | Namespace de subdomínio |
|-------------------------------|------------------------------------------------------------------|----------------------------------|-----------------------------|
| Portal público | portal.&lt;region>.&lt;fqdn> | Portais | &lt;region>.&lt;fqdn> |
| Portal de administração | adminportal.&lt;region>.&lt;fqdn> | Portais | &lt;region>.&lt;fqdn> |
| Público do Gerenciador de recursos do Azure | gerenciamento. &lt;região >. &lt;fqdn > | Azure Resource Manager | &lt;region>.&lt;fqdn> |
| Administração do Gerenciador de recursos do Azure | adminmanagement. &lt;região >. &lt;fqdn > | Azure Resource Manager | &lt;region>.&lt;fqdn> |
| ACSBlob | *.blob.&lt;region>.&lt;fqdn><br>(Certificado SSL curinga) | Armazenamento de Blobs | blob.&lt;region>.&lt;fqdn> |
| ACSTable | *.table.&lt;region>.&lt;fqdn><br>(Certificado SSL curinga) | Armazenamento de Tabelas | tabela. &lt;região >. &lt;fqdn > |
| ACSQueue | *.queue.&lt;region>.&lt;fqdn><br>(Certificado SSL curinga) | Armazenamento de Filas | queue.&lt;region>.&lt;fqdn> |
| KeyVault | *.vault.&lt;region>.&lt;fqdn><br>(Certificado SSL curinga) | Key Vault | cofre. &lt;região >. &lt;fqdn > |
| KeyVaultInternal | *.adminvault.&lt;region>.&lt;fqdn><br>(Certificado SSL curinga) |  Keyvault interno |  adminvault. &lt;região >. &lt;fqdn > |

Se você implantar a pilha do Azure usando o modo de implantação do AD do Azure, você precisa solicitar certificados listados na tabela anterior. No entanto, se você implantar a pilha do Azure usando o modo de implantação do AD FS, você também deve solicitar os certificados descritos na tabela a seguir:

|Pasta de implantação|Entidade do certificado necessária e nomes alternativos da entidade (SAN)|Escopo (por região)|Namespace de subdomínio|
|-----|-----|-----|-----|
|ADFS|adfs.*&lt;region>.&lt;fqdn>*<br>(Certificado SSL)|ADFS|*&lt;region>.&lt;fqdn>*|
|Grafo|graph.*&lt;region>.&lt;fqdn>*<br>(Certificado SSL)|Grafo|*&lt;region>.&lt;fqdn>*|
|

> [!IMPORTANT]
> Todos os certificados listados nesta seção devem ter a mesma senha. 

## <a name="optional-paas-certificates"></a>Certificados de PaaS opcionais
Se você estiver planejando implantar os serviços adicionais do Azure PaaS de pilha (SQL, MySQL e serviço de aplicativo) após a pilha do Azure foi implantado e configurado, você precisará solicitar certificados adicionais para cobrir os pontos de extremidade dos serviços de PaaS. 

> [!IMPORTANT]
> Os certificados que você usa para provedores de recursos do serviço de aplicativo, SQL e MySQL precisam ter a mesma autoridade raiz como aqueles usados para os pontos de extremidade do Azure pilha global. 

A tabela a seguir descreve os pontos de extremidade e os certificados necessários para os adaptadores de SQL e MySQL e para o serviço de aplicativo. Você não precisa copiar esses certificados para a pasta de implantação da pilha do Azure. Em vez disso, você fornecer esses certificados quando você instala os provedores de recursos adicionais. 

|Escopo (por região)|Certificado|Entidade do certificado necessária e nomes de alternativo da entidade (SANs)|Namespace de subdomínio|
|-----|-----|-----|-----|
|SQL, MySQL|SQL e MySQL|&#42;.dbadapter.*&lt;region>.&lt;fqdn>*<br>(Certificado SSL curinga)|dbadapter.*&lt;region>.&lt;fqdn>*|
|Serviço de Aplicativo|Certificado SSL de padrão de tráfego da Web|&#42;.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.sso.appservice.*&lt;region>.&lt;fqdn>*<br>(Certificado de SSL curinga do domínio de várias<sup>1</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|Serviço de Aplicativo|API|api.appservice.*&lt;region>.&lt;fqdn>*<br>(Certificado SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|Serviço de Aplicativo|FTP|ftp.appservice.*&lt;region>.&lt;fqdn>*<br>(Certificado SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|Serviço de Aplicativo|SSO|sso.appservice.*&lt;region>.&lt;fqdn>*<br>(Certificado SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|

<sup>1</sup> requer um certificado com vários nomes alternativos de entidade de caractere curinga. Curinga várias SANs em um único certificado pode não ter suporte de todas as autoridades de certificação pública 

<sup>2</sup> um &#42;.appservice. *&lt;região >. &lt;fqdn >* certificado curinga não pode ser usado no lugar desses três certificados (api.appservice. *&lt;região >. &lt;fqdn >*, ftp.appservice. *&lt;região >. &lt;fqdn >* e sso.appservice. *&lt;região >. &lt;fqdn >*. Serviço de aplicativo explicitamente requer o uso de certificados separados para esses pontos de extremidade. 

## <a name="learn-more"></a>Saiba mais
Saiba como [gerar certificados PKI para implantação do Azure pilha](azure-stack-get-pki-certs.md). 

## <a name="next-steps"></a>Próximas etapas
[Integração de identidade](azure-stack-integrate-identity.md)

