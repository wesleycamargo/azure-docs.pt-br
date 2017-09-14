---
title: Analisar e Processar os documentos JSON com o Hive no HDInsight | Microsoft Docs
description: "Saiba como usar documentos JSON e analisá-los usando o Hive no HDInsight."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: e17794e8-faae-4264-9434-67f61ea78f13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: 763bc597bdfc40395511cdd9d797e5c7aaad0fdf
ms.openlocfilehash: ee7d40d2ff0ae1ac10b54f4c1f1dd704a70eb70c
ms.contentlocale: pt-br
ms.lasthandoff: 09/06/2017

---
# <a name="process-and-analyze-json-documents-using-hive-in-hdinsight"></a>Processar e analisar documentos JSON usando Hive no HDInsight

Saiba como processar e analisar arquivos JSON usando Hive no HDInsight. O documento JSON a seguir é usado no tutorial:

    {
        "StudentId": "trgfg-5454-fdfdg-4346",
        "Grade": 7,
        "StudentDetails": [
            {
                "FirstName": "Peggy",
                "LastName": "Williams",
                "YearJoined": 2012
            }
        ],
        "StudentClassCollection": [
            {
                "ClassId": "89084343",
                "ClassParticipation": "Satisfied",
                "ClassParticipationRank": "High",
                "Score": 93,
                "PerformedActivity": false
            },
            {
                "ClassId": "78547522",
                "ClassParticipation": "NotSatisfied",
                "ClassParticipationRank": "None",
                "Score": 74,
                "PerformedActivity": false
            },
            {
                "ClassId": "78675563",
                "ClassParticipation": "Satisfied",
                "ClassParticipationRank": "Low",
                "Score": 83,
                "PerformedActivity": true
            }
        ]
    }

O arquivo pode ser encontrado em wasb://processjson@hditutorialdata.blob.core.windows.net/. Para obter mais informações sobre como usar o armazenamento de Blobs do Azure com o HDInsight, consulte [Usar o armazenamento de Blobs do Azure compatível com HDFS com o Hadoop no HDInsight](hdinsight-hadoop-use-blob-storage.md). Você pode copiar o arquivo para o contêiner padrão do seu cluster.

Neste tutorial, você usa o console do Hive.  Para obter instruções sobre como abrir o console do Hive, consulte [Usar o Hive com Hadoop no HDInsight com Área de Trabalho Remota](hdinsight-hadoop-use-hive-remote-desktop.md).

## <a name="flatten-json-documents"></a>Nivelar os documentos JSON
Os métodos listados na próxima seção exigem que o documento JSON estejam em uma única linha. Portanto, você deve nivelar o documento JSON com uma cadeia de caracteres. Se seu documento JSON já está nivelado, ignore esta etapa e vá diretamente para a próxima seção sobre como analisar dados JSON.

    DROP TABLE IF EXISTS StudentsRaw;
    CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

    DROP TABLE IF EXISTS StudentsOneLine;
    CREATE EXTERNAL TABLE StudentsOneLine
    (
      json_body string
    )
    STORED AS TEXTFILE LOCATION '/json/students';

    INSERT OVERWRITE TABLE StudentsOneLine
    SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
          FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
          GROUP BY INPUT__FILE__NAME;

    SELECT * FROM StudentsOneLine

O arquivo JSON bruto está localizado em **wasb://processjson@hditutorialdata.blob.core.windows.net/**. A tabela *StudentsRaw* do Hive aponta para o documento JSON bruto não nivelado.

