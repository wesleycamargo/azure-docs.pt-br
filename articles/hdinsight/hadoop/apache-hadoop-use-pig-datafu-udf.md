---
title: Usar Apache DataFu com Apache Pig no HDInsight - Azure
description: Apache DataFu Pig é uma coleção de bibliotecas para usar com Apache Pig no Apache Hadoop. Saiba como usar DataFu com Pig no cluster do HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/16/2018
ms.author: hrasheed
ms.openlocfilehash: d67c3e452da05c626721d4c3144e612e6f9e0af4
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56338437"
---
# <a name="use-apache-datafu-pig-with-apache-pig-on-hdinsight"></a>Usar Apache DataFu Pig com Apache Pig no HDInsight

Saiba como usar Apache DataFU Pig com o HDInsight.

Apache DataFu Pig é uma coleção de bibliotecas de software livre para usar com Apache Pig no Apache Hadoop.
Para obter mais informações sobre DataFu Pig, consulte [https://datafu.apache.org/](https://datafu.apache.org/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure.

* Um cluster do HDInsight do Azure (com base no Linux ou Windows)

  > [!IMPORTANT]  
  > O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Familiaridade básica com [usar o Apache Pig no HDInsight](hdinsight-use-pig.md)

## <a name="install-datafu-on-linux-based-hdinsight"></a>Instalar DataFu no HDInsight baseados em Linux

> [!IMPORTANT]  
> DataFu é instalado em clusters baseados em Linux, da versão 3.3 e superior, e em clusters baseados no Windows. Ele não é instalado em clusters baseados em Linux anteriores à versão 3.3.
>
> Se você estiver usando um cluster baseado no Windows ou um cluster baseado em Linux superior à versão 3.3, ignore esta seção.

DataFu pode ser baixado e instalado no repositório Maven. Use as etapas a seguir para localizar a versão necessária e adicioná-la ao seu cluster HDInsight:

> [!WARNING]  
> Versões DataFU podem ter requisitos que não são atendidos pelo HDInsight. Por exemplo, se você usar uma versão mais antiga do DataFu, ele pode exigir uma versão diferente do Pig que está incluído no HDInsight.

### <a name="find-a-version"></a>Encontrar uma versão

1. No navegador da web, navegue até https://mvnrepository.com/artifact/org.apache.datafu/datafu-pig e localize a versão necessária.

2. Selecione o número de versão vinculada.

3. Selecione __Exibir tudo__ para exibir todos os arquivos.

4. Na lista de arquivos, localize o arquivo .jar. Normalmente, esse arquivo é o maior dos listados, pois inclui todas as dependências. Clique com botão direito no link e copie o endereço do link.

### <a name="download-datafu-to-hdinsight"></a>Baixar DataFu para HDInsight

1. Utilize SSH para se conectar ao cluster do HDInsight para Linux. Para obter mais informações, confira [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Use o comando a seguir para baixar o arquivo jar do DataFu usando o utilitário wget:

    > [!IMPORTANT]  
    > Substitua o link no comando com a URL que você copiou anteriormente.

    ```
    wget https://central.maven.org/maven2/org/apache/datafu/datafu-pig/1.4.0/datafu-pig-1.4.0.jar
    ```

3. Em seguida, carregue o arquivo no armazenamento padrão para o cluster HDInsight. Colocar o arquivo no armazenamento padrão torna-o disponível para todos os nós no cluster.

    > [!IMPORTANT]  
    > Substitua o número de versão no nome do arquivo com a versão que você baixou.

    ```
    hdfs dfs -put datafu-pig-1.4.0.jar /example/jars
    ```

    > [!NOTE]  
    > O comando anterior armazena o jar em `/example/jars`, já que este diretório já existe no armazenamento de cluster. Você pode usar qualquer local que desejar no armazenamento de cluster HDInsight.

## <a name="use-datafu-with-pig"></a>Usar DataFu com Pig

As etapas nesta seção pressupõem que você esteja familiarizado com o uso do Pig no HDInsight. Para obter mais informações sobre como usar o Pig com o HDInsight, consulte [Usar o Pig com o HDInsight](hdinsight-use-pig.md).

> [!IMPORTANT]  
> Se você instalou manualmente DataFu usando as etapas na seção anterior, você deve registrá-lo antes de usá-lo.
>
> * Se o cluster usar o Armazenamento do Azure, use um caminho `wasb://`. Por exemplo, `register wasb:///example/jars/datafu-pig-1.4.0.jar`.
>
> * Se o cluster usa o Azure Data Lake Store Gen2, use um caminho `abfs://`. Por exemplo, `register abfs://home/example/jars/datafu-pig-1.4.0.jar`.
>
> * Se o cluster usa o Azure Data Lake Store Gen1, use um caminho `adl://`. Por exemplo, `register adl://home/example/jars/datafu-pig-1.4.0.jar`.

Normalmente, você define um alias para funções DataFu. O exemplo a seguir define um alias de `SHA`:

```piglatin
DEFINE SHA datafu.pig.hash.SHA();
```

Em seguida, você pode usar esse alias em um script de Pig Latin para gerar um hash para os dados de entrada. Por exemplo, o código a seguir substitui a localização nos dados de entrada com um valor de hash:

```piglatin
raw = LOAD '/HdiSamples/HdiSamples/SensorSampleData/building/building.csv' USING
    org.apache.pig.piggybank.storage.CSVExcelStorage(',', 'NO_MULTILINE', 'UNIX', 'SKIP_INPUT_HEADER') AS
    (int1:int,
     id1:chararray,
     int2:int,
     id2:chararray,
     location:chararray);
mask = FOREACH raw GENERATE int1, id1, int2, id2, SHA(location);
DUMP mask;
```

Isso gera a saída a seguir:

    (1,M1,25,AC1000,aa5ab35a9174c2062b7f7697b33fafe5ce404cf5fecf6bfbbf0dc96ba0d90046)
    (2,M2,27,FN39TG,7a1ca4ef7515f7276bae7230545829c27810c9d9e98ab2c06066bee6270d5153)
    (3,M3,28,JDNS77,07f62b021771d3cf67e2e1faf18769cc5e5c119ad7d4d1847a11e11d6d5a7ecb)
    (4,M4,17,GG1919,aed8f531aa7e785be255bc435e2582e74c58defedebcb629eeabf365b809bd6f)
    (5,M5,3,ACMAX22,1ed8c7e56c947bebc0cfcf88c4ea0f02c944568f71df893a99970e4f0c78cddc)
    (6,M6,9,AC1000,c96dd81db196cca5f57bd4270bbb9d9e9d1b242d67f9364005ee1dfdc2632523)
    (7,M7,13,FN39TG,3e049d78d958038ac6bd5dcf038075cc73362b4956aaf7308c3a69c8eca76297)
    (8,M8,25,JDNS77,c1ef40ce0484c698eb4bd27fe56c1e7b68d74f9780ed674210d0e5013dae45e9)
    (9,M9,11,GG1919,a7d355b26bda6bf1196ccffead0b2cf2b81f0a9de5b4876b44407f1dc07e51e6)
    (10,M10,23,ACMAX22,10436829032f361a3de50048de41755140e581467bc1895e6c1a17f423e42d10)
    (11,M11,14,AC1000,348841ef53dbd5a64008a86be432973db790774fb28b59b0d99702a3188b3705)
    (12,M12,26,FN39TG,aed8f531aa7e785be255bc435e2582e74c58defedebcb629eeabf365b809bd6f)
    (13,M13,25,JDNS77,ed9ad13611d7164839dd3feaf9a7f6a75a4138f389e0d45f8e07fa38da1116a2)
    (14,M14,17,GG1919,80db4ccdca106d37b920206331fcfe3e9e50a9e763d89b54ce3ad5ac8cf30f03)
    (15,M15,19,ACMAX22,3552ac0c032467fbf592cb4d10c5c9267800c01e343ad8ca557256d882ae9327)
    (16,M16,23,AC1000,07c67d76ef92ac9853588e098983fefba4ba5965f8fec95f42ab0d04c27865ba)
    (17,M17,11,FN39TG,557c1599d9a04895d3817d293e0806a4419a14de31958386182798d0d2ed3a56)
    (18,M18,25,JDNS77,dbc74a36d8e7439c45c64d856388506cc9b1218619cef979c3d605115a7a4546)
    (19,M19,14,GG1919,be55ef3f4c4e6c2d9c2afe2a33ac90ad0f50d4de7f9163999877e2a9ca5a54f8)
    (20,M20,19,ACMAX22,ea0b937ea317101ee2c26b03a4843a19ceced8a2b9673c3cf409a726ca2b0fd8)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre DataFu ou Pig, consulte os seguintes documentos:

* [Guia de Introdução do Apache DataFu Pig](https://datafu.apache.org/docs/datafu/getting-started.html).
* [Usar o Apache Pig com o HDInsight](hdinsight-use-pig.md)
