---
title: Sistemas integrados de requisitos de certificado de infra-estrutura de chave pública da pilha do Azure para o Azure Stack | Microsoft Docs
description: Descreve os requisitos de implantação de certificado PKI de pilha do Azure para sistemas integrados do Azure Stack.
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
ms.date: 09/05/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: d4322e5a7f9d587049e9225c3adde08d04325afc
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2018
ms.locfileid: "44024297"
---
# <a name="azure-stack-public-key-infrastructure-certificate-requirements"></a>Requisitos de certificado de infraestrutura de chave pública do Azure Stack

O Azure Stack tem uma rede de infraestrutura pública usando acessíveis externamente endereços IP públicos atribuídos a um pequeno conjunto de serviços do Azure Stack e, possivelmente, VMs do locatário. Certificados PKI com os nomes DNS apropriados para esses pontos de extremidade de infraestrutura pública do Azure Stack são necessários durante a implantação do Azure Stack. Este artigo fornece informações sobre:

- Quais certificados são necessários para implantar o Azure Stack
- O processo de obtenção de certificados correspondentes às especificações
- Como preparar, validar e usar esses certificados durante a implantação

> [!Note]  
> Durante a implantação, você deve copiar certificados para a pasta de implantação que corresponde ao provedor de identidade que você está implantando contra (Azure AD ou AD FS). Se você usar um único certificado para todos os pontos de extremidade, você deve copiar esse arquivo de certificado para cada pasta de implantação, conforme descrito nas tabelas a seguir. A estrutura de pasta é criada previamente na máquina virtual de implantação e podem ser encontrada em: C:\CloudDeployment\Setup\Certificates. 

## <a name="certificate-requirements"></a>Requisitos de certificado
A lista a seguir descreve os requisitos de certificado são necessários para implantar o Azure Stack: 
- Certificados devem ser emitidos por uma autoridade de certificação interna ou uma autoridade de certificação pública. Se uma autoridade de certificação pública é usada, ela deve ser incluída na imagem base do sistema operacional como parte do que o programa de autoridade de raiz confiável do Microsoft. Você pode encontrar a lista completa aqui: https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca 
- Sua infraestrutura do Azure Stack deve ter acesso à rede local de lista de revogação de certificados (CRL) da autoridade de certificação publicado no certificado. Essa CRL deve ser um ponto de extremidade http
- Quando a rotação de certificados, os certificados devem ser emitidos por qualquer um da mesma autoridade de certificado interno usada para assinar certificados fornecidos na implantação ou qualquer autoridade de certificação pública acima
- Não há suporte para o uso de certificados autoassinados
- Para a implantação e a rotação que você pode usam um único certificado, que abrange todos os espaços de nome nos campos de nome de assunto e o nome alternativo da entidade (SAN) do certificado ou você pode usar o indivíduo certificados para cada um dos namespaces abaixo que o Azure Stack você planeja utilizar os serviços exigem. Observação: as duas abordagens exigem o uso de curingas para pontos de extremidade em que eles são necessários, como **KeyVault** e **KeyVaultInternal**. 
- O algoritmo de assinatura de certificado não pode ser SHA1, pois ele deve ser mais forte. 
- O formato do certificado deve ser PFX, como as chaves públicas e privadas são necessárias para a instalação do Azure Stack. 
- Os arquivos pfx de certificado devem ter um valor de "Assinatura Digital" e "KeyEncipherment" em seu campo "Key Usage".
- Os arquivos pfx de certificado devem ter os valores "Autenticação do servidor (1.3.6.1.5.5.7.3.1)" e "Autenticação do cliente (1.3.6.1.5.5.7.3.2)" no campo "Uso avançado de chave".
- O certificado "emitido para:" campo não deve ser o mesmo que seu "emitido por:" campo.
- As senhas para todos os arquivos pfx de certificado devem ser o mesmo no momento da implantação
- Senha para o pfx de certificado deve ser uma senha complexa.
- Certifique-se de que os nomes de assunto e os nomes alternativos da entidade na correspondência de extensão (x509v3_config) do nome alternativo da entidade. O campo de nome alternativo da entidade permite especificar os nomes de host adicionais (sites, endereços IP, nomes comuns) a ser protegido por um único certificado SSL.

> [!NOTE]  
> Não há suporte para certificados de Self assinado.

> [!NOTE]  
> Suporte para a presença de autoridades de certificação intermediário em IS de cadeia de relações de confiança do certificado. 

## <a name="mandatory-certificates"></a>Certificados obrigatórios
A tabela nesta seção descreve os certificados PKI do ponto de extremidade público do Azure Stack são necessários para o AD do Azure e implantações do AD FS do Azure Stack. Requisitos de certificado são agrupados por área, bem como os namespaces usados e os certificados que são necessários para cada namespace. A tabela também descreve a pasta na qual o seu provedor de soluções copia os certificados diferentes por ponto de extremidade público. 

Certificados com os nomes DNS apropriados para cada ponto de extremidade de infraestrutura pública do Azure Stack são necessários. Nome DNS de cada ponto de extremidade é expresso no formato:  *&lt;prefixo >.&lt; região >. &lt;fqdn >*. 

Para sua implantação, [Região] e [externalfqdn] valores devem coincidir com a região e nomes de domínio externo que você escolheu para seu sistema do Azure Stack. Por exemplo, se o nome da região tiver sido *Redmond* e o nome de domínio externo foi *contoso.com*, os nomes DNS devem ter o formato *&lt;prefixo >. redmond.contoso.com*. O  *&lt;prefixo >* valores são predesignated pela Microsoft para descrever o ponto de extremidade protegido pelo certificado. Além disso, o  *&lt;prefixo >* valores dos pontos de extremidade externos infraestrutura dependem do serviço do Azure Stack que usa o ponto de extremidade específico. 

> [!note]  
> Certificados podem ser fornecidos como um certificado curinga única que abrange todos os namespaces nos campos assunto e o nome alternativo da entidade (SAN) copiados para todos os diretórios ou certificados individuais para cada ponto de extremidade é copiado para o diretório correspondente. Lembre-se de que as duas opções exigem que você use certificados curinga para pontos de extremidade, como **acs** e o Key Vault no qual eles são necessários. 

| Pasta de implantação | Assunto do certificado necessário e nomes alternativos de entidade (SAN) | Escopo (por região) | Namespace de subdomínio |
|-------------------------------|------------------------------------------------------------------|----------------------------------|-----------------------------|
| Portal público | portal.&lt;region>.&lt;fqdn> | Portais | &lt;region>.&lt;fqdn> |
| Portal de administração | adminportal.&lt;region>.&lt;fqdn> | Portais | &lt;region>.&lt;fqdn> |
| Público do Gerenciador de recursos do Azure | gerenciamento. &lt;região >. &lt;fqdn > | Azure Resource Manager | &lt;region>.&lt;fqdn> |
| Administração do Gerenciador de recursos do Azure | adminmanagement. &lt;região >. &lt;fqdn > | Azure Resource Manager | &lt;region>.&lt;fqdn> |
| ACSBlob | *.blob.&lt;region>.&lt;fqdn><br>(Certificado SSL curinga) | Armazenamento de Blobs | blob.&lt;region>.&lt;fqdn> |
| ACSTable | *.table.&lt;region>.&lt;fqdn><br>(Certificado SSL curinga) | Armazenamento de Tabelas | tabela. &lt;região >. &lt;fqdn > |
| ACSQueue | *.queue.&lt;region>.&lt;fqdn><br>(Certificado SSL curinga) | Armazenamento de Filas | queue.&lt;region>.&lt;fqdn> |
| KeyVault | *.vault.&lt;region>.&lt;fqdn><br>(Certificado SSL curinga) | Key Vault | cofre. &lt;região >. &lt;fqdn > |
| KeyVaultInternal | *.adminvault.&lt;region>.&lt;fqdn><br>(Certificado SSL curinga) |  Cofre de chaves interno |  adminvault. &lt;região >. &lt;fqdn > |
| Host de extensão de administração | *.adminhosting. \<região >. \<fqdn > (os certificados SSL curinga) | Host de extensão de administração | adminhosting. \<região >. \<fqdn > |
| Host de extensão público | * .hosting. \<região >. \<fqdn > (os certificados SSL curinga) | Host de extensão público | hospedagem. \<região >. \<fqdn > |

