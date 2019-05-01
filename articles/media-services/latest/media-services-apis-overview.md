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
ms.date: 04/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: a336f7faae24517aa4e29c991886107c6a82d2b5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64692550"
---
# <a name="developing-with-media-services-v3-apis"></a>Desenvolver com os serviços de mídia APIs v3

Este artigo discute as regras que se aplicam às entidades e APIs ao desenvolver com os serviços de mídia v3.

## <a name="accessing-the-azure-media-services-api"></a>Acessando a API de serviços de mídia do Azure

Para acessar os recursos dos serviços de mídia do Azure, você pode usar a autenticação de entidade de serviço do Azure Active Directory (AD).
A API de serviços de mídia requer que o usuário ou aplicativo que faz a API REST solicitações têm acesso ao recurso da conta de serviços de mídia e usar um **Colaborador** ou **proprietário** função. A API pode ser acessada com o **Reader** função, mas apenas **obter** ou **lista**   operações estarão disponíveis. Para obter mais informações, consulte [controle de acesso baseado em função para contas de serviços de mídia](rbac-overview.md).

Em vez de criar uma entidade de serviço, considere o uso de identidades gerenciadas para recursos do Azure para acessar a API de serviços de mídia por meio do Azure Resource Manager. Para saber mais sobre identidades gerenciadas para recursos do Azure, consulte [What ' s identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Entidade de serviço do Azure AD 

Se você estiver criando um aplicativo do Azure AD e o serviço principal, o aplicativo deve ser no seu próprio locatário. Depois de criar o aplicativo, dê o aplicativo **Colaborador** ou **proprietário** acesso da função para a conta de serviços de mídia. 

Se você não tiver certeza se você tem permissões para criar um aplicativo do Azure AD, consulte [permissões necessárias](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Na figura a seguir, os números representam o fluxo das solicitações em ordem cronológica:

![Aplicativos de camada intermediária](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Um aplicativo de camada intermediária solicita um token de acesso do AD do Azure que tem os seguintes parâmetros:  

   * Ponto de extremidade do locatário do Azure AD.
   * URI de recurso dos Serviços de Mídia.
   * URI de recurso dos Serviços de Mídia REST.
   * Valores do aplicativo do Azure AD: a ID do cliente e o segredo do cliente.
   
   Para obter todos os valores necessários, consulte [acesso API de serviços de mídia do Azure com a CLI do Azure](access-api-cli-how-to.md)

2. O token de acesso do Azure AD é enviado à camada intermediária.
4. A camada intermediária envia a solicitação à API REST da Mídia do Azure com o token do Azure AD.
5. A camada intermediária obtém novamente os dados dos Serviços de Mídia.

## <a name="naming-conventions"></a>Convenções de nomenclatura

Nomes de recursos do Serviços de Mídia do Azure v3 (por exemplo, ativos, trabalhos, transformações) estão sujeitos a restrições de nomenclatura do Azure Resource Manager. De acordo com o Azure Resource Manager, os nomes dos recursos são sempre exclusivos. Desse modo, é possível usar qualquer cadeia de caracteres de identificador exclusivo (por exemplo, GUIDs) para os nomes dos recursos. 

Nomes de recurso dos Serviços de Mídia não podem incluir: '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', o caractere de aspas simples ou quaisquer caracteres de controle. Todos os outros caracteres são permitidos. O comprimento máximo de um nome de recurso é de 260 caracteres. 

Para obter mais informações sobre a atribuição de nome do Azure Resource Manager, confira: [Requisitos de nomenclatura](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) e [Convenções de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

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

## <a name="provide-feedback"></a>Fornecer comentários

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

[Comece a desenvolver com os serviços de mídia v3 API usando ferramentas/SDKs](developers-guide.md)
