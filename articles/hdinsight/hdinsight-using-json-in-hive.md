---
title: Analisar e Processar os documentos JSON com o Hive no HDInsight | Microsoft Docs
description: "Saiba como usar documentos JSON e analisá-los usando o Hive no HDInsight."
services: hdinsight
documentationcenter: 
author: rashimg
manager: mwinkle
editor: cgronlun
ms.assetid: e17794e8-faae-4264-9434-67f61ea78f13
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/22/2015
ms.author: rashimg
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 89618f7549d8589d5ac684772832748ab9765d1a


---
# <a name="process-and-analyze-json-documents-using-hive-in-hdinsight"></a>Processar e analisar documentos JSON usando Hive no HDInsight
Saiba como processar e analisar arquivos JSON usando Hive no HDInsight. O documento JSON a seguir será usado no tutorial

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

O arquivo pode ser encontrado em wasbs://processjson@hditutorialdata.blob.core.windows.net/. Para obter mais informações sobre como usar o armazenamento de Blob do Azure com o HDInsight, consulte [Use HDFS-compatible Azure Blob storage with Hadoop in HDInsight (Usar o armazenamento de Blob do Azure compatível com HDFS com o Hadoop no HDInsight)](hdinsight-hadoop-use-blob-storage.md). Você pode copiar o arquivo para o contêiner padrão do seu cluster se desejar.

Neste tutorial, você usará o console do Hive.  Para obter instruções sobre como abrir o console do Hive, consulte [Usar o Hive com Hadoop no HDInsight com Área de Trabalho Remota](hdinsight-hadoop-use-hive-remote-desktop.md).

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

O arquivo JSON bruto está localizado em **wasbs://processjson@hditutorialdata.blob.core.windows.net/**. A tabela *StudentsRaw* do Hive aponta para o documento JSON bruto não nivelado.

