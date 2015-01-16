<properties title="Azure Machine Learning Sample: Sentiment analysis" pageTitle="Amostra de Aprendizado de Máquina: Análise de sentimento | Azure" description="Uma experiência de aprendizado de máquina do Azure de exemplo que usa a classificação de sentimento para prever as opiniões sobre o produto." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />
 

# Amostra de aprendizado de máquina do Azure: Análise de sentimento

>[AZURE.NOTE]
>A [amostra de experimento] e a [amostra de conjunto de dados] associados a este modelo estão disponíveis no Estúdio AM. Consulte a seguir para obter mais detalhes.
[Amostra de experimento]: #sample-experiment
[Amostra de conjunto de dados]: #sample-dataset


##Descrição do problema
Prever uma classificação de revisão de produto. As classificações são 1,2,3,4,5. Este é um problema de regressão ordinal, que pode ser resolvido como problema de regressão e como problema de multiclassificação.
 
##Dados
Análises de manuais na Amazon, suprimidas do site da Amazon por pesquisadores UPenn ([http://www.cs.jhu.edu/~mdredze/datasets/sentiment/](http://www.cs.jhu.edu/~mdredze/datasets/sentiment/)). O conjunto de dados original tem 975K de análises com classificações 1,2,3,4,5. Para acelerar o teste, reduzimos a amostra do conjunto de dados para 10K de análises. Todas as análises estão em inglês. As análises foram escritas entre 1997-2007. 
 
##Modelo
Usamos o módulo com hash no recurso para transformar o texto em inglês em recursos com valor de número inteiro. Comparamos três modelos:  
 
1. regressão linear   
2. regressão ordinal usando regressão logística de duas classes como um classificador básico
3. classificação de multiclasse usando o classificador de regressão logística
 
##Resultados

Algoritmo                 | Erro médio absoluto | Erro de raiz quadrada média
:---------                | :-----------------: | :--------------------:
Regressão ordinal        | 0.82                | 1.41
Regressão linear         | 1,04                | 1,36
Classificação multiclasse | 0,85                | 1,57
 
De acordo com esses resultados, escolhemos o modelo de regressão ordinal e criamos um serviço web com base nele.
 
<!-- Removed until this part is fixed
##API
We have built a web service that takes a plain text review and predicts its rating.
-->


## Amostra de teste

A seguinte amostra de teste associada a esse modelo está disponível no Estúdio AM na seção **TESTES** sob a guia **AMOSTRAS**.

> **Amostra de teste - Classificação sentimento - Desenvolvimento**


## Conjunto de dados de amostra

O seguinte conjunto de dados de exemplo usado por essa experiência está disponível no Estúdio AM na paleta de módulo em **Conjuntos de dados salvos**.

###Resenhas de livros da Amazon
[AZURE.INCLUDE [machine-learning-sample-dataset-book-reviews-from-amazon](../includes/machine-learning-sample-dataset-book-reviews-from-amazon.md)]
