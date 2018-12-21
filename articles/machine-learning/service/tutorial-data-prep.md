---
title: 'Tutorial de modelo de regressão: Preparar dados'
titleSuffix: Azure Machine Learning service
description: Na primeira parte deste tutorial, você aprenderá a preparar os dados em Python para modelagem de regressão usando o SDK do Azure ML.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: tutorial
author: cforbe
ms.author: cforbe
ms.reviewer: trbye
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: d20ff1fabfb73c899153cf42bb6f2d7a8f233e21
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314679"
---
# <a name="tutorial-prepare-data-for-regression-modeling"></a>Tutorial: Preparar dados para modelagem de regressão

Neste tutorial, você aprenderá a preparar os dados para modelagem de regressão usando o SDK de Preparação de Dados do Azure Machine Learning. Execute várias transformações para filtrar e combinar dois diferentes conjuntos de dados de Táxis de Nova York. O objetivo final deste conjunto de tutoriais é prever o custo de uma corrida de táxi por meio do treinamento de um modelo em relação a recursos de dados, incluindo a hora do início da corrida, o dia da semana, o número de passageiros e as coordenadas. Este tutorial é a primeira parte de uma série com duas partes.

Neste tutorial, você irá:

> [!div class="checklist"]
> * Configurar um ambiente de Python e importar pacotes
> * Carregar dois conjuntos de dados com nomes de campos diferentes
> * Limpar dados para remover anomalias
> * Transformar dados usando transformações inteligentes para criar novos recursos
> * Salvar o objeto de fluxo de dados para usar em um modelo de regressão

