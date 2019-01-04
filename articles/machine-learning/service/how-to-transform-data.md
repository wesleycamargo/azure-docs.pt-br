---
title: 'Transformações: SDK do Python de preparação de dados'
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre a transformação e a limpeza de dados com o SDK de preparação de dados do Azure Machine Learning. Use métodos de transformação para adicionar colunas, filtrar linhas ou colunas indesejadas e acrescentar valores ausentes.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: d32244cd49ebd42192b2388215f79a64cacb3caa
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53186133"
---
# <a name="transform-data-with-the-azure-machine-learning-data-prep-sdk"></a>Transformar dados com o SDK de preparação de dados do Azure Machine Learning

Neste artigo, você aprenderá diferentes métodos de carregamento de dados usando o [Azure Machine Learning Data prep SDK](https://aka.ms/data-prep-sdk). O SDK oferece funções para simplificar a adição de colunas, a filtragem de linhas ou colunas indesejadas e a imputação de valores ausentes.

Atualmente existem funções para as seguintes tarefas:

- [Adicionar coluna usando uma expressão](#column)
- [Acrescentar valores ausentes](#impute-missing-values)
- [Derivar colunas por exemplo](#derive-column-by-example)
- [Filtragem](#filtering)
- [Transformações personalizadas de Python](#custom-python-transforms)

## <a name="add-column-using-an-expression"></a>Adicionar coluna usando uma expressão

O SDK de preparação de dados do Azure Machine Learning inclui expressões de `substring` que você pode usar para calcular um valor de colunas existentes e, em seguida, colocar esse valor em uma nova coluna. Neste exemplo, você carrega dados e tenta adicionar colunas a esses dados de entrada.

```python
import azureml.dataprep as dprep

# loading data
dataflow = dprep.read_csv(path=r'data\crime0-10.csv')
dataflow.head(3)
```

||ID|Número do Caso|Data|Bloco|IUCR|Texto Primário|DESCRIÇÃO|Descrição do Local|Detenção|Nacional|...|Ward|Área da Comunidade|Código do FBI|Coordenada X|Coordenada Y|Ano|Atualizado Em|Latitude|Longitude|Local padrão|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
|0|10140490|HY329907|05/07/2015 11:50:00 pm|050XX N NEWLAND AVE|0820|ROUBO|US $500 E ABAIXO DE|RUA|falso|falso|...|41|10|06|1129230|1933315|2015|12/07/2015 |12:42:46 pm|41,973309466|-87,800174996|(41,973309466, -87,800174996)|
|1|10139776|HY329265|05/07/2015 11:30:00 pm|011XX W MORSE AVE|0460|BATERIA|SIMPLES|RUA|falso|verdadeiro|...|49|1|08B|1167370|1946271|2015|12/07/2015 12:42:46 pm|42,008124017|-87,65955018|(42,008124017, -87,65955018)|
|2|10140270|HY329253|05/07/2015 11:20:00 pm|121XX S FRONT ALVAR|0486|BATERIA|BATERIA DOMÉSTICA SIMPLE|RUA|falso|verdadeiro|...|9|53|08B|||2015|12/07/2015 12:42:46 pm|


Use a expressão `substring(start, length)` para extrair o prefixo da coluna Número do caso e colocar essa sequência em uma nova coluna, `Case Category`. Passar a variável `substring_expression` para o parâmetro `expression` cria uma nova coluna calculada que executa a expressão em cada registro.

```python
substring_expression = dprep.col('Case Number').substring(0, 2)
case_category = dataflow.add_column(new_column_name='Case Category',
                                    prior_column='Case Number',
                                    expression=substring_expression)
case_category.head(3)
```

||ID|Número do Caso|Categoria do Caso|Data|Bloco|IUCR|Texto Primário|DESCRIÇÃO|Descrição do Local|Detenção|...|Ward|Área da Comunidade|Código do FBI|Coordenada X|Coordenada Y|Ano|Atualizado Em|Latitude|Longitude|Local padrão|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|HY|05/07/2015 11:50:00 pm|050XX N NEWLAND AVE|0820|ROUBO|US $500 E ABAIXO DE|RUA|falso|falso|...|41|10|06|1129230|1933315|2015|12/07/2015 |12:42:46 pm|41,973309466|-87,800174996|(41,973309466, -87,800174996)|
|1|10139776|HY329265|HY|05/07/2015 11:30:00 pm|011XX W MORSE AVE|0460|BATERIA|SIMPLES|RUA|falso|verdadeiro|...|49|1|08B|1167370|1946271|2015|12/07/2015 12:42:46 pm|42,008124017|-87,65955018|(42,008124017, -87,65955018)|
|2|10140270|HY329253|HY|05/07/2015 11:20:00 pm|121XX S FRONT ALVAR|0486|BATERIA|BATERIA DOMÉSTICA SIMPLE|RUA|falso|verdadeiro|...|9|53|08B|||2015|12/07/2015 12:42:46 pm|



Use a `substring(start)` expressão para extrair apenas o número da coluna de número de casos e criar uma nova coluna. Convertê-lo em um tipo de dados numéricos usando a `to_number()` função e passar o nome da coluna de cadeia de caracteres como um parâmetro.

```python
substring_expression2 = dprep.col('Case Number').substring(2)
case_id = dataflow.add_column(new_column_name='Case Id',
                              prior_column='Case Number',
                              expression=substring_expression2)
case_id = case_id.to_number('Case Id')
case_id.head(3)
```

||ID|Número do Caso|ID do Caso|Data|Bloco|IUCR|Texto Primário|DESCRIÇÃO|Descrição do Local|Detenção|...|Ward|Área da Comunidade|Código do FBI|Coordenada X|Coordenada Y|Ano|Atualizado Em|Latitude|Longitude|Local padrão|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|329907.0|05/07/2015 11:50:00 pm|050XX N NEWLAND AVE|0820|ROUBO|US $500 E ABAIXO DE|RUA|falso|falso|...|41|10|06|1129230|1933315|2015|12/07/2015 |12:42:46 pm|41,973309466|-87,800174996|(41,973309466, -87,800174996)|
|1|10139776|HY329265|329265.0|05/07/2015 11:30:00 pm|011XX W MORSE AVE|0460|BATERIA|SIMPLES|RUA|falso|verdadeiro|...|49|1|08B|1167370|1946271|2015|12/07/2015 12:42:46 pm|42,008124017|-87,65955018|(42,008124017, -87,65955018)|
|2|10140270|HY329253|329265.0|05/07/2015 11:20:00 pm|121XX S FRONT ALVAR|0486|BATERIA|BATERIA DOMÉSTICA SIMPLE|RUA|falso|verdadeiro|...|9|53|08B|||2015|12/07/2015 12:42:46 pm|

## <a name="impute-missing-values"></a>Acrescentar valores ausentes

O SDK pode imputar valores ausentes em colunas especificadas. Neste exemplo, você carrega valores de latitude e longitude e, em seguida, tenta imputar valores ausentes nos dados de entrada.

```python
import azureml.dataprep as dprep

# loading input data
df = dprep.read_csv(r'data\crime0-10.csv')
df = df.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
df = df.to_number(['Latitude', 'Longitude'])
df.head(5)
```

||ID|Detenção|Latitude|Longitude|
|-----|------|-----|------|-----|
|0|10140490|falso|41,973309|-87,800175|
|1|10139776|falso|42,008124|-87,659550|
|2|10140270|falso|NaN|NaN|
|3|10139885|falso|41,902152|-87,754883|
|4|10140379|falso|41,885610|-87,657009|

O terceiro registro não tem valores de latitude e longitude. Para imputar esses valores ausentes, use `ImputeMissingValuesBuilder` para aprender uma expressão fixa. Ele pode imputar as colunas com um valor calculado de `MIN`, `MAX`, `MEAN` ou um valor `CUSTOM`. Quando `group_by_columns` for especificado, serão inseridos valores ausentes pelo grupo com `MIN`, `MAX` e `MEAN` calculados por grupo.

Verifique o valor `MEAN` da coluna de latitude usando a função `summarize()`. Essa função aceita uma matriz de colunas no parâmetro `group_by_columns` para especificar o nível de agregação. O parâmetro `summary_columns` aceita uma chamada `SummaryColumnsValue`. Essa chamada de função especifica o nome da coluna atual, o novo nome de campo calculado e o `SummaryFunction` a ser executado.

```python
df_mean = df.summarize(group_by_columns=['Arrest'],
                       summary_columns=[dprep.SummaryColumnsValue(column_id='Latitude',
                                                                 summary_column_name='Latitude_MEAN',
                                                                 summary_function=dprep.SummaryFunction.MEAN)])
df_mean = df_mean.filter(dprep.col('Arrest') == 'false')
df_mean.head(1)
```

||Detenção|Latitude_MEAN|
|-----|-----|----|
|0|falso|41,878961|

O valor de `MEAN` das latitudes parece preciso, use a função `ImputeColumnArguments` para imputá-lo. Esta função aceita uma cadeia de caracteres `column_id` e uma `ReplaceValueFunction` para especificar o tipo de impute. Para o valor de longitude faltando, imputa-lo com 42 base no conhecimento externo.

Etapas de imputação podem ser encadeadas em um objeto `ImputeMissingValuesBuilder`, usando a função de construtor `impute_missing_values()`. O parâmetro `impute_columns` aceita uma matriz de `ImputeColumnArguments` objetos. Chame a função `learn()` para armazenar as etapas de impute e, em seguida, aplique a um objeto de fluxo de dados usando `to_dataflow()`.

```python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id='Latitude',
                                          impute_function=dprep.ReplaceValueFunction.MEAN)
# impute with custom value 42
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=42)
# get instance of ImputeMissingValuesBuilder
impute_builder = df.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
# call learn() to learn a fixed program to impute missing values
impute_builder.learn()
# call to_dataflow() to get a data flow with impute step added
df_imputed = impute_builder.to_dataflow()

# check impute result
df_imputed.head(5)
```

||ID|Detenção|Latitude|Longitude|
|-----|------|-----|------|-----|
|0|10140490|falso|41,973309|-87,800175|
|1|10139776|falso|42,008124|-87,659550|
|2|10140270|falso|41,878961|42,000000|
|3|10139885|falso|41,902152|-87,754883|
|4|10140379|falso|41,885610|-87,657009|

Conforme mostrado no resultado acima, a latitude ausente foi inserida com o valor `MEAN` do grupo `Arrest=='false'`. A longitude ausente foi acrescentada com 42.

```python
imputed_longitude = df_imputed.to_pandas_dataframe()['Longitude'][2]
assert imputed_longitude == 42
```

## <a name="derive-column-by-example"></a>Derivar colunas por exemplo

Uma das ferramentas mais avançadas no SDK de preparação de dados de aprendizado de máquina do Azure é a capacidade de derivar colunas usando exemplos de resultados desejados. Isso permite que você dê um exemplo ao SDK para gerar código para alcançar a transformação pretendida.

```python
import azureml.dataprep as dprep
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/BostonWeather.csv')
dataflow.head(10)
```

||DATE|REPORTTPYE|HOURLYDRYBULBTEMPF|HOURLYRelativeHumidity|HOURLYWindSpeed|
|----|----|----|----|----|----|
|0|1/1/2015 0:54|FM-15|22|50|10|
|1|1/1/2015 1:00|FM-12|22|50|10|
|2|1/1/2015 1:54|FM-15|22|50|10|
|3|1/1/2015 2:54|FM-15|22|50|11|
|4|1/1/2015 3:54|FM-15|24|46|13|
|5|1/1/2015 4:00|FM-12|24|46|13|
|6|1/1/2015 4:54|FM-15|22|52|15|
|7|1/1/2015 5:54|FM-15|23|48|17|
|8|1/1/2015 6:54|FM-15|23|50|14|
|9|1/1/2015 7:00|FM-12|23|50|14|

Suponha que você precise unir esse arquivo com um conjunto de dados em que a data e a hora estejam em um formato '10 de março de 2018|2 AM-4AM'.

```python
builder = dataflow.builders.derive_column_by_example(source_columns=['DATE'], new_column_name='date_timerange')
builder.add_example(source_data=df.iloc[1], example_value='Jan 1, 2015 12AM-2AM')
builder.preview() 
```

||DATE|date_timerange|
|----|----|----|
|0|1/1/2015 0:54|1 de janeiro de 2015 0:00 - 2:00|
|1|1/1/2015 1:00|1 de janeiro de 2015 0:00 - 2:00|
|2|1/1/2015 1:54|1 de janeiro de 2015 0:00 - 2:00|
|3|1/1/2015 2:54|1 de janeiro de 2015 2:00 - 4:00|
|4|1/1/2015 3:54|1 de janeiro de 2015 2:00 - 4:00|
|5|1/1/2015 4:00|1 de janeiro de 2015 4:00 - 6:00|
|6|1/1/2015 4:54|1 de janeiro de 2015 4:00 - 6:00|
|7|1/1/2015 5:54|1 de janeiro de 2015 4:00 - 6:00|
|8|1/1/2015 6:54|1 de janeiro de 2015 6:00 - 8:00|
|9|1/1/2015 7:00|1 de janeiro de 2015 6:00 - 8:00|

O código anterior primeiro cria um construtor para a coluna derivada. Você fornece uma matriz de colunas de origem a serem consideradas (`DATE`) e um nome para a nova coluna a ser adicionada. Como o primeiro exemplo, você passa na segunda linha (índice 1) e fornece um valor esperado para a coluna derivada.

Finalmente, você chama `builder.preview()` e pode ver a coluna derivada ao lado da coluna de origem. O formato parece correto, mas você só vê valores para a mesma data "1º de janeiro de 2015".

Agora, passe o número de linhas que você deseja `skip` da parte superior para ver as linhas mais abaixo.

```
builder.preview(skip=30)
```

||DATE|date_timerange|
|-----|-----|-----|
|30|1/1/2015 22:54|1 de janeiro de 2015 22:00 - 00:00|
|31|1/1/2015 23:54|1 de janeiro de 2015 22:00 - 00:00|
|32|1/1/2015 23:59|1 de janeiro de 2015 22:00 - 00:00|
|33|2/1/2015 0:54|1 de fevereiro de 2015 0:00 - 2:00|
|34|2/1/2015 1:00|1 de fevereiro de 2015 0:00 - 2:00|
|35|2/1/2015 1:54|1 de fevereiro de 2015 0:00 - 2:00|
|36|2/1/2015 2:54|2 de fevereiro de 2015 2:00 - 4:00|
|37|2/1/2015 3:54|2 de fevereiro de 2015 2:00 - 4:00|
|38|2/1/2015 4:00|2 de fevereiro de 2015 4:00 - 6:00|
|11,8|2/1/2015 4:54|2 de fevereiro de 2015 4:00 - 6:00|

Aqui você vê um problema com o programa gerado. Baseado apenas no exemplo fornecido acima, o programa derive escolheu analisar a data como "Dia/Mês/Ano", o que não é o que você deseja neste caso. Para corrigir esse problema, forneça outro exemplo usando `add_example()` a função na `builder` variável.

```python
builder.add_example(source_data=preview_df.iloc[3], example_value='Jan 2, 2015 12AM-2AM')
builder.preview(skip=30, count=10)
```

||DATE|date_timerange|
|-----|-----|-----|
|30|1/1/2015 22:54|1 de janeiro de 2015 22:00 - 00:00|
|31|1/1/2015 23:54|1 de janeiro de 2015 22:00 - 00:00|
|32|1/1/2015 23:59|1 de janeiro de 2015 22:00 - 00:00|
|33|2/1/2015 0:54|2 de janeiro de 2015 0:00 - 2:00|
|34|2/1/2015 1:00|2 de janeiro de 2015 0:00 - 2:00|
|35|2/1/2015 1:54|2 de janeiro de 2015 0:00 - 2:00|
|36|2/1/2015 2:54|2 de janeiro de 2015 2:00 - 4:00|
|37|2/1/2015 3:54|2 de janeiro de 2015 2:00 - 4:00|
|38|2/1/2015 4:00|2 de janeiro de 2015 4:00 - 6:00|
|11,8|2/1/2015 4:54|2 de janeiro de 2015 4:00 - 6:00|

Agora, as linhas tratam corretamente "1/2/2015" como "2 de janeiro de 2015", mas se você olhar mais para baixo na coluna derivada, verá que os valores no final não têm nada na coluna derivada. Para corrigir isso, você precisa fornecer outro exemplo para a linha 66.

```python
builder.add_example(source_data=preview_df.iloc[66], example_value='Jan 29, 2015 8PM-10PM')
builder.preview(count=10)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|1 de janeiro de 2015 22:00 - 00:00|
|1|1/1/2015 23:54|1 de janeiro de 2015 22:00 - 00:00|
|2|1/1/2015 23:59|1 de janeiro de 2015 22:00 - 00:00|
|3|2/1/2015 0:54|2 de janeiro de 2015 0:00 - 2:00|
|4|2/1/2015 1:00|2 de janeiro de 2015 0:00 - 2:00|
|5|2/1/2015 1:54|2 de janeiro de 2015 0:00 - 2:00|
|6|2/1/2015 2:54|2 de janeiro de 2015 2:00 - 4:00|
|7|2/1/2015 3:54|2 de janeiro de 2015 2:00 - 4:00|
|8|2/1/2015 4:00|2 de janeiro de 2015 4:00 - 6:00|
|9|2/1/2015 4:54|2 de janeiro de 2015 4:00 - 6:00|

Para separar data e hora com '|', adicione outro exemplo. Desta vez, em vez de passar em uma linha da versão prévia, construa um dicionário de nome de coluna para o valor para o parâmetro `source_data`.

```python
builder.add_example(source_data={'DATE': '11/11/2015 0:54'}, example_value='Nov 11, 2015 | 12AM-2AM')
builder.preview(count=10)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|Nenhum|
|1|1/1/2015 23:54|Nenhum|
|2|1/1/2015 23:59|Nenhum|
|3|2/1/2015 0:54|Nenhum|
|4|2/1/2015 1:00|Nenhum|
|5|2/1/2015 1:54|Nenhum|
|6|2/1/2015 2:54|Nenhum|
|7|2/1/2015 3:54|Nenhum|
|8|2/1/2015 4:00|Nenhum|
|9|2/1/2015 4:54|Nenhum|

Isso claramente teve efeitos negativos, pois agora as únicas linhas que têm valores na coluna derivada são aquelas que correspondem exatamente aos exemplos fornecidos a você. Chamar `list_examples()` no objeto de construtor para ver uma lista dos atuais derivações de exemplo.

```python
examples = builder.list_examples()
```

| |DATE|exemplo|example_id|
| -------- | -------- | -------- | -------- |
|0|1/1/2015 1:00|1 de janeiro de 2015 0:00 - 2:00|-1|
|1|2/1/2015 0:54|2 de janeiro de 2015 0:00 - 2:00|-2|
|2|29/11/2015 20:54|29 de janeiro de 2015 20:00 - 22:00|-3|
|3|11/11/2015 0:54|11 de novembro de 2015 \| 0:00 - 2:00|-4|

Nesse caso, exemplos inconsistentes foram fornecidos. Para corrigir o problema, substitua os três primeiros exemplos pelos corretos (incluindo '|' entre data e hora).

Corrija exemplos inconsistentes excluindo exemplos que estão incorretos (transmitindo `example_row` do DataFrame pandas ou passando o valor `example_id`) e, em seguida, adicionando novos exemplos modificados novamente.

```python
builder.delete_example(example_id=-1)
builder.delete_example(example_row=examples.iloc[1])
builder.delete_example(example_row=examples.iloc[2])
builder.add_example(examples.iloc[0], 'Jan 1, 2015 | 12AM-2AM')
builder.add_example(examples.iloc[1], 'Jan 2, 2015 | 12AM-2AM')
builder.add_example(examples.iloc[2], 'Jan 29, 2015 | 8PM-10PM')
builder.preview()
```

| | DATE | date_timerange |
| -------- | -------- | -------- |
| 0 | 1/1/2015 0:54 | 1 de janeiro de 2015 \| 0:00 - 2:00 |
| 1 | 1/1/2015 1:00 | 1 de janeiro de 2015 \| 0:00 - 2:00 |
| 2 | 1/1/2015 1:54 | 1 de janeiro de 2015 \| 0:00 - 2:00 |
| 3 | 1/1/2015 2:54 | 1 de janeiro de 2015 \| 2:00 - 4:00 |
| 4 | 1/1/2015 3:54 | 1 de janeiro de 2015 \| 2:00 - 4:00 |
| 5 | 1/1/2015 4:00 | 1 de janeiro de 2015 \| 4:00 - 6:00|
| 6 | 1/1/2015 4:54 | 1 de janeiro de 2015 \| 4:00 - 6:00|
| 7 | 1/1/2015 5:54 | 1 de janeiro de 2015 \| 4:00 - 6:00|
| 8 | 1/1/2015 6:54 | 1 de janeiro de 2015 \| 6:00 - 8:00|
| 9 | 1/1/2015 7:00 | 1 de janeiro de 2015 \| 6:00 - 8:00|

Agora os dados parecem corretos e você chama `to_dataflow()` no construtor, que retornará um fluxo de dados com as colunas derivadas desejadas incluídas.

```python
dataflow = builder.to_dataflow()
df = dataflow.to_pandas_dataframe()
```

## <a name="filtering"></a>Filtragem

O SDK inclui os métodos `Dataflow.drop_columns` e `Dataflow.filter` para permitir que você filtre colunas ou linhas.

### <a name="initial-setup"></a>Configuração inicial

```python
import azureml.dataprep as dprep
from datetime import datetime
dataflow = dprep.read_csv(path='https://dprepdata.blob.core.windows.net/demo/green-small/*')
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Store_and_fwd_flag|RateCodeID|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_longitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|
|1|01-08-2013 08:14:37|01-08-2013 09:09:06|N|1|0|0|0|0|1|.00|0|0|21.25|
|2|01-08-2013 09:13:00|01-08-2013 11:38:00|N|1|0|0|0|0|2|.00|0|0|75|
|3|01-08-2013 09:48:00|01-08-2013 09:49:00|N|5|0|0|0|0|1|.00|0|1|2,1|
|4|01-08-2013 10:38:35|01-08-2013 10:38:51|N|1|0|0|0|0|1|.00|0|0|3.25|

### <a name="filtering-columns"></a>Filtrando colunas

Para filtrar colunas, use `Dataflow.drop_columns`. Esse método usa uma lista de colunas para descartar ou um argumento mais complexo chamado `ColumnSelector`.

#### <a name="filtering-columns-with-list-of-strings"></a>Filtrando colunas com lista de cadeias de caracteres

Neste exemplo, `drop_columns` usa uma lista de cadeias de caracteres. Cada cadeia de caracteres deve corresponder exatamente à coluna desejada para soltar.

```python
dataflow = dataflow.drop_columns(['Store_and_fwd_flag', 'RateCodeID'])
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_longitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|
|1|01-08-2013 08:14:37|01-08-2013 09:09:06|0|0|0|0|1|.00|0|0|21.25|
|2|01-08-2013 09:13:00|01-08-2013 11:38:00|0|0|0|0|2|.00|0|0|75|
|3|01-08-2013 09:48:00|01-08-2013 09:49:00|0|0|0|0|1|.00|0|1|2,1|
|4|01-08-2013 10:38:35|01-08-2013 10:38:51|0|0|0|0|1|.00|0|0|3.25|

#### <a name="filtering-columns-with-regex"></a>Filtrando colunas com regex

Como alternativa, use a expressão `ColumnSelector` para descartar colunas que correspondam a uma expressão regex. Neste exemplo, você solta todas as colunas que correspondem à expressão `Column*|.*longitude|.*latitude`.

```python
dataflow = dataflow.drop_columns(dprep.ColumnSelector('Column*|.*longitud|.*latitude', True, True))
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|Nenhum|
|1|01-08-2013 08:14:37|01-08-2013 09:09:06|1|.00|0|0|21.25|
|2|01-08-2013 09:13:00|01-08-2013 11:38:00|2|.00|0|0|75|
|3|01-08-2013 09:48:00|01-08-2013 09:49:00|1|.00|0|1|2,1|
|4|01-08-2013 10:38:35|01-08-2013 10:38:51|1|.00|0|0|3.25|

## <a name="filtering-rows"></a>Filtrando dados

Para filtrar linhas, use `DataFlow.filter`. Esse método usa uma expressão do SDK de preparação de dados do Azure Machine Learning como um argumento e retorna um novo fluxo de dados com as linhas que a expressão avalia como True. As expressões são criadas usando construtores de expressões (`col`, `f_not`, `f_and`, `f_or`) e operadores regulares (>, <, >=, <=, ==, !=).

### <a name="filtering-rows-with-simple-expressions"></a>Filtrando linhas com expressões simples

Use o construtor de expressões `col`, especifique o nome da coluna como um argumento de cadeia de caracteres `col('column_name')`. Use essa expressão em combinação com um dos seguintes operadores padrão>, <,> =, <=, ==,! = Para criar uma expressão como `col('Tip_amount') > 0`. Por fim, passe a expressão criada na função `Dataflow.filter`.

Neste exemplo, `dataflow.filter(col('Tip_amount') > 0)` retorna um novo fluxo de dados com as linhas em que o valor de `Tip_amount` é maior que 0.

> [!NOTE] 
> `Tip_amount` é primeiro convertido em números, o que nos permite criar uma expressão comparando-o a outros valores numéricos.

```python
dataflow = dataflow.to_number(['Tip_amount'])
dataflow = dataflow.filter(dprep.col('Tip_amount') > 0)
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|01-08-2013 19:33:28|01-08-2013 19:33:21|5|.00|0.08|0|4.58|
|1|05-08-2013 13:16:38|05-08-2013 13:18:24|1|.00|0,30|0|3.8|
|2|05-08-2013 14:11:42|05-08-2013 14:12:47|1|.00|1.05|0|4.55|
|3|05-08-2013 14:15:56|05-08-2013 14:18:04|5|.00|2.22|0|5.72|
|4|05-08-2013 14:42:14|05-08-2013 14:42:38|1|.00|0.88|0|4.38|

### <a name="filtering-rows-with-complex-expressions"></a>Filtrar linhas com expressões complexas

Para filtrar usando expressões complexas, combine uma ou mais expressões simples com os construtores de expressões `f_not`, `f_and` ou `f_or`.

Neste exemplo, `Dataflow.filter` retorna um novo fluxo de dados com as linhas em que o valor de `'Passenger_count'` é maior que 5 e `'Tolls_amount'` é maior que 0.

```python
dataflow = dataflow.to_number(['Passenger_count', 'Tolls_amount'])
dataflow = dataflow.filter(dprep.f_and(dprep.col('Passenger_count') < 5, dprep.col('Tolls_amount') > 0))
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|08-08-2013 12:16:00|08-08-2013 12:16:00|1.0|.00|2.25|5.00|19.75|
|1|12-08-2013 14:43:53|12-08-2013 15:04:50|1.0|5.28|6.46|5.33|32.29|
|2|12-08-2013 19:48:12|2013-08-12 20:03:42|1.0|5.50|1.00|10.66|30.66|
|3|13-08-2013 06:11:06|13-08-2013 06:30:28|1.0|9.57|7.47|5.33|44.8|
|4|16-08-2013 20:33:50|13-08-2013 20:48:50|1.0|5.63|3.00|5.33|27.83|

Também é possível filtrar linhas combinando mais de um construtor de expressões para criar uma expressão aninhada.

> [!NOTE]
> `lpep_pickup_datetime` e `Lpep_dropoff_datetime` são primeiro convertidos em números, o que nos permite criar uma expressão comparando-o a outros valores de data/hora.

```python
dataflow = dataflow.to_datetime(['lpep_pickup_datetime', 'Lpep_dropoff_datetime'], ['%Y-%m-%d %H:%M:%S'])
dataflow = dataflow.to_number(['Total_amount', 'Trip_distance'])
mid_2013 = datetime(2013,7,1)
dataflow = dataflow.filter(
    dprep.f_and(
        dprep.f_or(
            dprep.col('lpep_pickup_datetime') > mid_2013,
            dprep.col('Lpep_dropoff_datetime') > mid_2013),
        dprep.f_and(
            dprep.col('Total_amount') > 40,
            dprep.col('Trip_distance') < 10)))
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|13-08-2013 06:11:06+00:00|13-08-2013 06:30:28+00:00|1.0|9.57|7.47|5.33|44.80|
|1|23-08-2013 06:30:28+00:00|23-08-2013 12:50:28+00:00|2,0|8.22|8.08|5.33|40.41|
|2|25-08-2013 09:12:52+00:00|25-08-2013 09:34:34+00:00|1.0|8.80|8.33|5.33|41.66|
|3|25-08-2013 16:46:51+00:00|25-08-2013 17:13:55+00:00|2,0|9.66|7.37|5.33|44.20|
|4|25-08-2013 17:42:11+00:00|25-08-2013 18:02:57+00:00|1.0|9.60|6.87|5.33|41.20|

## <a name="custom-python-transforms"></a>Transformações personalizadas de Python

Sempre haverá cenários em que a opção mais fácil para fazer uma transformação é escrever seu próprio script. O SDK fornece três pontos de extensão que você pode usar para scripts Python personalizados.

- Nova coluna de script
- Novo filtro de script
- Transformar partição

Cada uma das extensões é compatível com o runtime de ampliação e expansão. A principal vantagem de usar esses pontos de extensão é que você não precisa efetuar pull de todos os dados para criar um quadro de dados. Seu código Python será executado assim como como outras transformações, em escala, por partição e, geralmente, em paralelo.

### <a name="initial-data-preparation"></a>Preparação de dados inicial

Comece a carregar alguns dados do Blob do Azure.

```python
import azureml.dataprep as dprep
col = dprep.col

df = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv', skip_rows=1)
df.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|ALABAMA|1|101710|Condado de Hale|10171002158| |
|1|ALABAMA|1|101710|Condado de Hale|10171002162| |
|2|ALABAMA|1|101710|Condado de Hale|10171002158| |
|3|ALABAMA|1|101710|Condado de Hale|10171002158|2|
|4|ALABAMA|1|101710|Condado de Hale|10171000589| |

Reduza o conjunto de dados e faça algumas transformações básicas.

```python
df = df.keep_columns(['stnam', 'leanm10', 'ncessch', 'MAM_MTH00numvalid_1011'])
df = df.replace_na(columns=['leanm10', 'MAM_MTH00numvalid_1011'], custom_na_list='.')
df = df.to_number(['ncessch', 'MAM_MTH00numvalid_1011'])
df.head(5)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Condado de Hale|1.017100e+10|Nenhum|
|1|ALABAMA|Condado de Hale|1.017100e+10|Nenhum|
|2|ALABAMA|Condado de Hale|1.017100e+10|Nenhum|
|3|ALABAMA|Condado de Hale|1.017100e+10|2|
|4|ALABAMA|Condado de Hale|1.017100e+10|Nenhum|

Procure valores null com o filtro a seguir.

```python
df.filter(col('MAM_MTH00numvalid_1011').is_null()).head(5)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Condado de Hale|1.017100e+10|Nenhum|
|1|ALABAMA|Condado de Hale|1.017100e+10|Nenhum|
|2|ALABAMA|Condado de Hale|1.017100e+10|Nenhum|
|3|ALABAMA|Condado de Hale|1.017100e+10|Nenhum|
|4|ALABAMA|Condado de Hale|1.017100e+10|Nenhum|

### <a name="transform-partition"></a>Transformar partição

Use uma função pandas para substituir todos os valores nulos por um 0. Este código será executado por partição, não em todo o conjunto de dados de uma só vez. Isso significa que, em um grande conjunto de dados, esse código pode ser executado em paralelo à medida que o tempo de execução processa os dados, partição por partição.

O script Python deve definir uma função chamada `transform()` que recebe dois argumentos, `df` e `index`. O argumento `df` será um dataframe do pandas que contém os dados para a partição e o argumento `index` é um identificador exclusivo da partição. A função de transformação pode editar completamente o dataframe transmitido, mas deve retornar um dataframe. Quaisquer bibliotecas importadas pelo script Python devem existir no ambiente em que o fluxo de dados é executado.

```python
df = df.transform_partition("""
def transform(df, index):
    df['MAM_MTH00numvalid_1011'].fillna(0,inplace=True)
    return df
""")
df.head(5)
```

||stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Condado de Hale|1.017100e+10|0,0|
|1|ALABAMA|Condado de Hale|1.017100e+10|0,0|
|2|ALABAMA|Condado de Hale|1.017100e+10|0,0|
|3|ALABAMA|Condado de Hale|1.017100e+10|2,0|
|4|ALABAMA|Condado de Hale|1.017100e+10|0,0|

### <a name="new-script-column"></a>Nova coluna de script

Você pode usar o código Python para criar uma nova coluna que tenha o nome do município e o nome do estado, além de colocar o nome do estado em maiúsculas. Para fazer isso, use o método `new_script_column()` no fluxo de dados.

O script Python deve definir uma função chamada `newvalue()` que usa um único argumento `row`. O argumento `row` é um dict (`key`: nome da coluna, `val`: valor atual) e será passado para esta função para cada linha no conjunto de dados. Esta função deve retornar um valor a ser usado na nova coluna. Quaisquer bibliotecas importadas pelo script Python devem existir no ambiente em que o fluxo de dados é executado.

```python
df = df.new_script_column(new_column_name='county_state', insert_after='leanm10', script="""
def newvalue(row):
    return row['leanm10'] + ', ' + row['stnam'].title()
""")
df.head(5)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Condado de Hale|Hale County, Alabama|1.017100e+10|0,0|
|1|ALABAMA|Condado de Hale|Hale County, Alabama|1.017100e+10|0,0|
|2|ALABAMA|Condado de Hale|Hale County, Alabama|1.017100e+10|0,0|
|3|ALABAMA|Condado de Hale|Hale County, Alabama|1.017100e+10|2,0|
|4|ALABAMA|Condado de Hale|Hale County, Alabama|1.017100e+10|0,0|

### <a name="new-script-filter"></a>Novo Filtro de Script

Crie uma expressão Python para filtrar o conjunto de dados para apenas as linhas em que "Hale" não está na nova coluna `county_state`. A expressão retorna `True` se quisermos manter a linha, e `False` para removê-la.

```python
df = df.new_script_filter("""
def includerow(row):
    val = row['county_state']
    return 'Hale' not in val
""")
df.head(5)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e+10|1.0|
|1|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e+10|0,0|
|2|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e+10|0,0|
|3|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e+10|0,0|
|4|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e+10|0,0|
