---
title: Versões de API do provedor de recursos com suporte por perfis na pilha do Azure | Microsoft Docs
description: Saiba mais sobre a versão do Gerenciador de recursos do Azure com suporte perfis na pilha do Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 1a516c890441c3b703d43f31816b7c37cac364fd
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054374"
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>Versões de API do provedor de recursos com suporte por perfis na pilha do Azure

Você pode encontrar os números de versão e o provedor de recursos para cada perfil de API usado pela pilha do Azure neste artigo. As tabelas neste artigo listam as versões com suporte para cada provedor de recursos e as versões de API dos perfis. Cada provedor de recursos contém um conjunto de tipos de recurso e os números de versão específico.

O perfil de API usa convenções de nomenclatura de três:
 - mais recente
 - aaaa-mm-dd-híbrido
 - aaaa-mm-dd-perfil

Para obter uma explicação de perfis de API e cadência de lançamento de versão para a pilha do Azure, consulte [perfis de versão de API de gerenciamento na pilha do Azure](azure-stack-version-profiles.md).

> [!Note]  
> O **mais recente** API perfil contém a versão mais recente da versão de API do provedor de recursos e não estiver neste artigo.

## <a name="overview-of-2018--03-01-hybrid"></a>Visão geral de 2018-03-01-híbrido

| Provedor de recursos | versão de API |
|-----------------------------------------------|-----------------------------------------------------|
| Microsoft.Compute | 2017-03-30 |
| Microsoft.Network | 2017-10-01<br>Gateway de VPN será 2017-03-01 |
| Microsoft (plano de dados) | 2017-04-17 |
| Microsoft (plano de controle) | 2016-01-01 |
| Microsoft. Web | 2016-08-01<br>qual é a versão mais recente (a partir de agora) no Azure |
| Microsoft.KeyVault | 2016-10-01 (não alterar) |
| Microsoft.Resources (Gerenciador de recursos do Azure em si) | 2016-02-01 |
| Microsoft (operações de política) | 2015-11-01 |
| Microsoft.insights | 2015-11-01 |
| Microsoft.Keyvault | 2016-10-01 |
| Política | 2016-10-01 |
| Recursos | 2016-10-01 |
| Resources_Links | 2016-10-01 |
| Resources_Locks | 2016-10-01 |
| Assinaturas | 2016-10-01 |