Você pode preparar seus dados em Python usando o [SDK de preparação de dados do Azure Machine Learning](https://aka.ms/data-prep-sdk).

## <a name="get-the-notebook"></a>Obter o bloco de anotações

Para sua conveniência, este tutorial está disponível como um [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/regression-part1-data-prep.ipynb). Execute o Notebook `regression-part1-data-prep.ipynb` em Azure Notebooks ou em seu próprio servidor de Jupyter Notebook.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

## <a name="import-packages"></a>Importar pacotes

Comece importando o SDK.


```python
import azureml.dataprep as dprep
```

## <a name="load-data"></a>Carregar dados

Baixe os dois conjuntos diferentes de dados de Táxis de Nova York em objetos de fluxo de dados.  Esses conjuntos de dados contêm campos ligeiramente diferentes. O método `auto_read_file()` reconhece automaticamente o tipo de arquivo de entrada.


```python
dataset_root = "https://dprepdata.blob.core.windows.net/demo"

green_path = "/".join([dataset_root, "green-small/*"])
yellow_path = "/".join([dataset_root, "yellow-small/*"])

green_df = dprep.read_csv(path=green_path, header=dprep.PromoteHeadersMode.GROUPED)
# auto_read_file will automatically identify and parse the file type, and is useful if you don't know the file type
yellow_df = dprep.auto_read_file(path=yellow_path)

display(green_df.head(5))
display(yellow_df.head(5))
```

## <a name="cleanse-data"></a>Limpar dados

Agora, você pode preencher algumas variáveis com transformações de atalho que se aplicam a todos os fluxos de dados. A variável `drop_if_all_null` será usada para excluir registros nos quais todos os campos são nulos. A variável `useful_columns` contém uma matriz de descrições de coluna que são mantidas em cada fluxo de dados.

```python
all_columns = dprep.ColumnSelector(term=".*", use_regex=True)
drop_if_all_null = [all_columns, dprep.ColumnRelationship(dprep.ColumnRelationship.ALL)]
useful_columns = [
    "cost", "distance", "dropoff_datetime", "dropoff_latitude", "dropoff_longitude",
    "passengers", "pickup_datetime", "pickup_latitude", "pickup_longitude", "store_forward", "vendor"
]
```

Primeiro, você trabalha com os dados dos táxis verdes e os coloca em uma forma válida que possa ser combinada com os dados dos táxis amarelos. Crie um fluxo de dados temporário `tmp_df`. Chame as funções `replace_na()`, `drop_nulls()` e `keep_columns()` usando as variáveis de transformação de atalho criadas. Além disso, renomeie todas as colunas no dataframe para corresponder aos nomes em `useful_columns`.


```python
tmp_df = (green_df
    .replace_na(columns=all_columns)
    .drop_nulls(*drop_if_all_null)
    .rename_columns(column_pairs={
        "VendorID": "vendor",
        "lpep_pickup_datetime": "pickup_datetime",
        "Lpep_dropoff_datetime": "dropoff_datetime",
        "lpep_dropoff_datetime": "dropoff_datetime",
        "Store_and_fwd_flag": "store_forward",
        "store_and_fwd_flag": "store_forward",
        "Pickup_longitude": "pickup_longitude",
        "Pickup_latitude": "pickup_latitude",
        "Dropoff_longitude": "dropoff_longitude",
        "Dropoff_latitude": "dropoff_latitude",
        "Passenger_count": "passengers",
        "Fare_amount": "cost",
        "Trip_distance": "distance"
     })
    .keep_columns(columns=useful_columns))
tmp_df.head(5)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>fornecedor</th>
      <th>pickup_datetime</th>
      <th>dropoff_datetime</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passageiros</th>
      <th>distância</th>
      <th>cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>01-08-2013 08:14:37</td>
      <td>01-08-2013 09:09:06</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>21.25</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>01-08-2013 09:13:00</td>
      <td>01-08-2013 11:38:00</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>.00</td>
      <td>74,5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>01-08-2013 09:48:00</td>
      <td>01-08-2013 09:49:00</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>01-08-2013 10:38:35</td>
      <td>01-08-2013 10:38:51</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>3.25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>01-08-2013 11:51:45</td>
      <td>01-08-2013 12:03:52</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>8.5</td>
    </tr>
  </tbody>
</table>
</div>

Substitua a variável `green_df` pelas transformações executadas em `tmp_df` na etapa anterior.

```python
green_df = tmp_df
```

Execute as mesmas etapas de transformação nos dados dos táxis amarelos.


```python
tmp_df = (yellow_df
    .replace_na(columns=all_columns)
    .drop_nulls(*drop_if_all_null)
    .rename_columns(column_pairs={
        "vendor_name": "vendor",
        "VendorID": "vendor",
        "vendor_id": "vendor",
        "Trip_Pickup_DateTime": "pickup_datetime",
        "tpep_pickup_datetime": "pickup_datetime",
        "Trip_Dropoff_DateTime": "dropoff_datetime",
        "tpep_dropoff_datetime": "dropoff_datetime",
        "store_and_forward": "store_forward",
        "store_and_fwd_flag": "store_forward",
        "Start_Lon": "pickup_longitude",
        "Start_Lat": "pickup_latitude",
        "End_Lon": "dropoff_longitude",
        "End_Lat": "dropoff_latitude",
        "Passenger_Count": "passengers",
        "passenger_count": "passengers",
        "Fare_Amt": "cost",
        "fare_amount": "cost",
        "Trip_Distance": "distance",
        "trip_distance": "distance"
    })
    .keep_columns(columns=useful_columns))
tmp_df.head(5)
```

Novamente, substitua `yellow_df` por `tmp_df` e chame a função `append_rows()` nos dados de táxis verdes para acrescentar os dados de táxis amarelos, criando um novo dataframe combinado.


```python
yellow_df = tmp_df
combined_df = green_df.append_rows([yellow_df])
```

### <a name="convert-types-and-filter"></a>Converter tipos e filtrar 

Examine as estatísticas de resumo das coordenadas de partida e chegada para ver como os dados estão distribuídos. Primeiro, defina um objeto `TypeConverter` para alterar os campos de lat/long para o tipo decimal. Em seguida, chame a função `keep_columns()` a fim de restringir a saída para somente os campos de lat/long e, em seguida, chame `get_profile()`.


```python
decimal_type = dprep.TypeConverter(data_type=dprep.FieldType.DECIMAL)
combined_df = combined_df.set_column_types(type_conversions={
    "pickup_longitude": decimal_type,
    "pickup_latitude": decimal_type,
    "dropoff_longitude": decimal_type,
    "dropoff_latitude": decimal_type
})
combined_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude", 
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Tipo</th>
      <th>Min</th>
      <th>max</th>
      <th>Contagem</th>
      <th>Contagem faltando</th>
      <th>Sem contagem faltando</th>
      <th>Percentual faltando</th>
      <th>Contagem de erros</th>
      <th>Contagem vazia</th>
      <th>0,1% quantil</th>
      <th>1% quantil</th>
      <th>5% quantil</th>
      <th>25% quantil</th>
      <th>50% quantil</th>
      <th>75% quantil</th>
      <th>95% quantil</th>
      <th>99% quantil</th>
      <th>99,9% quantil</th>
      <th>Desvio padrão</th>
      <th>Média</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-115,179337</td>
      <td>0,000000</td>
      <td>7722,0</td>
      <td>0,0</td>
      <td>7722,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>-88,114046</td>
      <td>-73,961840</td>
      <td>-73,961964</td>
      <td>-73,947693</td>
      <td>-73,922097</td>
      <td>-73,846670</td>
      <td>0,000000</td>
      <td>0,000000</td>
      <td>0,000000</td>
      <td>18,792672</td>
      <td>-68,833579</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>0,000000</td>
      <td>40,919121</td>
      <td>7722,0</td>
      <td>0,0</td>
      <td>7722,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,000000</td>
      <td>40,682889</td>
      <td>40,675541</td>
      <td>40,721075</td>
      <td>40,756159</td>
      <td>40,803909</td>
      <td>40,849406</td>
      <td>40,870681</td>
      <td>40,891244</td>
      <td>10,345967</td>
      <td>37,936742</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-115,179337</td>
      <td>0,000000</td>
      <td>7722,0</td>
      <td>0,0</td>
      <td>7722,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>-87,699611</td>
      <td>-73,984734</td>
      <td>-73,985777</td>
      <td>-73,956250</td>
      <td>-73,928948</td>
      <td>-73,866208</td>
      <td>0,000000</td>
      <td>0,000000</td>
      <td>0,000000</td>
      <td>18,696526</td>
      <td>-68,896978</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>0,000000</td>
      <td>41,008934</td>
      <td>7722,0</td>
      <td>0,0</td>
      <td>7722,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,000000</td>
      <td>40,662763</td>
      <td>40,654851</td>
      <td>40,717821</td>
      <td>40,756534</td>
      <td>40,784688</td>
      <td>40,852437</td>
      <td>40,879289</td>
      <td>40,937291</td>
      <td>10,290780</td>
      <td>37,963774</td>
    </tr>
  </tbody>
</table>



Na saída das estatísticas de resumo, você pode ver que há coordenadas que estão faltando e coordenadas que não são da Cidade de Nova York. Filtre para excluir as coordenadas fora da cidade encadeando comandos de filtro de coluna na função `filter()` e definindo limites mínimos e máximos para cada campo. Em seguida, chame `get_profile()` novamente para verificar a transformação.


```python
tmp_df = (combined_df
    .drop_nulls(
        columns=["pickup_longitude", "pickup_latitude", "dropoff_longitude", "dropoff_latitude"],
        column_relationship=dprep.ColumnRelationship(dprep.ColumnRelationship.ANY)
    ) 
    .filter(dprep.f_and(
        dprep.col("pickup_longitude") <= -73.72,
        dprep.col("pickup_longitude") >= -74.09,
        dprep.col("pickup_latitude") <= 40.88,
        dprep.col("pickup_latitude") >= 40.53,
        dprep.col("dropoff_longitude") <= -73.72,
        dprep.col("dropoff_longitude") >= -74.09,
        dprep.col("dropoff_latitude") <= 40.88,
        dprep.col("dropoff_latitude") >= 40.53
    )))
tmp_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude", 
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Tipo</th>
      <th>Min</th>
      <th>max</th>
      <th>Contagem</th>
      <th>Contagem faltando</th>
      <th>Sem contagem faltando</th>
      <th>Percentual faltando</th>
      <th>Contagem de erros</th>
      <th>Contagem vazia</th>
      <th>0,1% quantil</th>
      <th>1% quantil</th>
      <th>5% quantil</th>
      <th>25% quantil</th>
      <th>50% quantil</th>
      <th>75% quantil</th>
      <th>95% quantil</th>
      <th>99% quantil</th>
      <th>99,9% quantil</th>
      <th>Desvio padrão</th>
      <th>Média</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74,078156</td>
      <td>-73,736481</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>-74,076314</td>
      <td>-73,962542</td>
      <td>-73,962893</td>
      <td>-73,948975</td>
      <td>-73,927856</td>
      <td>-73,866662</td>
      <td>-73,830438</td>
      <td>-73,823160</td>
      <td>-73,769750</td>
      <td>0,048711</td>
      <td>-73,913865</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40,575485</td>
      <td>40,879852</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>40,632884</td>
      <td>40,713105</td>
      <td>40,711600</td>
      <td>40,721403</td>
      <td>40,758142</td>
      <td>40,805145</td>
      <td>40,848855</td>
      <td>40,867567</td>
      <td>40,877690</td>
      <td>0,048348</td>
      <td>40,765226</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74,085747</td>
      <td>-73,720871</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>-74,078828</td>
      <td>-73,985650</td>
      <td>-73,985813</td>
      <td>-73,959041</td>
      <td>-73,936681</td>
      <td>-73,884846</td>
      <td>-73,815507</td>
      <td>-73,776697</td>
      <td>-73,733471</td>
      <td>0,055961</td>
      <td>-73,920718</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40,583530</td>
      <td>40,879734</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>40,597741</td>
      <td>40,695376</td>
      <td>40,695115</td>
      <td>40,727549</td>
      <td>40,758160</td>
      <td>40,788378</td>
      <td>40,850372</td>
      <td>40,867968</td>
      <td>40,878586</td>
      <td>0,050462</td>
      <td>40,759487</td>
    </tr>
  </tbody>
</table>



Substitua `combined_df` pelas transformações que você fez em `tmp_df`.


```python
combined_df = tmp_df
```

### <a name="split-and-rename-columns"></a>Dividir e renomear colunas

Examine o perfil de dados da coluna `store_forward`.


```python
combined_df.keep_columns(columns='store_forward').get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Tipo</th>
      <th>Min</th>
      <th>max</th>
      <th>Contagem</th>
      <th>Contagem faltando</th>
      <th>Sem contagem faltando</th>
      <th>Percentual faltando</th>
      <th>Contagem de erros</th>
      <th>Contagem vazia</th>
      <th>0,1% quantil</th>
      <th>1% quantil</th>
      <th>5% quantil</th>
      <th>25% quantil</th>
      <th>50% quantil</th>
      <th>75% quantil</th>
      <th>95% quantil</th>
      <th>99% quantil</th>
      <th>99,9% quantil</th>
      <th>Desvio padrão</th>
      <th>Média</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>N</td>
      <td>S</td>
      <td>7059,0</td>
      <td>99,0</td>
      <td>6960,0</td>
      <td>0,014025</td>
      <td>0,0</td>
      <td>0,0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>



Na saída do perfil de dados de `store_forward`, você verá que os dados estão inconsistentes e que há valores ausentes/nulo. Substitua esses valores usando as funções `replace()` e `fill_nulls()` e, em ambos os casos, altere para a cadeia de caracteres "N".


```python
combined_df = combined_df.replace(columns="store_forward", find="0", replace_with="N").fill_nulls("store_forward", "N")
```

Execute outra função `replace`, desta vez no campo `distance`. Isso reformata os valores de distância rotulados incorretamente como `.00` e preenche eventuais valores nulos com zeros. Converta o campo `distance` em formato numérico.


```python
combined_df = combined_df.replace(columns="distance", find=".00", replace_with=0).fill_nulls("distance", 0)
combined_df = combined_df.to_number(["distance"])
```

Divida as datas e horas de partida e chegada nas colunas respectivas de data e hora. Use `split_column_by_example()` para realizar a divisão. Nesse caso, o parâmetro opcional `example` de `split_column_by_example()` é omitido. Portanto, a função determinará automaticamente onde dividir com base nos dados.


```python
tmp_df = (combined_df
    .split_column_by_example(source_column="pickup_datetime")
    .split_column_by_example(source_column="dropoff_datetime"))
tmp_df.head(5)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>fornecedor</th>
      <th>pickup_datetime</th>
      <th>pickup_datetime_1</th>
      <th>pickup_datetime_2</th>
      <th>dropoff_datetime</th>
      <th>dropoff_datetime_1</th>
      <th>dropoff_datetime_2</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passageiros</th>
      <th>distância</th>
      <th>cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>01-08-2013 17:22:00</td>
      <td>01-08-2013</td>
      <td>17:22:00</td>
      <td>01-08-2013 17:22:00</td>
      <td>01-08-2013</td>
      <td>17:22:00</td>
      <td>N</td>
      <td>-73,937767</td>
      <td>40,758480</td>
      <td>-73,937767</td>
      <td>40,758480</td>
      <td>1</td>
      <td>0,0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>01-08-2013 17:24:00</td>
      <td>01-08-2013</td>
      <td>17:24:00</td>
      <td>01-08-2013 17:25:00</td>
      <td>01-08-2013</td>
      <td>17:25:00</td>
      <td>N</td>
      <td>-73,937927</td>
      <td>40,757843</td>
      <td>-73,937927</td>
      <td>40,757843</td>
      <td>1</td>
      <td>0,0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>06-08-2013 06:51:19</td>
      <td>06-08-2013</td>
      <td>06:51:19</td>
      <td>06-08-2013 06:51:36</td>
      <td>06-08-2013</td>
      <td>06:51:36</td>
      <td>N</td>
      <td>-73,937721</td>
      <td>40,758404</td>
      <td>-73,937721</td>
      <td>40,758369</td>
      <td>1</td>
      <td>0,0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>06-08-2013 13:26:34</td>
      <td>06-08-2013</td>
      <td>13:26:34</td>
      <td>06-08-2013 13:26:57</td>
      <td>06-08-2013</td>
      <td>13:26:57</td>
      <td>N</td>
      <td>-73,937691</td>
      <td>40,758419</td>
      <td>-73,937790</td>
      <td>40,758358</td>
      <td>1</td>
      <td>0,0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>06-08-2013 13:27:53</td>
      <td>06-08-2013</td>
      <td>13:27:53</td>
      <td>06-08-2013 13:28:08</td>
      <td>06-08-2013</td>
      <td>13:28:08</td>
      <td>N</td>
      <td>-73,937805</td>
      <td>40,758396</td>
      <td>-73,937775</td>
      <td>40,758450</td>
      <td>1</td>
      <td>0,0</td>
      <td>3.3</td>
    </tr>
  </tbody>
</table>
</div>


Renomeie as colunas geradas por `split_column_by_example()` com nomes relevantes.


```python
tmp_df_renamed = (tmp_df
    .rename_columns(column_pairs={
        "pickup_datetime_1": "pickup_date",
        "pickup_datetime_2": "pickup_time",
        "dropoff_datetime_1": "dropoff_date",
        "dropoff_datetime_2": "dropoff_time"
    }))
tmp_df_renamed.head(5)
```

Substitua `combined_df` pelas transformações executadas e chame `get_profile()` para ver as estatísticas de resumo completo após todas as transformações.


```python
combined_df = tmp_df_renamed
combined_df.get_profile()
```

## <a name="transform-data"></a>Transformar dados

Divida ainda mais a data de partida e chegada em dia da semana, dia do mês e mês. Para obter o dia da semana, use a função `derive_column_by_example()`. Essa função usa como parâmetro uma matriz de objetos de exemplo que define os dados de entrada e saída desejada. Em seguida, a função determina automaticamente sua transformação desejada. Para colunas de hora de partida e chegada, divida em hora, minuto e segundo usando a função `split_column_by_example()` sem nenhum parâmetro de exemplo.

Após gerar esses novos recursos, exclua os campos originais em favor dos recursos recém-gerados usando `drop_columns()`. Renomeie todos os campos restantes com descrições precisas.


```python
tmp_df = (combined_df
    .derive_column_by_example(
        source_columns="pickup_date", 
        new_column_name="pickup_weekday", 
        example_data=[("2009-01-04", "Sunday"), ("2013-08-22", "Thursday")]
    )
    .derive_column_by_example(
        source_columns="dropoff_date",
        new_column_name="dropoff_weekday",
        example_data=[("2013-08-22", "Thursday"), ("2013-11-03", "Sunday")]
    )
          
    .split_column_by_example(source_column="pickup_time")
    .split_column_by_example(source_column="dropoff_time")
    # the following two split_column_by_example calls reference the generated column names from the above two calls
    .split_column_by_example(source_column="pickup_time_1")
    .split_column_by_example(source_column="dropoff_time_1")
    .drop_columns(columns=[
        "pickup_date", "pickup_time", "dropoff_date", "dropoff_time", 
        "pickup_date_1", "dropoff_date_1", "pickup_time_1", "dropoff_time_1"
    ])
          
    .rename_columns(column_pairs={
        "pickup_date_2": "pickup_month",
        "pickup_date_3": "pickup_monthday",
        "pickup_time_1_1": "pickup_hour",
        "pickup_time_1_2": "pickup_minute",
        "pickup_time_2": "pickup_second",
        "dropoff_date_2": "dropoff_month",
        "dropoff_date_3": "dropoff_monthday",
        "dropoff_time_1_1": "dropoff_hour",
        "dropoff_time_1_2": "dropoff_minute",
        "dropoff_time_2": "dropoff_second"
    }))

tmp_df.head(5)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>fornecedor</th>
      <th>pickup_datetime</th>
      <th>pickup_weekday</th>
      <th>pickup_hour</th>
      <th>pickup_minute</th>
      <th>pickup_second</th>
      <th>dropoff_datetime</th>
      <th>dropoff_weekday</th>
      <th>dropoff_hour</th>
      <th>dropoff_minute</th>
      <th>dropoff_second</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passageiros</th>
      <th>distância</th>
      <th>cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>01-08-2013 17:22:00</td>
      <td>Quinta-feira</td>
      <td>17</td>
      <td>22</td>
      <td>00</td>
      <td>01-08-2013 17:22:00</td>
      <td>Quinta-feira</td>
      <td>17</td>
      <td>22</td>
      <td>00</td>
      <td>N</td>
      <td>-73,937767</td>
      <td>40,758480</td>
      <td>-73,937767</td>
      <td>40,758480</td>
      <td>1</td>
      <td>0,0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>01-08-2013 17:24:00</td>
      <td>Quinta-feira</td>
      <td>17</td>
      <td>24</td>
      <td>00</td>
      <td>01-08-2013 17:25:00</td>
      <td>Quinta-feira</td>
      <td>17</td>
      <td>25</td>
      <td>00</td>
      <td>N</td>
      <td>-73,937927</td>
      <td>40,757843</td>
      <td>-73,937927</td>
      <td>40,757843</td>
      <td>1</td>
      <td>0,0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>06-08-2013 06:51:19</td>
      <td>Terça-feira</td>
      <td>06</td>
      <td>51</td>
      <td>19</td>
      <td>06-08-2013 06:51:36</td>
      <td>Terça-feira</td>
      <td>06</td>
      <td>51</td>
      <td>36</td>
      <td>N</td>
      <td>-73,937721</td>
      <td>40,758404</td>
      <td>-73,937721</td>
      <td>40,758369</td>
      <td>1</td>
      <td>0,0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>06-08-2013 13:26:34</td>
      <td>Terça-feira</td>
      <td>13</td>
      <td>26</td>
      <td>34</td>
      <td>06-08-2013 13:26:57</td>
      <td>Terça-feira</td>
      <td>13</td>
      <td>26</td>
      <td>57</td>
      <td>N</td>
      <td>-73,937691</td>
      <td>40,758419</td>
      <td>-73,937790</td>
      <td>40,758358</td>
      <td>1</td>
      <td>0,0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>06-08-2013 13:27:53</td>
      <td>Terça-feira</td>
      <td>13</td>
      <td>27</td>
      <td>53</td>
      <td>06-08-2013 13:28:08</td>
      <td>Terça-feira</td>
      <td>13</td>
      <td>28</td>
      <td>08</td>
      <td>N</td>
      <td>-73,937805</td>
      <td>40,758396</td>
      <td>-73,937775</td>
      <td>40,758450</td>
      <td>1</td>
      <td>0,0</td>
      <td>3.3</td>
    </tr>
  </tbody>
</table>
</div>

Nos dados acima, você vê que os componentes de data e hora das partidas e das chegadas produzidos pelas transformações derivadas estão corretos. Descarte as colunas `pickup_datetime` e `dropoff_datetime` conforme elas deixam de ser necessárias.


```python
tmp_df = tmp_df.drop_columns(columns=["pickup_datetime", "dropoff_datetime"])
```

Use a funcionalidade de inferência de tipo para verificar automaticamente o tipo de dados de cada campo e exibir os resultados de inferência.


```python
type_infer = tmp_df.builders.set_column_types()
type_infer.learn()
type_infer
```

    Column types conversion candidates:
    'pickup_weekday': [FieldType.STRING],
    'pickup_hour': [FieldType.DECIMAL],
    'pickup_minute': [FieldType.DECIMAL],
    'pickup_second': [FieldType.DECIMAL],
    'dropoff_hour': [FieldType.DECIMAL],
    'dropoff_minute': [FieldType.DECIMAL],
    'dropoff_second': [FieldType.DECIMAL],
    'store_forward': [FieldType.STRING],
    'pickup_longitude': [FieldType.DECIMAL],
    'dropoff_longitude': [FieldType.DECIMAL],
    'passengers': [FieldType.DECIMAL],
    'distance': [FieldType.DECIMAL],
    'vendor': [FieldType.STRING],
    'dropoff_weekday': [FieldType.STRING],
    'pickup_latitude': [FieldType.DECIMAL],
    'dropoff_latitude': [FieldType.DECIMAL],
    'cost': [FieldType.DECIMAL]

Os resultados de inferência parecem corretos com base nos dados. Agora, aplique as conversões de tipo ao fluxo de dados.


```python
tmp_df = type_infer.to_dataflow()
tmp_df.get_profile()
```

Antes de empacotar o fluxo de dados, execute dois últimos filtros no conjunto de dados. Para eliminar pontos de dados incorretos, filtre o fluxo de dados nos registros em que tanto o `cost` quanto a `distance` são maiores que zero.

```python
tmp_df = tmp_df.filter(dprep.col("distance") > 0)
tmp_df = tmp_df.filter(dprep.col("cost") > 0)
```

Até o momento, você tem um objeto de fluxo de dados totalmente transformado e preparado para uso em um modelo de machine learning. O SDK inclui a funcionalidade de serialização de objetos, que é usada desta forma.

```python
import os
file_path = os.path.join(os.getcwd(), "dflows.dprep")

dflow_prepared = tmp_df
package = dprep.Package([dflow_prepared])
package.save(file_path)
```

## <a name="clean-up-resources"></a>Limpar recursos

Exclua o arquivo `dflows.dprep` (se você estiver executando localmente ou no Azure Notebooks) de seu diretório atual se não quiser continuar com a parte dois do tutorial. Se você prosseguir para a parte dois, precisará do arquivo `dflows.dprep` no diretório atual.

## <a name="next-steps"></a>Próximas etapas

Na primeira parte deste tutorial, você acabou de:

> [!div class="checklist"]
> * Configurar seu ambiente de desenvolvimento
> * Carregar e limpar conjuntos de dados
> * Usar transformações inteligentes para prever sua lógica com base em um exemplo
> * Mesclar e empacotar conjuntos de dados para treinamento de aprendizado de máquina

Você está pronto para usar esses dados de treinamento na próxima parte da série de tutoriais:

> [!div class="nextstepaction"]
> [Tutorial 2: Treinar o modelo de regressão](tutorial-auto-train-models.md)
