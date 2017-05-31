---
title: "Funções do Data Factory e Variáveis do Sistema | Microsoft Docs"
description: "Fornece uma lista de funções do Azure Data Factory e variáveis do sistema"
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
services: data-factory
ms.assetid: b6b3c2ae-b0e8-4e28-90d8-daf20421660d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: shlo
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 95ffafb276009f0acfa9cd96b9d4e575bd6a9d28
ms.contentlocale: pt-br
ms.lasthandoff: 05/05/2017


---
# <a name="azure-data-factory---functions-and-system-variables"></a>Azure Data Factory - Funções e Variáveis do Sistema
Este artigo fornece informações sobre funções e variáveis com suporte no Azure Data Factory.

## <a name="data-factory-system-variables"></a>Variáveis do sistema do Data Factory
| Nome de variável | Descrição | Escopo do objeto | Escopo JSON e casos de uso |
| --- | --- | --- | --- |
| WindowStart |Início do intervalo de tempo para a janela da execução de atividade atual |atividade |<ol><li>Especifica consultas de seleção de dados. Veja os artigos sobre o conector referenciados no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md) .</li> |
| WindowEnd |Fim do intervalo de tempo para a janela da execução de atividade atual |atividade |o mesmo que o descrito acima |
| SliceStart |Início do intervalo de tempo para a fatia de dados sendo gerada |atividade<br/>conjunto de dados |<ol><li>Especifique caminhos de pasta dinâmicos e nomes de arquivos enquanto estiver trabalhando com o [Blob do Azure](data-factory-azure-blob-connector.md) e [Conjuntos de dados do sistema de arquivos](data-factory-onprem-file-system-connector.md).</li><li>Especificar dependências de entrada com funções de data factory na coleção de entradas da atividade.</li></ol> |
| SliceEnd |Fim do intervalo de tempo para a fatia de dados atual que está sendo gerada |atividade<br/>dataset |o mesmo que SliceStart. |

> [!NOTE]
> Atualmente, o data factory exige que a agenda especificada na atividade corresponda exatamente à agenda especificada na disponibilidade do conjunto de dados de saída. Portanto, WindowStart, WindowEnd, SliceStart e SliceEnd sempre são mapeados para o mesmo período de tempo e uma única fatia de saída.
> 

### <a name="example-for-using-a-system-variable"></a>Exemplo para usar uma variável de sistema
No exemplo a seguir, ano, mês, dia e hora do **SliceStart** são extraídos em variáveis separadas que são usadas pelas propriedades **folderPath** e **fileName**.

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

## <a name="data-factory-functions"></a>Funções do Data Factory
Você pode usar funções no Data Factory junto com as variáveis do sistema para as seguintes finalidades:

1. Especificando consultas de seleção de dados (veja os artigos sobre o conector referenciados no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md) .
   
   A sintaxe para invocar uma função do Data Factory é: **$$<function>** para consultas de seleção de dados e outras propriedades na atividade e nos conjuntos de dados.  
2. Especificar dependências de entrada com funções de data factory na coleção de entradas da atividade.
   
    $$ não é necessário para especificar expressões de dependência de entrada.     

No exemplo a seguir, a propriedade **sqlReaderQuery** em um arquivo JSON é atribuída a um valor retornado pela função `Text.Format`. Este exemplo também usa uma variável de sistema chamada **WindowStart**, que representa a hora de início da janela de execução de atividade.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

