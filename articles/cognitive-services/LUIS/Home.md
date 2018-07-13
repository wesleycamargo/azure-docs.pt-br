---
title: Sobre o Reconhecimento Vocal (LUIS) no Azure | Microsoft Docs
description: Saiba como usar o Reconhecimento Vocal (LUIS) para levar a capacidade de aprendizado de máquina para seus aplicativos.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/22/2017
ms.author: v-geberr
ms.openlocfilehash: 1c68c586fb799a540f70804d181aa66b2bda9e97
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952531"
---
# <a name="what-is-language-understanding-luis"></a>O que é Reconhecimento Vocal (LUIS)?
O Reconhecimento Vocal (LUIS) é um serviço baseado em nuvem que aplica aprendizado de máquina personalizado em um texto de linguagem natural de conversação do usuário prever o significado geral, e extrair informações detalhadas relevantes. 

Um aplicativo cliente para LUIS pode ser qualquer aplicativo que se comunica com um usuário em linguagem natural para completar uma tarefa. Exemplos de aplicativos clientes incluem aplicativos de mídia social, chatbots e aplicativos para área de trabalho habilitados para fala.  

![Imagem conceitual de 3 aplicativos que alimentam informações no LUIS](./media/luis-overview/luis-entry-point.png)

## <a name="what-is-a-luis-app"></a>O que é um aplicativo LUIS?
Um aplicativo LUIS contém um modelo de linguagem natural de domínio específico que você cria. Você pode iniciar seu aplicativo LUIS com um modelo de domínio predefinido, criar seu próprio ou combinar partes de um domínio predefinido com suas próprias informações personalizadas.

[Modelos de domínio predefinidos](luis-how-to-use-prebuilt-domains.md) incluem todas essas peças para você e são uma ótima maneira de começar a usar LUIS rapidamente.

O aplicativo LUIS também contém as configurações de integração, [colaboradores](luis-concept-collaborator.md), e [versões](luis-concept-version.md).

## <a name="using-a-luis-app"></a>Usar um aplicativo LUIS
<a name="Accessing-LUIS"></a> Depois que seu aplicativo LUIS é publicado, seu aplicativo cliente envia expressões para a [API do ponto de extremidade][endpoint-apis] LUIS e recebe os resultados de previsão como respostas em JSON.

No diagrama a seguir, primeiro seu chatbot cliente envia o texto de usuário que uma pessoa deseja em suas próprias palavras para LUIS em uma solicitação HTTP. Segundo, o LUIS aplica seu modelo adquirido para a linguagem natural para obter sentido da entrada do usuário e retorna uma resposta de formato JSON (JavaScript Object Notation). Terceiro, seu chatbot cliente usa a resposta JSON para atender às solicitações do usuário. 

![Imagens conceituais de LUIS trabalhando com Chatbot](./media/luis-overview/luis-overview-process-2.png)

### <a name="example-of-json-endpoint-response"></a>Exemplo de resposta do ponto de extremidade JSON

A resposta do ponto de extremidade JSON, contém no mínimo a expressão de consulta e a intenção de maior pontuação. 

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

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>
## <a name="what-is-a-natural-language-model"></a>O que é um modelo de linguagem natural?
Um modelo começa com uma lista de intenções gerais do usuário, chamado _intenções_, como "Reservar voo" ou "Contato técnico." Você fornece o texto de exemplo do usuário, chamado de _expressões de exemplo_ para as intenções. Em seguida, marca significativas palavras ou frases na expressão, chamadas _entidades_.


Um modelo inclui:

