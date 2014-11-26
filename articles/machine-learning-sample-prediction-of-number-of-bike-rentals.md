<properties title="Azure Machine Learning Sample: Prediction of the number of bike rentals" pageTitle="Machine Learning Sample: Prediction of bike rentals | Azure" description="A sample Azure Machine Learning experiment to develop a regression model that predicts the number of bike rentals hourly." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Amostra de Aprendizado de Máquina do Azure: Previsão do número de aluguéis de bicicletas

<em>Você pode encontrar o teste de amostra associado a esse modelo no Estúdio AM na seção **TESTES** sob a guia **AMOSTRAS**. O nome do teste é:</em>

    Sample Experiment - Demand Forecasting of Bikes

## Descrição do problema

Previsão do número de bicicletas que serão alugadas em cada hora hoje, histórico de aluguel anterior fornecido, temperatura medida de hora em hora, indicação se hoje é feriado/dia de semana/final de semana. As previsões são diferentes para cada hora (por exemplo,
 há vários aluguéis durante a manhã e quase nenhum a noite).

## Dados

O conjunto de dados Aluguel de Bicicletas da UCI que é baseado nos dados reais da empresa Capital Bikeshare, que mantém a rede de aluguéis de bicicletas em Washington DC. O conjunto de dados possui uma linha por hora de cada dia em 2011 e 2012, no total, 17.379 linhas. O intervalo de aluguéis de bicicletas por hora é de 1 a 977.

## Modelo

Usamos os dados de 2011 como um conjunto de treinamento e os dados de 2012 como um conjunto de teste. Comparamos 4 conjuntos de recursos:

1.  recursos de clima + feriado + dia de semana + final de semana para o dia previsto
2.  número de bicicletas que foram alugadas nas últimas 12 horas, de hora em hora
3.  número de bicicletas que foram alugadas nos últimos 12 dias, na mesma hora
4.  número de bicicletas que foram alugadas nas últimas 12 semanas, na mesma hora e no mesmo dia

Os recursos B capturam demandas muito recentes para as bicicletas. Os recursos C capturam a demanda pelas bicicletas em uma determinada hora. Os recursos D capturam a demanda pelas bicicletas em uma determinada hora e em um determinado dia da semana.

Como o rótulo (número de aluguéis) é realmente avaliado, temos a configuração de regressão. Além disso, como o número de recursos é relativamente pequeno (menos de 100) e não são esparsos, o limite de decisão provavelmente não é linear. Com base nisso, decidimos usar o algoritmo de regressão de árvore de decisão aumentada.

## Resultados

| Recursos | Erro Absoluto Médio | Erro Quadrado Médio de Raiz |
|----------|---------------------|-----------------------------|
| Um       | 89.7                | 124.9                       |
| A+B      | 51.2                | 86.7                        |
| A+B+C    | 48.5                | 83.7                        |
| A+B+C+D  | 48.8                | 83.2                        |

</table>
Os melhores resultados são mostrados pelos recursos A+B+C e A+B+C+D. Surpreendentemente, os recursos D não mostraram nenhuma melhoria significativa com relação ao A+B+C.

