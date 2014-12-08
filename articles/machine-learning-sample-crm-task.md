<properties title="Azure Machine Learning Sample: CRM task" pageTitle="Amostra de Aprendizado de Máquina: Tarefa do CRM | Azure" description="A sample Azure Machine Learning experiment to develop multiple models that predict customer churn, upsell, and propensity to buy a new product." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Amostra de aprendizado de máquina do Azure: Tarefa CRM

>[AZURE.NOTE]
>A [amostra de experimento] e a [amostra de conjuntos de dados] associados a este modelo estão disponíveis no Estúdio AM. Consulte a seguir para obter mais detalhes.
[Amostra de teste]: #sample-experiment
[Conjuntos de dados de amostra]: #sample-datasets

<!--
- [Problem Description]
- [Data]
- [Model]
- [Results]
- [API]
- [Sample Experiment]
-->
[Descrição do problema]: #problem-description
[Dados]: #data
[Modelo]: #model
[Resultados]: #results
[API]: #api

## Descrição do problema ##

Previsão de variação do cliente (provedor do comutador), venda especial (compra de atualizações ou complementos) e propensão de compra de um produto novo (melhor solução) 

## Dados ##

Clientes com conjunto de dados de 50K da French Telecom Company Orange na França. Cada cliente possui 230 recursos mantidos no anonimato. Esses dados são provenientes do KDD Cup 2009. 

Há recursos numéricos e em cadeia de caracteres. Os recursos são muito esparsos.
 
## Modelo ##

O único pré-processamento dos dados foi tratar de valores ausentes. Substituímos por "0" os valores ausentes nos recursos de cadeia de caracteres. Quase todos os recursos numéricos têm valores não negativos. Adicionamos 1 aos valores existentes de recursos numéricos e substituímos por 0 as entradas com valores ausentes. Dessa maneira, distinguimos entre os 0's verdadeiramente originais e os 0's que indicam valores ausentes. Fizemos isso primeiro aplicando uma operação matemática (+1) somente aos recursos numéricos. Depois, substituímos todos os valores ausentes por 0 (ou "0" para cadeias de caracteres). 

Como temos recursos numéricos e categóricos, usamos um classificador de árvore de decisão ampliado. Todos os 3 problemas estão desequilibrados, sendo que os exemplos positivos são a minoria. Em alguns casos, isso pode fazer com que o classificador ignore regiões no espaço do recurso com poucas amostras positivas. Para testar isso, tentamos dois modelos de cada problema: um usando os dados brutos e o outro replicando exemplos positivos, de forma que o novo número de exemplos positivos quase se igualou ao número de exemplos negativos de teste. Isso foi obtido usando um script R simples que separou os exemplos positivos dos negativos e depois anexou 13 cópias do conjunto positivo ao conjunto negativo. 

## Resultados ##

Os modelos exibiram uma pontuação relacionada à probabilidade de um resultado positivo de cada uma das tarefas. Como um limite de corte arbitrário pode ser selecionado para rotular uma observação como positiva, medimos o desempenho por área sob a curva (AUC) da característica operacional do receptor (ROC). Observe que para ambos os casos, de Variação e de Venda Especial, replicar amostras para criar um conjunto mais equilibrado melhorou marginalmente o desempenho do modelo.  

<table border="1">
<tr><td>Variação</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>no replication</td><td>0.711</td></tr>
<tr><td>positivos replicados</td><td>0,728</td></tr>
</table>


<table border="0">
<tr><td>Venda agregada</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>no replication</td><td>0.853</td></tr>
<tr><td>positivos replicados</td><td>0,865</td></tr>
</table>


<table border="0">
<tr><td>Apetência</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>no replication</td><td>0.805</td></tr>
<tr><td>positivos replicados</td><td>0,8</td></tr>
</table>

## API ##

Não operacionalizamos o modelo, pois ele possui recursos anônimos.



## Amostra de teste

A seguinte amostra de teste associada a esse modelo está disponível no Estúdio AM na seção **TESTES** sob a guia **AMOSTRAS**.

> **Amostra de testes - CRM - Desenvolvimento**

## Conjuntos de dados de amostra

O seguinte conjunto de dados de exemplo usado por esse teste está disponível no Estúdio AM na paleta do módulo em **Conjuntos de dados salvos**.

###Conjunto de dados CRM compartilhado
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-dataset-shared](../includes/machine-learning-sample-dataset-crm-dataset-shared.md)]

###Rótulos de apetência CRM compartilhados
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-appetency-labels-shared](../includes/machine-learning-sample-dataset-crm-appetency-labels-shared.md)]

###Rótulos de variação CRM compartilhados
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-churn-labels-shared](../includes/machine-learning-sample-dataset-crm-churn-labels-shared.md)]

###Rótulos de vendas agregadas CRM compartilhados
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-upselling-labels-shared](../includes/machine-learning-sample-dataset-crm-upselling-labels-shared.md)]
