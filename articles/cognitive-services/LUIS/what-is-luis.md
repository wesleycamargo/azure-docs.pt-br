---
title: O que é o Reconhecimento Vocal (LUIS) - Serviços Cognitivos da Microsoft | Microsoft Docs
description: O Reconhecimento Vocal (LUIS) é um serviço de API baseado em nuvem que aplica inteligência de aprendizado de máquina personalizado em um texto de linguagem natural de conversação do usuário prever o significado geral, e extrair informações detalhadas relevantes. Um aplicativo cliente para LUIS é qualquer aplicativo que se comunica com um usuário em linguagem natural para completar uma tarefa. Exemplos de aplicativos clientes incluem aplicativos de mídia social, chatbots e aplicativos para área de trabalho habilitados para fala.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: overview
ms.date: 08/15/2018
ms.author: diberry
ms.openlocfilehash: a8e9deb7c677d04634b223045adc2d31fa74ba6e
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033032"
---
# <a name="what-is-language-understanding-luis"></a>O que é Reconhecimento Vocal (LUIS)?

O Reconhecimento Vocal (LUIS) é um serviço de API baseado em nuvem que aplica inteligência de aprendizado de máquina personalizado em um texto de linguagem natural de conversação do usuário prever o significado geral, e extrair informações detalhadas relevantes. 

Um aplicativo cliente para LUIS é qualquer aplicativo que se comunica com um usuário em linguagem natural para completar uma tarefa. Exemplos de aplicativos clientes incluem aplicativos de mídia social, chatbots e aplicativos para área de trabalho habilitados para fala.  

![Imagem conceitual de 3 aplicativos clientes trabalhando com Reconhecimento Vocal (LUIS) dos Serviços Cognitivos](./media/luis-overview/luis-entry-point.png "Imagem conceitual de 3 aplicativos clientes trabalhando com Reconhecimento Vocal (LUIS) dos Serviços Cognitivos")

## <a name="use-luis-in-a-chat-bot"></a>Usar o LUIS em um chatbot

<a name="Accessing-LUIS"></a>

Depois que seu aplicativo LUIS for publicado, seu aplicativo cliente envia expressões (texto) para a [API][endpoint-apis] do ponto de extremidade de processamento da linguagem natural LUIS e recebe os resultados como respostas em JSON. Um aplicativo cliente comum para LUIS é um chatbot.


![Imagens conceituais do LUIS trabalhando com o Chatbot para prever o texto do usuário com reconhecimento de linguagem natural (NLP)](./media/luis-overview/luis-overview-process-2.png "Imagens conceituais do LUIS trabalhando com o Chatbot para prever o texto do usuário com reconhecimento de linguagem natural (NLP")

|Etapa|Ação|
|:--|:--|
|1|O aplicativo cliente envia a _expressão_ do usuário (texto em suas próprias palavras), “Eu quero chamar meu representante de RH”. para o ponto de extremidade do LUIS como uma solicitação HTTP.|
|2|O LUIS aplica o modelo aprendido no texto de linguagem natural para fornecer um reconhecimento inteligente sobre a entrada do usuário. O LUIS retorna uma resposta formatada em JSON, com uma intenção principal, “ContatoRH”. A resposta mínima do ponto de extremidade JSON, contém a expressão de consulta e a intenção de maior pontuação. Ele também pode extrair dados, como a entidade do Tipo de contato.|
|3|O aplicativo cliente usa a resposta JSON para tomar decisões sobre como atender às solicitações do usuário. Essas decisões podem incluir algumas árvores de decisão no código do Bot Framework e chamadas para outros serviços. |

O aplicativo LUIS fornece inteligência, para que o aplicativo cliente possa fazer escolhas inteligentes. O LUIS não oferece essas escolhas. 

<!--

### Example of JSON endpoint response

The minimum JSON endpoint response contains the query utterance, and the top scoring intent. It can also extract data such as the following **Contact Type** entity. 

```JSON
{
  "query": "I want to call my HR rep.",
  "topScoringIntent": {
    "intent": "HRContact",
    "score": 0.921233
  },
  "entities": [
    {
      "entity": "call",
      "type": "Contact Type",
      "startIndex": 10,
      "endIndex": 13,
      "score": 0.7615982
    }
  ]
}
```
-->

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Processamento de idioma natural

