<properties
	pageTitle="Aplicativo de Aprendizado de Máquina:  Serviço de Análise de Texto para analisar o sentimento | Azure "
	description="A API de Análise de Texto é um pacote de análises de texto criado com o Aprendizado de Máquina do Azure.  A API pode ser usada para analisar texto não estruturado para tarefas como análise de sentimento e extração de frases-chave."
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
	ms.date="01/27/2015"
	ms.author="luisca"/>


# Serviço de Análise de Texto do Aprendizado de Máquina#
##Visão geral
A API de Análise de Texto é um pacote de [serviços Web]( https://datamarket.azure.com/dataset/amla/text-analytics) criado com o Aprendizado de Máquina do Azure.  A API pode ser usada para analisar texto não estruturado para tarefas como análise de sentimento e extração de frases-chave.  Nenhum dado de treinamento é necessário para usar essa API, basta colocar seus dados de texto.  Somente damos suporte para a língua inglesa no momento.  Essa API usa técnicas de processamento de linguagem natural avançadas nos bastidores.

[AZURE.INCLUDE [machine-learning-free-trial](../includes/machine-learning-free-trial.md)] 
 
## Análise de sentimento##
A API retorna uma pontuação numérica entre 0 e 1.  Classificações próximas de 1 indicam sentimento positivo, enquanto as classificações próximas de 0 indicam sentimento negativo.  A pontuação de sentimento é gerada usando técnicas de classificação.  Os recursos de entrada para o classificador incluem n-gramas, recursos gerados por meio de marcas de partes de fala incorporações de palavras.
 
## Extração de frases-chave##
A API retorna uma lista de cadeias de caracteres representando os principais pontos de discussão no texto de entrada.  Empregamos técnicas do kit de ferramentas de Processamento de Linguagem Natural sofisticado do Microsoft Office.

## Definição da API##

###GetSentiment###

**URL**	

https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**Exemplo de solicitação**

Na chamada GET abaixo, estamos solicitando o sentimento da frase *Hello World*

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

Cabeçalhos:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64("AccountKey:" + yourActualAccountKey);  

Obtenha a chave da conta [aqui]( https://datamarket.azure.com/account/keys). 

**Exemplo de resposta**

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
		"Score":1.0
	}

---

###GetKeyPhrases###

**URL**

https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**Exemplo de solicitação**

Na chamada GET abaixo, solicitamos o sentimento das frases-chave no texto *Foi maravilhosa a estada naquele hotel, com uma decoração única e equipe amigável.*

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
	Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

Cabeçalhos:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64("AccountKey:" + yourActualAccountKey)

Obtenha a chave da conta [aqui]( https://datamarket.azure.com/account/keys). 


**Exemplo de resposta**

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata","KeyPhrases":[
	    "wonderful hotel","unique decor","friendly staff"]
	}
 

<!--HONumber=49-->