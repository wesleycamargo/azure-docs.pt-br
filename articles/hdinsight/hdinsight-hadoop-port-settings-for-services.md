---
title: Portas usadas pelo HDInsight | Microsoft Docs
description: Uma lista de portas usadas pelos serviços do Hadoop em execução no HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/13/2016
ms.author: larryfr

---
# Portas e URIs usados pelo HDInsight
Este documento fornece uma lista das portas usadas pelos serviços do Hadoop em execução em clusters HDInsight baseados em Linux. Ele também fornece informações sobre portas usadas para se conectar ao cluster usando SSH.

## Portas públicas versus portas não públicas
Os clusters HDInsight baseados em Linux expõem apenas três portas publicamente na Internet: 22, 23 e 443. Elas são usadas para acessar com segurança o cluster usando SSH e serviços expostos pelo protocolo HTTPS seguro.

Internamente, o HDInsight é implementado por várias Máquinas Virtuais do Azure (os nós no cluster) em execução em uma Rede Virtual do Azure. Dentro da rede virtual, você pode acessar portas não expostas pela Internet. Por exemplo, se você se conectar a um dos nós de cabeçalho usando SSH, do nó de cabeçalho, é possível acessar diretamente os serviços em execução nos nós de cluster.

> [!IMPORTANT]
> Ao criar um cluster HDInsight, se você não especificar uma Rede Virtual do Azure como uma opção de configuração, uma será criada. No entanto, não é possível ingressar outros computadores (como outras Máquinas Virtuais do Azure ou seu computador de desenvolvimento de cliente) nessa rede virtual criada automaticamente.
> 
> 

Para ingressar mais computadores na rede virtual, primeiramente é preciso criar a rede virtual e depois especificá-la na criação do cluster HDInsight. Para saber mais, confira [Estender os recursos do HDInsight usando a Rede Virtual do Azure](hdinsight-extend-hadoop-virtual-network.md)

## Portas públicas
Todos os nós em um cluster HDInsight estão localizados em uma Rede Virtual do Azure e não podem ser acessados diretamente da internet. Um gateway público fornece acesso à internet para as portas a seguir, que são comuns a todos os tipos de cluster HDInsight.