A tabela *StudentsOneLine* do Hive armazena os dados no sistema de arquivos padrão do HDInsight no caminho */json/students/*.

A instrução INSERT preenche a tabela StudentOneLine com os dados JSON nivelados.

A instrução SELECT deve retornar apenas uma linha.

Veja abaixo a saída da instrução SELECT:

![Nivelamento do documento JSON.][image-hdi-hivejson-flatten]

## <a name="analyze-json-documents-in-hive"></a>Analisar documentos JSON no Hive
O Hive fornece três mecanismos diferentes para executar consultas em documentos JSON:

* usar a UDF (função definida pelo Usuário) GET\_JSON\_OBJECT
* usar a UDF JSON_TUPLE
* usar SerDe personalizado
* gravar sua própria UDF usando Python ou outras linguagens. Consulte [este artigo][hdinsight-python] sobre como executar seu próprio código Python com o Hive.

### <a name="use-the-getjsonobject-udf"></a>Usar a UDF GET\_JSON_OBJECT
O Hive fornece uma UDF interna chamada [get json object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object), que pode executar consultas JSON durante o tempo de execução. Esse método requer dois argumentos: o nome da tabela e o nome do método que tem o documento JSON nivelado, e o campo JSON que precisa ser analisado. Vejamos um exemplo para ver como essa UDF funciona.

Obter o nome e o sobrenome de cada aluno

    SELECT
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
    FROM StudentsOneLine;

Aqui está a saída obtida ao executar essa consulta na janela do console.

![UDF get_json_object][image-hdi-hivejson-getjsonobject]

A UDF get-json_object tem algumas limitações.

* Uma vez que cada campo na consulta requer a reanálise da consulta, o desempenho é afetado.
* GET\_JSON_OBJECT() retorna a representação da cadeia de caracteres de uma matriz. Para converter isso em uma matriz do Hive, você precisa usar expressões regulares para substituir os colchetes “[” e “]” e depois também realizar a divisão para obter a matriz.

É por isso que o wiki do Hive recomenda o uso de json_tuple.  

### <a name="use-the-jsontuple-udf"></a>Usar a UDF JSON_TUPLE
Outra UDF fornecida pelo Hive é denominada [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), que é mais bem executada do que [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Esse método usa um conjunto de chaves e uma cadeia de caracteres JSON e retorna uma tupla de valores usando uma função. A consulta a seguir retorna a ID e a série do aluno por meio do documento JSON:

    SELECT q1.StudentId, q1.Grade
      FROM StudentsOneLine jt
      LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
        AS StudentId, Grade;

A saída deste script no console do Hive:

![UDF json_tuple][image-hdi-hivejson-jsontuple]

JSON\_TUPLE usa a sintaxe da [exibição lateral](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) no Hive, que permite que a json\_tupla criar uma tabela virtual aplicando a função UDT em cada linha da tabela original.  JSONs complexos se tornam muito complicados devido ao uso repetido de LATERAL VIEW. Além disso, JSON_TUPLE não pode manipular JSONs aninhados.

### <a name="use-custom-serde"></a>Usar SerDe personalizado
SerDe é a melhor opção para analisar documentos JSON aninhados, pois permite definir o esquema JSON e usar o esquema para analisar os documentos. Para obter instruções, consulte [como usar um Serde JSON personalizado com o Microsoft Azure HDInsight](https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Resumo
Concluindo, o tipo de operador JSON no Hive que você escolher dependerá de seu cenário. Se você tiver um documento JSON simples e apenas um campo para pesquisar, poderá optar por usar a UDF get\_json\_object do Hive. Se tiver mais de uma chave para pesquisar, poderá usar json_tuple. Se tiver um documento aninhado, deverá usar o SerDe JSON.

## <a name="next-steps"></a>Próximas etapas

Para outros artigos relacionados, consulte

* [Usar o Hive e o HiveQL com o Hadoop no HDInsight para analisar um arquivo log4j do Apache de exemplo](hdinsight-use-hive.md)
* [Analisar dados de atrasos de voos usando o Hive no HDInsight](hdinsight-analyze-flight-delay-data.md)
* [Analisar dados do Twitter usando o Hive no HDInsight](hdinsight-analyze-twitter-data.md)
* [Executar um trabalho do Hadoop usando o Azure Cosmos DB e o HDInsight](../documentdb/documentdb-run-hadoop-with-hdinsight.md)

[hdinsight-python]: hdinsight-python.md

[image-hdi-hivejson-flatten]: ./media/hdinsight-using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/hdinsight-using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/hdinsight-using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png

