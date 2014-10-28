<properties title="Azure Machine Learning Sample: Sentiment analysis" pageTitle="Machine Learning Sample: Sentiment analysis | Azure" description="A sample Azure Machine Learning experiment that uses sentiment classification to predict product reviews." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Amostra de Aprendizado de Máquina do Azure: Análise de sentimento

<em>Você pode encontrar o teste de amostra associado a esse modelo no Estúdio AM na seção **TESTES** sob a guia **AMOSTRAS**. O nome do teste é:</em>

    Sample Experiment - Sentiment Classification - Development

## Descrição do problema

Prever uma classificação de revisão de produto. As classificações são 1,2,3,4,5. Este é um problema de regressão ordinal, que pode ser resolvido como problema de regressão e como problema de multiclassificação.

## Dados

Análises de manuais na Amazon, suprimidas do site da Amazon por pesquisadores UPenn ([][]<http://www.cs.jhu.edu/~mdredze/datasets/sentiment/></a>). O conjunto de dados original tem 975K de análises com classificações 1,2,3,4,5. Para acelerar o teste, reduzimos a amostra do conjunto de dados para 10K de análises. Todas as análises estão em inglês. As análises foram escritas entre 1997-2007.

## Modelo

Usamos o módulo com hash no recurso para transformar o texto em inglês em recursos com valor de número inteiro. Comparamos três modelos:

1.  regressão linear
2.  regressão ordinal usando regressão logística de duas classes como um classificador básico
3.  classificação de multiclasse usando o classificador de regressão logística

## Resultados

Algoritmo Média de erro absoluto| Média de erro em raiz quadrada
---------|
Regressão ordinal | 0,82| 1,41|
Regressão linear | 1,04| 1,36|
Classificação em multiclasses | 0,85 | 1,57

De acordo com esses resultados, escolhemos o modelo de regressão ordinal e criamos um serviço web com base nele.

<!-- Removed until this part is fixed ##API We have built a web service that takes a plain text review and predicts its rating. -->

  []: http://www.cs.jhu.edu/~mdredze/datasets/sentiment/
