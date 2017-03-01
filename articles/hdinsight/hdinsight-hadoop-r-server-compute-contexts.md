---
title: "Opções de contexto de computação para o Servidor R no HDInsight | Microsoft Docs"
description: "Conheça as diferentes opções de contexto de computação disponíveis para usuários com o Servidor R no HDInsight"
services: HDInsight
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 0deb0b1c-4094-459b-94fc-ec9b774c1f8a
ms.service: HDInsight
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 841e70fa3a80bbeb7e2281246bac2f99c0de899f
ms.openlocfilehash: 169743012b1f50d67d5eafdb279e706719752eb8
ms.lasthandoff: 11/22/2016


---
# <a name="compute-context-options-for-r-server-on-hdinsight"></a>Opções de contexto de computação para o Servidor R no HDInsight
O Microsoft R Server no Azure HDInsight fornece os recursos mais recentes para a análise baseada em R. Ele usa dados armazenados no HDFS em um contêiner na conta de armazenamento de [Blob do Azure](../storage/storage-introduction.md "Armazenamento de Blobs do Azure"), em um repositório Data Lake Store ou no sistema de arquivos local do Linux. Uma vez que o Servidor R é criado no R de software livre, os aplicativos baseados em R que você compilar podem aproveitar qualquer um dos mais de 8000 pacotes de R de software livre. Eles também podem aproveitar as rotinas no [ScaleR](http://www.revolutionanalytics.com/revolution-r-enterprise-scaler "Revolution Analytics ScaleR"), o pacote de análise de Big Data da Microsoft que está incluído no Servidor R.  

O nó de borda de um cluster Premium fornece um local conveniente para se conectar ao cluster e executar os scripts de R. Com um nó do borda, você tem a opção de executar funções distribuídas paralelizadas do ScaleR nos núcleos do servidor do nó de borda. Você também tem a opção de executá-las em todos os nós do cluster usando o Hadoop Map Reduce do ScaleR ou os contextos de computação do Spark.

## <a name="compute-contexts-for-an-edge-node"></a>Contextos de computação para um nó de extremidade
Em geral, um script de R que é executado no Servidor R no nó de borda é executado no interpretador de R nesse nó. As exceções são aquelas etapas que chamam uma função ScaleR. As chamadas de ScaleR são executadas em um ambiente de computação que é determinado pela configuração do contexto de computação do ScaleR.  Ao executar o script de R de um nó de borda, os possíveis valores de contexto de computação são local sequencial ('local'), local paralelo ('localpar'), Map Reduce e Spark.

As opções ‘local’ e ‘localpar’ diferem apenas em como chamadas do rxExec são executadas. As duas executam outras chamadas de função rx de forma paralela entre os núcleos disponíveis, a menos que seja especificado de outra forma por meio do uso da opção numCoresToUse de ScaleR, por exemplo, rxOptions(numCoresToUse=6). Veja a seguir um resumo das diversas opções de contexto de computação

| Contexto de computação  | Como definir                      | Contexto de execução                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Local sequencial | rxSetComputeContext(‘local’)    | Execução em paralelo entre os núcleos do servidor de nó de borda, exceto para chamadas rxExec, que são executadas em série |
| Local paralelo   | rxSetComputeContext(‘localpar’) | Execução paralela entre os núcleos do servidor de nó de borda |
| Spark            | RxSpark()                       | Execução distribuída em paralelo por meio do Spark em todos os nós do cluster do HDI |
| Map Reduce       | RxHadoopMR()                    | Execução distribuída em paralelo por meio do Map Reduce em todos os nós do cluster do HDI |

Supondo que você preferiria a execução em paralelo para fins de desempenho, há três opções. A opção escolhida depende da natureza de seu trabalho de análise e do tamanho e localização dos dados.

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Diretrizes para decidir em um contexto de computação
Atualmente, não há uma fórmula que informa qual contexto de computação usar. No entanto, há alguns princípios importantes que podem ajudar você a fazer a escolha certa ou, pelo menos, ajudar a refinar suas escolhas antes de executar um benchmark. Esses princípios básicos incluem:

1. O sistema de arquivos Linux local é mais rápido do que o HDFS.
2. As análises repetidas serão mais rápidas se os dados forem locais e em XDF.
3. É preferível para transmitir pequenas quantidades de dados de uma fonte de dados de texto. Se a quantidade de dados for maior, converta-a para XDF antes da análise.
4. A sobrecarga da cópia ou transmissão dos dados para o nó de borda para análise se torna incontrolável para grandes quantidades de dados.
5. Spark é mais rápido do que Map Reduce para análise no Hadoop.

Com esses princípios, algumas regras gerais para selecionar um contexto de computação são:

### <a name="local"></a>Local
* Se a quantidade de dados a ser analisada for pequena e não demandar análise repetida, transmita-a diretamente para a rotina de análise e use 'local' ou 'localpar'.
* Se a quantidade de dados a ser analisada for de pequeno ou médio porte e necessitar de análise repetida, copie-a para o sistema de arquivos local, importe-a para XDF e analise-a por meio de 'local' ou 'localpar'.

### <a name="hadoop-spark"></a>Hadoop Spark
* Se a quantidade de dados a serem analisados for grande, importe-os para um Spark DataFrame usando RxHiveData ou RxParquetData ou para o XDF no HDFS (exceto se o armazenamento for um problema) e analise-os por meio do ‘Spark’.

### <a name="hadoop-map-reduce"></a>Hadoop Map Reduce
* Use somente se você encontrar um problema intransponível com o uso do contexto de computação do Spark, pois geralmente ele será mais lento.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Ajuda embutida em rxSetComputeContext
Para obter mais informações e exemplos de contextos de computação de ScaleR, confira a ajuda embutida sobre R no método rxSetComputeContext, por exemplo:

    > ?rxSetComputeContext

Você também pode consultar o “[Guia de computação distribuída do ScaleR](https://msdn.microsoft.com/microsoft-r/scaler-distributed-computing)” disponível na biblioteca [MSDN do R Server](https://msdn.microsoft.com/library/mt674634.aspx "R Server no MSDN").

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como criar um novo cluster do HDInsight que inclui o Servidor R. Você também aprendeu os fundamentos de como usar o console de R de uma sessão SSH. Agora você pode ler os artigos a seguir para descobrir outras maneiras de trabalhar com o Servidor R no HDInsight:

* [Visão geral do Servidor R no Hadoop](hdinsight-hadoop-r-server-overview.md)
* [Introdução ao Servidor R para o Hadoop](hdinsight-hadoop-r-server-get-started.md)
* [Adicionar Servidor do RStudio ao HDInsight (se não instalado durante a criação de cluster)](hdinsight-hadoop-r-server-install-r-studio.md)
* [Opções de Armazenamento do Azure para o Servidor R no HDInsight](hdinsight-hadoop-r-server-storage.md)


