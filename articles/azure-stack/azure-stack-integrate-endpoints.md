---
title: "Azure pilha datacenter integração - publicar pontos de extremidade"
description: Saiba como publicar pontos de extremidade de pilha do Azure no seu datacenter
services: azure-stack
author: jeffgilb
ms.service: azure-stack
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wamota
keywords: 
ms.openlocfilehash: e368109adc7db4c589ac37b28c4891cb3ec5346f
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure pilha datacenter integração - publicar pontos de extremidade

*Aplica-se a: sistemas integrados de pilha do Azure*

A pilha do Azure define vários pontos de extremidade (VIPs - endereços IP virtuais) para suas funções de infraestrutura. Esses VIPs são alocados do pool de endereços IP público. Cada VIP é protegida com uma lista de controle de acesso (ACL) na camada de rede definida pelo software. As ACLs também são usadas entre os comutadores físicos (tes e BMC) para proteger ainda mais a solução. Uma entrada de DNS é criada para cada ponto de extremidade na zona DNS externo que foi especificado no momento da implantação.


O seguinte diagrama de arquitetura mostra as camadas de rede diferente e ACLs:

![Diagrama de arquitetura](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>Portas e protocolos (entrada)

VIPs de infraestrutura que são necessários para publicação pontos de extremidade de pilha do Azure para redes externas são listados na tabela a seguir. A lista mostra cada ponto de extremidade, a porta necessária e o protocolo. Pontos de extremidade necessários para provedores de recursos adicionais, como o provedor de recursos do SQL e outros, são abordados a documentação de implantação do provedor de recurso específico.

Infraestrutura interna que VIPs não são listadas porque eles não são necessários para a pilha de publicação do Azure.

> [!NOTE]
> VIPs de usuário são dinâmicos, definidos pelos usuários com nenhum controle pelo operador pilha do Azure.


|Ponto de extremidade (VIP)|Um registro de host DNS|Protocolo|Portas|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portal (administrador)|Adminportal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13020<br>13021<br>13026<br>30015|
|Gerenciador de recursos do Azure (administrador)|Adminmanagement.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|Portal (usuário)|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13020<br>13021<br>30015<br>13003|
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
|Provedor de recursos MySQL|mysqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304

## <a name="ports-and-urls-outbound"></a>Portas e URLs (saídas)

Pilha do Azure oferece suporte somente a servidores de proxy transparente. Em uma implantação onde uplinks um proxy transparente em um servidor de proxy tradicional, você deve permitir que as URLs e portas a seguir para comunicação de saída:


|Finalidade|URL|Protocolo|Portas|
|---------|---------|---------|---------|
|Identidade|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net|HTTP<br>HTTPS|80<br>443|
|Distribuição do Marketplace|https://management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|
|Patch e atualização|https://&#42;.azureedge.net|HTTPS|443|
|Registro|https://management.azure.com|HTTPS|443|
|Uso|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.com|HTTPS|443|


## <a name="next-steps"></a>Próximas etapas
[Requisitos de PKI de pilha do Azure](azure-stack-pki-certs.md)