---
title: Gerenciar chaves
titleSuffix: Language Understanding - Azure Cognitive Services
description: Depois de criar uma chave de ponto de extremidade do LUIS no portal do Azure, atribua a chave ao aplicativo LUIS e obtenha a URL de ponto de extremidade correta. Use essa URL de ponto de extremidade para obter previsões do LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 0a278ded7ce290347645f345e4eee0b15972787f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53088095"
---
# <a name="add-an-azure-luis-resource-to-app"></a>Adicionar um recurso de LUIS do Azure ao aplicativo

Depois de criar um recurso do LUIS no portal do Azure, atribua o recurso ao aplicativo LUIS e obtenha a URL de ponto de extremidade correta. Use essa URL de ponto de extremidade para obter previsões do LUIS.

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
<a name="key-concepts"></a>
<a name="authoring-key"></a>
<a name="create-and-use-an-endpoint-key"></a>
<a name="assign-endpoint-key"></a>
<a name="assign-resource"></a>


## <a name="assign-resource-in-luis-portal"></a>Atribuir recursos no portal do LUIS

1. Crie uma chave LUIS no [portal do Azure](https://portal.azure.com). Para instruções adicionais, consulte [Criar uma chave de ponto de extremidade usando o Azure](luis-how-to-azure-subscription.md).
 
2. Selecione **Gerenciar** no menu superior direito e, em seguida, selecione **Chaves e pontos de extremidade**.

    [ ![Página de chaves e pontos de extremidade](./media/luis-manage-keys/keys-and-endpoints.png) ](./media/luis-manage-keys/keys-and-endpoints.png#lightbox)

3. Para adicionar o LUIS, selecione **Atribuir Recurso +**.

    ![Atribuir um recurso ao seu aplicativo](./media/luis-manage-keys/assign-key.png)

4. Selecione um Locatário na caixa de diálogo associado ao endereço de email com o qual você fez logon no site do LUIS.  

5. Escolha o **Nome da Assinatura** associado ao recurso do Azure que deseja adicionar.

6. Selecione o **Nome do recurso do LUIS**. 

7. Selecione **Atribuir recurso**. 

8. Localize a nova linha na tabela e copie a URL de ponto de extremidade. Ela é construída corretamente para fazer uma solicitação GET de HTTP para o ponto de extremidade do LUIS para uma previsão. 

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

### <a name="unassign-resource"></a>Cancelar a atribuição do recurso
Quando você cancela a atribuição da chave do ponto de extremidade, ela não é excluída do Azure. Ela é apenas desvinculada do LUIS. 

Quando uma chave de ponto de extremidade tem a atribuição cancelada ou não é atribuída ao aplicativo, qualquer solicitação para a URL do ponto de extremidade retorna um erro: `401 This application cannot be accessed with the current subscription`. 

### <a name="include-all-predicted-intent-scores"></a>Incluir todas as pontuações de intenção previstas
A caixa de seleção **Incluir todas as pontuações de intenção previstas** permite que a resposta da consulta do ponto de extremidade inclua a pontuação de previsão para cada intenção. 

Essa configuração permite que seu aplicativo que chama o LUIS ou chatbot tome uma decisão programática com base nas pontuações das intenções retornadas. Geralmente, as duas principais intenções são as mais interessantes. Se a pontuação superior for a intenção None, seu chatbot poderá optar por fazer uma pergunta de acompanhamento que realize uma escolha definitiva entre a intenção None e a outra intenção de pontuação alta. 

As intenções e suas classificações também estão incluídas os logs de ponto de extremidade. Você pode [exportar](luis-how-to-start-new-app.md#export-app) esses logs e analisar as pontuações. 

```JSON
{
  "query": "book a flight to Cairo",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.5223427
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.5223427
    },
    {
      "intent": "BookFlight",
      "score": 0.372391433
    }
  ],
  "entities": []
}
```

### <a name="enable-bing-spell-checker"></a>Habilitar o verificador ortográfico do Bing 
Nas **Configurações da URL de ponto de extremidade**, o botão de alternância **Verificador ortográfico do Bing** permite ao LUIS corrigir a ortografia de palavras antes da previsão. Crie uma **[chave de Verificação Ortográfica do Bing](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)**. 

Adicione o parâmetro de cadeia de caracteres de consulta **spellCheck=true** e **bing-spell-check-subscription-key={YOUR_BING_KEY_HERE}** . Substitua o `{YOUR_BING_KEY_HERE}` pela sua chave do verificador ortográfico do Bing.

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```


### <a name="publishing-regions"></a>Regiões de publicação

Saiba mais sobre [regiões](luis-reference-regions.md) de publicação, incluindo a publicação na [Europa](luis-reference-regions.md#publishing-to-europe) e na [Austrália](luis-reference-regions.md#publishing-to-australia). Regiões de publicação são diferentes de regiões de criação. Crie um aplicativo na região de criação correspondente à região de publicação desejada para o ponto de extremidade.

## <a name="assign-resource-without-luis-portal"></a>Atribuir recursos sem o portal do LUIS

Para fins de automação, como um pipeline de CI/CD, você talvez queira automatizar a atribuição de um recurso do LUIS para um aplicativo LUIS. Para isso, é necessário executar as seguintes etapas:

1. Obter um token do Azure Resource Manager deste [site](https://resources.azure.com/api/token?plaintext=true). Esse token expira, então use-o imediatamente. A solicitação retorna um token do Azure Resource Manager.

    ![Solicite o token do Azure Resource Manager e receba o token do Azure Resource Manager](./media/luis-manage-keys/get-arm-token.png)

1. Use o token para solicitar os recursos do LUIS entre assinaturas, da [API Obter contas do Azure do LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), que sua conta de usuário tem acesso. 

    Essa API POST requer as seguintes configurações:

    |Cabeçalho|Valor|
    |--|--|
    |`Authorization`|O valor de `Authorization` é `Bearer {token}`. Observe que o valor do token deve ser precedido pela palavra `Bearer` e um espaço.| 
    |`Ocp-Apim-Subscription-Key`|Sua [chave de criação](luis-how-to-account-settings.md).|

    Essa API retorna uma matriz de objetos JSON das suas assinaturas do LUIS, incluindo a ID da assinatura, o grupo de recursos e o nome do recurso, retornado como o nome da conta. Localize um item na matriz que é o recurso do LUIS a atribuir ao aplicativo LUIS. 

1. Atribua o token para o recurso do LUIS com a API [Atribuir contas do Azure do LUIS a um aplicativo](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515). 

    Essa API POST requer as seguintes configurações:

    |Tipo|Configuração|Valor|
    |--|--|--|
    |Cabeçalho|`Authorization`|O valor de `Authorization` é `Bearer {token}`. Observe que o valor do token deve ser precedido pela palavra `Bearer` e um espaço.|
    |Cabeçalho|`Ocp-Apim-Subscription-Key`|Sua [chave de criação](luis-how-to-account-settings.md).|
    |Cabeçalho|`Content-type`|`application/json`|
    |Querystring|`appid`|A ID do aplicativo de LUIS. 
    |Corpo||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    Quando essa API for bem-sucedida, ela retornará um status 201 – criado. 

## <a name="next-steps"></a>Próximas etapas

Use a chave para publicar seu aplicativo na página **Publicar aplicativo**. Para obter instruções sobre a publicação, confira [Publicar aplicativo](luis-how-to-publish-app.md).

Confira [Keys in LUIS](luis-concept-keys.md) (Chaves no LUIS) para entender os conceitos de autoria e de chave do ponto de extremidade LUIS.
