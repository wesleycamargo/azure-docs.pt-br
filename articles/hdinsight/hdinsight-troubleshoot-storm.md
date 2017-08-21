---
title: "Solução de problemas do STORM – Azure HDInsight | Microsoft Docs"
description: Use as perguntas frequentes sobre o Storm para obter respostas a perguntas comuns sobre Storm na plataforma do Azure HDInsight.
keywords: "Azure HDInsight, Storm, perguntas frequentes, guia de solução de problemas, problemas comuns"
services: Azure HDInsight
documentationcenter: na
author: raviperi
manager: 
editor: 
ms.assetid: 74E51183-3EF4-4C67-AA60-6E12FAC999B5
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: raviperi
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: b52462096ce977b94ff9514df650607b05e17030
ms.contentlocale: pt-br
ms.lasthandoff: 07/11/2017

---

# <a name="storm-troubleshooting"></a>Solução de problemas do STORM

Este artigo descreve os principais problemas e suas resoluções para trabalhar com conteúdo do Storm no Apache Ambari.

## <a name="how-do-i-access-storm-ui-on-a-cluster"></a>Como fazer para acessar a interface do usuário do Storm em um cluster

### <a name="issue"></a>Problema:
Há duas maneiras de acessar a interface do usuário do Storm em um navegador:

#### <a name="ambari-ui"></a>Interface do usuário do Ambari
1. Navegar para o Painel do Ambari
1. Selecione Storm na Lista de serviços à esquerda
1. Selecione a opção interface do usuário do Storm no menu suspenso de Links rápidos

#### <a name="direct-link"></a>Link direto
A interface do usuário do Storm está acessível da URL:

https://\<ClusterDnsName\>/stormui

exemplo: https://stormcluster.azurehdinsight.net/stormui

## <a name="how-do-i-transfer-storm-eventhub-spout-checkpoint-information-from-one-topology-to-another"></a>Como fazer para transferir informações do ponto de verificação do spout Storm eventhub de uma topologia para outra

### <a name="issue"></a>Problema:
Ao desenvolver topologias que leem de hubs de evento usando o jar do spout Storm eventhub do HDInsight, como é possível implantar uma topologia com o mesmo nome em um novo cluster, mas manter os dados do ponto de verificação confirmados no zookeeper no cluster antigo?

#### <a name="where-is-checkpoint-data-stored"></a>O local em que os dados de ponto de verificação são armazenados
Dados de ponto de verificação para deslocamentos são armazenados pelo spout do EventHub no Zookeeper em dois caminhos raiz:
- Pontos de verificação spout não transacionais são armazenados em: /eventhubspout
- Dados de ponto de verificação de spout da transação são armazenados em: /transational

#### <a name="how-to-restore"></a>Como restaurar
Os scripts e as bibliotecas para exportar dados do zookeeper e importá-los de volta com um novo nome podem ser encontrados em: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0

A pasta lib tem arquivos Jar que contêm a implementação para a operação de importação/exportação.
A pasta bash tem um script de exemplo sobre como exportar dados do servidor do Zookeeper no cluster antigo e importá-lo de volta para o servidor do zookeeper no novo cluster.

O script [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) precisa ser executado dos nós do Zookeeper para importar/exportar dados.
O script precisa ser atualizado para corrigir a cadeia de caracteres de versão HDP nele.
(O HDInsight está trabalhando para tornar esses scripts genéricos, para que eles possam ser executados de qualquer nó no cluster sem necessidade de modificações pelo usuário final).

O comando de exportação gravará os metadados em um Caminho HDFS (repositório de ADLS ou BLOB) no local especificado.

### <a name="examples"></a>Exemplos

##### <a name="export-offset-metadata"></a>Exportação de metadados de Deslocamento:
1. SSH no cluster zookeeper no cluster antigo do qual o deslocamento do ponto de verificação precisa ser exportado.
1. Execute o comando abaixo (depois de atualizar a cadeia de caracteres de versão hdp) exportar dados de deslocamento do zookeeper para o caminho HDFS /stormmetadata/zkdata.

```apache   
   java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
```

##### <a name="import-offset-metadata"></a>Importar metadados de deslocamento
1. SSH no cluster zookeeper no cluster antigo do qual o deslocamento do ponto de verificação precisa ser exportado.
1. Execute o comando abaixo (depois de atualizar a cadeia de caracteres de versão hdp) para importar dados de deslocamento zookeeper do caminho HDFS /stormmetadata/zkdata para o servidor Zookeeper no cluster de destino).

```apache
   java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
```
   
