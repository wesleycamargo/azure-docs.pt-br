---
title: Entenda suas chaves do LUIS
titleSuffix: Azure Cognitive Services
description: 'O LUIS usa duas chaves: criação e ponto de extremidade. A chave de criação é criada para você automaticamente quando você cria sua conta LUIS. Quando você estiver pronto para publicar seu aplicativo de LUIS, será necessário criar a chave do ponto de extremidade, atribuí-la ao seu aplicativo de LUIS e usá-la com a consulta de ponto de extremidade.'
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: d0e7c3f7aeb42fd1525519b7908b1be6fc48aa65
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49362510"
---
# <a name="keys-in-luis"></a>Chaves no LUIS
O LUIS usa duas chaves: [criação](#programmatic-key) e [ponto de extremidade](#endpoint-key). A chave de criação é criada para você automaticamente quando você cria sua conta LUIS. Quando estiver pronto para publicar seu aplicativo LUIS, será necessário [criar a chave do ponto de extremidade](luis-how-to-azure-subscription.md#create-luis-endpoint-key), [atribuí-la](luis-how-to-manage-keys.md#assign-endpoint-key) ao seu aplicativo LUIS e [usá-la com a consulta de ponto de extremidade](#use-endpoint-key-in-query). 

|Chave|Finalidade|
|--|--|
|[Chave de criação](#programmatic-key)|Criação, publicação, gerenciamento de colaboradores e controle de versão|
|[Chave do ponto de extremidade](#endpoint-key)| Consultas|

É importante criar aplicativos LUIS em [regiões](luis-reference-regions.md#publishing-regions) em que você também deseje publicar e consultar.

<a name="programmatic-key" ></a>
## <a name="authoring-key"></a>Chave de criação

Uma chave de criação, também conhecida como uma chave de início, é criada automaticamente quando você cria uma conta LUIS e é gratuita. Você tem uma chave de autoria entre todos os seus aplicativos LUIS para cada [região](luis-reference-regions.md) de autoria. A chave de criação é fornecida para criar seu aplicativo LUIS ou para testar consultas de ponto de extremidade. 

Para localizar a chave de criação, faça logon no [LUIS](luis-reference-regions.md#luis-website) e clique no nome da conta na barra de navegação superior direita para abrir as **Configurações de Conta**.

![chave de criação](./media/luis-concept-keys/programatic-key.png)

Quando desejar fazer **consultas de ponto de extremidade de produção**, crie uma [assinatura do LUIS](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) do Azure. 

> [!CAUTION]
> Para sua conveniência, muitos dos exemplos usam a chave de criação, porque ela fornece algumas chamadas de ponto de extremidade em sua [cota](luis-boundaries.md#key-limits).  

## <a name="endpoint-key"></a>Chave do ponto de extremidade
 Quando precisar de **consultas de ponto de extremidade de produção**, crie uma [chave LUIS](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) no portal do Azure. Lembre-se do nome usado para criar a chave. Você precisará dele quando for adicionar a chave ao aplicativo.

Quando o processo de assinatura do LUIS for concluído, [atribua a chave](luis-how-to-manage-keys.md#assign-endpoint-key) ao aplicativo. 

A chave do ponto de extremidade permite uma cota de ocorrências de ponto de extremidade com base no plano de uso especificado ao criar a chave. Confira [Preços de Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h) para obter informações sobre preços.

A chave do ponto de extremidade pode ser usada para todos os seus aplicativos LUIS ou para aplicativos LUIS específicos. 

Não use a chave do ponto de extremidade para criar aplicativos LUIS. 

## <a name="use-endpoint-key-in-query"></a>Usar a chave do ponto de extremidade na consulta
O ponto de extremidade LUIS aceita dois estilos de consulta, ambos usam a chave do ponto de extremidade, mas em diferentes locais:

|Verbo|Local da chave e da URL de exemplo|
|--|--|
|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn%20on%20the%20lights`<br><br>valor de cadeia de caracteres de consulta para `subscription-key`<br><br>Altere o valor da consulta do ponto de extremidade para o `subscription-key` da chave de criação (de início) para a nova chave do ponto de extremidade para usar a taxa de cota da chave do ponto de extremidade LUIS. Se você criar a chave e atribuí-la, mas não alterar o valor da consulta do ponto de extremidade para subscription-key, você não usará sua cota da chave do ponto de extremidade.|
|[POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2`<br><br> valor de cabeçalho para `Ocp-Apim-Subscription-Key`<br><br>Altere o valor da consulta do ponto de extremidade para o `Ocp-Apim-Subscription-Key` da chave de criação (de início) para a nova chave do ponto de extremidade para usar a taxa de cota da chave do ponto de extremidade LUIS. Se você criar a chave e atribuí-la, mas não alterar o valor da consulta do ponto de extremidade para `Ocp-Apim-Subscription-Key`, você não usará sua cota da chave do ponto de extremidade.|

A ID do aplicativo usado nas URLs anteriores, `df67dcdb-c37d-46af-88e1-8b97951ca1c2`, é o aplicativo público de IoT usado para a [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). 

## <a name="api-usage-of-ocp-apim-subscription-key"></a>Uso de API de Ocp-Apim-Subscription-Key
As APIs do LUIS usam o cabeçalho, `Ocp-Apim-Subscription-Key`. O nome do cabeçalho não é alterado dependendo de qual chave e conjunto de APIs você está usando. Defina o cabeçalho para a chave de autoria para criar APIs. Se você estiver usando o ponto de extremidade, defina o cabeçalho como a chave do ponto de extremidade. 

Não é possível passar a chave do ponto de extremidade para criar APIs. Se fizer isso, você receberá um erro 401 - acesso negado devido à chave de ponto de extremidade inválida. 

## <a name="key-limits"></a>Limites de chave
Confira [Limites de chave](luis-boundaries.md#key-limits) e [Regiões do Azure](luis-reference-regions.md). A chave de criação é gratuita e usada para criação. A chave de ponto de extremidade de LUIS tem uma camada gratuita, mas deve ser criada por você e associada ao aplicativo de LUIS na página **Publicar**. Ela não pode ser usada para criação, mas apenas para consultas do ponto de extremidade.

Regiões de publicação são diferentes de regiões de criação. Certifique-se de criar um aplicativo na região de criação correspondente à região de publicação desejada.

## <a name="key-limit-errors"></a>Erros no limite de chave
Se você exceder sua cota por segundo, receberá um erro HTTP 429. Se você exceder sua cota por mês, receberá um erro HTTP 403. Corrija esses erros obtendo uma chave de [ponto de extremidade](#endpoint-key) de LUIS, [atribuindo](luis-how-to-manage-keys.md#assign-endpoint-key) a chave ao aplicativo na página **Publicar** do site do [LUIS](luis-reference-regions.md#luis-website).

## <a name="automating-assignment-of-the-endpoint-key"></a>Automatizar a atribuição da chave do ponto de extremidade

Para atribuir a chave do ponto de extremidade a um aplicativo de LUIS, você precisa usar o site do LUIS para a criação correta e a publicação de [regiões](luis-reference-regions.md). Não há **nenhum** método automatizado para fazer isso, independentemente do mecanismo, como com um script do Azure Resource Manager, a CLI do Azure, o SDK programático ou com as APIs.

## <a name="next-steps"></a>Próximas etapas

* Conheça os [conceitos](luis-how-to-manage-keys.md#assign-endpoint-key) sobre criação e chaves do ponto de extremidade.