| O Barramento de | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| sshd |22 |SSH |Conecta os clientes a sshd no nó de cabeçalho primário. Confira [Usar SSH com HDInsight baseado no Linux](hdinsight-hadoop-linux-use-ssh-windows.md) |
| sshd |22 |SSH |Conecta clientes ao sshd no nó de borda (apenas HDInsight Premium). Confira [Introdução ao uso do Servidor R no HDInsight](hdinsight-hadoop-r-server-get-started.md) |
| sshd |23 |SSH |Conecta os clientes a sshd no nó de cabeçalho secundário. Confira [Usar SSH com HDInsight baseado no Linux](hdinsight-hadoop-linux-use-ssh-windows.md) |
| Ambari |443 |HTTPS |Interface de usuário da Web do Ambari. Confira [Gerenciar clusters HDInsight usando a interface de usuário da Web do Ambari](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |API REST do Ambari. Confira [Gerenciar clusters HDInsight usando a API REST do Ambari](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |API REST do HCatalog. Confira [Usar Hive com Curl](hdinsight-hadoop-use-Pig-curl.md), [Usar Pig com Curl](hdinsight-hadoop-use-Pig-curl.md), [Usar MapReduce com Curl](hdinsight-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBC |Conecta-se ao Hive usando ODBC. Confira [Connect Excel to HDInsight with the Microsoft ODBC driver (Conectar o Excel ao HDInsight com o driver ODBC da Microsoft)](hdinsight-connect-excel-hive-odbc-driver.md). |
| HiveServer2 |443 |JDBC |Conecta-se ao Hive usando JDBC. Confira [Conectar-se ao Hive no Azure HDInsight usando o driver JDBC do Hive](hdinsight-connect-hive-jdbc-driver.md) |

Veja a seguir os tipos de cluster específicos que estão disponíveis:

| O Barramento de | Port | Protocolo | Tipo de cluster | Descrição |
| --- | --- | --- | --- | --- |
| Stargate |443 |HTTPS |HBase |API REST do HBase. Confira [Introdução ao HBase](hdinsight-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |Spark |API REST do Spark. Confira [Enviar trabalhos do Spark remotamente usando o Livy](hdinsight-apache-spark-livy-rest-interface.md) |
| Storm |443 |HTTPS |Storm |Interface de usuário do Storm para Web. Confira [Implantar e gerenciar topologias Storm no HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md) |

### Autenticação
Todos os serviços publicamente expostos na Internet devem ser autenticados:

| Port | Credenciais |
| --- | --- |
| 22 ou 23 |As credenciais de usuário SSH especificadas durante a criação do cluster |
| 443 |O nome de login (padrão: admin) e a senha que foram definidos durante a criação do cluster |

## Portas não públicas
> [!NOTE]
> Alguns serviços só estão disponíveis em tipos de cluster específicos. Por exemplo, HBase só está disponível em tipos de cluster HBase.
> 
> 

### Portas HDFS
| O Barramento de | Nó(s) | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Interface de usuário do NameNode na Web |Nós de cabeçalho |30070 |HTTPS |Interface de usuário na Web para exibir o status atual |
| Serviço de metadados NameNode |nós de cabeçalho |8020 |IPC |Metadados do sistema de arquivos |
| DataNode |Todos os nós de trabalho |30075 |HTTPS |Interface de usuário na Web para exibir status, logs, etc. |
| DataNode |Todos os nós de trabalho |30010 |&nbsp; |Transferência de dados |
| DataNode |Todos os nós de trabalho |30020 |IPC |Operações de metadados |
| NameNode secundário |Nós de cabeçalho |50090 |HTTP |Ponto de verificação para metadados do NameNode |

### Portas YARN
| O Barramento de | Nó(s) | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Interface de usuário na Web do Resource Manager |Nós de cabeçalho |8088 |HTTP |Interface de usuário na Web do Resource Manager |
| Interface de usuário na Web do Resource Manager |Nós de cabeçalho |8090 |HTTPS |Interface de usuário na Web do Resource Manager |
| Interface administrativa do Resource Manager |nós de cabeçalho |8141 |IPC |Para envios de aplicativo (Hive, servidor Hive, Pig, etc.) |
| Agendador do Resource Manager |nós de cabeçalho |8030 |HTTP |Interface administrativa |
| Interface de aplicativo do Resource Manager |nós de cabeçalho |8050 |HTTP |Endereço da interface do gerenciador de aplicativos |
| NodeManager |Todos os nós de trabalho |30050 |&nbsp; |O endereço do gerenciador de contêineres |
| Interface de usuário na Web do NodeManager |Todos os nós de trabalho |30060 |HTTP |Interface do Resource Manager |
| Endereço do Timeline |Nós de cabeçalho |10200 |RPC |O serviço RPC do serviço do Timeline. |
| Interface de usuário na Web do Timeline |Nós de cabeçalho |8181 |HTTP |Interface de usuário na Web do Timeline |

### Portas Hive
| O Barramento de | Nó(s) | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| HiveServer2 |Nós de cabeçalho |10001 |Thrift |Serviço para conexão programática ao Hive (Thrift/JDBC) |
| HiveServer |Nós de cabeçalho |10000 |Thrift |Serviço para conexão programática ao Hive (Thrift/JDBC) |
| Metastore do Hive |Nós de cabeçalho |9083 |Thrift |Serviço para conexão programática aos metadados do Hive (Thrift/JDBC) |

### Portas WebHCat
| O Barramento de | Nó(s) | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Servidor WebHCat |Nós de cabeçalho |30111 |HTTP |API Web sobre o HCatalog e outros serviços do Hadoop |

### Portas MapReduce
| O Barramento de | Nó(s) | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| JobHistory |Nós de cabeçalho |19888 |HTTP |Interface de usuário na Web JobHistory do MapReduce |
| JobHistory |Nós de cabeçalho |10020 |&nbsp; |Servidor JobHistory do MapReduce |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |Transfere saídas intermediárias Map para redutores de solicitação |

### Oozie
| O Barramento de | Nó(s) | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Servidor Oozie |Nós de cabeçalho |11000 |HTTP |URL para o serviço do Oozie |
| Servidor Oozie |Nós de cabeçalho |11001 |HTTP |Porta para administração do Oozie |

### Métricas do Ambari
| O Barramento de | Nó(s) | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| TimeLine (histórico do aplicativo) |Nós de cabeçalho |6188 |HTTP |Interface de usuário na Web do serviço TimeLine |
| TimeLine (histórico do aplicativo) |Nós de cabeçalho |30200 |RPC |Interface de usuário na Web do serviço TimeLine |

### Portas HBase
| O Barramento de | Nó(s) | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| HMaster |Nós de cabeçalho |16000 |&nbsp; |&nbsp; |
| Interface de usuário na Web para informações do HMaster |Nós de cabeçalho |16010 |HTTP |A porta para a interface de usuário na Web Mestre do HBase |
| Servidor de região |Todos os nós de trabalho |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |A porta que os clientes usam para se conectar ao ZooKeeper |

### Portas Kafka
| O Barramento de | Nó(s) | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Agente |Nós de trabalho |9092 |[Protocolo Kafka Wire ](http://kafka.apache.org/protocol.html) |Usado para comunicação do cliente |
| &nbsp; |Nós do Zookeeper |2181 |&nbsp; |A porta que os clientes usam para se conectar ao Zookeeper |

<!---HONumber=AcomDC_0921_2016-->