Um aplicativo LUIS contém um modelo de linguagem natural de domínio específico. Você pode iniciar o aplicativo LUIS com um modelo de domínio predefinido, criar seu próprio modelo ou combinar partes de um domínio predefinido com suas próprias informações personalizadas.

* **Modelo predefinido** LUIS tem muitos modelos de domínio que incluem intenções, expressões e entidades predefinidas. Você pode usar as entidades predefinidas sem a necessidade de usar as intenções e expressões do modelo predefinido. [Modelos de domínio predefinidos](luis-how-to-use-prebuilt-domains.md) incluem todo o design para você e são uma ótima maneira de começar a usar o LUIS rapidamente.

* **Entidades personalizadas** LUIS oferece várias maneiras de identificar suas próprias intenções e entidades personalizadas incluindo entidades de aprendizado de máquina, entidades específicas ou literais e uma combinação de literais e aprendizado de máquina.

## <a name="build-the-luis-model"></a>Criar o modelo LUIS
Criar o modelo com as APIs de [criação](https://aka.ms/luis-authoring-apis) ou com o portal do LUIS.

O modelo LUIS começa com categorias de intenções de usuário chamadas **[intenções](luis-concept-intent.md)**. Cada intenção precisa de exemplos de **[expressões](luis-concept-utterance.md)** do usuário. Cada expressão pode fornecer vários dados que precisam ser extraídos com **[entidades](luis-concept-entity-types.md)**. 

|Expressão de usuário de exemplo|Intenção|Entidades|
|-----------|-----------|-----------|
|"Reservar um voo para __Seattle__?"|BookFlight|Seattle|
|"Quando a sua loja é __aberta__?"|StoreHoursAndLocation|aberta|
|"Agendar uma reunião às __13h__ com __Bob__ na distribuição"|ScheduleMeeting|13h, Bob|

<!--
## What is a natural language model?

A model begins with a list of general user intentions, called _intents_, such as "Book Flight" or "Contact Help Desk." You provide user's example text, called _example utterances_ for the intents. Then mark significant words or phrases in the utterance, called _entities_.


A model includes:

* **[intents](#intents)**: categories of user intentions (overall intended action or result)
* **[entities](#entities)**: specific types of data in utterances such as number, email, or name contained in text
* **[example utterances](#example-utterances)**: example text a user might enter in the client application

### Intents 

An [intent](luis-how-to-add-intents.md), short for _intention_, is a purpose or goal expressed in a user's utterance, such as booking a flight, paying a bill, or finding a news article. You create an intent for each action. A LUIS travel app may define an intent named "BookFlight." Each prediction query includes the top scored intent. 

The client application can use the top scoring intent to trigger an action. For example, when "BookFlight" intent is returned from LUIS, a client application could trigger an API call to an external service for booking a plane ticket.

### Entities

An [entity](luis-how-to-add-entities.md) represents detailed information found within the utterance that is relevant to the user's request. For example, in the utterance "Book a ticket to Paris",  a single ticket is requested, and "Paris" is a location. Two entities are found "a ticket" indicating a single ticket and "Paris" indicating the destination. 

After LUIS returns the entities found in the user’s utterance, the client application can use the list of entities as parameters to trigger an action. For example, booking a flight requires entities like the travel destination, date, and airline.
-->
<!--
### Example utterances

An example [utterance](luis-how-to-add-example-utterances.md) is text input from the user that the client application needs to understand. It may be a sentence, like "Book a ticket to Paris", or a fragment of a sentence, like "Booking" or "Paris flight." Utterances aren't always well-formed, and there can be many utterance variations for a particular intent. Add 10 to 20 example utterances to each intent and mark entities in every utterance.

|Example user utterance|Intent|Entities|
|-----------|-----------|-----------|
|"Book a flight to __Seattle__?"|BookFlight|Seattle|
|"When does your store __open__?"|StoreHoursAndLocation|open|
|"Schedule a meeting at __1pm__ with __Bob__ in Distribution"|ScheduleMeeting|1pm, Bob|
-->
## <a name="query-prediction-endpoint"></a>Ponto de extremidade de previsão da consulta

Depois que o modelo é criado e publicado no ponto de extremidade, o aplicativo cliente envia expressões à API de [ponto de extremidade](https://aka.ms/luis-endpoint-apis) da previsão publicada. A API aplica o modelo ao texto para análise. A API responde com os resultados de previsão em um formato JSON.  

A resposta mínima do ponto de extremidade JSON, contém a expressão de consulta e a intenção de maior pontuação. Ele também pode extrair dados, como a entidade do **Tipo de contato** seguinte. 

```JSON
{
  "query": "I want to call my HR rep.",
  "topScoringIntent": {
    "intent": "HRContact",
    "score": 0.921233
  },
  "entities": [
    {
      "entity": "call",
      "type": "Contact Type",
      "startIndex": 10,
      "endIndex": 13,
      "score": 0.7615982
    }
  ]
}
```

## <a name="improve-model-prediction"></a>Melhorar a previsão do modelo

Depois que um modelo LUIS é publicado e recebe expressões de usuário real, LUIS fornece vários métodos para aumentar a precisão de previsão: [aprendizado ativo](#active-learning) de expressões de ponto de extremidade, [listas de frases](#phrase-lists) para inclusão de palavra de domínio, e [padrões](#patterns) para reduzir o número de expressões necessárias.
<!--
### Active learning

In the [active learning](luis-how-to-review-endoint-utt.md) process, LUIS allows you to adapt the LUIS app to real-world utterances by selecting utterances it received at the endpoint for your review. You can accept or correct the endpoint prediction, retrain, and republish. LUIS learns quickly with this iterative process, taking the minimum amount of your time and effort. 

### Phrase lists 

LUIS provides [phrases lists](luis-concept-feature.md) so you can indicate important words or phrases of the model. LUIS uses these lists to add additional significance to those words and phrases that would otherwise not be found in the model.

### Patterns 

Patterns allow you to simplify an intent's utterance collection into common [templates](luis-concept-patterns.md) of word choice and word order. This allows LUIS to learn quicker by needing fewer example utterances for the intents. Patterns are a hybrid system of regular expressions and machine-learned expressions. 
-->
<a name="using-luis"></a>
<!--
## Authoring and accessing models
Author LUIS from the [authoring](https://aka.ms/luis-authoring-apis) APIs or from the LUIS portal. Query the published prediction endpoint of the model from the [endpoint](https://aka.ms/luis-endpoint-apis) APIs.
-->

## <a name="integrating-with-luis"></a>Integração com o LUIS
O LUIS, como uma API REST, pode ser usado com qualquer produto, serviço ou estrutura que faz uma solicitação HTTP. A lista a seguir contém os principais produtos e serviços da Microsoft usados com o LUIS.

Os aplicativos clientes da Microsoft para LUIS incluem:
* [Bot de aplicativo Web](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-3.0) que cria rapidamente um chatbot habilitado para LUIS para conversar com um usuário por meio de entrada de texto. Usa o [Bot Framework][bot-framework] versão [3.x](https://github.com/Microsoft/BotBuilder) ou [4.x](https://github.com/Microsoft/botbuilder-dotnet) para uma experiência completa de bot.
* [Windows Mixed Reality](https://docs.microsoft.com/windows/mixed-reality/) - saiba mais com esse [Curso de realidade misturada](https://docs.microsoft.com/windows/mixed-reality/mr-azure-303) com o LUIS. 

Ferramentas da Microsoft para usar o LUIS com um bot:
* [Expedição](https://aka.ms/dispatch-tool) permite que vários aplicativos LUIS e QnA Maker sejam usados em um aplicativo pai usando o modelo de dispatcher.
* [Aprendiz de conversa](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) permite que você crie conversas de bot mais rapidamente com o LUIS.
* [Bate-papo de personalidade do projeto](https://docs.microsoft.com/azure/cognitive-services/project-personality-chat/overview) para lidar com conversa informais do bot.

Outros Serviços Cognitivos usados com o LUIS:
* [QnA Maker][qnamaker] permite que vários tipos de texto sejam combinados em uma base de conhecimento de perguntas e respostas.
* [API de Verificação Ortográfica do Bing](../bing-spell-check/proof-text.md) fornece correção de texto antes da previsão. 
* [Serviço de Fala](../Speech-Service/overview.md) converte as solicitações de linguagem falada em texto. 



## <a name="next-steps"></a>Próximas etapas

Crie um novo aplicativo LUIS com um domínio [predefinido](luis-get-started-create-app.md) ou [personalizado](luis-quickstart-intents-only.md). [Consulte o ponto de extremidade de previsão](luis-get-started-cs-get-intent.md) de um aplicativo de IoT público.

<!-- Reference-style links -->
[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://aka.ms/luis-authoring-api
[endpoint-apis]: https://aka.ms/luis-endpoint-apis
[qnamaker]: https://qnamaker.ai/