---
title: Gravar dados com o SDK de preparação de dados do Azure Machine Learning – Python
description: Saiba mais sobre a gravação de dados com o SDK de preparação de dados do Azure Machine Learning. Você pode gravar dados em qualquer ponto de um fluxo de dados e em arquivos em qualquer local com suporte (sistema de arquivos local, Armazenamento de Blobs do Azure e Azure Data Lake Storage).
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 4a2af832fda8a85ee8a4aba395a8f436172153ed
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52308555"
---
# <a name="write-data-using-the-azure-machine-learning-data-prep-sdk"></a>Gravar dados usando o SDK de preparação de dados do Azure Machine Learning

Neste artigo, você aprenderá os diferentes métodos para gravar dados usando o SDK do Azure Machine Learning Data Prep. Dados de saída podem ser gravados em qualquer ponto em um fluxo de dados e gravações são adicionadas como etapas ao fluxo de dados resultante e são executadas sempre que é o fluxo de dados. Os dados são gravados em vários arquivos de partição para permitir gravações paralelas.

Como não há nenhuma limitação para a quantidade de etapas de gravação em um pipeline, você pode facilmente adicionar mais etapas de gravação para obter resultados intermediários para solução de problemas ou para outros pipelines.

Sempre que você executar uma etapa de gravação, ocorrerá um pull completo dos dados no fluxo de dados. Por exemplo, um fluxo de dados com três etapas de gravação lerá e processar todos os registros no conjunto de dados três vezes.

## <a name="supported-data-types-and-location"></a>Tipos de dados e locais com suporte

Os formatos de arquivo a seguir têm suporte
-   Arquivos delimitados (CSV, TSV, etc.)
-   Arquivos Parquet

