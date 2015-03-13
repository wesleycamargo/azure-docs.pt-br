<properties 
	pageTitle="Aplicativo de exemplo de aprendizado de máquina: Normalmente juntos | Azure" 
	description="Um serviço Web de aprendizado de máquina que executa análise online de carrinho de compras online para produzir recomendações de produtos de itens frequentemente comprados juntos de transações de histórico fornecidas pelo usuário." 
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

# Exemplo de aplicativo de aprendizado de máquina: Frequentemente comprados juntos
 
Esse [serviço Web]( https://datamarket.azure.com/dataset/amla/mba) de aprendizado de máquina executa análise online de carrinho de compras para produzir recomendações de produtos de itens frequentemente comprados juntos por meio de transações históricas fornecidas pelo usuário. Recomendações de itens frequentemente comprados juntos ajudam os compradores identificar em um catálogo os produtos que são mais relevantes ao comprar um item específico. Mostrar essas recomendações com destaque provou ser eficaz para elevar as vendas para lojas online. 
  
##Introdução 
Depois de se inscrever para o [serviço Web]( https://datamarket.azure.com/dataset/amla/mba), você pode usar o [aplicativo Web de amostra de itens Frequentemente comprados juntos](https://marketbasket.cloudapp.net/) para facilmente carregar seus dados para um modelo e descobrir conjuntos de produtos frequentemente comprados juntos.  Para usar a API ou o aplicativo, primeiro você precisará da sua chave de API, que pode ser obtida na [página da conta do Azure Data Market](https://datamarket.azure.com/account)

##Consumo do serviço Web 

Esse serviço contém APIs para gerenciar modelos de criação de itens frequentemente comprados juntos, carregar transações históricas e recuperar o conjunto de produtos frequentemente comprados juntos mais bem classificado para um determinado produto.  Exemplos que demonstram como usar essas APIs podem ser encontrados no repositório do [Azure-MachineLearning-DataScience no GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Apps/FrequentlyBoughtTogether).


<!--HONumber=46--> 