* **[intenções](#intents)**: categorias de intenção do usuário (ação pretendida ou resultado)
* **[entidades](#entities)**: tipos específicos de dados em expressões como nome, e-mail ou número
* **[expressões de exemplo](#example-utterances)**: texto de exemplo que um usuário insere no aplicativo cliente

### <a name="intents"></a>Intenções 
Uma [intenção](luis-how-to-add-intents.md), abreviação de _intenção_, é a finalidade ou meta expressa na expressão de um usuário, como reservar um voo, pagar uma fatura ou localizar um artigo de notícias. Você cria uma intenção para cada ação. Um aplicativo de viagem LUIS pode definir uma intenção denominada "BookFlight". Seu aplicativo cliente pode usar a intenção de maior pontuação para acionar uma ação. Por exemplo, quando a intenção "BookFlight" é retornada de LUIS, seu aplicativo cliente pode acionar uma chamada à API para um serviço externo para reservar uma passagem de avião.

### <a name="entities"></a>Entidades
Uma [entidade](luis-how-to-add-entities.md) representa informações detalhadas encontradas dentro da expressão que é relevante para a solicitação do usuário. Por exemplo, na expressão "Reserve uma passagem para Paris", uma única passagem é solicitada e "Paris" é um local. Duas entidades são encontradas: “uma passagem”, indicando uma única passagem, e “Paris”, indicando o destino.  

Depois que LUIS retorna as entidades encontradas na expressão do usuário, o aplicativo cliente pode usar a lista de entidades como parâmetros para acionar uma ação. Por exemplo, reservar um voo requer entidades, como o destino de viagem, data e companhia aérea.

LUIS fornece várias maneiras de identificar e categorizar entidades.

* **Entidades predefinidas** LUIS tem muitos modelos de domínio que incluem intenções, expressões e [entidades predefinidas](luis-prebuilt-entities.md). Você pode usar as entidades predefinidas sem a necessidade de usar as intenções e expressões do modelo predefinido. As entidades predefinidas economizam tempo.

* **Entidades personalizadas** LUIS oferece várias maneiras de identificar suas próprias [entidades](luis-concept-entity-types.md) personalizadas incluindo entidades de aprendizado de máquina, entidades específicas ou literais e uma combinação de literais e aprendizado de máquina.

### <a name="example-utterances"></a>Exemplo de enunciados
Um exemplo de [expressão](luis-how-to-add-example-utterances.md) é entrada de texto do usuário que o aplicativo cliente precisa reconhecer. Pode ser uma frase, como "Reservar uma passagem para Paris" ou um fragmento de uma frase, como "Reservar" ou "Voo Paris." As expressões nem sempre são bem formadas e pode haver muitas variações de expressão para uma intenção específica. Adicione 10 a 20 expressões de exemplo para cada intenção e marque as entidades em cada expressão.

|Expressão de usuário de exemplo|Intenção|Entidades|
|-----------|-----------|-----------|
|"Reservar um voo para __Seattle__?"|BookFlight|Seattle|
|"Quando a sua loja é __aberta__?"|StoreHoursAndLocation|aberta|
|"Agendar uma reunião às __13h__ com __Bob__ na distribuição"|ScheduleMeeting|13h, Bob|

## <a name="improve-prediction-accuracy"></a>Melhorar a precisão da previsão
Depois que seu aplicativo LUIS é publicado e recebe expressões de usuário real, LUIS fornece vários métodos para aumentar a precisão de previsão: [aprendizado ativo](#active-learning) de expressões de ponto de extremidade, [listas de frases](#phrase-lists) para inclusão de palavra de domínio, e [padrões](#patterns) para reduzir o número de expressões necessárias.

### <a name="active-learning"></a>Aprendizado ativo
No processo de [aprendizado ativo](luis-how-to-review-endoint-utt.md), LUIS permite que você adapte seu aplicativo LUIS a expressões reais, selecionando expressões que ele recebeu no ponto de extremidade para sua análise. Você pode aceitar ou corregir a previsão de ponto de extremidade, treinar novamente e republicar. LUIS aprende rapidamente com esse processo iterativo, levando a quantidade mínima de tempo e esforço. 

### <a name="phrase-lists"></a>Listas de frase 
LUIS fornece [listas de frases](luis-concept-feature.md) para que você possa indicar palavras ou frases importantes em seu domínio de modelo. LUIS usa essas listas para adicionar significado adicional a essas palavras e frases que, de outra forma, não seriam encontradas no modelo.

### <a name="patterns"></a>Padrões 
Os padrões permitem que você simplifique uma coleção de expressões da intenção em [modelos](luis-concept-patterns.md) comuns de escolha de palavras e ordem de palavras. Isto permite que LUIS aprenda mais rápido ao precisar de menos expressões de exemplos para as intenções. Os padrões são um sistema híbrido de expressões regulares e expressões de aprendizado de máquina. 

<a name="using-luis"></a>

## <a name="authoring-and-accessing-luis"></a>Criação e acesso ao LUIS
Crie seu aplicativo LUIS do website LUIS ou de forma programada com as APIs [de criação](https://aka.ms/luis-authoring-apis) ou use ambos dependendo da necessidade de criação. Acesse seu aplicativo LUIS publicado pelo [ponto de extremidade](https://aka.ms/luis-endpoint-apis) da consulta. 

LUIS fornece três websites em todo o mundo, dependendo da sua região de criação. A região de criação determina a região do Azure onde você pode publicar seu aplicativo LUIS.
<!--
|Authoring region|Publishing region(s)|
|--|--|
|[www.luis.ai](https://www.luis.ai)|**U.S.**<br>West US<br>West US 2<br>East US<br>East US 2<br>South Central US<br>West Central US<br><br>**Asia**<br>Southeast Asia<br>East Asia<br><br>**South America**<br>Brazil South |
|[au.luis.ai](https://au.luis.ai)|Australia East|
|[eu.luis.ai](https://eu.luis.ai)|West Europe<br>North Europe|
-->

Saiba [mais](luis-reference-regions.md) sobre regiões de criação e publicação.

## <a name="what-technologies-work-with-luis"></a>Quais tecnologias funcionam com LUIS?
Muitas tecnologias da Microsoft funcionam com LUIS:

* [API de Verificação Ortográfica do Bing](../bing-spell-check/proof-text.md) fornece correção de texto antes da previsão. 
* [Bot Framework][bot-framework] permite que um chatbot converse com um usuário por meio de entrada de texto. Selecione o SDK [3.x](https://github.com/Microsoft/BotBuilder) ou [4.x](https://github.com/Microsoft/botbuilder-dotnet) para uma experiência completa de bot.
* [QnA Maker][qnamaker] permite que vários tipos de texto sejam combinados em uma base de conhecimento de perguntas e respostas.
* [Fala](../Speech/home.md) converte as solicitações de linguagem falada em texto. Após convertidas em texto, LUIS processa as solicitações. Consulte [SDK de Fala](https://aka.ms/csspeech) para mais informações.
* [Análise de Texto](../text-analytics/overview.md) fornece análise de sentimento e extração de dados de frases principais.

## <a name="next-steps"></a>Próximas etapas
Crie um novo aplicativo LUIS com um domínio [predefinido](luis-get-started-create-app.md) ou [personalizado](luis-quickstart-intents-only.md).

<!-- Reference-style links -->
[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://aka.ms/luis-authoring-api
[endpoint-apis]: https://aka.ms/luis-endpoint-apis
[qnamaker]: https://qnamaker.ai/