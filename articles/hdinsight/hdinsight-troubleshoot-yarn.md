---
title: "Solução de problemas do YARN – Azure HDInsight | Microsoft Docs"
description: Use as perguntas frequentes sobre o Yarn para obter respostas a perguntas comuns sobre o Yarn na plataforma do Azure HDInsight.
keywords: "Azure HDInsight, Yarn, perguntas frequentes, guia de solução de problemas, perguntas comuns"
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: 
editor: 
ms.assetid: F76786A9-99AB-4B85-9B15-CA03528FC4CD
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: arijitt
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 576d6bfe9697d2abd6f84824960dad62e9ed3c11
ms.contentlocale: pt-br
ms.lasthandoff: 07/11/2017


---

# <a name="yarn-troubleshooting"></a>Solução de problemas do YARN

Este artigo descreve os principais problemas e suas resoluções para trabalhar com conteúdo do YARN no Apache Ambari.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Como fazer para criar uma nova fila do Yarn em um cluster

### <a name="issue"></a>Problema:

Criar uma nova fila do Yarn com alocação de capacidade no cluster do HDInsight.  

### <a name="resolution-steps"></a>Etapas de Resolução: 

Use as etapas a seguir por meio do Ambari para criar uma nova fila do Yarn e equilibrar a alocação de capacidade entre todas as filas. 

Neste exemplo, duas filas existentes (padrão e thriftsvr) são alteradas de uma sobre capacidade de 50% para uma capacidade de 25% que permite que a nova fila (Spark) tenha uma capacidade de 50%.
| Fila | Capacidade | Máx. Capacidade |
| --- | --- | --- | --- |
| padrão | 25% | 50% |
| thrftsvr | 25% | 50% |
| spark | 50% | 50% |
1. Clique no ícone de Exibições do Abari, o padrão de grade e escolha **Gerenciador de filas do Yarn**.

    ![Clique no ícone Exibições do Ambari](media/hdinsight-troubleshoot-yarn/create-queue-1.png)
1. Selecione a fila **padrão**.

    ![Selecione a fila padrão](media/hdinsight-troubleshoot-yarn/create-queue-2.png)
1. Altere a **Capacidade** de 50% para 25% para a fila **padrão** e altere também para 25% para a fila **thriftsvr**.

    ![Alterar a Capacidade para 25% para as filas padrão e thriftsvr](media/hdinsight-troubleshoot-yarn/create-queue-3.png)
1. Clique em **Adicionar Fila** para criar uma nova fila.

    ![Clique em Adicionar Fila](media/hdinsight-troubleshoot-yarn/create-queue-4.png)
1. Dê um nome à nova fila.

    ![Dê à fila o nome Spark](media/hdinsight-troubleshoot-yarn/create-queue-5.png)  
1. Deixe os valores de **Capacidade** em 50% e clique no botão **Ações**.

    ![Clique no botão Ações](media/hdinsight-troubleshoot-yarn/create-queue-6.png)  
1. Escolha **Salvar e Atualizar filas**.

    ![Escolha Salvar e Atualizar filas](media/hdinsight-troubleshoot-yarn/create-queue-7.png)  

Essas alterações estarão visíveis imediatamente na interface do usuário do Agendador do Yarn.

### <a name="further-reading"></a>Leitura Adicional:

- [Agendador de capacidade yarn](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)


## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Como fazer para baixar logs do Yarn de um cluster

#### <a name="issue"></a>Problema:

Necessário baixar o mestre de aplicativo Yarn e outros logs de contêiner do cluster do HDInsight.  

#### <a name="resolution-steps"></a>Etapas de Resolução: 

1. Conecte-se ao cluster do HDInsight com um cliente SSH (Secure Shell) (consulte a seção Leitura adicional abaixo).
1. Liste todas as IDs de aplicativo dos aplicativos Yarn em execução no momento com o seguinte comando:

```apache
yarn top
```
As IDs são listadas na coluna `APPLICATIONID`, cujos logs devem ser baixados.

```apache
YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
Queue(s) Containers: 2 allocated, 0 pending, 0 reserved

                  APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
 application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
 application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
```

Baixe os logs de contêineres do Yarn para todos os mestres de aplicativo com o seguinte comando:
   
```apache
yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
```

Isso criará o arquivo de log chamado `amlogs.txt` no formato de texto. 

Baixe os logs do contêiner do Yarn apenas para o mestre aplicativo mais recente com o seguinte comando:

```apache
yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
```

Isso criará o arquivo de log chamado `latestamlogs.txt` no formato de texto. 

Baixe os logs do contêiner do YARN para os dois primeiros mestres de aplicativo com o seguinte comando:

```apache
yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt 
```

Isso criará o arquivo de log chamado `first2amlogs.txt` no formato de texto. 

Baixe todos os logs do contêiner do Yarn com o seguinte comando:

```apache
yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
```

Isso criará o arquivo de log chamado `logs.txt` no formato de texto. 

Baixe o log do contêiner yarn para um contêiner específico com o seguinte comando:

```apache
yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt 
```

Isso criará o arquivo de log chamado `containerlogs.txt` no formato de texto.

#### <a name="further-readings"></a>Leituras Adicionais:

- [Conectar-se ao cluster do HDInsight usando SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)

- [Aplicativos e conceitos do Apache Hadoop Yarn](https://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/)









