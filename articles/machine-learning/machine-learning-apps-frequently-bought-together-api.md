<properties 
	pageTitle="Aplicativo de exemplo de Aprendizado de Máquina: Frequentemente comprados juntos | Microsoft Azure" 
	description="Um serviço Web de Aprendizado de Máquina que executa análise online de carrinho de compras online para produzir recomendações de produtos de itens frequentemente comprados juntos de transações de histórico fornecidas pelo usuário." 
	services="machine-learning" 
	documentationCenter="" 
	authors="CoromT" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/31/2016" 
	ms.author="luisca"/>

# Aplicativo de exemplo de Aprendizado de Máquina: Frequentemente comprados juntos


## IMPORTANTE: ESSE SERVIÇO FOI PRETERIDO

**A funcionalidade Frequentemente Comprados Juntos fornecida por esse serviço agora foi integrada à nossa [API de Recomendações](http://gallery.cortanaintelligence.com/MachineLearningAPI/3574432384684cac9cc766e57729ea4c), mais abrangente. É recomendável usar as Recomendações em vez desse serviço.**

##Visão geral

O [serviço Web Frequentemente comprados juntos](https://datamarket.azure.com/dataset/amla/mba) no Aprendizado de Máquina executa uma análise de carrinho de compras online para produzir, por meio do histórico de transações, recomendações de produtos de itens frequentemente comprados juntos. Recomendações de itens frequentemente comprados juntos ajudam os compradores identificar em um catálogo os produtos que são mais relevantes ao comprar um item específico. Mostrar essas recomendações em destaque comprovou-se uma maneira eficaz de melhorar as vendas para lojas online.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]
  
##Introdução 

Depois que você se inscrever no serviço Web Frequentemente comprados juntos, você pode usar o [aplicativo Web de exemplo de serviço de Análise da Cesta de Compras](https://marketbasket.cloudapp.net/) para carregar com facilidade os dados em um modelo e descobrir conjuntos de produtos comprados com frequência. Para usar a API ou o aplicativo, primeiro você precisa da sua chave de API, que pode ser obtida na [Página da Conta do Azure Data Market](https://datamarket.azure.com/account).

##Consumo do serviço Web 

Este serviço contém APIs para criar modelos de Frequentemente comprados juntos, carregar transações históricas e obter o conjunto de produtos frequentemente comprados juntos com melhor classificação para um determinado produto. Exemplos que demonstram como usar essas APIs podem ser encontrados no repositório [Azure-MachineLearning-DataScience](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Apps/FrequentlyBoughtTogether) no GitHub.

 

<!---HONumber=AcomDC_0601_2016-->