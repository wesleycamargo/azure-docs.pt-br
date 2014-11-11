<properties title="Azure Machine Learning Sample: Flight delay prediction" pageTitle="Machine Learning Sample: Flight delay prediction | Azure" description="A sample Azure Machine Learning experiment to develop a model that predicts whether a scheduled passenger flight will be delayed by more than 15 minutes." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Amostra de Aprendizado de Máquina do Azure: Previsão de atraso de voo

<em>Você pode encontrar o teste de amostra associado a esse modelo no Estúdio AM na seção **TESTES** sob a guia **AMOSTRAS**. O nome do teste é:</em>

    Sample Experiment - Flight Delay Prediction - Development

## Descrição do problema

Prever se a chegada do voo do passageiro agendado está atrasada em mais de 15 minutos usando desempenho histórico pontual e dados meteorológicos.

## Dados

**Dados de desempenho pontual do voo do passageiro da coleção de dados da TranStats do Departamento de Transporte dos Estados Unidos:** [][]<http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time></a>

O conjunto de dados abrange de abril a outubro de 2013. Antes de carregar o Estúdio AM do Azure:

-   O conjunto de dados foi filtrado para cobrir os 70 aeroportos mais movimentados do continente norte-americano.
-   As seguintes colunas foram selecionadas: 'Year', 'Month', 'DayofMonth', 'DayOfWeek','Carrier', 'OriginAirportID','DestAirportID','CRSDepTime','DepDelay','DepDel15', 'CRSArrTime','ArrDelay','ArrDel15','Cancelled'
-   Os voos cancelados foram rotulados como com atrasos de mais de 15 minutos.
-   Voos desviados foram retirados.

**Observações meteorológicas com base no horário de pouso de NOAA:** <http://cdo.ncdc.noaa.gov/qclcd_ascii/>

Os dados meteorológicos abrangem observações de estações meteorológicas associadas a cada aeroporto, de abril a outubro de 2013. Antes de carregar no Estúdio AM:

-   As IDs da estação meteorológica foram mapeadas para as IDs do aeroporto correspondente.
-   As estações meteorológicas não associadas aos 70 aeroportos mais movimentados foram filtradas e retiradas
-   A coluna de Data foi dividida em colunas separadas para Ano, Mês e Dia
-   As seguintes colunas foram selecionadas: 'AirportID', 'Year','Month','Day', 'Time', 'TimeZone', 'SkyCondition', 'Visibility','WeatherType','DryBulbFarenheit', 'DryBulbCelsius', 'WetBulbFarenheit', 'WetBulbCelsius', 'DewPointFarenheit','DewPointCelsius', 'RelativeHumidity', 'WindSpeed', 'WindDirection', 'ValueForWindCharacter','StationPressure', 'PressureTendency', 'PressureChange', 'SeaLevelPressure', 'RecordType', 'HourlyPrecip', 'Altimeter'

## Modelo

**Pré-processamento de dados do voo**

Primeiro, colunas que são possíveis vazamentos de destinos: DepDelay, DepDel15, ArrDelay, Cancelled são colunas excluídas do conjunto de dados.

As colunas 'DayOfWeek','OriginAirportID' e 'DestAirportID' representam atributos categóricos. Entretanto, como elas possuem o valor de números inteiros, são inicialmente analisadas como numéricos contínuos, e precisam ser convertidas em categóricos usando o Editor de Metadados.

Temos o objetivo de juntar os registros de voo com os registros meteorológicos usando a hora de partida agendada como uma das chaves de junção. Para isso, a coluna CSRDepTime é arredondada para a hora inferior mais próxima.

**Processamento de dados meteorológicos**

Primeiro, colunas que possuem maior proporção de valores ausentes são excluídas. Dentre elas incluem

-   Todas as colunas com valor de cadeia de caracteres
-   ValueForWindCharacter,WetBulbFarenheit,WetBulbCelsius,PressureTendency,PressureChange,SeaLevelPressure,StationPressure

Em seguida, o Programa de limpeza de valores ausentes é aplicado às colunas restantes.

A hora da observação meteorológica é arredondada para cima, para a hora inteira mais próxima, assim ela pode ser equiparada à hora de partida agendada para os voos. Observe que a hora do voo agendada e a hora da observação meteorológica são arredondadas para direções opostas. Isso acontece para assegurar que o modelo use somente observações meteorológicas que aconteceram no passado com relação à hora do voo.

**Unindo os conjuntos de dados**

Os registros de voos são unidos aos dados meteorológicos na origem do voo e no destino do voo.

Observe que os dados meteorológicos são relatados em hora local, mas a origem e o destino podem estar em fuso horário diferente. Portanto, um ajuste na diferença do fuso horário é feito subtraindo as colunas de fuso horário da hora de partida agendada e da hora da observação meteorológica.

**Preparando as amostras de Treinamento e Validação**

As amostras de treinamento e validação são criadas dividindo-se os dados nos registros de abril a setembro para treinamento, e nos registros de outubro para validação. As colunas de ano e mês são, então, removidas do conjunto de dados.

Além do mais, os dados de treinamento são quantizados por compartimentação de altura igual, e a mesma compartimentação é aplicada aos dados de validação.

**Modelo de Treinamento e Validação**

O modelo de árvore de decisão ampliado de duas classes é testado com relação à amostra de treinamento. O modelo é otimizado para o melhor AUC usando limpeza de parâmetro aleatório de 10 dobras. Para comparação, o modelo de regressão logístico de duas classes é otimizado da mesma maneira.

Os modelos treinados são, então, pontuados com relação ao conjunto de validação, e o módulo Avaliar modelo é usado para analisar a qualidade dos modelos com relação uns aos outros.

**Pós-processamento**

As IDs de aeroporto são unidas aos nomes de aeroportos e locais legíveis aos humanos, para facilitar a análise dos resultados.

## Resultados

O modelo de árvore de decisão ampliado possui AUC de 0,697 com relação ao conjunto de validação, o que está um pouco melhor do que o modelo de regressão lógica com AUC de 0,675.

  []: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
