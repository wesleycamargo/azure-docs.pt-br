<properties 
	pageTitle="Aplicativo de exemplo de Aprendizado de Máquina:  Frequentemente comprados juntos | Azure" 
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
	ms.date="10/17/2014" 
	ms.author="coromt"/> 

# Aplicativo de exemplo de Aprendizado de Máquina:  Frequentemente comprados juntos

##Visão geral
 
O [serviço Web Frequentemente comprados juntos]( https://datamarket.azure.com/dataset/amla/mba) no Aprendizado de Máquina executa análise de carrinho de compras online para produzir recomendações de produtos de itens frequentemente comprados juntos por meio do histórico de transações.  Recomendações de itens frequentemente comprados juntos ajudam os compradores identificar em um catálogo os produtos que são mais relevantes ao comprar um item específico.  Mostrar essas recomendações em destaque comprovou-se uma maneira eficaz de melhorar as vendas para lojas online. 

[AZURE.INCLUDE [machine-learning-free-trial](../includes/machine-learning-free-trial.md)] 
  
##Introdução 

Depois que você se inscrever no serviço Web Frequentemente comprados juntos, você pode usar o [aplicativo Web de exemplo de Serviço de Análise de Carrinho de Compras](https://marketbasket.cloudapp.net/) para carregar com facilidade os dados em um modelo e descobrir conjuntos de produtos comprados com frequência.  Para usar a API ou o aplicativo, primeiro você precisa da sua chave de API, que pode ser obtida na [página da conta do Azure Data Market](https://datamarket.azure.com/account).

##Consumo do serviço Web 

Este serviço contém APIs para criar modelos de Frequentemente comprados juntos, carregar transações históricas e obter o conjunto de produtos frequentemente comprados juntos com melhor classificação para um determinado produto.  Exemplos que demonstram como usar essas APIs podem ser encontrados no repositório [Azure-MachineLearning-DataScience](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Apps/FrequentlyBoughtTogether) no GitHub.


<!--HONumber=49-->