Para mais de uma lista das versões para cada tipo de recurso para os provedores no perfil de api, consulte [detalhes para 2018-03-01-híbrido](#details-for-the-2018-03-01-hybrid) perfil.

## <a name="overview-of-2017-03-09-profile"></a>Visão geral de 2017-03-09-perfil

| Provedor de recursos | versão de API |
|------------------------------------------------|------------------------------|
| Microsoft.Compute | 2016-03-30 |
| Microsoft.Network | 2015-06-15 |
| Microsoft (plano de dados) | 2015-04-05  |
| Microsoft (plano de controle) | 2016-01-01   |
| Microsoft.Websites | 2016-01-01 |
| Microsoft.KeyVault | 2016-10-01<br>(Não alterar) |
| Microsoft.Resources<br>(Azure próprio Gerenciador de recursos) | 2016-02-01 |
| Microsoft.Authorization<Br>(operações de política) | 2015-11-01 |
| Microsoft.insights | 2015-11-01 |
| Microsoft.Keyvault | 2016-10-01 |
| Política | 2015-10-01-preview |
| Recursos | 2016-02-01 |
| Resources_Links | 2016-09-01 |
| Resources_Locks | 2016-09-01 |
| Assinaturas | 2016-06-1 |

Para mais de uma lista das versões para cada tipo de recurso para os provedores no perfil de api, consulte [detalhes de 2017-03-09-perfil](#details-for-the-2017-03-09-profile)

## <a name="details-for-the-2018-03-01-hybrid"></a>Detalhes de 2018-03-01-híbrido

### <a name="microsoftauthorization"></a>Microsoft.Authorization

Você pode usar o controle de acesso baseado em função para gerenciar as ações em recursos de usuários em sua organização. Esse conjunto de operações permite que você definir funções, atribuir funções aos usuários ou grupos e obter informações sobre permissões. Para obter mais informações, consulte [autorização](https://docs.microsoft.com/rest/api/authorization/).

| Tipos de recurso | Versões de API |
|---------------------|--------------------|
| Bloqueios | 2017-04-01 |
| Operações | 2015-07-01 |
| Permissões | 2015-07-01 |
| Atribuições de Política | 2016-12-01 (2017-06-01-preview) |
| Definições de Política | 2016-12-01 |
| Operações do provedor | 2015-07-01-preview |
| Atribuições de função | 2015-07-01 |
| Definições de função | 2015-07-01 |

### <a name="microsoftcommerce"></a>Microsoft.Commerce

| Tipo de recurso | Versão da API |
|----------------------------------|----------------------|
| Assinaturas de delegado do provedor | 2015-06-01 - preview |
| Agregações de uso de delegados | 2015-06-01 - preview |
| Recursos de previsão gastos | 2015-06-01-preview |
| Operações | 2015-06-01 - preview |
| Agregações de uso do assinante | 2015-06-01 - preview |
| Agregações de Uso | 2015-06-01 - preview |

### <a name="microsoftcompute"></a>Microsoft.Compute

As APIs de computação do Azure oferecem acesso programático às máquinas virtuais e seus recursos de suporte. Para obter mais informações, consulte [de computação do Azure](https://docs.microsoft.com/rest/api/compute/).

| Tipo de recurso | Versão da API |
|---------------------------------------------------------------|-------------|
| Conjuntos de Disponibilidade | 2016-03-30 |
| Locais | 2016-03-30 |
| Locais/operações | 2016-03-30 |
| Locais/editores | 2016-03-30 |
| Usos/locais | 2016-03-30 |
| Locais/vmSizes | 2016-03-30 |
| Operações | 2016-03-30 |
| Máquinas Virtuais | 2016-03-30 |
| Máquinas virtuais/extensões | 2016-03-30 |
| Conjuntos de Dimensionamento de Máquinas Virtuais | 2016-03-30 |
| Extensões/conjuntos de escala de máquinas virtuais | 2016-03-30 |
| Interfaces de rede/conjuntos de escala de máquinas virtuais | 2016-03-30 |
| Máquinas virtuais/conjuntos de escala de máquina virtual | 2016-03-30 |
| Conjuntos de escala de máquinas virtuais/virtualMachines/networkInterfaces | 2016-03-30 |

### <a name="microsoftgallery"></a>Microsoft.Gallery

| Tipo de recurso | Versão da API |
|------------------|-------------|
| Curadoria | 2015-04-01 |
| Conteúdo da Curadoria | 2015-04-01 |
| Extrair da Curadoria | 2015-04-01 |
| Itens da Galeria | 2015-04-01 |
| Operações | 2015-04-01 |
| Portal | 2015-04-01 |
| Search | 2015-04-01 |
| Sugerir | 2015-04-01 |

### <a name="microsoftinsights"></a>Microsoft.insights

| Tipos de recurso | Versões de API |
|--------------------|--------------------|
| Operações | 2015-04-01 |
| Tipos de evento | 2015-04-01 |
| Categorias de evento | 2015-04-01 |
| Definições de Métrica | 2018-01-01 |
| Métricas | 2018-01-01 |
| Configurações de Diagnóstico | 2017-05-01-preview |
| Categorias de configurações de diagnóstico | 2017-05-01-preview |


### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

Gerenciar sua chave de cofres, bem como certificados dentro de seu Cofre de chave, chaves e segredos. Para obter mais informações, consulte [referência de API de REST do Cofre de chaves do Azure](https://docs.microsoft.com/rest/api/keyvault/).

| Tipos de recurso | Versões de API |
|-------------------------|--------------|
| Operações | 2016-10-01 |
| Cofres | 2016-10-01 |
| Os cofres / políticas de acesso | 2016-10-01 |
| Vaults/secrets | 2016-10-01 |

### <a name="microsoftnetwork"></a>Microsoft.Network

Resultado da chamada de operações é uma representação da lista de operações de nuvem de rede disponível. Para obter mais informações, consulte [operação REST API](https://docs.microsoft.com/rest/api/operation/).

| Tipos de recurso | Versões de API |
|---------------------------|--------------|
| conexões | 2015-06-15 |
| Zonas DNS | 2016-04-01 |
| Balanceadores de Carga | 2015-06-15 |
| Gateway de Rede Local | 2015-06-15 |
| Locais | 2016-04-01 |
| Location/operationResults | 2016-04-01 |
| Locais/operações | 2016-04-01 |
| Usos/locais | 2016-04-01 |
| Interfaces de Rede | 2015-06-15 |
| Grupos de segurança de rede | 2015-06-15 |
| Operações | 2015-06-15 |
| Endereço IP público | 2015-06-15 |
| Tabelas de Rotas | 2015-06-15 |
| Gateway de Rede Virtual | 2015-06-15 |
| Redes Virtuais | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

O Azure Resource Manager permite que você implante e gerencie a infraestrutura para suas soluções do Azure. Organize os recursos relacionados em grupos de recursos e implante seus recursos com modelos JSON. Para obter uma introdução à implantação e gerenciamento de recursos no Gerenciador de recursos, consulte [visão geral do Gerenciador de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

| Tipos de recurso | Versões de API |
|-----------------------------------------|-------------------|
| Registros de aplicativo | 2015-01-01 |
| Verificar Nome do Recurso | 2015-012016-09-01 |
| Provedores de delegados | 2015-01-01 |
| Provedores de ofertas delegadas | 2015-01-01 |
| DelegatedProviders/offers/estimatePrice | 2015-01-01 |
| Implantações | 2016-0209-01 |
| Implantações/operações | 2016-0209-01 |
| Metadados de extensões | 2015-01-01 |
| Links | 2015-012016-09-01 |
| Locais | 2015-01-01 |
| Ofertas | 2015-01-01 |
| Operações | 2015-01-01 |
| Provedores | 2015-012017-08-01 |
| Grupos de recursos | 2015-012016-09-01 |
| Recursos | 2015-012016-09-01 |
| Assinaturas | 2015-012016-09-01 |
| Assinaturas/local | 2015-012016-09-01 |
| Resultados de assinaturas/operação | 2015-012016-09-01 |
| Assinaturas/provedores | 2015-012017-08-01 |
| Grupos de recursos/assinaturas | 2015-012016-09-01 |
| Subscriptions/resourceGroups/resources | 2015-012016-09-01 |
| Assinaturas/recursos | 2015-012016-09-01 |
| Assinaturas/tagNames | 2016-0609-01 |
| Subscriptions/tagNames/tagValues | 2016-0609-01 |
| Locatários | 2015-012017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage 

O provedor de recursos de armazenamento (SRP) permite que você gerencie sua conta de armazenamento e chaves por meio de programação. Para obter mais informações, consulte [referência de API REST do provedor de recursos de armazenamento do Azure](https://docs.microsoft.com/rest/api/storagerp/).

| Tipos de recurso | Versões de API |
|-------------------------|--------------|
| Verificar disponibilidade do nome | 2016-01-01 |
| Locais | 2016-01-01 |
| Locations/quotas | 2016-01-01 |
| Operações | 2016-01-01 |
| StorageAccounts | 2016-01-01 |
| Usos | 2016-01-01 |

## <a name="details-for-the-2017-03-09-profile"></a>Detalhes de 2017-03-09-perfil

### <a name="microsoft-authorization"></a>Autorização da Microsoft

| Tipos de recurso | Versões de API |
|---------------------|---------------------------------|
| Bloqueios | 2017-04-01 |
| Operações | 2015-07-01 |
| Permissões | 2015-07-01 |
| Atribuições de Política | 2016-12-01 (2017-06-01-preview) |
| Definições de Política | 2016-12-01 |
| Operações do provedor | 2015-07-01-preview |
| Atribuições de função | 2015-07-01 |
| Definições de função | 2015-07-01 |

### <a name="microsoftcompute"></a>Microsoft.Compute

| Tipo de recurso | Versão da API |
|---------------------------------------------------------------|-------------|
| Conjuntos de Disponibilidade | 2016-03-30 |
| Locais | 2016-03-30 |
| Locais/operações | 2016-03-30 |
| Locais/editores | 2016-03-30 |
| Usos/locais | 2016-03-30 |
| Locais/vmSizes | 2016-03-30 |
| Operações | 2016-03-30 |
| Máquinas Virtuais | 2016-03-30 |
| Máquinas virtuais/extensões | 2016-03-30 |
| Conjuntos de Dimensionamento de Máquinas Virtuais | 2016-03-30 |
| Extensões/conjuntos de escala de máquinas virtuais | 2016-03-30 |
| Interfaces de rede/conjuntos de escala de máquinas virtuais | 2016-03-30 |
| Máquinas virtuais/conjuntos de escala de máquina virtual | 2016-03-30 |
| Conjuntos de escala de máquinas virtuais/virtualMachines/networkInterfaces | 2016-03-30 |

### <a name="microsoftnetwork"></a>Microsoft.Network

| Tipos de recurso | Versões de API |
|---------------------------|--------------|
| conexões | 2015-06-15 |
| Zonas DNS | 2016-04-01 |
| Balanceadores de Carga | 2015-06-15 |
| Gateway de Rede Local | 2015-06-15 |
| Locais | 2016-04-01 |
| Location/operationResults | 2016-04-01 |
| Locais/operações | 2016-04-01 |
| Usos/locais | 2016-04-01 |
| Interfaces de Rede | 2015-06-15 |
| Grupos de segurança de rede | 2015-06-15 |
| Operações | 2015-06-15 |
| Endereço IP público | 2015-06-15 |
| Tabelas de Rotas | 2015-06-15 |
| Gateway de Rede Virtual | 2015-06-15 |
| Redes Virtuais | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

| Tipos de recurso | Versões de API |
|-----------------------------------------|--------------|
| Registros de aplicativo | 2015-01-01 |
| Verificar Nome do Recurso | 2016-09-01 |
| Provedores de delegados | 2015-01-01 |
| Provedores de ofertas delegadas | 2015-01-01 |
| DelegatedProviders/offers/estimatePrice | 2015-01-01 |
| Implantações | 2016-09-01 |
| Implantações/operações | 2016-09-01 |
| Metadados de extensões | 2015-01-01 |
| Links | 2016-09-01 |
| Locais | 2015-01-01 |
| Ofertas | 2015-01-01 |
| Operações | 2015-01-01 |
| Provedores | 2017-08-01 |
| Grupos de recursos | 2016-09-01 |
| Recursos | 2016-09-01 |
| Assinaturas | 2016-09-01 |
| Assinaturas/local | 2016-09-01 |
| Resultados de assinaturas/operação | 2016-09-01 |
| Assinaturas/provedores | 2017-08-01 |
| Grupos de recursos/assinaturas | 2016-09-01 |
| Subscriptions/resourceGroups/resources | 2016-09-01 |
| Assinaturas/recursos | 2016-09-01 |
| Subscriptiosn/tagNames | 2016-09-01 |
| Subscriptions/tagNames/tagValues | 2016-09-01 |
| Locatários | 2017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage

| Tipos de recurso | Versões de API |
|-------------------------|--------------|
| Verificar disponibilidade do nome | 2016-01-01 |
| Locais | 2016-01-01 |
| Locations/quotas | 2016-01-01 |
| Operações | 2016-01-01 |
| StorageAccounts | 2016-01-01 |
| Usos | 2016-01-01 |

## <a name="next-steps"></a>Próximas etapas

* [Instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md)
* [Configurar o ambiente do PowerShell do usuário a pilha do Azure](azure-stack-powershell-configure-user.md)  
