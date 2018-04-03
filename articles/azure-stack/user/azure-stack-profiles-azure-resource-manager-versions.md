---
title: Versões de API do provedor de recursos com suporte por perfis na pilha do Azure | Microsoft Docs
description: Saiba mais sobre a versão do Gerenciador de recursos do Azure com suporte perfis na pilha do Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 2E21C8DE-D540-4C1C-A0EF-1B7125DB7A6E
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 8cc5ce1ec113df7ce92c54022dbe1b6219c8c235
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>Versões de API do provedor de recursos com suporte por perfis na pilha do Azure

Um provedor de recursos do Azure fornece recursos que você pode implantar e gerenciar através do Gerenciador de recursos do Azure. Cada provedor oferece operações para trabalhar com recursos. Alguns provedores de recursos comuns incluem Microsoft. Compute, que fornece as máquinas virtuais, a Microsoft, que fornece recursos de conta de armazenamento, e a Microsoft, que fornece recursos relacionados a aplicativos web. Para saber mais, veja [Provedores e tipos de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services).

A tabela a seguir para cada provedor de recursos indica a versão com suporte da versão de API para a pilha do Azure ao usar os perfis.

### <a name="microsoftauthorization"></a>Microsoft.Authorization

Você pode usar o controle de acesso baseado em função para gerenciar as ações em recursos de usuários em sua organização. Esse conjunto de operações permite que você definir funções, atribuir funções aos usuários ou grupos e obter informações sobre permissões. Para obter mais informações, consulte [autorização](https://docs.microsoft.com/rest/api/authorization/).

| Tipos de Recurso | Versões de API |
|---------------------|--------------------|
| Bloqueios | 2017-04-01 |
| Operações | 2015-07-01 |
| Permissões | 2015-07-01 |
| Atribuições de Política | 2016-12-01 (2017-06-01-preview) |
| Definições de política | 2016-12-01 |
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

As APIs de computação do Azure oferecem acesso programático às máquinas virtuais e seus recursos de suporte. Para obter mais informações, consulte [de computação do Azure](https://docs.microsoft.com/en-us/rest/api/compute/).

| Tipo de recurso | Versão da API |
|---------------------------------------------------------------|-------------|
| Conjuntos de Disponibilidade | 2016-03-30 |
| Locais | 2016-03-30 |
| Locations/operations | 2016-03-30 |
| Locations/publishers | 2016-03-30 |
| Usos/locais | 2016-03-30 |
| Locais/vmSizes | 2016-03-30 |
| Operações | 2016-03-30 |
| Máquinas Virtuais | 2016-03-30 |
| Máquinas virtuais/extensões | 2016-03-30 |
| Conjuntos de Escala de Máquina Virtual | 2016-03-30 |
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

| Tipos de Recurso | Versões de API |
|--------------------|--------------------|
| Regras de Alerta | 2016-03-01 |
| Categorias de evento | 2017-03-01-preview |
| Tipos de evento | 2017-03-01-preview |
| Definições de Métrica | 2016-03-01 |
| Operações | 2015-04-01 |

### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

Gerenciar sua chave de cofres, bem como certificados dentro de seu Cofre de chave, chaves e segredos. Para obter mais informações, consulte [referência de API de REST do Cofre de chaves do Azure](https://docs.microsoft.com/rest/api/keyvault/).

| Tipos de Recurso | Versões de API |
|-------------------------|--------------|
| Operações | 2016-10-01 |
| Cofres | 2016-10-01 |
| Os cofres / políticas de acesso | 2016-10-01 |
| Vaults/secrets | 2016-10-01 |

### <a name="microsoftkeyvaultadmin"></a>Microsoft.Keyvault.Admin

Gerenciar sua chave de cofres, bem como certificados dentro de seu Cofre de chave, chaves e segredos. Para obter mais informações, consulte [referência de API de REST do Cofre de chaves do Azure](https://docs.microsoft.com/rest/api/keyvault/).

| Tipos de Recurso | Versões de API |
|------------------|--------------------|
| Locais | 2017-02-01-preview |
| Locations/quotas | 2017-02-01-preview |

### <a name="microsoftnetwork"></a>Microsoft.Network

Resultado da chamada de operações é uma representação da lista de operações de nuvem de rede disponível. Para obter mais informações, consulte [operação REST API](https://docs.microsoft.com/rest/api/operation/).

| Tipos de Recurso | Versões de API |
|---------------------------|--------------|
| conexões | 2015-06-15 |
| Zonas DNS | 2016-04-01 |
| Balanceadores de Carga | 2015-06-15 |
| Gateway de Rede Local | 2015-06-15 |
| Locais | 2016-04-01 |
| Location/operationResults | 2016-04-01 |
| Locations/operations | 2016-04-01 |
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

| Tipos de Recurso | Versões de API |
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
| Subscriptions/location | 2015-012016-09-01 |
| Resultados de assinaturas/operação | 2015-012016-09-01 |
| Subscriptions/providers | 2015-012017-08-01 |
| Grupos de recursos/assinaturas | 2015-012016-09-01 |
| Subscriptions/resourceGroups/resources | 2015-012016-09-01 |
| Assinaturas/recursos | 2015-012016-09-01 |
| Subscriptions/tagNames | 2016-0609-01 |
| Subscriptions/tagNames/tagValues | 2016-0609-01 |
| Locatários | 2015-012017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage 

O provedor de recursos de armazenamento (SRP) permite que você gerencie sua conta de armazenamento e chaves por meio de programação. Para obter mais informações, consulte [referência de API REST do provedor de recursos de armazenamento do Azure](https://docs.microsoft.com/rest/api/storagerp/).

| Tipos de Recurso | Versões de API |
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
