---
title: Padrões de caminho de DateTime para a saída de blobs do Azure Stream Analytics (versão prévia)
description: Este artigo descreve o recurso de padrões de caminho de DateTime personalizados para a saída do Armazenamento de Blobs de trabalhos do Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: ba386539c3f3c6740b843575bbccd4b028b8a5a7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090765"
---
# <a name="custom-datetime-path-patterns-for-azure-stream-analytics-blob-storage-output-preview"></a>Padrões de caminho de DateTime personalizados para a saída do Armazenamento de Blobs do Azure Stream Analytics (versão prévia)

O Azure Stream Analytics dá suporte a especificadores de formato data e hora personalizados no caminho do arquivo para saídas do Armazenamento de Blobs. Padrões de caminho de DateTime personalizados permitem que você especifique um formato de saída que se alinhe com as convenções de Streaming de Hive, possibilitando ao Azure Stream Analytics enviar dados para o Azure HDInsight e o Azure Databricks para processamento downstream. Padrões de caminho de DateTime personalizados são implementados facilmente usando a palavra-chave `datetime` no campo de Prefixo do caminho de sua saída de blob, juntamente com o especificador de formato. Por exemplo, `{datetime:yyyy}`.

Use este link para o [Portal do Azure](https://portal.azure.com/?Microsoft_Azure_StreamAnalytics_bloboutputcustomdatetimeformats=true) para alternar o sinalizador de recurso que habilita os padrões de caminho de DateTime personalizados para a versão prévia da saída do Armazenamento de Blobs. Este recurso será habilitado em breve no portal principal.

## <a name="supported-tokens"></a>Tokens com suporte

Os seguintes tokens especificadores de formato podem ser usados sozinhos ou de forma combinada para chegar aos formatos de DateTime personalizados:

|Especificador de formato   |DESCRIÇÃO   |Resulta na hora de exemplo 2018-01-02T10:06:08|
|----------|-----------|------------|
|{datetime:yyyy}|O ano como um número de quatro dígitos|2018|
|{datetime:MM}|Mês de 01 a 12|01|
|{datetime:M}|Mês de 1 a 12|1|
|{datetime:dd}|Dia de 01 a 31|02|
|{datetime:d}|Dia de 1 a 12|2|
|{datetime:HH}|Hora usando o formato de 24 horas, de 00 a 23|10|
|{datetime:mm}|Minutos de 00 a 24|06|
|{datetime:m}|Minutos de 0 a 24|6|
|{datetime:ss}|Segundos de 00 a 60|08|

Se não quiser usar padrões de DateTime personalizados, você poderá adicionar o token {date} e/ou {time} ao prefixo do caminho para gerar um menu suspenso com os formatos de DateTime internos.

![Formatos de DateTime antigos do Stream Analytics](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

## <a name="extensibility-and-restrictions"></a>Extensibilidade e restrições

Você pode usar tantos tokens, `{datetime:<specifier>}`, quantos quiser no padrão do caminho, até alcançar o limite de caracteres do Prefixo do caminho. Especificadores de formato não podem ser combinados em um único token além das combinações já listadas pelos menus suspensos de data e hora. 

Para uma partição de caminho de `logs/MM/dd`:

|Expressão válida   |Expressão inválida   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

Você pode usar o mesmo especificador de formato várias vezes no Prefixo do caminho. O token deve ser repetido a cada vez.

## <a name="hive-streaming-conventions"></a>Convenções de streaming de Hive

Padrões de caminho personalizados para o Armazenamento de Blobs podem ser usados com a convenção de streaming de Hive, que espera que as pastas sejam rotuladas com `column=` no nome da pasta.

Por exemplo, `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

A saída personalizada elimina o incômodo de alterar as tabelas e adicionar partições manualmente para mover dados entre o Azure Stream Analytics e o Hive. Em vez disso, muitas pastas podem ser adicionadas automaticamente usando:

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Exemplo

Crie uma conta de armazenamento, um grupo de recursos, um trabalho do Stream Analytics e uma fonte de entrada de acordo com o guia de início rápido do [Azure Stream Analytics no Portal do Azure](stream-analytics-quick-create-portal.md) guia de início rápido. Use os mesmos dados de exemplo usados no guia de início rápido, também disponíveis no [GitHub](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json).

Crie um coletor de saída de blob com a seguinte configuração:

![Criar coletor de saída de blob do Stream Analytics](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

O padrão de caminho completo é o seguinte:


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


Quando você inicia o trabalho, uma estrutura de pastas com base no padrão do caminho é criada em seu contêiner de blob. Você pode fazer drill down até o nível do dia.

![Saída de blob do Stream Analytics com padrão de caminho personalizado](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>Próximas etapas

* [Entender as saídas do Azure Stream Analytics](stream-analytics-define-outputs.md)
