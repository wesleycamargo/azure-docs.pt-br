---
title: Azure pilha datacenter integração - publicar pontos de extremidade | Microsoft Docs
description: Saiba como publicar pontos de extremidade de pilha do Azure no seu datacenter
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 04/06/2018
ms.author: jeffgilb
ms.reviewer: wamota
keywords: ''
ms.openlocfilehash: 014dbf6ff6d30bfb988c404bfe35693fe68f22fd
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure pilha datacenter integração - publicar pontos de extremidade
A pilha do Azure configura endereços IP virtuais (VIPs) para suas funções de infraestrutura. Esses VIPs são alocados do pool de endereços IP público. Cada VIP é protegida com uma lista de controle de acesso (ACL) na camada de rede definida pelo software. As ACLs também são usadas entre os comutadores físicos (tes e BMC) para proteger ainda mais a solução. Uma entrada de DNS é criada para cada ponto de extremidade na zona de DNS externo especificado no momento da implantação.


O seguinte diagrama de arquitetura mostra as camadas de rede diferente e ACLs:

![Imagem estrutural](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>Portas e protocolos (entrada)

Um conjunto de infraestrutura VIPs são necessários para publicação pontos de extremidade de pilha do Azure para redes externas. O *ponto de extremidade (VIP)* tabela mostra cada ponto de extremidade, a porta necessária e o protocolo. Consulte a documentação de implantação do provedor de recursos específicos para pontos de extremidade que exigem os provedores de recursos adicionais, como o provedor de recursos do SQL.

A infraestrutura interna VIPs não são listados porque eles não são necessários para a pilha de publicação do Azure.

> [!NOTE]
> VIPs de usuário são dinâmicos, definidos pelos usuários com nenhum controle pelo operador pilha do Azure.


|Ponto de extremidade (VIP)|Um registro de host DNS|Protocolo|Portas|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portal (administrador)|Adminportal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13012<br>13020<br>13021<br>13026<br>30015|
|Gerenciador de recursos do Azure (administrador)|Adminmanagement.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|Portal (usuário)|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13012<br>13020<br>13021<br>30015<br>13003|
|Gerenciador de recursos do Azure (usuário)|Management.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|Grafo|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Lista de certificados revogados|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|TCP E UDP|53|
|Cofre de chaves (usuário)|&#42;.vault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Cofre de chaves (administrador)|&#42;.adminvault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Fila de Armazenamento|&#42;.queue.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Tabela de Armazenamento|&#42;.table.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Blob de Armazenamento|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Provedor de recursos do SQL|sqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|Provedor de recursos MySQL|mysqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|Serviço de Aplicativo|&#42;.appservice.*&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300 (Gerenciador de recursos do azure)|
|  |ftp.appservice.*&lt;region>.&lt;fqdn>*|TCP, UDP|21, 1021, 10001-101000 (FTP)<br>990 (FTPS)|

## <a name="ports-and-urls-outbound"></a>Portas e URLs (saídas)

Pilha do Azure oferece suporte somente a servidores de proxy transparente. Em uma implantação onde uplinks um proxy transparente em um servidor de proxy tradicional, você deve permitir que as URLs e portas a seguir para comunicação de saída:


|Finalidade|URL|Protocolo|Portas|
|---------|---------|---------|---------|
|Identidade|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https://secure.aadcdn.microsoftonline-p.com|HTTP<br>HTTPS|80<br>443|
|Distribuição do Marketplace|https://management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|
|Patch e atualização|https://&#42;.azureedge.net|HTTPS|443|
|Registro|https://management.azure.com|HTTPS|443|
|Uso|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.com|HTTPS|443|
|O Windows Defender|. wdcp.microsoft.com<br>. wdcpalt.microsoft.com<br>*. updates.microsoft.com<br>*. download.microsoft.com<br>https://msdl.microsoft.com/download/symbols<br>http://www.microsoft.com/pkiops/crl<br>http://www.microsoft.com/pkiops/certs<br>http://crl.microsoft.com/pki/crl/products<br>http://www.microsoft.com/pki/certs<br>https://secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|



## <a name="next-steps"></a>Próximas etapas

[Requisitos de PKI de pilha do Azure](azure-stack-pki-certs.md)