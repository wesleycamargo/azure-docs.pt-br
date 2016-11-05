---
title: Opções de contexto de computação para o Servidor R no HDInsight (preview) | Microsoft Docs
description: Conheça as diferentes opções de contexto de computação disponíveis para usuários com o Servidor R no HDInsight (preview)
services: HDInsight
documentationcenter: ''
author: jeffstokes72
manager: jhubbard
editor: cgronlun

ms.service: HDInsight
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 07/21/2016
ms.author: jeffstok

---
# Opções de contexto de computação para o Servidor R no HDInsight (preview)
O Servidor R da Microsoft no Azure HDInsight (preview) fornece os recursos mais recentes para a análise baseada em R. Ele usa dados armazenados no HDFS em um contêiner na conta de armazenamento de [Blobs do Azure](../storage/storage-introduction.md "Armazenamento do Blob do Azure") ou no sistema de arquivos local do Linux. Uma vez que o Servidor R é criado no R de software livre, os aplicativos baseados em R que você compilar podem aproveitar qualquer um dos mais de 8000 pacotes de R de software livre. Eles também podem aproveitar as rotinas no [ScaleR](http://www.revolutionanalytics.com/revolution-r-enterprise-scaler "Revolution Analytics ScaleR"), o pacote de análise de Big Data da Microsoft que está incluído no Servidor R.

O nó de borda de um cluster Premium fornece um local conveniente para se conectar ao cluster e executar os scripts de R. Com um nó do borda, você tem a opção de executar funções distribuídas paralelizadas do ScaleR nos núcleos do servidor do nó de borda. Você também tem a opção de executá-las em todos os nós do cluster usando o Hadoop Map Reduce do ScaleR ou os contextos de computação do Spark.

## Contextos de computação para um nó de extremidade
Em geral, um script de R que é executado no Servidor R no nó de borda é executado no interpretador de R nesse nó. A exceção é aquelas etapas que chamam uma função ScaleR. As chamadas de ScaleR são executadas em um ambiente de computação que é determinado pela configuração do contexto de computação do ScaleR. Ao executar o script de R de um nó de borda, os possíveis valores de contexto de computação são local sequencial ('local'), local paralelo ('localpar'), Map Reduce e Spark.

As opções ‘local’ e ‘localpar’ diferem apenas em como chamadas do rxExec são executadas. As duas executam outras chamadas de função rx de forma paralela entre os núcleos disponíveis, a menos que seja especificado de outra forma por meio do uso da opção numCoresToUse de ScaleR, por exemplo, rxOptions(numCoresToUse=6). Veja a seguir um resumo das diversas opções de contexto de computação

| Contexto de computação | Como definir | Contexto de execução |
| --- | --- | --- |
| Local sequencial |rxSetComputeContext(‘local’) |Execução em paralelo entre os núcleos do servidor de nó de borda, exceto para chamadas rxExec, que são executadas em série |
| Local paralelo |rxSetComputeContext(‘localpar’) |Execução paralela entre os núcleos do servidor de nó de borda |
| Spark |RxSpark() |Execução distribuída em paralelo por meio do Spark em todos os nós do cluster do HDI |
| Map Reduce |RxHadoopMR() |Execução distribuída em paralelo por meio do Map Reduce em todos os nós do cluster do HDI |

Supondo que você preferiria a execução em paralelo para fins de desempenho, há três opções. A opção escolhida depende da natureza de seu trabalho de análise e do tamanho e localização dos dados.

## Diretrizes para decidir em um contexto de computação
Atualmente, não há uma fórmula que informa qual contexto de computação usar. No entanto, há alguns princípios importantes que podem ajudar você a fazer a escolha certa ou, pelo menos, ajudar a refinar suas escolhas antes de executar um benchmark. Esses princípios básicos incluem:

1. O sistema de arquivos Linux local é mais rápido do que o HDFS.
2. As análises repetidas serão mais rápidas se os dados forem locais e em XDF.
3. É preferível para transmitir pequenas quantidades de dados de uma fonte de dados de texto. Se a quantidade de dados for maior, converta-a para XDF antes da análise.
4. A sobrecarga da cópia ou transmissão dos dados para o nó de borda para análise se torna incontrolável para grandes quantidades de dados.
5. Spark é mais rápido do que Map Reduce para análise no Hadoop.

Com esses princípios, algumas regras gerais para selecionar um contexto de computação são:

### Local
* Se a quantidade de dados a ser analisada for pequena e não demandar análise repetida, transmita-a diretamente para a rotina de análise e use 'local' ou 'localpar'.
* Se a quantidade de dados a ser analisada for de pequeno ou médio porte e necessitar de análise repetida, copie-a para o sistema de arquivos local, importe-a para XDF e analise-a por meio de 'local' ou 'localpar'.

### Hadoop Spark
* Se a quantidade de dados a ser analisada for grande, importe-a para XDF no HDFS (a menos que o armazenamento seja um problema) e analise-a via 'Spark'.

### Hadoop Map Reduce
* Use somente se você encontrar um problema intransponível com o uso do contexto de computação do Spark, pois geralmente ele será mais lento.

## Ajuda embutida em rxSetComputeContext
Para obter mais informações e exemplos de contextos de computação de ScaleR, confira a ajuda embutida sobre R no método rxSetComputeContext, por exemplo:

    > ?rxSetComputeContext

Você também pode conferir o “Guia de computação distribuída do ScaleR” disponível na biblioteca [MSDN do Servidor R](https://msdn.microsoft.com/library/mt674634.aspx "R Server no MSDN").

## Próximas etapas
Neste artigo, você aprendeu como criar um novo cluster do HDInsight que inclui o Servidor R. Você também aprendeu os fundamentos de como usar o console de R de uma sessão SSH. Agora você pode ler os artigos a seguir para descobrir outras maneiras de trabalhar com o Servidor R no HDInsight:

* [Visão geral do Servidor R no Hadoop](hdinsight-hadoop-r-server-overview.md)
* [Introdução ao Servidor R para o Hadoop](hdinsight-hadoop-r-server-get-started.md)
* [Adicionar RStudio Server ao HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)
* [Opções de Armazenamento do Azure para o Servidor R no HDInsight Premium](hdinsight-hadoop-r-server-storage.md)

<!---HONumber=AcomDC_0921_2016-->