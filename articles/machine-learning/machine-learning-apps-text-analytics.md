<properties
	pageTitle="Aplicativo de Aprendizado de Máquina: análise de sentimento | Microsoft Azure"
	description="A API de Análise de Texto é um pacote de análises de texto criado com o Aprendizado de Máquina do Azure. A API pode ser usada para analisar texto não estruturado para tarefas como análise de sentimento e extração de frases-chave."
	services="machine-learning"
	documentationCenter=""
	authors="LuisCabrer"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/28/2015"
	ms.author="luisca"/>


# Aplicativo de Aprendizado de Máquina: Serviço de Análise de Texto para análise de sentimento#
##Visão geral
API de Análise de Texto é um pacote de análises de texto de [serviços Web](https://datamarket.azure.com/dataset/amla/text-analytics) compilado com o Aprendizado de Máquina do Azure. A API pode ser usada para analisar texto não estruturado para tarefas como análise de sentimento e extração de frases-chave. Nenhum dado de treinamento é necessário para usar essa API, basta colocar seus dados de texto. Somente damos suporte para a língua inglesa no momento. Essa API usa técnicas de processamento de linguagem natural avançadas nos bastidores.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]
 
## Análise de sentimento##
A API retorna uma pontuação numérica entre 0 e 1. Classificações próximas de 1 indicam sentimento positivo, enquanto as classificações próximas de 0 indicam sentimento negativo. A pontuação de sentimento é gerada usando técnicas de classificação. Os recursos de entrada para o classificador incluem n-gramas, recursos gerados por meio de marcas de partes de fala incorporações de palavras.
 
## Extração de frases-chave##
A API retorna uma lista de cadeias de caracteres representando os principais pontos de discussão no texto de entrada. Empregamos técnicas do kit de ferramentas de Processamento de Linguagem Natural sofisticado do Microsoft Office.

## Definição da API##

###GetSentiment###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**Solicitação de exemplo**

Na chamada GET abaixo, estamos solicitando o sentimento sobre a frase *Hello World*

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

Cabeçalhos:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

Obtenha a chave de conta [aqui](https://datamarket.azure.com/account/keys).

**Exemplo de resposta**

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
		"Score":1.0
	}

---

###GetKeyPhrases###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**Solicitação de exemplo**

Na chamada GET abaixo, estamos solicitando o sentimento de frases-chave no texto *Era um hotel maravilhoso, com decoração exclusiva e equipe amigável*

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
	Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

Cabeçalhos:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey)

Obtenha a chave de conta [aqui](https://datamarket.azure.com/account/keys).


**Exemplo de resposta**

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata","KeyPhrases":[
	    "wonderful hotel","unique decor","friendly staff"]
	}
 
---

###GetSentimentBatch###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**Solicitação de exemplo**

Na chamada de POST abaixo, estamos solicitando sentimentos sobre as frases a seguir: Hello World, Hello Foo World, Hello My World, no corpo da solicitação

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

Corpo:

	{"Inputs":
	[
	    {"Id":"1","Text":"hello world"},
    	{"Id":"2","Text":"hello foo world"},
    	{"Id":"3","Text":"hello my world"},
	]}


Cabeçalhos:

	Authorization: Basic <creds>
	Accept: application/json

	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  


Obtenha a chave de conta [aqui](https://datamarket.azure.com/account/keys).

**Exemplo de resposta**

Na resposta abaixo, você obtém a lista de pontuações associada às IDs do texto:

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata", "SentimentBatch":
		[{"Score":0.9549767,"Id":"1"},
		 {"Score":0.7767222,"Id":"2"},
		 {"Score":0.8988889,"Id":"3"}
		],  
		"Errors":[] 
	}


---

###GetKeyPhrasesBatch###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**Solicitação de exemplo**

Na chamada POST abaixo, estamos solicitando a lista de sentimentos para as frases-chave nos textos a seguir:

*Era um hotel maravilhoso, com decoração exclusiva e equipe amigável*
 
*Foi uma conferência incrível, com palestras muito interessantes*

*O tráfego estava péssimo, passei três horas indo até o aeroporto*



	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

Corpo:

	{"Inputs":
	[
		{"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
		{"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
		{"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
	]}

Cabeçalhos:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey)

Obtenha a chave de conta [aqui](https://datamarket.azure.com/account/keys).


**Exemplo de resposta**

Na resposta abaixo, você obtém a lista de frases-chave, associada às IDs do texto:

	{ "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
	 	"KeyPhrasesBatch":
		[
		   {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
		   {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
		   {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
		],
		"Errors":[ ]
	}

---

**Notas relacionadas ao processamento em lotes**

As Ids inseridas no sistema são as Ids retornadas pelo sistema. O serviço Web não verifica se as Ids são exclusivas. É responsabilidade do chamador verificar a exclusividade.
 

<!---HONumber=July15_HO3-->