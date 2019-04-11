---
title: Desenvolver com APIs v3 – Azure | Microsoft Docs
description: Este artigo discute as regras que se aplicam às entidades e APIs ao desenvolver com os serviços de mídia v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 18b72ceaee0ca0747a0bf2144d5f9ffddbee8b8c
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471854"
---
# <a name="developing-with-media-services-v3-apis"></a>Desenvolver com os serviços de mídia APIs v3

Este artigo discute as regras que se aplicam às entidades e APIs ao desenvolver com os serviços de mídia v3.

## <a name="naming-conventions"></a>Convenções de nomenclatura

Nomes de recursos do Serviços de Mídia do Azure v3 (por exemplo, ativos, trabalhos, transformações) estão sujeitos a restrições de nomenclatura do Azure Resource Manager. De acordo com o Azure Resource Manager, os nomes dos recursos são sempre exclusivos. Desse modo, é possível usar qualquer cadeia de caracteres de identificador exclusivo (por exemplo, GUIDs) para os nomes dos recursos. 

Nomes de recurso dos Serviços de Mídia não podem incluir: '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', o caractere de aspas simples ou quaisquer caracteres de controle. Todos os outros caracteres são permitidos. O comprimento máximo de um nome de recurso é de 260 caracteres. 

Para obter mais informações sobre a atribuição de nome do Azure Resource Manager, confira: [Requisitos de nomenclatura](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) e [Convenções de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="v3-api-design-principles-and-rbac"></a>Princípios de design de API v3 e o RBAC

Um dos princípios de design de chave da API v3 é tornar a API mais segura. APIs v3 não retornar segredos ou as credenciais na **Obtenha** ou **lista** operações. As chaves são sempre nulas, vazias ou corrigidas na resposta. O usuário precisa chamar um método de ação separada para obter as credenciais ou segredos. O **leitor** função não é possível chamar as operações para que ele não é possível chamar operações como Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets. Ter ações separadas permite que você defina permissões mais granulares de segurança RBAC em uma função personalizada se desejado.

Para obter mais informações, consulte:

- [Definições de função interna](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)
- [Use o RBAC para gerenciar o acesso](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Controle de acesso baseado em função para contas de serviços de mídia](rbac-overview.md)
- [Obter política de chave de conteúdo - .NET](get-content-key-policy-dotnet-howto.md).

## <a name="long-running-operations"></a>Operações de longa execução

As operações marcadas com `x-ms-long-running-operation` nos serviços de mídia do Azure [swagger arquivos](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) são longas operações de execução. 

Para obter detalhes sobre como controlar operações assíncronas no Azure, consulte [operações assíncronas](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Os serviços de mídia tem as seguintes operações de longa execução:

* Criar LiveEvent
* Atualizar LiveEvent
* Delete LiveEvent
* Iniciar LiveEvent
* Parar LiveEvent
* Reset LiveEvent
* Criar LiveOutput
* Delete LiveOutput
* Criar StreamingEndpoint
* Atualizar StreamingEndpoint
* Excluir StreamingEndpoint
* Iniciar StreamingEndpoint
* Parar um StreamingEndpoint
* Escala StreamingEndpoint

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtragem, classificação, paginação de entidades dos Serviços de Mídia

Consulte [filtragem, ordenação, paginação de entidades de serviços de mídia do Azure](entities-overview.md)

## <a name="next-steps"></a>Próximas etapas

[Iniciar o desenvolvimento com a API dos Serviços de Mídia v3 usando SDKs/ferramentas](developers-guide.md)
