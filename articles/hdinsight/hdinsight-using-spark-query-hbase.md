---
title: Use o Spark para ler e gravar dados em HBase - Azure HDInsight| Microsoft Docs
description: Use o conector do HBase Spark para ler e gravar dados de um cluster Spark para um cluster HBase.
services: hdinsight
documentationcenter: 
author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: maxluk
ms.openlocfilehash: ccbcd1d9cb45da7076d73f71a2ed692e71816650
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
# <a name="use-spark-to-read-and-write-hbase-data"></a>Usar o Spark para ler e gravar dados do HBase

Apache HBase costuma ser consultada com sua API de nível inferior (verificações, obtenções e colocações) ou com uma sintaxe SQL usando Phoenix. Apache também fornece conector HBase Spark, que é uma alternativa conveniente e de alto desempenho para consultar e modificar os dados armazenados pelo HBase.

## <a name="prerequisites"></a>pré-requisitos

* Dois clusters HDInsight separados, um HBase e um Spark com Spark 2.1 (HDInsight 3.6) instalado.
* O cluster Spark precisa se comunicar diretamente com o cluster HBase com latência mínima, portanto a configuração recomendada é implantar ambos os clusters na mesma rede virtual. Para obter mais informações, consulte [Criar clusters baseados em Linux no HDInsight usando o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md).
* Acesso SSH para cada cluster.
* Acesso ao armazenamento padrão de cada cluster.

## <a name="overall-process"></a>Processo geral

O processo de alto nível para habilitar seu cluster Spark para consultar seu cluster HDInsight é o seguinte:

1. Preparar alguns dados de exemplo no HBase.
2. Adquira o arquivo hbase-site.xml da pasta de configuração de cluster HBase (/ etc/hbase/conf).
3. Coloque uma cópia do hbase-site.XML na sua pasta de configuração Spark 2 (/ etc/spark2/conf).
4. Execute `spark-shell` referenciar o conector do HBase Spark por seu Maven coordena na opção `packages`.
5. Defina um catálogo que mapeia o esquema do Spark para HBase.
6. Interagir com os dados do HBase usando as APIs de DataFrame ou RDD.

## <a name="prepare-sample-data-in-hbase"></a>Preparar os dados de exemplo no HBase

Nesta etapa, você pode criar e popular uma tabela simple no HBase, você pode consultar usando Spark.

