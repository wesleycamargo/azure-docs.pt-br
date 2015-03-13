<properties 
	pageTitle="Etapa 6: Acessar o serviço Web de Aprendizado de Máquina | Azure" 
	description="Solução alternativa. Etapa 6: Acessar um serviço Web de aprendizado de máquina do Azure ativa" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/06/2014" 
	ms.author="garye"/>


Esta é a última etapa do passo a passo, [Desenvolvendo uma Solução Preditiva com o AM do Azure][develop]:

[develop]: ../machine-learning-walkthrough-develop-predictive-solution/


1.	[Criar um espaço de trabalho de AM][create-workspace]
2.	[Carregar dados existentes][upload-data]
3.	[Criar um novo teste][create-new]
4.	[Treinar e avaliar os modelos][train-models]
5.	[Publicar o serviço Web][publish]
6.	**Acessar o serviço Web**

[create-workspace]: ../machine-learning-walkthrough-1-create-ml-workspace/
[upload-data]: ../machine-learning-walkthrough-2-upload-data/
[create-new]: ../machine-learning-walkthrough-3-create-new-experiment/
[train-models]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
[publish]: ../machine-learning-walkthrough-5-publish-web-service/
[access-ws]: ../machine-learning-walkthrough-6-access-web-service/

----------

# Etapa 6: Acessar o serviço Web de Aprendizado de Máquina do Azure

Para ser útil como um serviço Web, os usuários precisam poder enviar dados para o serviço e receber os resultados. O serviço Web é um serviço Web do Azure que pode receber e retornar dados de uma das duas maneiras:  

-	**Solicitação/Resposta** - O usuário envia um único conjunto de dados de crédito ao serviço usando um protocolo HTTP e o serviço responde com um único conjunto de resultados.
-	**Execução em lote** - O usuário envia ao serviço a URL de um BLOB do Azure que contém uma ou mais linhas de dados de crédito. O serviço armazena os resultados em outro BLOB e retorna a URL desse contêiner.  

Na guia **PAINEL** para o serviço Web, há dois links para informações que ajudarão um desenvolvedor a escrever código para acessar esse serviço. Clique no link **Página de ajuda de API** na linha de **SOLICITAR/RESPONDER** e uma página será aberta contendo exemplo de código para usar o protocolo de solicitar/responder do serviço. Do mesmo modo, o link na linha **EXECUÇÃO EM LOTE** fornece um exemplo de código para fazer uma solicitação em lote para o serviço.  

A página de ajuda de API inclui exemplos de linguagens de programação R, C# e Python. Por exemplo, aqui o código R que você pode usar para acessar o serviço Web que publicamos (a URL do Serviço real seria exibida no código de exemplo):  

	library("RCurl")
	library("RJSONIO")
	
	h = basicTextGatherer()
	req = list(Id="score00001",
	 Instance=list(FeatureVector=list(
	    "row.names"= "0",
	    "Status of checking account"= "0",
	    "Duration in months"= "0",
	    "Credit history"= "0",
	    "Purpose"= "0",
	    "Credit amount"= "0",
	    "Savings account/bond"= "0",
	    "Present employment since"= "0",
	    "Installment rate in percentage of disposable income"= "0",
	    "Personal status and sex"= "0",
	    "Other debtors"= "0",
	    "Present residence since"= "0",
	    "Property"= "0",
	    "Age in years"= "0",
	    "Other installment plans"= "0",
	    "Housing"= "0",
	    "Number of existing credits"= "0",
	    "Job"= "0",
	    "Number of people providing maintenance for"= "0",
	    "Telephone"= "0",
	    "Foreign worker"= "0",
	    "Credit risk"= "0"
	 ),GlobalParameters=fromJSON('{}')))
	
	body = toJSON(req)
	api_key = "abc123" # You can obtain the API key from the publisher of the web service
	
	h$reset()
	curlPerform(url = "http://xxx.cloudapp.net/workspaces/xxx/services/xxx/score",
	            httpheader=c('Content-Type' = "application/json", 'Authorization' = "Bearer " + api_key),
	            postfields=body,
	            writefunction = h$update,
	            verbose = TRUE
	            )
	
	result = h$value()



<!--HONumber=46--> 