A tabela *StudentsOneLine* do Hive armazenará os dados no sistema de arquivos padrão do HDInsight no caminho */json/students/*.

A instrução INSERT preenche a tabela StudentOneLine com os dados JSON nivelados.

A instrução SELECT deve retornar apenas 1 linha.

Veja abaixo a saída da instrução SELECT:

![Nivelamento do documento JSON.][image-hdi-hivejson-flatten]

## <a name="analyze-json-documents-in-hive"></a>Analisar documentos JSON no Hive
O Hive fornece três mecanismos diferentes para executar consultas em documentos JSON:

* usar a UDF (Função Definida pelo Usuário) GET\_JSON\_OBJECT
* usar a UDF JSON_TUPLE
* usar SerDe personalizado
* gravar sua própria UDF usando Python ou outras linguagens. Consulte [este artigo][hdinsight-python] sobre como executar seu próprio código Python com o Hive.

### <a name="use-the-getjsonobject-udf"></a>Usar a UDF GET\_JSON_OBJECT
O Hive fornece uma UDF interna chamada [get json object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) que pode executar consultas JSON durante o tempo de execução. Esse método requer dois argumentos: o nome da tabela e o nome do método que tem o documento JSON nivelado, e o campo JSON que precisa ser analisado. Vejamos um exemplo para ver como essa UDF funciona.

Obter o nome e o sobrenome de cada aluno

    SELECT
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
    FROM StudentsOneLine;

Aqui está a saída obtida ao executar essa consulta na janela do console.

![UDF get_json_object][image-hdi-hivejson-getjsonobject]

A UDF get-json_object tem algumas limitações.

* Uma vez que cada campo na consulta requer a reanálise da consulta, o desempenho é afetado.
* GET\_JSON_OBJECT() retorna a representação da cadeia de caracteres de uma matriz. Para converter isso em uma matriz do Hive, você precisará usar expressões regulares para substituir os colchetes “[” e “]” e depois também realizar a divisão para obter a matriz.

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
SerDe é a melhor opção para analisar documentos JSON aninhados, pois permite definir o esquema JSON e usar o esquema para analisar os documentos. Neste tutorial, usaremos um dos SerDe mais populares desenvolvidos por [rcongiu](https://github.com/rcongiu).

**Para usar o SerDe personalizado:**

1. Instale o [Java SE Development Kit 7u55 JDK 1.7.0_55](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u55-oth-JPR). Escolha a versão Windows X64 do JDK se você for usar a implantação do Windows do HDInsight
   
   > [!WARNING]
   > O JDK 1.8 não funciona com este SerDe.
   > 
   > 
   
    Após a instalação ser concluída, adicione uma nova variável de ambiente de usuário:
   
   1. Abra **Exibir configurações avançadas do sistema** na tela do Windows.
   2. Clique em **Variáveis de Ambiente**.  
   3. Adicione uma nova variável de ambiente **JAVA_HOME**, que aponte para **C:\Program Files\Java\jdk1.7.0_55** ou onde quer que o JDK esteja instalado.
      
      ![Definir valores de configuração correto para o JDK][image-hdi-hivejson-jdk]
2. Instale o [Maven 3.3.1](http://mirror.olnevhost.net/pub/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.zip)
   
    Adicione a pasta bin ao seu caminho indo para o Painel de Controle --> Editar as Variáveis do Sistema para as Variáveis de ambiente da conta. A captura de tela abaixo mostra como fazer isso.
   
    ![Configurando o Maven][image-hdi-hivejson-maven]
3. Clone o projeto do site do github [Hive-JSON-SerDe](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) . Você pode fazer isso clicando no botão "Baixar Zip" conforme mostrado na captura de tela abaixo.
   
    ![Clonar o projeto][image-hdi-hivejson-serde]

4: vá para a pasta onde você baixou este pacote e digite "mvn package". Isso deverá criar os arquivos jar necessários que você poderá copiar para o cluster.

5: vá para a pasta de destino na pasta raiz na qual você baixou o pacote. Carregue o arquivo json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar para o nó principal do seu cluster. Normalmente, eu o coloco na pasta de binários de hive: C:\apps\dist\hive-0.13.0.2.1.11.0-2316\bin ou algo semelhante.

6: no prompt do Hive, digite "add jar /path/to/json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar". Como o jar está na pasta C:\apps\dist\hive-0.13.x\bin no meu caso, posso adicionar o jar diretamente com o nome mostrado abaixo:

    add jar json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar;

    ![Adding JAR to your project][image-hdi-hivejson-addjar]

Agora você está pronto para usar o SerDe para executar consultas no documento JSON.

A instrução a seguir cria uma tabela com um esquema definido

    DROP TABLE json_table;
    CREATE EXTERNAL TABLE json_table (
      StudentId string,
      Grade int,
      StudentDetails array<struct<
          FirstName:string,
          LastName:string,
          YearJoined:int
          >
      >,
      StudentClassCollection array<struct<
          ClassId:string,
          ClassParticipation:string,
          ClassParticipationRank:string,
          Score:int,
          PerformedActivity:boolean
          >
      >
    ) ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
    LOCATION '/json/students';

Para listar o nome e sobrenome do aluno

    SELECT StudentDetails.FirstName, StudentDetails.LastName FROM json_table;

Este é o resultado no console do Hive.

![Consulta SerDe 1][image-hdi-hivejson-serde_query1]

Para calcular a soma das pontuações do documento JSON

    SELECT SUM(scores)
    FROM json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as scores;

A consulta anterior usa a UDF [explodir exibição lateral](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) para expandir a matriz de pontuações para que possam ser somados.

Esta é a saída do console do Hive.

![Consulta SerDe 2][image-hdi-hivejson-serde_query2]

Para localizar quais matérias um aluno específico obteve mais de 80 pontos, use SELECT  
      jt.StudentClassCollection.ClassId FROM json_table jt lateral view explode(jt.StudentClassCollection.Score) collection as score  where score > 80;

A consulta acima retorna uma matriz do Hive diferente de get\_json\_object, que retorna uma cadeia de caracteres.

![Consulta SerDe 3][image-hdi-hivejson-serde_query3]

Se você quiser realizar skil em JSON malformado, conforme explicado no [página wiki](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) desse SerDe, isso pode ser feito digitando o código a seguir:  

    ALTER TABLE json_table SET SERDEPROPERTIES ( "ignore.malformed.json" = "true");




## <a name="summary"></a>Resumo
Concluindo, o tipo de operador JSON no Hive que você escolher dependerá de seu cenário. Se você tiver um documento JSON simples e apenas um campo para pesquisar, poderá optar por usar a UDF get\_json\_object do Hive. Se tiver mais de uma chave para pesquisar, poderá usar json_tuple. Se tiver um documento aninhado, deverá usar o SerDe JSON.

Para outros artigos relacionados, consulte

* [Usar o Hive e o HiveQL com o Hadoop no HDInsight para analisar um arquivo log4j do Apache de exemplo](hdinsight-use-hive.md)
* [Analisar dados de atrasos de voos usando o Hive no HDInsight](hdinsight-analyze-flight-delay-data.md)
* [Analisar dados do Twitter usando o Hive no HDInsight](hdinsight-analyze-twitter-data.md)
* [Executar um trabalho do Hadoop usando o Banco de Dados de Documentos e o HDInsight](../documentdb/documentdb-run-hadoop-with-hdinsight.md)

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



<!--HONumber=Dec16_HO2-->


