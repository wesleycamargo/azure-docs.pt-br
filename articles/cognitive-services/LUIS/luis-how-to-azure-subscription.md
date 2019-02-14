---
title: Chaves de assinatura
titleSuffix: Language Understanding - Azure Cognitive Services
description: Você não precisa criar chaves de assinatura para usar suas primeiras 1.000 consultas de ponto de extremidade gratuitas. Se receber um erro _fora da cota_ na forma de um erro HTTP 403 ou 429, você precisará criar uma chave e atribuí-la a seu aplicativo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/29/2019
ms.author: diberry
ms.openlocfilehash: 3fd05e2dd5b55dd590af24f0757229bead041b6d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859106"
---
# <a name="using-subscription-keys-with-your-luis-app"></a>Usando chaves de assinatura com seu aplicativo LUIS

Você não precisa criar chaves de assinatura para usar suas primeiras 1.000 consultas de ponto de extremidade gratuitas. Depois que essas consultas de ponto de extremidade forem usadas, crie um recurso do Azure no [portal do Azure](http://portal.azure.com) e, em seguida, atribua esse recurso a um aplicativo do LUIS no [portal do LUIS](https://www.luis.ai).

Se receber um erro _fora da cota_ na forma de um erro HTTP 403 ou 429, você precisará criar uma chave e atribuí-la a seu aplicativo. 

Para testar e protótipo apenas, use a camada gratuita (F0). Para sistemas de produção, use uma camada [paga](https://aka.ms/luis-price-tier). Não use a [chave de criação](luis-concept-keys.md#authoring-key) para consultas de ponto de extremidade em produção.

<a name="create-luis-service"></a>

## <a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>Criar chave de ponto de extremidade de Reconhecimento vocal no portal do Azure

Este procedimento cria um recurso de **Reconhecimento vocal**. Se você quiser um recurso que possa ser usado em todos os Serviços Cognitivos, crie o **[Serviço Cognitivo](../cognitive-services-apis-create-account.md)** com chave tudo em um em vez do recurso de Reconhecimento vocal. 

Essa chave deve ser usada somente para consultas de previsão de ponto de extremidade. Não use essa chave para alterações no modelo ou no aplicativo. 

1. Entre no **[Portal do Azure](https://ms.portal.azure.com/)**. 
1. Selecione o sinal verde **+** no painel superior esquerdo, pesquise por `Language Understanding` no marketplace e, em seguida, selecione **Reconhecimento Vocal** e siga as instruções em **criar experiência**  para criar uma conta de assinatura do LUIS. 

    ![Azure Search](./media/luis-azure-subscription/azure-search.png) 

1. Configure a assinatura com as configurações incluindo o nome da conta, os preços das camadas etc. 

    ![Opção de API do Azure](./media/luis-azure-subscription/azure-api-choice.png) 

1. Depois de criar o recurso de Reconhecimento vocal, você pode exibir as chaves de acesso geradas em **Gerenciamento de Recursos -> Chaves**. A próxima seção mostrará como conectar esse novo recurso a um aplicativo do LUIS no portal do LUIS. Você precisa do nome do recurso do LUIS da etapa 3.

    ![Chaves do Azure](./media/luis-azure-subscription/azure-keys.png)

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


## <a name="assign-resource-key-to-luis-app-in-luis-portal"></a>Atribuir chave de recurso ao aplicativo do LUIS no Portal do LUIS

1. Entre no portal do LUIS, escolha um aplicativo ao qual deseja adicionar a nova chave e, em seguida, selecione **Gerenciar** no menu superior direito e selecione **Chaves e pontos de extremidade**.

    [ ![Página de chaves e pontos de extremidade](./media/luis-manage-keys/keys-and-endpoints.png) ](./media/luis-manage-keys/keys-and-endpoints.png#lightbox)

1. Para adicionar o LUIS, selecione **Atribuir Recurso +**.

    ![Atribuir um recurso ao seu aplicativo](./media/luis-manage-keys/assign-key.png)

1. Selecione um Locatário na caixa de diálogo associada ao endereço de email usado para entrar no site do LUIS.  

1. Escolha o **Nome da Assinatura** associado ao recurso do Azure que deseja adicionar.

1. Selecione o **Nome do recurso do LUIS**. 

1. Selecione **Atribuir recurso**. 

1. Localize a nova linha na tabela e copie a URL de ponto de extremidade. Ela é construída corretamente para fazer uma solicitação GET de HTTP para o ponto de extremidade do LUIS para uma previsão. 

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

Adicione o parâmetro de cadeia de caracteres de consulta **spellCheck=true** e **bing-spell-check-subscription-key={YOUR_BING_KEY_HERE}**. Substitua o `{YOUR_BING_KEY_HERE}` pela sua chave do verificador ortográfico do Bing.

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

Para fins de automação, como um pipeline de CI/CD, você talvez queira automatizar a atribuição de um recurso do LUIS para um aplicativo LUIS. Para fazer isso, é necessário executar as seguintes etapas:

1. Obter um token do Azure Resource Manager deste [site](https://resources.azure.com/api/token?plaintext=true). Esse token expira, então use-o imediatamente. A solicitação retorna um token do Azure Resource Manager.

    ![Solicite e receba o token do Azure Resource Manager](./media/luis-manage-keys/get-arm-token.png)

1. Use o token para solicitar os recursos do LUIS entre assinaturas, da [API Obter contas do Azure do LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), que sua conta de usuário tem acesso. 

    Essa API POST requer as seguintes configurações:

    |Cabeçalho|Valor|
    |--|--|
    |`Authorization`|O valor de `Authorization` é `Bearer {token}`. Observe que o valor do token deve ser precedido pela palavra `Bearer` e um espaço.| 
    |`Ocp-Apim-Subscription-Key`|Sua [chave de criação](luis-how-to-account-settings.md).|

    Essa API retorna uma matriz de objetos JSON das suas assinaturas do LUIS, incluindo a ID da assinatura, o grupo de recursos e o nome do recurso, retornado como o nome da conta. Localize um item na matriz que é o recurso do LUIS a atribuir ao aplicativo LUIS. 

1. Atribua o token para o recurso do LUIS com a API [Atribuir contas do Azure do LUIS a um aplicativo](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515). 

    Essa API POST requer as seguintes configurações:

    |Type|Configuração|Valor|
    |--|--|--|
    |Cabeçalho|`Authorization`|O valor de `Authorization` é `Bearer {token}`. Observe que o valor do token deve ser precedido pela palavra `Bearer` e um espaço.|
    |Cabeçalho|`Ocp-Apim-Subscription-Key`|Sua [chave de criação](luis-how-to-account-settings.md).|
    |Cabeçalho|`Content-type`|`application/json`|
    |Querystring|`appid`|A ID do aplicativo de LUIS. 
    |Corpo||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    Quando essa API for bem-sucedida, ela retornará um status 201 – criado. 

## <a name="change-pricing-tier"></a>Alterar tipo de preço

1.  No [Azure](https://portal.azure.com), localize sua assinatura do LUIS. Selecione a assinatura do LUIS.
    ![Localize sua assinatura do LUIS](./media/luis-usage-tiers/find.png)
1.  Selecione **Tipo de preço** para ver os tipos de preços disponíveis. 
    ![Exibir os tipos de preço](./media/luis-usage-tiers/subscription.png)
1.  Selecione o tipo de preço e clique em **Selecionar** para salvar a alteração. 
    ![Altere sua camada de pagamento do LUIS](./media/luis-usage-tiers/plans.png)
1.  Quando a alteração de preços é concluída, uma janela pop-up verifica a nova camada de preços. 
    ![Verifique sua camada de pagamento do LUIS](./media/luis-usage-tiers/updated.png)
1. Lembre-se de [atribuir essa chave do ponto de extremidade](#assign-endpoint-key) na página **Publicar** e usá-la em todas as consultas de ponto de extremidade. 

## <a name="how-to-fix-out-of-quota-errors-when-the-key-exceeds-pricing-tier-usage"></a>Como corrigir erros de limite de cota quando a chave ultrapassa o uso do tipo de preço
Cada camada permite solicitações de ponto de extremidade para sua conta do LUIS em uma taxa específica. Se a taxa de solicitações for maior do que a taxa permitida de sua conta limitada por minuto ou por mês, as solicitações receberão um erro HTTP de "429: muitas solicitações".

Cada camada permite solicitações cumulativas por mês. Se o total de solicitações for maior do que a taxa permitida, as solicitações receberão um erro HTTP de "403: proibido".  

## <a name="viewing-summary-usage"></a>Exibindo uso de resumo
Você pode exibir informações de uso do LUIS no Azure. A página **Visão geral** mostra as informações de resumidas recentes, incluindo chamadas e erros. Se você fizer uma solicitação de ponto de extremidade do LUIS, acompanhe na **página Visão geral** e aguarde até cinco minutos para o uso ser exibido.

![Exibindo uso de resumo](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>Como personalizar gráficos de uso
As métricas fornecem uma exibição mais detalhada dos dados.

![Métricas padrão](./media/luis-usage-tiers/metrics-default.png)

Você pode configurar os gráficos de métricas para o período de tempo e o tipo de métrica. 

![Métricas personalizadas](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>Alerta de limite total de transações
Se você quiser saber quando atingiu um determinado limite transação, por exemplo, 10.000 transações, crie um alerta. 

![Alertas padrão](./media/luis-usage-tiers/alert-default.png)

Adicionar um alerta de métrica para a métrica **total de chamadas** para um determinado período de tempo. Adicione endereços de email de todas as pessoas que devem receber o alerta. Adicione webhooks para todos os sistemas que devem receber o alerta. Você também poderá executar um aplicativo lógico quando o alerta for disparado. 

## <a name="next-steps"></a>Próximas etapas

Saiba como usar [versões](luis-how-to-manage-versions.md) para gerenciar as alterações ao seu aplicativo do LUIS.
