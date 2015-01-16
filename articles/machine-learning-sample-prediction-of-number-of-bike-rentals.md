<properties title="Azure Machine Learning Sample: Prediction of the number of bike rentals" pageTitle="Amostra de Aprendizado de Máquina: Previsão de locações de bicicletas | Azure" description="Uma experiência de aprendizado de máquina do Azure de exemplo para desenvolver um modelo de regressão que prevê o número de aluguéis de bicicleta por hora." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Amostra de aprendizado de máquina do Azure: Previsão do número de aluguéis de bicicletas

>[AZURE.NOTE]
>A [amostra de experimento] e a [amostra de conjunto de dados] associados a este modelo estão disponíveis no Estúdio AM. Consulte a seguir para obter mais detalhes.
[Amostra de experimento]: #sample-experiment
[Amostra de conjunto de dados]: #sample-dataset


## Descrição do problema ##
Previsão do número de bicicletas que serão alugadas em cada hora hoje, histórico de aluguel anterior fornecido, temperatura medida de hora em hora, indicação se hoje é feriado/dia de semana/final de semana. As previsões são diferentes para cada hora (por exemplo
 há muita locações pela manhã e quase nenhuma locação à noite). 

## Dados ##
O conjunto de dados Aluguel de Bicicletas da UCI que é baseado nos dados reais da empresa Capital Bikeshare, que mantém a rede de aluguéis de bicicletas em Washington DC. O conjunto de dados possui uma linha por hora de cada dia em 2011 e 2012, no total, 17.379 linhas. O intervalo de aluguéis de bicicletas por hora é de 1 a 977.

## Modelo ##
Usamos os dados de 2011 como um conjunto de treinamento e os dados de 2012 como um conjunto de teste. Comparamos quatro conjuntos de recursos:

1. recursos de clima + feriado + dia de semana + final de semana para o dia previsto
1. número de bicicletas que foram alugadas nas últimas 12 horas, de hora em hora
1. número de bicicletas que foram alugadas nos últimos 12 dias, na mesma hora
1. número de bicicletas que foram alugadas nas últimas 12 semanas, na mesma hora e no mesmo dia

Os recursos B capturam demandas muito recentes para as bicicletas. Os recursos C capturam a demanda pelas bicicletas em uma determinada hora. Os recursos D capturam a demanda pelas bicicletas em uma determinada hora e em um determinado dia da semana.

Como o rótulo (número de aluguéis) é realmente avaliado, temos a configuração de regressão. Além disso, como o número de recursos é relativamente pequeno (menos de 100) e não são esparsos, o limite de decisão provavelmente não é linear. Com base nisso, decidimos usar o algoritmo de regressão de árvore de decisão aumentada.

## Resultados ##

<table border="1">
<tr><th>Recursos</th><th>Erro médio absoluto</th> <th>Erro de raiz quadrada média</th> </tr>
<tr style="background-color: #fff"><td>A</td> <td> 89,7</td> <td>124,9 </td> </tr>
<tr style="background-color: #fff"><td>A+B</td><td>51,2 </td> <td>86,7 </td></tr>
<tr style="background-color: #fff"><td>A+B+C</td><td> 48,5</td> <td> 83,7 </td></tr>
<tr style="background-color: #fff"><td>A+B+C+D</td><td> 48,8 </td> <td>83,2 </td></tr>
</table>

</table>

Os melhores resultados são mostrados pelos recursos A+B+C e A+B+C+D. Surpreendentemente, os recursos D não mostraram nenhuma melhoria significativa com relação ao A+B+C. 

## Amostra de teste

A seguinte amostra de teste associada a esse modelo está disponível no Estúdio AM na seção **TESTES** sob a guia **AMOSTRAS**.

> **Amostra de teste - Previsão de demanda de bicicletas**


## Conjunto de dados de amostra

O seguinte conjunto de dados de exemplo usado por essa experiência está disponível no Estúdio AM na paleta de módulo em **Conjuntos de dados salvos**.

###Conjunto de dados UCI de locação de bicicletas
[AZURE.INCLUDE [machine-learning-sample-dataset-bike-rental-uci-dataset](../includes/machine-learning-sample-dataset-bike-rental-uci-dataset.md)]


