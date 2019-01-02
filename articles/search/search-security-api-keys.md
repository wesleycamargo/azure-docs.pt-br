---
title: Criar, gerenciar e proteger as chaves de API de administração e consulta – Azure Search
description: Chaves de API controlam o acesso ao ponto de extremidade de serviço. Chaves de administração concedem acesso de gravação. As chaves de consulta podem ser criadas para acesso somente leitura.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 6ba63fa776e92dd2f8035cfbbdb8cea2860d106f
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316906"
---
# <a name="create-and-manage-api-keys-for-an-azure-search-service"></a>Criar e gerenciar chaves de API para um serviço do Azure Search

Todas as solicitações para um serviço de pesquisa precisam de uma api-key somente leitura que tenha sido gerada especificamente para o serviço. A api-key é o único mecanismo para autenticar o acesso ao ponto de extremidade de serviço de pesquisa e deve ser incluída em todas as solicitações. Em [soluções REST](search-get-started-nodejs.md#update-the-configjs-with-your-search-service-url-and-api-key), a api-key normalmente é especificada em um cabeçalho de solicitação. Em [soluções .NET](search-howto-dotnet-sdk.md#core-scenarios), uma chave frequentemente é especificada como uma definição de configuração e, em seguida, transmitida como [Credenciais](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) (chave de administração) ou [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.searchcredentials) (chave de consulta) no [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient).

As chaves são criadas com o serviço de pesquisa durante o provisionamento de serviços. É possível exibir e obter valores de chave no [portal do Azure](https://portal.azure.com).

![Página do portal, Configurações, seção Chaves](media/search-manage/azure-search-view-keys.png)

## <a name="what-is-an-api-key"></a>O que é uma api-key

Uma chave de api é uma cadeia de caracteres composta de letras e números gerados aleatoriamente. Por meio de [permissões baseadas em função](search-security-rbac.md), você pode excluir ou ler as chaves, mas não pode substituir uma chave por uma senha definida pelo usuário ou usar o Active Directory como a metodologia de autenticação primária para acessar operações de pesquisa. 

Dois tipos de chaves são usados para acessar seu serviço de pesquisa: administrador (leitura-gravação) e consulta (somente leitura).

|Chave|DESCRIÇÃO|limites|  
|---------|-----------------|------------|  
|Administrador|Concede direitos totais para todas as operações, incluindo a capacidade de gerenciar o serviço, criar e excluir índices, indexadores e fontes de dados.<br /><br /> Duas chaves de API de administrador, chamadas de chaves *primária* e *secundária* no portal, são geradas quando o serviço é criado e podem ser regeneradas individualmente sob demanda. Ter duas chaves permite que você substitua uma chave enquanto usa a segunda chave de acesso contínuo para o serviço.<br /><br /> As chaves de administrador são especificadas somente nos cabeçalhos de solicitação HTTP. Não é possível colocar uma api-key de administrador em uma URL.|Máximo de dois por serviço|  
|Consultar|Concede acesso somente leitura a índices e documentos e normalmente são distribuídas para aplicativos cliente que emitem solicitações de pesquisa.<br /><br /> As chaves de consulta são criadas sob demanda. Você pode criá-las manualmente no portal ou programaticamente por meio da [API REST de gerenciamento](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> As chaves de consulta podem ser especificadas em um cabeçalho de solicitação HTTP para pesquisa, sugestões ou operação de pesquisa. Como alternativa, você pode passar uma chave de consulta como um parâmetro em uma URL. Dependendo de como seu aplicativo cliente formula a solicitação, pode ser mais fácil passar a chave como um parâmetro de consulta:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2017-11-11&api-key=[query key]`|50 por serviço|  

 Visualmente, não há nenhuma distinção entre as chaves de administrador ou de consulta. As duas chaves são cadeias de caracteres compostas de 32 caracteres alfanuméricos gerados aleatoriamente. Se você perder o controle de qual tipo de chave está especificado em seu aplicativo, poderá [verificar os valores de chave no portal](https://portal.azure.com) ou usar a [API REST](https://docs.microsoft.com/rest/api/searchmanagement/) para retornar o valor e o tipo de chave.  

> [!NOTE]  
>  Passar dados confidenciais, como um `api-key`, no URI de solicitação, é considerado uma prática de segurança inadequada. Por esse motivo, o Azure Search só aceita uma chave de consulta como uma `api-key` na cadeia de consulta, e você deve evitar fazer isso, a menos que o conteúdo do índice precise estar publicamente disponível. Em vez disso, recomendamos passar a `api-key` como um cabeçalho de solicitação.  

## <a name="find-api-keys-for-your-service"></a>Localizar chaves de API para seu serviço

Você pode obter chaves de acesso no portal ou por meio da [API REST de Gerenciamento](https://docs.microsoft.com/rest/api/searchmanagement/). Para obter mais informações, consulte [Gerenciar as chaves de API de administrador e consulta](search-security-api-keys.md).

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Liste os [serviços de pesquisa](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) para sua assinatura.
3. Selecione o serviço e, na página de serviço, localize **Configurações** >**Chaves** para exibir as chaves de administrador e de consulta.

![Página do portal, Configurações, seção Chaves](media/search-security-overview/settings-keys.png)

## <a name="regenerate-admin-keys"></a>Regenerar chaves de administrador

Duas chaves de administrador são criadas para cada serviço para que você possa sobrepor uma chave primária usando a chave secundária para acesso continuado.

Se você regenerar chaves primárias e secundárias ao mesmo tempo, nenhum aplicativo usando qualquer uma delas para acessar operações de serviço não terá mais acesso ao serviço.

1. Na página **Configurações** >**Chaves**, copie a chave secundária.
2. Para todos os aplicativos, atualize as configurações de chave de API para usar a chave secundária.
3. Regenere a chave primária.
4. Atualize todos os aplicativos para usar a nova chave primária.

## <a name="secure-api-keys"></a>Proteger api-keys
A chave de segurança é protegida restringindo o acesso por meio do portal ou interfaces do Gerenciador de Recursos (PowerShell ou interface de linha de comando). Conforme observado, os administradores de assinatura podem exibir e gerar novamente todas as chaves de api. Como precaução, revise as atribuições de função para entender quem tem acesso às chaves admin.

+ No painel de serviços, clique em **Controle de Acesso (IAM)** e, em seguida, na guia **Atribuições de função** para exibir as atribuições de função do serviço.

Os membros das seguintes funções podem exibir e regenerar as chaves: Proprietário, Colaborador, [Colaboradores do Serviço de Pesquisa](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor)

> [!Note]
> Para o acesso baseado em identidade sobre os resultados da pesquisa, é possível criar filtros de segurança para cortar resultados por identidade, removendo documentos para os quais o solicitante não deve ter acesso. Para obter mais informações, consulte [Filtros de segurança](search-security-trimming-for-azure-search.md) e [Proteger com Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="see-also"></a>Consulte também

+ [Controle de acesso baseado em função no Azure Search](search-security-rbac.md)
+ [Gerenciar usando o PowerShell](search-manage-powershell.md) 
+ [Artigo Desempenho e otimização](search-performance-optimization.md)