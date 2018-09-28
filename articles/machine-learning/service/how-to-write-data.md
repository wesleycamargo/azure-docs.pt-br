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
ms.openlocfilehash: da5823473b7f69fa0a6c65d5ea7319bfd2e92720
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946758"
---
# <a name="write-data-with-the-azure-machine-learning-data-prep-sdk"></a>Gravar dados com o SDK de preparação de dados do Azure Machine Learning
Você pode gravar dados em qualquer ponto de um fluxo de dados. Essas gravações são adicionadas como etapas ao fluxo de dados resultante e são executadas sempre que o fluxo de dados é executado. Como não há nenhuma limitação para a quantidade de etapas de gravação em um pipeline, é fácil gravar resultados intermediários para solução de problemas ou para serem capturados por outros pipelines. É importante observar que a execução de cada etapa de gravação resulta em um pull completo dos dados no fluxo de dados. Por exemplo, um fluxo de dados com três etapas de gravação lerá e processará três vezes todos os registros no conjunto de dados.

## <a name="writing-to-files"></a>Gravando em arquivos
Com o [SDK de preparação de dados do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py), você pode gravar dados em arquivos em todos os locais com suporte (sistema de arquivos local, Armazenamento de Blobs do Azure e Azure Data Lake Storage). Os dados são gravados em vários arquivos de partição para permitir gravações paralelas. Depois que uma gravação é concluída, um arquivo de sentinela chamado SUCESSO também é gerado. Isso ajuda a identificar quando uma gravação intermediária foi concluída sem precisar esperar a conclusão do pipeline inteiro.

Ao executar um fluxo de dados no Spark, você precisa gravar em uma pasta vazia. A tentativa de executar uma gravação em uma pasta existente falhará. Verifique se a pasta de destino está vazia ou use um local de destino diferente para cada execução, caso contrário, a gravação falhará.

O SDK de preparação de dados do Azure Machine Learning dá suporte aos seguintes formatos de arquivo:
-   Arquivos delimitados (CSV, TSV, etc.)
-   Arquivos Parquet

Neste exemplo, comece a carregar dados em um fluxo de dados. Esses dados serão reutilizados com diferentes formatos.

```

import azureml.dataprep as dprep
t = dprep.smart_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(10)
```   

|   |  Column1 |    Column2 | Column3 | Column4  |Column5   | Column6 | Column7 | Column8 | Column9 |
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

## <a name="delimited-files"></a>Arquivos delimitados
A linha a seguir cria um novo fluxo de dados com uma etapa de gravação, mas a gravação real ainda não ocorreu. Quando o fluxo de dados for executado, a gravação será executada.

```
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))
```
Agora execute o fluxo de dados, que executa a operação de gravação.
```
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```
|   |  Column1 |    Column2 | Column3 | Column4  |Column5   | Column6 | Column7 | Column8 | Column9 |
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

Os dados gravados contêm vários erros nas colunas numéricas por causa dos números que não foram analisados corretamente. Quando gravados em um CSV, esses valores nulos são substituídos pela cadeia de caracteres "ERROR", por padrão. Você pode adicionar parâmetros à chamada de gravação e especificar uma cadeia de caracteres a ser usada para declarar valores nulos.

```
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'), 
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```
|   |  Column1 |    Column2 | Column3 | Column4  |Column5   | Column6 | Column7 | Column8 | Column9 |
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
## <a name="parquet-files"></a>Arquivos Parquet

 Da mesma forma que na função `write_to_csv` acima, `write_to_parquet` retorna um novo fluxo de dados com uma etapa de gravação de Parquet que será executada quando o fluxo de dados for executado.

```
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
error='MiscreantData')
```
 Agora, executamos o fluxo de dados, que executa a operação de gravação.

```
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(10)
```
|   |  Column1 |    Column2 | Column3 | Column4  |Column5   | Column6 | Column7 | Column8 | Column9 |
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
