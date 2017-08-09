---
title: "Notas de versão dos componentes do Hadoop no Azure HDInsight | Microsoft Docs"
description: "Últimas notas de versão e versões de componentes do Hadoop para Azure HDInsight. Obtenha detalhes e dicas de desenvolvimento para Spark, Servidor R, Hive e muito mais."
services: hdinsight
documentationcenter: 
editor: cgronlun
manager: jhubbard
author: nitinme
tags: azure-portal
ms.assetid: a363e5f6-dd75-476a-87fa-46beb480c1fe
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: 91620e993ae0413080dad03344290e971bf74949
ms.contentlocale: pt-br
ms.lasthandoff: 07/28/2017

---
# <a name="release-notes-for-hadoop-components-on-azure-hdinsight"></a>Notas de versão dos componentes do Hadoop no Azure HDInsight

Este artigo fornece informações sobre as atualizações de versão **mais recentes** do Azure HDInsight. Para obter informações sobre versões anteriores, consulte o [Arquivo morto de notas de versão do HDInsight](hdinsight-release-notes-archive.md).

> [!IMPORTANT]
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para saber mais, confira o [artigo sobre controle de versão do HDInsight](hdinsight-component-versioning.md).

## <a name="04062017---general-availability-of-hdinsight-36"></a>06/04/2017 – Disponibilidade geral do HDInsight 3.6

* Com esta versão, o Azure HDInsight adiciona a versão 3.6, que se baseia no HDP 2.6. As notas de versão do HDP 2.6 estão disponíveis [aqui](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html) e mais informações sobre versões do HDInsight podem ser encontradas [aqui](hdinsight-component-versioning.md). O HDInsight 3.6 está disponível para as cargas de trabalho a seguir:

    * Hadoop v2.7.3
    * HBase v1.1.2
    * Storm v1.1.0
    * Spark v2.1.0
    * Hive Interativo v2.1.0

* **Suporte à Exibição do Hive 2.0**. Isso deve melhorar a experiência do usuário para o Hive Interativo. Para obter mais informações, consulte a [documentação da Hortonworks](http://docs.hortonworks.com/HDPDocuments/Ambari-2.5.0.3/bk_ambari-views/content/ch_using_hive_view.html).

* **Aprimoramentos de desempenho com o Hive LLAP**. Para obter mais informações, consulte a [documentação da Hortonworks](https://hortonworks.com/blog/top-5-performance-boosters-with-apache-hive-llap/) para mais detalhes.

* **Novos recursos no Hive**. Consulte a [documentação da Hortonworks](https://hortonworks.com/apache/hive/#section_4) para obter mais detalhes.

* **Substituição da CLI do hive**: a CLI do Hive está sendo preterida e os clientes são incentivados a usar o Beeline em seu lugar. Para obter mais informações, veja a [documentação do Apache](https://cwiki.apache.org/confluence/display/Hive/Replacing+the+Implementation+of+Hive+CLI+Using+Beeline). Para obter instruções sobre como usar o Beeline com o HDInsight, consulte [Usar clusters Beeline com o Hadoop HDInsight](hdinsight-hadoop-use-hive-beeline.md).

* **Novos recursos no Apache Phoenix e HBase**.
    * Suporte a cota de armazenamento: comumente usadas em ambientes multilocatário, permitindo espaço de armazenamento limitado por tabela por nível de namespace.
    * Melhorias de indexação do Phoenix: criar índice incremental e recompilar/retomar a indexação após falhas anteriores.
    * Ferramenta de integridade de dados do Phoenix: dá suporte à validação de esquema, índice e outros metadados.


* **Problema com o HBase**: durante a execução de um trabalho de upload de CSV em massa do MapReduce, a sintaxe a seguir pode resultar em um erro.

        HADOOP_CLASSPATH=$(hbase mapredcp):/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv

    Use a sintaxe a seguir, em vez disso:

        HADOOP_CLASSPATH=/path/to/hbase-protocol.jar:/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv


## <a name="02282017---release-of-spark-21-on-hdinsight-36-preview"></a>28/02/2017 – Lançamento do Spark 2.1 no HDInsight 3.6 (Visualização)
* O [Spark 2.1](http://spark.apache.org/releases/spark-release-2-1-0.html) melhora muitos problemas de estabilidade e usabilidade com versões anteriores. Ele também traz novos recursos em todas as cargas de trabalho Spark, como Spark Core, SQL, ML e Streaming.
* O fluxo estruturado obtém escalabilidade aprimorada com suporte para marcas d'água de hora do evento e conector Kafka 0.10.
* O particionamento SQL do Spark agora é tratado usando o novo mecanismo de Tratamento de Partição Escalonável. Veja mais detalhes [aqui](http://spark.apache.org/releases/spark-release-2-1-0.html) sobre como atualizar.
* Atualmente, o Spark 2.1 no Azure HDInsight 3.6 Preview não oferece suporte à conectividade da Ferramenta de BI usando o driver ODBC.
* Não há suporte para acesso do Azure Data Lake Store usando clusters do Spark 2.1 nesta versão Preview.


## <a name="11182016---release-of-spark-201-on-hdinsight-35"></a>18/11/2016 – Lançamento do Spark 2.0.1 no HDInsight 3.5
O Spark 2.0.1 agora está disponível em clusters do Spark (HDInsight versão 3.5).

## <a name="11162016---release-of-r-server-90-on-hdinsight-35-spark-20"></a>16/11/2016 – Lançamento do R Server 9.0 no HDInsight 3.5 (Spark 2.0)
*   Os clusters de Servidor R agora incluem a opção para duas versões: Servidor R 9.0 HDI 3.5 (Spark 2.0) e Servidor R 8.0 em HDI 3.4 (Spark 1.6).
*   O Servidor R 9.0 no HDI 3.5 (Spark 2.0) está incluído no R 3.3.2 e inclui novas funções de fonte de dados ScaleR chamadas RxHiveData e RxParquetData para carregar dados do Hive e do Parquet diretamente para DataFrames Spark para análise do ScaleR. Para mais informações, veja a ajuda embutida dessas funções no R por meio do uso dos comandos **?RxHiveData** e **?RxParquetData**.
*   A edição RStudio Server community está instalada por padrão (com uma opção de recusa) na folha Configuração de Cluster, como parte do fluxo de provisionamento.

## <a name="11092016---release-of-spark-20-on-hdinsight"></a>11/09/2016 – Lançamento do Spark 2.0 no HDInsight
* Os clusters Spark 2.0 no HDInsight 3.5 agora oferecem suporte aos serviços Livy e Jupyter.

## <a name="10262016---release-of-r-server-on-hdinsight"></a>26/10/2016 – Versão do R Server no HDInsight
* O URI para o acesso ao nó de borda foi alterado para **nomedocluster**-ed-ssh.azurehdinsight.net
* O provisionamento de cluster do Servidor R no HDInsight foi simplificado.
* O Servidor R no HDInsight agora está disponível como um tipo de cluster regular “Servidor R” do HDInsight e não é mais instalado como um aplicativo HDInsight separado. O nó de borda e os binários do Servidor R agora são provisionados como parte da implantação de cluster do Servidor R. Isso melhora a velocidade e a confiabilidade do provisionamento. O modelo de preço para o Servidor R é atualizado adequadamente.
* O preço do tipo de cluster Servidor R agora se baseia na camada de preço Standard mais o preço da sobretaxa do Servidor R. A camada Premium é reservada para recursos Premium disponíveis em diferentes tipos de cluster e não será usada para o tipo de cluster do Servidor R. Essa alteração não afeta o preço efetivo do Servidor R; ela altera apenas como as cobranças são apresentadas na fatura. Todos os clusters do Servidor R existentes continuarão funcionando e os modelos do Resource Manager continuarão funcionando até o aviso de substituição. **De qualquer forma, é recomendável atualizar as implantações com script para usar o novo modelo do Resource Manager.**

## <a name="08302016---release-of-r-server-on-hdinsight"></a>30/08/2016 – Versão do R Server no HDInsight
Os números completos da versão para clusters HDInsight baseados em Linux implantados com essa versão:

| HDI | Versão de cluster do HDI | HDP | Compilação HDP | Build do Ambari |
| --- | --- | --- | --- | --- |
| 3.2 |3.2.1000.0.8268980 |2.2 |2.2.9.1-19 |2.2.1.12-4 |
| 3.3 |3.3.1000.0.8268980 |2.3 |2.3.3.1-25 |2.2.1.12-4 |
| 3.4 |3.4.1000.0.8269383 |2.4 |2.4.2.4-5 |2.2.1.12-4 |

Os números completos da versão para clusters HDInsight baseados em Windows implantados com essa versão:

| HDI | Versão de cluster do HDI | HDP | Compilação HDP |
| --- | --- | --- | --- |
| 2,1 |2.1.10.1033.2559206 |1,3 |1.3.12.0-01795 |
| 3.0 |3.0.6.1033.2559206 |2,0 |2.0.13.0-2117 |
| 3.1 |3.1.4.1033.2559206 |2,1 |2.1.16.0-2374 |
| 3.2 |3.2.7.1033.2559206 |2.2 |2.2.9.1-11 |
| 3.3 |3.3.0.1033.2559206 |2.3 |2.3.3.1-25 |