Confira o tópico [Cadeias de caracteres de formato de data e hora personalizado](https://msdn.microsoft.com/library/8kb3ddd4.aspx) que descreve as diferentes opções de formatação que você pode usar (por exemplo: aa versus aaaa). 

### <a name="functions"></a>Funções
As tabelas a seguir listam todas as funções no Azure Data Factory:

| Categoria | Função | Parâmetros | Descrição |
| --- | --- | --- | --- |
| Hora |AddHours(X,Y) |X: DateTime  <br/><br/>Y: int |Adiciona Y horas até o momento X determinado. <br/><br/>Exemplo: 05/09/2013 00:00:00 + 2 horas = 05/09/2013 02:00:00 |
| Hora |AddMinutes(X,Y) |X: DateTime  <br/><br/>Y: int |Adiciona Y minutos a X.<br/><br/>Exemplo: 15/9/2013 12:00:00 + 15 minutos = 15/9/2013 12:15:00 |
| Hora |StartOfHour(X) |X: DateTime  |Obtém a hora de início para a hora representada pelo componente de hora do X. <br/><br/>Exemplo: a StartOfHour de 15/9/2013 17:10:23 é 15/9/2013 17:00:00 |
| Data |AddDays(X,Y) |X: DateTime <br/><br/>Y: int |Adiciona Y dias a X. <br/><br/>Exemplo: 15/9/2013 12:00:00 + 2 dias = 17/9/2013 12:00:00.<br/><br/>Você pode subtrair dias também, especificando Y como um número negativo.<br/><br/>Exemplo: 15/9/2013 12:00:00 – 2 dias = 13/9/2013 12:00:00. |
| Data |AddMonths(X,Y) |X: DateTime <br/><br/>Y: int |Adiciona Y meses a X.<br/><br/>Exemplo: 15/9/2013 12:00:00 + 1 mês = 15/10/2013 12:00:00.<br/><br/>Você pode subtrair meses também, especificando Y como um número negativo.<br/><br/>Exemplo: 15/9/2013 12:00:00 – 1 mês = 15/8/2013 12:00:00.|
| Data |AddQuarters(X,Y) |X: DateTime  <br/><br/>Y: int |Adiciona Y * 3 meses a X.<br/><br/>Exemplo: 15/9/2013 12:00:00 + 1 trimestre = 15/12/2013 12:00:00 |
| Data |AddWeeks(X,Y) |X: DateTime <br/><br/>Y: int |Adiciona Y * 7 dias a X<br/><br/>Exemplo: 15/9/2013 12:00:00 + 1 semana = 22/9/2013 12:00:00<br/><br/>Você pode subtrair semanas também, especificando Y como um número negativo.<br/><br/>Exemplo: 15/9/2013 12:00:00 – 1 semana = 7/9/2013 12:00:00. |
| Data |AddYears(X,Y) |X: DateTime <br/><br/>Y: int |Adiciona Y dias a X<br/><br/>Exemplo: 15/9/2013 12:00:00 + 1 ano = 15/9/2014 12:00:00<br/><br/>Você pode subtrair anos também, especificando Y como um número negativo.<br/><br/>Exemplo: 15/9/2013 12:00:00 – 1 ano = 15/9/2012 12:00:00. |
| Data |Day(X) |X: DateTime  |Obtém o componente de dia de X.<br/><br/>Exemplo: o componente Day de 15/9/2013 12:00:00 é 9. |
| Data |DayOfWeek(X) |X: DateTime  |Obtém o componente de dia da semana de X.<br/><br/>Exemplo: o DayOfWeek de 15/9/2013 12:00:00 é Domingo. |
| Data |DayOfYear(X) |X: DateTime  |Obtém o dia do ano que representa o componente de ano de X.<br/><br/>Exemplos:<br/>01/12/2015: dia 335 de 2015<br/>31/12/2015: dia 365 de 2015<br/>31/12/2016: dia 366 de 2016 (ano bissexto) |
| Data |DaysInMonth(X) |X: DateTime  |Obtém os dias do mês representados pelo componente de mês do parâmetro X.<br/><br/>Exemplo: DaysInMonth de 15/9/2013 são 30, já que há 30 dias do mês de setembro. |
| Data |EndOfDay(X) |X: DateTime  |Obtém a data e hora que representam o fim do dia (componente do dia) do X.<br/><br/>Exemplo: EndOfDay de 15/9/2013 17:10:23 é 15/9/2013 23:59:59. |
| Data |EndOfMonth(X) |X: DateTime  |Obtém o fim do mês representado pelo componente de mês do parâmetro X. <br/><br/>Exemplo: EndOfMonth de 15/09/2013 17:10:23 é 30/09/2013 23:59:59 (data e hora que representa o fim do mês de setembro) |
| Data |StartOfDay(X) |X: DateTime  |Obtém o início do dia representado pelo componente dia do parâmetro X.<br/><br/>Exemplo: StartOfDay de 15/9/2013 17:10:23 é 15/9/2013 12:00:00. |
| DateTime |From(X) |X: Cadeia de caracteres |Analise a cadeia de caracteres X para um valor de data e hora. |
| DateTime |Ticks(X) |X: DateTime  |Obtém os tiques de propriedade do parâmetro X. Um tique é igual a 100 nanossegundos. O valor dessa propriedade representa o número de tiques que se passaram desde 0h, meia-noite de 1º de janeiro de 0001. |
| Texto |Format(X) |X: variável de cadeia de caracteres |Formata o texto (use a combinação `\\'` para escapar o caractere `'`).|

> [!IMPORTANT]
> Ao usar uma função dentro de outra função, você não precisa usar o prefixo **$$** para a função interna. Por exemplo: $$Text.Format('PartitionKey eq \\'my_pkey_filter_value\\' and RowKey ge \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(SliceStart, -6)). Neste exemplo, observe que o prefixo **$$** não é usado para a função **Time.AddHours**. 

#### <a name="example"></a>Exemplo
No exemplo a seguir, parâmetros de entrada e saída para a atividade de Hive são determinados usando a função Text.Format e a variável de sistema SliceStart. 

```json  
{
    "name": "HiveActivitySamplePipeline",
        "properties": {
    "activities": [
            {
            "name": "HiveActivitySample",
            "type": "HDInsightHive",
            "inputs": [
                    {
                    "name": "HiveSampleIn"
                    }
            ],
            "outputs": [
                    {
                    "name": "HiveSampleOut"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                }
            }
            }
    ]
    }
}
```

### <a name="example-2"></a>Exemplo 2

No exemplo a seguir, o parâmetro DateTime para a atividade de procedimento armazenado é determinado usando a função Text.Format e a variável SliceStart. 

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
            "start": "2016-08-02T00:00:00Z",
            "end": "2016-08-02T05:00:00Z",
        "isPaused": false
    }
}
```

### <a name="example-3"></a>Exemplo 3
Para ler dados do dia anterior em vez do dia representado pelo SliceStart, use a função AddDays conforme mostrado no exemplo a seguir: 

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-01-01T08:00:00",
        "end": "2017-01-01T11:00:00",
        "description": "hive activity",
        "activities": [
            {
                "name": "SampleHiveActivity",
                "inputs": [
                    {
                        "name": "MyAzureBlobInput",
                        "startTime": "Date.AddDays(SliceStart, -1)",
                        "endTime": "Date.AddDays(SliceEnd, -1)"
                    }
                ],
                "outputs": [
                    {
                        "name": "MyAzureBlobOutput"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adftutorial\\hivequery.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
                        "Month": "$$Text.Format('{0:MM}',WindowStart)",
                        "Day": "$$Text.Format('{0:dd}',WindowStart)"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 2,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

Confira o tópico [Cadeias de caracteres de formato de data e hora personalizado](https://msdn.microsoft.com/library/8kb3ddd4.aspx) que descreve as diferentes opções de formatação que você pode usar (por exemplo: aa versus aaaa). 


