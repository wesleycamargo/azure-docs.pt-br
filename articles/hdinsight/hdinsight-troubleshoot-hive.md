---
title: "Solução de problemas do Hive – Azure HDInsight | Microsoft Docs"
description: Use as perguntas frequentes sobre o Hive para obter respostas a perguntas comuns sobre o Hive na plataforma do Azure HDInsight.
keywords: "Azure HDInsight, Hive, perguntas frequentes, guia de solução de problemas, perguntas comuns"
services: Azure HDInsight
documentationcenter: na
author: dharmeshkakadia
manager: 
editor: 
ms.assetid: 15B8D0F3-F2D3-4746-BDCB-C72944AA9252
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: dharmeshkakadia
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 67b096c0585f7b73a57b784683944052d1cfaa33
ms.contentlocale: pt-br
ms.lasthandoff: 07/11/2017


---

# <a name="hive-troubleshooting"></a>Solução de problemas do HIVE

Este artigo descreve os principais problemas e suas resoluções para trabalhar com conteúdo do Hive no Apache Ambari.

## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Como fazer para exportar um metastore do Hive e importá-lo para outro cluster

### <a name="issue"></a>Problema:

É necessário exportar o metastore do Hive e importá-lo para outro cluster do HDInsight.  

### <a name="resolution-steps"></a>Etapas de Resolução: 

1. Conecte-se ao cluster HDInsight com um cliente do Secure Shell (SSH) (consulte a seção Leitura adicional abaixo).
1. Execute o seguinte comando no cluster do HDInsight do qual você deseja exportar o metastore:

```apache
for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
```

Isso gerará um arquivo chamado `allatables.sql`.

- Copie o arquivo `alltables.sql` para o novo cluster do HDInsight e execute o seguinte comando:

```apache
hive -f alltables.sql
```

Esse código supõe que os caminhos de dados no novo cluster são os mesmos que no antigo. Caso não sejam, você pode editar manualmente o arquivo  
`alltables.sql` gerado para refletir as alterações.

### <a name="further-reading"></a>Leitura Adicional:

- [Conectar-se ao cluster do HDInsight usando SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)


## <a name="how-do-locate-hive-logs-on-a-cluster"></a>Como localizar os logs do Hive em um cluster

### <a name="issue"></a>Problema:

É necessário localizar os logs do cliente, do metastore e do hiveserver do Hive no cluster do HDInsight.  

### <a name="resolution-steps"></a>Etapas de Resolução: 

- Conecte-se ao cluster HDInsight com um cliente do Secure Shell (SSH) (consulte a seção Leitura adicional abaixo).
- Os logs cliente do Hive podem ser encontrados em:

```apache
/tmp/<username>/hive.log 
```

- Os logs do metastore do Hive podem ser encontrados em:

```apache
/var/log/hive/hivemetastore.log 
```

- Os logs do Hiveserver podem ser encontrados em:

```apache
/var/log/hive/hiveserver2.log 
```

### <a name="further-reading"></a>Leitura Adicional:

- [Conectar-se ao cluster do HDInsight usando SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)

## <a name="how-do-i-launch-hive-shell-with-specific-configurations-on-a-cluster"></a>Como fazer para inicializar o shell do Hive com configurações específicas em um cluster

### <a name="issue"></a>Problema:

É necessário substituir ou especificar configurações de shell do Hive no momento da inicialização em clusters do HDInsight.  

### <a name="resolution-steps"></a>Etapas de Resolução: 

- Especifique um par de chave-valor de configuração ao iniciar o shell do Hive (consulte a seção Leitura adicional abaixo):

```apache
hive -hiveconf a=b 
```

- Liste todas as configurações efetivas no shell de Hive com o seguinte comando:

```apache
hive> set;
```

Por exemplo, use o seguinte comando para iniciar o shell do Hive com registro em log de depuração habilitado no console:
             
```apache
hive -hiveconf hive.root.logger=ALL,console 
```

### <a name="further-reading"></a>Leitura Adicional:

- [Propriedades de configuração do Hive](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)


## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Como fazer para analisar dados de DAG do Tez em um caminho crítico do cluster

### <a name="issue"></a>Problema:

É necessário analisar informações de DAG (gráfico acíclico dirigido) do Tez particularmente no caminho crítico no cluster do HDInsight

### <a name="resolution-steps"></a>Etapas de Resolução:
 
- Conecte-se ao cluster HDInsight com um cliente do Secure Shell (SSH) (consulte a seção Leitura adicional abaixo).

- Execute o comando a seguir no prompt de comando:
   
```apache
hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
```

- Liste outros analisadores que podem ser usados para analisar DAG do Tez com o seguinte comando:

```apache
hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
```

Um programa de exemplo deve ser fornecido como o primeiro argumento.

Nomes de programa válidos são: ContainerReuseAnalyzer: imprimir detalhes de reutilização do contêiner em um DAG CriticalPath: localizar o caminho crítico de um DAG LocalityAnalyzer: imprimir detalhes de localidade em um DAG ShuffleTimeAnalyzer: analisar os detalhes de tempo de ordem aleatória em um DAG SkewAnalyzer: analisar os detalhes de distorção em um DAG SlowNodeAnalyzer: imprimir detalhes do nó em um DAG SlowTaskIdentifier: imprimir detalhes da tarefa lenta em um DAG SlowestVertexAnalyzer: imprimir detalhes do vértice mais lentos em um DAG SpillAnalyzer: imprimir detalhes de derramamento em um DAG TaskConcurrencyAnalyzer: imprimir os detalhes de simultaneidade de tarefa em um DAG VertexLevelCriticalPathAnalyzer: localizar o caminho crítico no nível do vértice em um DAG


### <a name="further-reading"></a>Leitura Adicional:

- [Conectar-se ao cluster do HDInsight usando SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)



## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Como fazer para baixar dados de DAG do Tez de um cluster

#### <a name="issue"></a>Problema:

É necessário baixar informações de DAG (gráfico acíclico dirigido) do Tez do cluster do HDInsight.

#### <a name="resolution-steps"></a>Etapas de Resolução:

Há duas maneiras de coletar os dados de DAG do Tez.

- Da linha de comando:
 
    Conecte-se ao cluster do HDInsight com um cliente SSH (Secure Shell). Execute o comando a seguir no prompt de comando:
   
```apache
hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId> 
```

- Ou você pode usar o modo de exibição do Ambari do Tez:
   
Vá para Ambari --> Vá para exibição do Tez (oculta sob o ícone de blocos no canto superior direito) --> Clique no DAG em que você está interessado --> Clique nos dados de Download.

#### <a name="further-reading"></a>Leitura Adicional:

1) [Conectar-se ao cluster do HDInsight usando SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)