##### <a name="delete-offset-metadata-so-topologies-can-start-processing-data-from-either-beginning-or-timestamp-of-user-choice"></a>Exclua metadados de Deslocamento para que as topologias possam começar a processar dados do (início ou carimbo de data/hora de preferência do usuário)
1. SSH no cluster zookeeper no cluster antigo do qual o deslocamento do ponto de verificação precisa ser exportado.
1. Execute o comando abaixo (depois de atualizar a cadeia de caracteres de versão hdp) para excluir todos os dados de deslocamento do zookeeper para o cluster atual.

```apache
   java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Como fazer para localizar binários do Storm em um cluster

### <a name="issue"></a>Problema:
 Local conhecido de binários para serviços do Storm no cluster do HDInsight

### <a name="resolution-steps"></a>Etapas de Resolução:

Binários do Storm para a Pilha HDP atual podem ser encontrados em: /usr/hdp/current/storm-client

Esse local é o mesmo para Headnodes, bem como nós de trabalho.
 
Pode haver vários binários específicos da versão HDP localizados em /usr/hdp (exemplo: /usr/hdp/2.5.0.1233/storm)

Porém, o /usr/hdp/current/storm-client está vinculado ao sym para a versão mais recente executada no cluster.

### <a name="further-reading"></a>Leitura Adicional:
 [Conecte-se ao cluster do HDInsight usando SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) [Storm](http://storm.apache.org/)
 
## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Como fazer para determinar a topologia de implantação de um cluster do Storm
 
### <a name="issue"></a>Problema:
 
Identifique todos os componentes instalados com o HDInsight Storm.
 
O cluster do Storm consiste em quatro categorias de nó
1. Gateway
1. Nós de cabeçalho
1. Nós do Zookeeper
1. Nós de trabalho
 
#### <a name="gateway-nodes"></a>Nós de gateway
É um serviço de proxy reverso e gateway que habilita o acesso público ao serviço de gerenciamento do Ambari ativo, lida com a eleição do Líder do Ambari.
 
#### <a name="zookeeper-nodes"></a>Nós do Zookeeper
O HDInsight vem com um quorum do Zookeeper de três nós.
O tamanho do quorum é fixo e não é configurável.
 
Serviços de Storm no cluster são configurados para usar o quorum ZK automaticamente.
 
#### <a name="head-nodes"></a>Nós de cabeçalho
Nós de cabeçalho do Storm executam os seguintes serviços:
1. Nimbus
1. Servidor Ambari
1. Servidor de Métricas do Ambari
1. Coletor de Métricas do Ambari
 
#### <a name="worker-nodes"></a>Nós de trabalho
 Nós de trabalho do Storm executam os seguintes serviços:
1. Supervisor
1. JVMs de trabalho para topologias em execução
1. Agente do Ambari
 
## <a name="how-do-i-locate-storm-eventhub-spout-binaries-for-development"></a>Como fazer para localizar binários Storm-EventHub-Spout para desenvolvimento
 
### <a name="issue"></a>Problema:
Como possa saber mais sobre o uso de jars do spout Storm eventhub com a minha topologia.
 
#### <a name="msdn-articles-on-how-to"></a>Artigos de instruções do MSDN
 
##### <a name="java-based-topology"></a>Topologia baseada em Java
https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-java-event-hub-topology
 
##### <a name="c-based-topology-using-mono-on-hdi-34-linux-storm-clusters"></a>Topologia baseada em C# (usando Mono em clusters HDI 3.4+ Linux Storm)
https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology
 
##### <a name="latest-storm-eventhub-spout-binaries-for-hdi35-linux-storm-clusters"></a>Binários do spout Storm EventHub mais recentes para clusters HDI3.5+ Linux Storm
Examine https://github.com/hdinsight/mvn-repo/blob/master/README.md sobre como usar o spout Storm eventhub mais recente que funciona com clusters do HDI3.5+ Linux Storm.
 
##### <a name="source-code-examples"></a>Exemplos de código-fonte:
https://github.com/Azure-Samples/hdinsight-java-storm-eventhub
 
## <a name="how-do-i-locate-storm-log4j-configuration-files-on-clusters"></a>Como fazer para localizar arquivos de configuração do Storm Log4J nos clusters
 
### <a name="issue"></a>Problema:
 
Identificar arquivos de configuração Log4J para serviços do Storm.
 
#### <a name="on-headnodes"></a>Em HeadNodes:
A configuração de Log4J Nimbus é lida de: /usr/hdp/<HDPVersion>/storm/log4j2/cluster.xml
 
#### <a name="worker-nodes"></a>Nós de trabalho
A configuração de Log4J do Supervisor é lida de: /usr/hdp/<HDPVersion>/storm/log4j2/cluster.xml
 
O arquivo de configuração do Log4J de trabalho é lido de: /usr.hdp/<HDPVersion>/storm/log4j2/worker.xml
 
Exemplo: /usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml /usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml







