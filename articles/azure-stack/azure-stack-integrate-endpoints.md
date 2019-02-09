---
title: Azure Stack integration data center – publicar pontos de extremidade | Microsoft Docs
description: Saiba como publicar pontos de extremidade do Azure Stack em seu datacenter
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/06/2019
ms.author: jeffgilb
ms.reviewer: wamota
ms.lastreviewed: 02/06/2019
ms.openlocfilehash: 9a209aaf730b356c8c102eab7a8832ce670204cc
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2019
ms.locfileid: "55977740"
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure Stack integration data center – publicar pontos de extremidade

O Azure Stack configura endereços IP virtuais (VIPs) para suas funções de infraestrutura. Esses VIPs são alocados do pool de endereços IP público. Cada VIP está protegido com uma lista de controle de acesso (ACL) na camada de rede definida pelo software. As ACLs também são usadas entre os comutadores físicos (tes e BMC) para proteger ainda mais a solução. Uma entrada de DNS é criada para cada ponto de extremidade na zona DNS externo que especificou no momento da implantação.


O diagrama de arquitetura a seguir mostra as diferentes camadas da rede e ACLs:

![Imagem estrutural](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>Portas e protocolos (entrada)

Um conjunto de infra-estrutura VIPs é necessário para pontos de extremidade de publicação do Azure Stack a redes externas. O *ponto de extremidade (VIP)* tabela mostra cada ponto de extremidade, a porta necessária e protocolo. Consulte a documentação de implantação de provedor de recursos específico para pontos de extremidade que exigem os provedores de recursos adicionais, como o provedor de recursos do SQL.

A infraestrutura interna VIPs não estão listados porque eles não são necessários para a publicação do Azure Stack.

> [!Note]  
> Usuário VIPs são dinâmicos, definidos pelos próprios usuários com nenhum controle pelo operador do Azure Stack.

|Ponto de extremidade (VIP)|DNS host A record|Protocolo|Portas|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portal (administrador)|Adminportal.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Adminhosting | *.adminhosting.\<region>.\<fqdn> | HTTPS | 443 |
|O Azure Resource Manager (administrador)|Adminmanagement.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portal (usuário)|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Azure Resource Manager (user)|Management.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Grafo|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Lista de certificados revogados|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|TCP E UDP|53|
|Hosting | *.hosting.\<region>.\<fqdn> | HTTPS | 443 |
|Cofre de chaves (usuário)|&#42;.vault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Cofre de chaves (administrador)|&#42;.adminvault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Fila de Armazenamento|&#42;.queue.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Tabela de Armazenamento|&#42;.table.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Blob de Armazenamento|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Provedor de recursos do SQL|sqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|Provedor de Recursos do MySQL|mysqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|Serviço de Aplicativo|&#42;.appservice.*&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300 (Azure Resource Manager)|
|  |ftp.appservice.*&lt;region>.&lt;fqdn>*|TCP, UDP|21, 1021, 10001-10100 (FTP)<br>990 (FTPS)|
|Gateways VPN|     |     |[Consulte as perguntas Frequentes do gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability).|
|     |     |     |     |

## <a name="ports-and-urls-outbound"></a>Portas e URLs (saídas)

O Azure Stack oferece suporte a apenas os servidores de proxy transparente. Em uma implantação onde uplinks um proxy transparente para um servidor proxy tradicional, você deve permitir as seguintes portas e URLs para comunicação de saída:

> [!Note]  
> O Azure Stack não oferece suporte ao uso de rota expressa para acessar os serviços do Azure listados na tabela a seguir.

|Finalidade|Destination URL|Protocolo|Portas|Rede de Origem|
|---------|---------|---------|---------|---------|
|Identidade|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https://secure.aadcdn.microsoftonline-p.com<br>office.com|HTTP<br>HTTPS|80<br>443|VIP - /27 públicos<br>Infraestrutura de rede pública|
|Sindicalização do Marketplace|https://management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|VIP - /27 públicos|
|Patch e atualização|https://&#42;.azureedge.net|HTTPS|443|VIP - /27 públicos|
|Registro|https://management.azure.com|HTTPS|443|VIP - /27 públicos|
|Uso|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.net |HTTPS|443|VIP - /27 públicos|
|Windows Defender|.wdcp.microsoft.com<br>.wdcpalt.microsoft.com<br>*.updates.microsoft.com<br>*.download.microsoft.com<br>https://msdl.microsoft.com/download/symbols<br>https://www.microsoft.com/pkiops/crl<br>https://www.microsoft.com/pkiops/certs<br>https://crl.microsoft.com/pki/crl/products<br>https://www.microsoft.com/pki/certs<br>https://secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|VIP - /27 públicos<br>Infraestrutura de rede pública|
|NTP|(Fornecido para a implantação de servidor IP de NTP)|UDP|123|VIP - /27 públicos|
|DNS|(Servidor de DNS do IP fornecido para implantação)|TCP<br>UDP|53|VIP - /27 públicos|
|CRL|(Em pontos de distribuição de CRL no certificado de URL)|HTTP|80|VIP - /27 públicos|
|LDAP|Floresta do Active Directory fornecido para a integração do Graph|TCP<br>UDP|389|VIP - /27 públicos|
|LDAP SSL|Floresta do Active Directory fornecido para a integração do Graph|TCP|636|VIP - /27 públicos|
|LDAP GC|Floresta do Active Directory fornecido para a integração do Graph|TCP|3268|VIP - /27 públicos|
|LDAP SSL DE GC|Floresta do Active Directory fornecido para a integração do Graph|TCP|3269|VIP - /27 públicos|
|AD FS|AD FS ponto de extremidade metadados fornecido para a integração do AD FS|TCP|443|VIP - /27 públicos|
|     |     |     |     |     |

> [!Note]  
> URLs de saída têm a carga balanceada usando o Gerenciador de tráfego do Azure para fornecer a melhor conectividade possíveis com base na localização geográfica. Com as URLs com balanceamento de carga, a Microsoft pode atualizar e alterar pontos de extremidade de back-end sem afetar os clientes. A Microsoft não compartilha a lista de endereços IP para as URLs com balanceamento de carga. Você deve usar um dispositivo que dá suporte à filtragem por URL em vez de IP.

## <a name="next-steps"></a>Próximas etapas

[Requisitos de PKI de pilha do Azure](azure-stack-pki-certs.md)