Usando o [SDK de preparação de dados do Azure Machine Learning](https://aka.ms/data-prep-sdk), é possível gravar dados em:
+ um sistema de arquivos local
+ Armazenamento do Blobs do Azure
+ Armazenamento do Azure Data Lake

## <a name="spark-considerations"></a>Considerações sobre o Spark

Ao executar um fluxo de dados no Spark, você precisa gravar em uma pasta vazia. Tentar executar uma gravação em uma pasta existente causará uma falha. Verifique se a pasta de destino está vazia ou use um local de destino diferente para cada execução, caso contrário, a gravação falhará.

## <a name="monitoring-write-operations"></a>Monitoramento de operações de gravação

Para sua conveniência, um arquivo de sentinela chamado SUCCESS é gerado quando a gravação é concluída. Sua presença ajuda a identificar quando uma gravação intermediária foi concluída sem precisar esperar a conclusão do pipeline inteiro.

## <a name="example-write-code"></a>Exemplo de código de gravação

Neste exemplo, comece a carregar dados em um fluxo de dados. Você reutilizar esses dados com diferentes formatos.

```python
import azureml.dataprep as dprep
t = dprep.smart_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(10)
```

Saída de exemplo:
|   |  Coluna1 |    Coluna2 | Coluna3 | Coluna4  |Coluna5   | Coluna6 | Coluna7 | Coluna8 | Coluna9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   Nenhum|       NÃO|     NÃO  |   ENRS    |NaN    |   NaN |   NaN|    
|   1|      10003.0 |   99999.0 |   Nenhum|       NÃO|     NÃO  |   ENSO|       NaN|        NaN |NaN|   
|   2|  10010.0|    99999.0|    Nenhum|   NÃO| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    Nenhum|   NÃO| NÃO| |   NaN|    NaN|    NaN|
|4| 10014.0|    99999.0|    Nenhum|   NÃO| NÃO| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    Nenhum|   NÃO| NÃO| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   Nenhum|   NÃO| NÃO|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    Nenhum|   NÃO| NÃO| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    Nenhum|   NÃO| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    Nenhum|   NÃO| SV|     |77000.0|   15500.0|    120.0|

### <a name="delimited-file-example"></a>Exemplo de arquivo delimitado

O código a seguir usa o `write_to_csv` função para gravar dados em um arquivo delimitado.

```python
# Create a new data flow using `write_to_csv` 
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))

# Run the data flow to begin the write operation.
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```

Saída de exemplo:
|   |  Coluna1 |    Coluna2 | Coluna3 | Coluna4  |Coluna5   | Coluna6 | Coluna7 | Coluna8 | Coluna9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   ERROR |       NÃO|     NÃO  |   ENRS    |ERROR    |   ERROR |   ERROR|    
|   1|      10003.0 |   99999.0 |   ERROR |       NÃO|     NÃO  |   ENSO|       ERROR|        ERROR |ERROR|   
|   2|  10010.0|    99999.0|    ERROR |   NÃO| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    ERROR |   NÃO| NÃO| |   ERROR|    ERROR|    ERROR|
|4| 10014.0|    99999.0|    ERROR |   NÃO| NÃO| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    ERROR |   NÃO| NÃO| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   ERROR |   NÃO| NÃO|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    ERROR |   NÃO| NÃO| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    ERROR |   NÃO| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    ERROR |   NÃO| SV|     |77000.0|   15500.0|    120.0|

Na saída anterior, vários erros aparecem nas colunas numéricas por causa de números que não foram analisados corretamente. Quando gravados em CSV, valores nulos são substituídos com a cadeia de caracteres "ERROR", por padrão.

Adicione parâmetros como parte de sua gravação chamar e especificam uma cadeia de caracteres a ser usado para representar valores nulos.

```python
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'), 
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```

O código anterior produz esta saída:
|   |  Coluna1 |    Coluna2 | Coluna3 | Coluna4  |Coluna5   | Coluna6 | Coluna7 | Coluna8 | Coluna9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   BadData |       NÃO|     NÃO  |   ENRS    |BadData    |   BadData |   BadData|    
|   1|      10003.0 |   99999.0 |   BadData |       NÃO|     NÃO  |   ENSO|       BadData|        BadData |BadData|   
|   2|  10010.0|    99999.0|    BadData |   NÃO| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    BadData |   NÃO| NÃO| |   BadData|    BadData|    BadData|
|4| 10014.0|    99999.0|    BadData |   NÃO| NÃO| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    BadData |   NÃO| NÃO| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   BadData |   NÃO| NÃO|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    BadData |   NÃO| NÃO| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    BadData |   NÃO| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    BadData |   NÃO| SV|     |77000.0|   15500.0|    120.0|

### <a name="parquet-file-example"></a>Exemplo de arquivo parquet

Da mesma forma que na função `write_to_csv`, `write_to_parquet` retorna um novo fluxo de dados com uma etapa de gravação de Parquet que será executada quando o fluxo de dados for executado.

```python
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
error='MiscreantData')
```

Execute o fluxo de dados para iniciar a operação de gravação.

```python
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(10)
```

O código anterior produz esta saída:
|   |  Coluna1 |    Coluna2 | Coluna3 | Coluna4  |Coluna5   | Coluna6 | Coluna7 | Coluna8 | Coluna9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   MiscreantData |       NÃO|     NÃO  |   ENRS    |MiscreantData    |   MiscreantData |   MiscreantData|    
|   1|      10003.0 |   99999.0 |   MiscreantData |       NÃO|     NÃO  |   ENSO|       MiscreantData|        MiscreantData |MiscreantData|   
|   2|  10010.0|    99999.0|    MiscreantData |   NÃO| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    MiscreantData |   NÃO| NÃO| |   MiscreantData|    MiscreantData|    MiscreantData|
|4| 10014.0|    99999.0|    MiscreantData |   NÃO| NÃO| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    MiscreantData |   NÃO| NÃO| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   MiscreantData |   NÃO| NÃO|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    MiscreantData |   NÃO| NÃO| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    MiscreantData |   NÃO| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    MiscreantData |   NÃO| SV|     |77000.0|   15500.0|    120.0|
