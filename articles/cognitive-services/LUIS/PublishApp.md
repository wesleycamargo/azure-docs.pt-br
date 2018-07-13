---
title: Publicar seu aplicativo de LUIS | Microsoft Docs
description: Depois de criar e testar seu aplicativo usando o LUIS (Serviço Inteligente de Reconhecimento Vocal), publique-o como um serviço Web no Azure.
services: cognitive-services
titleSuffix: Azure
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: 12a63e65a739be08d436f8f1b53df566255b1fb1
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321808"
---
# <a name="publish-your-trained-app"></a>Publicar seu aplicativo treinado
Quando você terminar de criar e testar seu aplicativo de LUIS, publique-o. Depois que o aplicativo tiver sido publicado, a página Publicar mostrará todos os [pontos de extremidade](luis-glossary.md#endpoint) HTTP associados. Esses pontos de extremidade, por [região](luis-reference-regions.md) e por [chave](Manage-Keys.md), então são integrados em qualquer aplicativo cliente, chatbot ou back-end. 

Você sempre pode [testar](interactive-test.md) seu aplicativo antes da publicação. 

## <a name="production-and-staging-slots"></a>Produção e slots de preparo
Você pode publicar seu aplicativo no **Slot de Preparo** ou no **Slot de Produção**. Usando os dois slots de publicação, isso permite que você tenha duas versões diferentes com pontos de extremidade publicados ou a mesma versão em dois pontos de extremidade diferentes. 

<!-- TBD: what is the technical difference? log files, endpoint quota? -->

## <a name="settings-configuration-requires-publishing-model"></a>A configuração de definições requer o modelo de publicação
Publique o ponto de extremidade depois de alterações às seguintes configurações. 

## <a name="external-services-settings"></a>Configurações de serviços externos
Configurações de serviço externo incluem **[Análise de Sentimento](#enable-sentiment-analysis)** e **[Preparação da Fala](#enable-speech-priming)**.

### <a name="enable-sentiment-analysis"></a>Habilitar análise de sentimento
Nas **Configurações de serviços externos**, a caixa de seleção **Habilitar Análise de Sentimento** permite ao LUIS integrar [Análise de Texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/) para fornecer análise de frase-chave e sentimento. Você não precisa fornecer uma chave de Análise de Texto e não é cobrando nenhum encargo para esse serviço na sua conta do Azure. Depois que você marcar essa configuração, ela será persistente. 

Dados de sentimento são uma pontuação entre 1 e 0 indicando o sentimento positivo (mais próximo de 1) ou negativo (mais próximo de 0) dos dados.

Para obter mais informações sobre a resposta do ponto de extremidade JSON com análise de sentimento, veja [Análise de sentimento](luis-concept-data-extraction.md#sentiment-analysis)

### <a name="enable-speech-priming"></a>Habilitar preparação da fala 
Nas **Configurações de serviços externos**, a caixa de seleção **Habilitar Preparação da Fala** permite que você tenha um único ponto de extremidade para obter um enunciado de um chatbot ou um aplicativo que chama LUIS e receber uma resposta de previsão de LUIS. A Preparação de fala usa o Serviço cognitivo [Speech API](../Speech-Service/rest-apis.md). 

![Imagem da caixa de diálogo de confirmação da preparação de fala](./media/luis-how-to-publish-app/speech-prime-modal.png)

Depois que esse recurso estiver habilitado, publique o seu aplicativo. Quando você publica seu aplicativo de LUIS, seu modelo de aplicativo é enviado ao seu próprio Serviço de fala para preparar o Serviço de fala. AS informações do seu modelo **não** são usadas fora do seu próprio serviço. 

Para concluir o uso da Preparação de fala, é necessário que você use as informações a seguir no [SDK de Fala](../speech-service/speech-sdk-reference.md):
* Uma chave de assinatura de LUIS.
* A ID do aplicativo de LUIS.
* Um domínio de ponto de extremidade, denominado "Nome do host" no SDK de Fala, como "westus.api.cognitive.microsoft.com", em que o primeiro subdomínio é a região na qual o aplicativo foi publicado.

Para obter mais informações, veja a amostra de [Conversão de fala em intenção](http://aka.ms/speechsdk).

Quando seu aplicativo de LUIS é excluído ou o Serviço de fala é excluído, os dados do modelo são removidos. 

## <a name="endpoint-url-settings"></a>Configurações da URL de ponto de extremidade
As configurações de serviços da URL de ponto de extremidade incluem deslocamento de **[Fuso horário](#set-timezone-offset)**, **[todas as pontuações intenção previstas](#include-all-predicted-intent-scores)** e o **[Verificador ortográfico do Bing](#enable-bing-spell-checker)**.

### <a name="set-timezone-offset"></a>Definir o deslocamento de fuso horário 
Parte da opção de slot é a seleção do fuso horário. Essa configuração de fuso horário permite ao LUIS [alterar](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) quaisquer valores de hora datetimeV2 predefinidos durante a previsão de modo que os dados da entidade retornada estejam corretos conforme o fuso horário selecionado. 

### <a name="include-all-predicted-intent-scores"></a>Incluir todas as pontuações de intenção previstas
A caixa de seleção **Incluir todas as pontuações de intenção previstas** permite que a resposta da consulta do ponto de extremidade inclua a pontuação de previsão para cada intenção. 

Essa configuração permite que seu aplicativo que chama o LUIS ou chatbot tome uma decisão programática com base nas pontuações das intenções retornadas. Geralmente, as duas principais intenções são as mais interessantes. Se a pontuação superior for a intenção None, seu chatbot poderá optar por fazer uma pergunta de acompanhamento que realize uma escolha definitiva entre a intenção None e a outra intenção de pontuação alta. 

As intenções e suas classificações também estão incluídas os logs de ponto de extremidade. Você pode [exportar](create-new-app.md#export-app) esses logs e analisar as pontuações. 

```
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
Nas **Configurações da url de ponto de extremidade**, a caixa de seleção **Habilitar verificador ortográfico do Bing** permite ao LUIS corrigir a ortografia de palavras antes da previsão. Isso exige que você crie uma **[Chave de Verificação Ortográfica do Bing](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)**. Depois que a chave tiver sido criada, dois parâmetros querystring serão adicionados à URL do ponto de extremidade na página de publicação. 

Se você estiver criando suas próprias URLs para o seu aplicativo que chama o LUIS, verifique o parâmetro querystring **spellCheck=true** e o **bing-spell-check-subscription-key={YOUR_BING_KEY_HERE}**. Substitua o `{YOUR_BING_KEY_HERE}` pela sua chave do verificador ortográfico do Bing.

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

## <a name="publish-your-trained-app-to-an-http-endpoint"></a>Publique seu aplicativo treinado para um ponto de extremidade HTTP
Abra o aplicativo clicando em seu nome na página **Meus Aplicativos** e, em seguida, clique em **Publicar** no painel superior. 

![Publicar página–](./media/luis-how-to-publish-app/publish-to-production.png)
 
Quando seu aplicativo tiver sido publicado com êxito, uma notificação de êxito em verde será exibida na parte superior do navegador. 

* Escolha se deseja publicar para **Produção** ou **Preparo** selecionando no menu suspenso em **Selecionar slot**. 

## <a name="assign-key"></a>Atribuir chave

Se você quiser usar uma chave que não a Starter_Key livre mostrada, clique no botão **Adicionar Chave**. Essa ação abre uma caixa de diálogo que permite selecionar uma chave de ponto de extremidade existente para atribuir ao aplicativo. Para obter mais informações sobre como criar e adicionar chaves de ponto de extremidade ao seu aplicativo de LUIS, veja [Gerenciar suas chaves](Manage-Keys.md).

Para ver os pontos de extremidade e as chaves associadas a outras regiões, use os botões de opção para mudar as regiões. Cada linha na tabela **Recursos e Chaves** lista os recursos do Azure associados à sua conta e as chaves de ponto de extremidade associadas ao recurso.

## <a name="endpoint-url-construction"></a>Construção da URL do ponto de extremidade
A URL do ponto de extremidade corresponde à região do Azure associada à chave do ponto de extremidade.

Esta tabela convenientemente reflete a configuração de publicação no ponto de extremidade da URL com opções de rota e valores de cadeia de caracteres de consulta. Se você estiver criando suas URLs de ponto de extremidade para seu aplicativo que chama o LUIS, verifique se esses mesmos valores de cadeia de caracteres de consulta e rotas estão definidos para o ponto de extremidade usado – se você desejar defini-los.

A rota da URL é construída com a região e a ID do aplicativo. Se você estiver publicando em outras regiões ou com outros aplicativos, a URL do ponto de extremidade poderá ser construída alterando os valores de ID do aplicativo e da região. 

* Selecione o slot de produção e o botão **Publicar**. Quando a publicação for bem-sucedida, use a URL do ponto de extremidade exibida para acessar seu aplicativo de LUIS. 

### <a name="optional-query-string-parameters"></a>Parâmetros de cadeia de caracteres de consulta opcionais
Os seguintes parâmetros de cadeia de caracteres de consulta podem ser usados com a URL do ponto de extremidade:

<!-- TBD: what about speech priming? -->

|Cadeia de consulta|type|Valor de exemplo|Finalidade|
|--|--|--|--|
|verbose|booleano|verdadeiro|Incluir [todas as pontuações de intenção](#include-all-predicted-intent-scores) para enunciado|
|timezoneOffset|número (a unidade está em minutos)|60|Definir [deslocamento de fuso horário](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) para [entidades datetimeV2 predefinidas](luis-reference-prebuilt-datetimev2.md)|
|spellCheck|booleano|verdadeiro|[Corrigir a ortografia](#enable-bing-spell-checker) do enunciado – usada em conjunto com o parâmetro de cadeia de caracteres de consulta bing-spell-check-subscription-key|
|bing-spell-check-subscription-key|ID da assinatura||Usado em conjunto com o parâmetro de cadeia de caracteres de consulta spellCheck|
|staging|booleano|falso|Selecionar o ponto de extremidade de produção ou de preparo|
|log|booleano|verdadeiro|Adicionar a consulta e os resultados a registrar em log|


## <a name="test-your-published-endpoint-in-a-browser"></a>Testar seu ponto de extremidade publicado em um navegador
Teste seu ponto de extremidade publicado selecionando a URL na coluna **Ponto de extremidade**. O navegador padrão se abre com a URL gerada. Defina o parâmetro de URL "&q" para sua consulta de teste. Por exemplo, acrescente `&q=Book me a flight to Boston on May 4` à sua URL e pressione Enter. O navegador exibe a resposta JSON do seu ponto de extremidade HTTP. 

![Resposta JSON de um ponto de extremidade HTTP publicado](./media/luis-how-to-publish-app/luis-publish-app-json-response.png)

## <a name="next-steps"></a>Próximas etapas

* Veja [Gerenciar chaves](./Manage-Keys.md) para adicionar chaves ao seu aplicativo de LUIS e saber mais sobre como as chaves são mapeadas para regiões.
* Veja [Treinar e testar seu aplicativo](interactive-test.md) para obter instruções sobre como testar seu aplicativo publicado no console de teste.
