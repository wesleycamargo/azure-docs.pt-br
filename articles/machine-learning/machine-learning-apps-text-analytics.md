<properties
	pageTitle="APIs de Aprendizado de Máquina: Análise de texto| Microsoft Azure"
	description="As APIs de análise de texto do Aprendizado de Máquina da Microsoft podem ser usadas para analisar texto não estruturado a fim de fazer a análise de sentimento, a extração de frases-chave, a detecção de idioma e a detecção de tópico."
	services="machine-learning"
	documentationCenter=""
	authors="onewth"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/05/2016"
	ms.author="onewth"/>


# APIs de Aprendizado de Máquina: análise de texto para sentimento, extração de frases-chave, detecção de idioma e detecção de tópico

>[AZURE.NOTE] Este guia destina-se à versão 1 da API. Para a versão 2, [**consulte este documento**](../cognitive-services-text-analytics-quick-start/). A versão 2 agora é a versão preferencial desta API.

## Visão geral

API de Análise de Texto é um pacote de [serviços Web](https://datamarket.azure.com/dataset/amla/text-analytics) de análise de texto criado com o Aprendizado de Máquina do Azure. A API pode ser usada para analisar textos não estruturados para a execução de tarefas como análise de sentimento, extração de frases-chave, detecção de idioma e de tópico. Nenhum dado de treinamento é necessário para usar essa API, basta colocar seus dados de texto. Essa API usa técnicas avançadas de processamento de idioma natural para proporcionar as melhores previsões.

Você pode ver a análise de texto em ação em nosso [site de demonstração](https://text-analytics-demo.azurewebsites.net/), no qual você também encontrará [exemplos](https://text-analytics-demo.azurewebsites.net/Home/SampleCode) de como implementar a análise de texto em C# e Python.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

---

## Análise de sentimento

A API retorna uma pontuação numérica entre 0 e 1. Classificações próximas de 1 indicam sentimento positivo, enquanto as classificações próximas de 0 indicam sentimento negativo. A pontuação de sentimento é gerada usando técnicas de classificação. Os recursos de entrada para o classificador incluem n-gramas, recursos gerados por meio de marcas de partes de fala incorporações de palavras. Atualmente, inglês é o único idioma com suporte.
 
## Extração de frases-chave

A API retorna uma lista de cadeias de caracteres representando os principais pontos de discussão no texto de entrada. Empregamos técnicas do kit de ferramentas de Processamento de Linguagem Natural sofisticado do Microsoft Office. Atualmente, inglês é o único idioma com suporte.

## Detecção de idioma

A API retorna o idioma detectado e uma pontuação numérica entre 0 e 1. Classificações próximas a 1 indicam 100% de certeza de que o idioma identificado é verdadeiro. Há 120 idiomas com suporte no total.

## Detecção de tópico

Essa é uma API recém-lançada que retorna os principais tópicos detectados para obter uma lista de registros de texto enviados. Um tópico é identificado por uma frase-chave, que pode ser uma ou mais palavras relacionadas. Essa API requer o envio de, no mínimo, 100 registros de texto, mas foi projetada para detectar os tópicos em centenas ou em milhares de registros. Observe que essa API cobra uma transação por registro de texto enviado. A API é projetada para funcionar bem com textos curto escritos por humanos, como revisões e comentários do usuário.

---

## Definição da API

### Cabeçalhos

Inclua os cabeçalhos corretos em sua solicitação, que devem da seguinte maneira:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

Você pode encontrar a chave da conta em sua conta no [Azure Data Market](https://datamarket.azure.com/account/keys). Observe que, atualmente, apenas JSON é aceita para formatos de entrada e saída. Não há suporte para XML.

---

## APIs de resposta única

### GetSentiment

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**Solicitação de exemplo**

Na chamada abaixo, estamos solicitando a análise de sentimento sobre a frase "Hello World":

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

Isso retornará uma resposta da seguinte maneira:

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
		"Score":1.0
	}

---

### GetKeyPhrases

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**Solicitação de exemplo**

Na chamada abaixo, estamos solicitando as frases-chave encontradas no texto "Era um hotel maravilhoso, com decoração exclusiva e equipe amigável":

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
	Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

Isso retornará uma resposta da seguinte maneira:

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
	  "KeyPhrases":[
	    "wonderful hotel",
	    "unique decor",
	    "friendly staff"
	  ]
	}
 
---

### GetLanguage

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguage

**Solicitação de exemplo**

Na chamada GET abaixo, solicitamos o sentimento das frases-chave no texto *Hello World*

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguages?
	Text=Hello+World

Isso retornará uma resposta da seguinte maneira:

	{
	  "UnknownLanguage": false,
	  "DetectedLanguages": [{
	    "Name": "English",
	    "Iso6391Name": "en",
	    "Score": 1.0
	  }]
	}

**Parâmetros opcionais**

`NumberOfLanguagesToDetect` é um parâmetro opcional. O padrão é 1.

---

## APIs de lote

O serviço de análise de texto permite que você faça extrações de opiniões e frases-chave no modo de lote. Observe que cada um dos registros pontuados conta como uma transação. Por exemplo, se você solicitar o sentimento de 1000 registros em uma única chamada, ocorrerá a dedução de 1000 transações.

Perceba que as IDs inseridas no sistema são as IDs retornadas pelo sistema. O serviço Web não verifica se as IDs são exclusivas. É responsabilidade do chamador verificar a exclusividade.


### GetSentimentBatch

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**Solicitação de exemplo**

Na chamada POST abaixo, estamos solicitando sentimentos sobre as frases "Hello World", "Hello Foo World", "Hello My World", no corpo da solicitação:

	POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

Corpo da solicitação:

	{"Inputs":
	[
	    {"Id":"1","Text":"hello world"},
	    {"Id":"2","Text":"hello foo world"},
	    {"Id":"3","Text":"hello my world"},
	]}

Na resposta abaixo, você obtém a lista de pontuações associada às IDs do texto:

	{
	  "odata.metadata":"<url>", 
	  "SentimentBatch":
	  [
		{"Score":0.9549767,"Id":"1"},
		{"Score":0.7767222,"Id":"2"},
		{"Score":0.8988889,"Id":"3"}
	  ],  
	  "Errors":[]
	}


---

### GetKeyPhrasesBatch

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**Solicitação de exemplo**

Neste exemplo, estamos solicitando a lista de sentimentos para as frases-chave nos textos a seguir:

* "Era um hotel maravilhoso, com decoração exclusiva e equipe amigável"
* "Foi uma conferência incrível, com palestras muito interessantes"
* "O tráfego estava péssimo, passei três horas indo até o aeroporto"

Essa solicitação é feita como uma chamada POST para o ponto de extremidade:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

Corpo da solicitação:

	{"Inputs":
	[
		{"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
		{"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
		{"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
	]}

Na resposta abaixo, você obtém a lista de frases-chave, associada às IDs do texto:

	{ "odata.metadata":"<url>",
	 	"KeyPhrasesBatch":
		[
		   {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
		   {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
		   {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
		],
		"Errors":[]
	}

---

### GetLanguageBatch

Na chamada POST abaixo, solicitamos a detecção de idioma para duas entradas de texto:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguageBatch

Corpo da solicitação:

    {
      "Inputs": [
        {"Text": "hello world", "Id": "1"},
        {"Text": "c'est la vie", "Id": "2"}
      ]
    }

Isso retorna a resposta a seguir, na qual o idioma inglês é detectado na primeira entrada, e francês na segunda entrada:

    {
       "LanguageBatch": [{
         "Id": "1",
         "DetectedLanguages": [{
            "Name": "English",
            "Iso6391Name": "en",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       },
       {
         "Id": "2",
         "DetectedLanguages": [{
            "Name": "French",
            "Iso6391Name": "fr",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       }],
       "Errors": []
    }

---

## APIs de detecção de tópico

Essa é uma API recém-lançada que retorna os principais tópicos detectados para obter uma lista de registros de texto enviados. Um tópico é identificado por uma frase-chave, que pode ser uma ou mais palavras relacionadas. Observe que essa API cobra uma transação por registro de texto enviado.

Essa API requer o envio de, no mínimo, 100 registros de texto, mas foi projetada para detectar os tópicos em centenas ou em milhares de registros.


### Tópicos – enviar trabalho

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection

**Solicitação de exemplo**


Na chamada POST abaixo, estamos solicitando tópicos para um conjunto de 100 artigos, em que o primeiro e o último artigo de entrada são exibidos e duas StopPhrases são incluídas.

	POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection HTTP/1.1

Corpo da solicitação:

	{"Inputs":[
		{"Id":"1","Text":"I loved the food at this restaurant"},
		...,
		{"Id":"100","Text":"I hated the decor"}
	],
	"StopPhrases":[
		"restaurant", “visitor"
	]}

Na resposta abaixo, você obtém a JobId do trabalho enviado:

	{
		"odata.metadata":"<url>",
		"JobId":"<JobId>"
	}

Uma lista de frases com uma ou mais palavras que não devem ser retornadas como tópicos. Pode ser usado para filtrar tópicos muito genéricos. Por exemplo, em um conjunto de dados sobre análises de hotéis, "hotel" e "hostel" podem ser frases de interrupção adequadas.

### Tópicos – pesquisa de resultados do trabalho

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult

**Solicitação de exemplo**

Passe a JobId retornada da etapa 'Enviar trabalho' para buscar os resultados. Recomendamos que você chame esse ponto de extremidade a cada minuto até o Status='Concluído' na resposta. Levará cerca de dez minutos para um trabalho ser concluído, ou mais em caso de trabalhos com milhares de registros.

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult?JobId=<JobId>


Durante o processamento, a resposta será a seguinte:

	{
		"odata.metadata":"<url>",
		"Status":"Running",
 		"TopicInfo":[],
		"TopicAssignment":[],
		"Errors":[]
	}


A API retorna o resultado em JSON no seguinte formato:

	{
		"odata.metadata":"<url>",
		"Status":"Finished",
		"TopicInfo":[
		{
			"TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
			"Score":8.0,
			"KeyPhrase":"food"
		},
		...
		{
			"TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
			"Score":6.0,
			"KeyPhrase":"decor"
    		}
  		],
		"TopicAssignment":[
		{
			"Id":"1",
			"TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
			"Distance":0.7809
		},
		...
		{
			"Id":"100",
			"TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
			"Distance":0.8034
		}
		],
		"Errors":[]


As propriedades de cada parte da resposta são as seguintes:

**Propriedades de TopicInfo**

| Chave | Descrição |
|:-----|:----|
| TopicId | Um identificador exclusivo para cada tópico. |
| Pontuação | Contagem de registros atribuídos ao tópico. |
| KeyPhrase | Uma palavra ou frase que resume o tópico. Pode ser uma ou várias palavras. |

**Propriedades de TopicAssignment**

| Chave | Descrição |
|:-----|:----|
| ID | Identificador do registro. É igual à ID incluída na entrada. |
| TopicId | A ID do tópico a que o registro foi atribuído. |
| Distância | Confiança em que o registro pertence ao tópico. Uma distância mais próxima a zero indica maior confiança. |

<!---HONumber=AcomDC_0518_2016-->