1. Conecte-se ao nó principal do cluster HBase usando o SSH. Para obter mais informações, consulte [Conectar ao HDInsight usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
2. Execute o shell HBase:

        hbase shell

3. Criar uma tabela `Contacts` com as famílias de coluna `Personal` e `Office`:

        create 'Contacts', 'Personal', 'Office'

4. Carregar algumas linhas de amostra de dados:

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
        put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'

## <a name="acquire-hbase-sitexml-from-your-hbase-cluster"></a>Adquirir hbase-site.xml do seu cluster HBase

1. Conecte-se ao nó principal do cluster HBase usando o SSH.
2. Copie o hbase-site.XML do armazenamento local para a raiz de armazenamento do padrão do seu cluster HBase:

        hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml /

3. Navegue ao seu cluster HBase usando o [portal do Azure](https://portal.azure.com).
4. Selecione Contas de armazenamento. 

    ![Contas de armazenamento](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

5. Selecione a Conta de armazenamento na lista que tem uma marca de seleção na coluna padrão.

    ![Conta de armazenamento padrão](./media/hdinsight-using-spark-query-hbase/default-storage.png)

6. No painel de conta de armazenamento, selecione o bloco de Blobs.

    ![Bloco de Blobs](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

7. Na lista de contêineres, selecione o contêiner que é usado por seu cluster HBase.
8. Na lista de arquivos, selecione `hbase-site.xml`.

    ![HBase-site.xml](./media/hdinsight-using-spark-query-hbase/hbase-site-xml.png)

9. No painel de propriedades de Blob, selecione fazer o download e salvar `hbase-site.xml` em um local no computador local.

    ![Baixar](./media/hdinsight-using-spark-query-hbase/download.png)

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Colocar hbase-site.XML em seu cluster Spark

1. Navegue ao seu cluster Spark usando o [portal do Azure](https://portal.azure.com).
2. Selecione Contas de armazenamento.

    ![Contas de armazenamento](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

3. Selecione a Conta de armazenamento na lista que tem uma marca de seleção na coluna padrão.

    ![Conta de armazenamento padrão](./media/hdinsight-using-spark-query-hbase/default-storage.png)

4. No painel de conta de armazenamento, selecione o bloco de Blobs.

    ![Bloco de Blobs](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

5. Na lista de contêineres, selecione o contêiner que é usado por seu cluster Spark.
6. Selecionar carregar.

    ![Carregar](./media/hdinsight-using-spark-query-hbase/upload.png)

7. Selecione o arquivo `hbase-site.xml` baixado anteriormente em seu computador local.

    ![Carregue hbase-site.xml](./media/hdinsight-using-spark-query-hbase/upload-selection.png)

8. Selecionar Carregar.
9. Conecte-se ao nó principal do cluster Spark usando o SSH.
10. Copie `hbase-site.xml` do armazenamento de padrão do seu cluster Spark para a pasta de configuração Spark 2 no armazenamento local do cluster:

        sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Execute o Shell de Spark referenciando o conector HBase Spark

1. Conecte-se ao nó principal do cluster Spark usando o SSH.
2. Inicie o Shell do Spark, especificando o pacote conector HBase Spark:

        spark-shell --packages com.hortonworks:shc-core:1.1.0-2.1-s_2.11

3. Mantenha essa instância do Shell do Spark aberta e continue para a próxima etapa.

## <a name="define-a-catalog-and-query"></a>Definir um catálogo e consulta

Nesta etapa, você deve definir um catálogo que mapeia o esquema do Spark para HBase. 

1. No seu Shell Spark aberto, execute as seguintes `import` instruções:

        import org.apache.spark.sql.{SQLContext, _}
        import org.apache.spark.sql.execution.datasources.hbase._
        import org.apache.spark.{SparkConf, SparkContext}
        import spark.sqlContext.implicits._

2. Defina um catálogo para a tabela Contatos criados no HBase:
    1. Definir um esquema de catálogo para a tabela do HBase chamada `Contacts`.
    2. Identificar a rowkey como `key` e mapear os nomes de coluna usados no Spark para a família de coluna, o nome da coluna e o tipo de coluna como usado no HBase.
    3. A rowkey também deve ser definida em detalhes como uma coluna nomeada (`rowkey`), que tem uma família de coluna específico `cf` de `rowkey`.

            def catalog = s"""{
                |"table":{"namespace":"default", "name":"Contacts"},
                |"rowkey":"key",
                |"columns":{
                |"rowkey":{"cf":"rowkey", "col":"key", "type":"string"},
                |"officeAddress":{"cf":"Office", "col":"Address", "type":"string"},
                |"officePhone":{"cf":"Office", "col":"Phone", "type":"string"},
                |"personalName":{"cf":"Personal", "col":"Name", "type":"string"},
                |"personalPhone":{"cf":"Personal", "col":"Phone", "type":"string"}
                |}
            |}""".stripMargin

3. Defina um método que fornece um DataFrame em torno da sua `Contacts` tabela no HBase:

            def withCatalog(cat: String): DataFrame = {
                spark.sqlContext
                .read
                .options(Map(HBaseTableCatalog.tableCatalog->cat))
                .format("org.apache.spark.sql.execution.datasources.hbase")
                .load()
            }

4. Crie uma instância do DataFrame:

        val df = withCatalog(catalog)

5. Consulte o DataFrame:

        df.show()

6. Você deve ver duas linhas de dados:

        +------+--------------------+--------------+-------------+--------------+
        |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
        +------+--------------------+--------------+-------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
        |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+-------------+--------------+

7. Registre uma tabela temporária para que você possa consultar a tabela do HBase usando Spark SQL:

        df.registerTempTable("contacts")

8. Emitir uma consulta SQL em relação a `contacts` tabela:

        val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
        query.show()

9. Você deve ver os resultados como estes:

        +-------------+--------------------+
        | personalName|       officeAddress|
        +-------------+--------------------+
        |    John Dole|1111 San Gabriel Dr.|
        |  Calvin Raji|5415 San Gabriel Dr.|
        +-------------+--------------------+

## <a name="insert-new-data"></a>Inserir nova linha

1. Para inserir um novo registro de contato, defina uma `ContactRecord` classe:

        case class ContactRecord(
            rowkey: String,
            officeAddress: String,
            officePhone: String,
            personalName: String,
            personalPhone: String
            )

2. Crie uma instância de `ContactRecord` e colocá-la em uma matriz:

        val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

        var newData = new Array[ContactRecord](1)
        newData(0) = newContact

3. Salve a matriz de novos dados em HBase:

        sc.parallelize(newData).toDF.write
        .options(Map(HBaseTableCatalog.tableCatalog -> catalog))
        .format("org.apache.spark.sql.execution.datasources.hbase").save()

4. Examine os resultados:
    
        df.show()

5. Você deve ver uma saída como a abaixo:

        +------+--------------------+--------------+------------+--------------+
        |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
        +------+--------------------+--------------+------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
        | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
        |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+------------+--------------+

## <a name="next-steps"></a>Próximas etapas

* [Conector HBase Spark](https://github.com/hortonworks-spark/shc)