Se você implantar o Azure Stack usando o modo de implantação do AD do Azure, você precisará solicitar certificados listados na tabela anterior. No entanto, se você implantar o Azure Stack usando o modo de implantação do AD FS, você também deve solicitar certificados descritos na tabela a seguir:

|Pasta de implantação|Assunto do certificado necessário e nomes alternativos de entidade (SAN)|Escopo (por região)|Namespace de subdomínio|
|-----|-----|-----|-----|
|ADFS|adfs.*&lt;region>.&lt;fqdn>*<br>(Certificado SSL)|ADFS|*&lt;region>.&lt;fqdn>*|
|Grafo|graph.*&lt;region>.&lt;fqdn>*<br>(Certificado SSL)|Grafo|*&lt;region>.&lt;fqdn>*|
|

> [!IMPORTANT]
> Todos os certificados listados nesta seção devem ter a mesma senha. 

## <a name="optional-paas-certificates"></a>Certificados de PaaS opcionais
Se você estiver planejando implantar os serviços de PaaS do Azure Stack adicionais (SQL, MySQL e o serviço de aplicativo) depois do Azure Stack foi implantado e configurado, você precisará solicitar certificados adicionais para cobrir os pontos de extremidade dos serviços de PaaS. 

> [!IMPORTANT]
> Os certificados que você usa para provedores de recursos do serviço de aplicativo, SQL e MySQL precisam ter a mesma autoridade raiz aqueles usados para os pontos de extremidade globais do Azure Stack. 

A tabela a seguir descreve os pontos de extremidade e os certificados necessários para os adaptadores de SQL e MySQL e para o serviço de aplicativo. Você não precisa copiar esses certificados para a pasta de implantação do Azure Stack. Em vez disso, você fornecer esses certificados, quando você instala os provedores de recursos adicionais. 

|Escopo (por região)|Certificado|Assunto do certificado necessário e nomes alternativos de entidade (SANs)|Namespace de subdomínio|
|-----|-----|-----|-----|
|SQL, MySQL|SQL e MySQL|&#42;.dbadapter.*&lt;region>.&lt;fqdn>*<br>(Certificado SSL curinga)|dbadapter.*&lt;region>.&lt;fqdn>*|
|Serviço de Aplicativo|Certificado SSL de padrão de tráfego da Web|&#42;.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.sso.appservice.*&lt;region>.&lt;fqdn>*<br>(Certificado de SSL curinga do domínio Multi<sup>1</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|Serviço de Aplicativo|API|api.appservice.*&lt;region>.&lt;fqdn>*<br>(Certificado SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|Serviço de Aplicativo|FTP|ftp.appservice.*&lt;region>.&lt;fqdn>*<br>(Certificado SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|Serviço de Aplicativo|SSO|sso.appservice.*&lt;region>.&lt;fqdn>*<br>(Certificado SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|

<sup>1</sup> requer um certificado com vários nomes alternativos da entidade de caractere curinga. Curinga várias SANs em um único certificado não pode ter suporte por todas as autoridades de certificação pública 

<sup>2</sup> um &#42;.appservice. *&lt;região >. &lt;fqdn >* certificado curinga não pode ser usado no lugar desses três certificados (api.appservice. *&lt;região >. &lt;fqdn >*, ftp.appservice. *&lt;região >. &lt;fqdn >* e sso.appservice. *&lt;região >. &lt;fqdn >*. Explicitamente, o serviço de aplicativo requer o uso de certificados separados para esses pontos de extremidade. 

## <a name="learn-more"></a>Saiba mais
Saiba como [gerar certificados PKI para implantação do Azure Stack](azure-stack-get-pki-certs.md). 

## <a name="next-steps"></a>Próximas etapas
[Integração de identidade](azure-stack-integrate-identity.md)

