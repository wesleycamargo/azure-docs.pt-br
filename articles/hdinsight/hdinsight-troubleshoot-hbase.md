---
title: "Solução de problemas do HBase – Azure HDInsight | Microsoft Docs"
description: "Solução de problemas da causa de falhas na cadeia de regiões."
services: hdinsight
documentationcenter: 
author: nitinver
manager: ashitg
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 7/7/2017
ms.author: nitinver
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 29b1776d6b0c456515738aae3c5fd836c9a0295d
ms.contentlocale: pt-br
ms.lasthandoff: 07/11/2017

---

# <a name="hbase-troubleshooting"></a>Solução de problemas do HBASE

Este artigo descreve os principais problemas e suas resoluções para trabalhar com conteúdo HBASE no Apache Ambari.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Como fazer para executar relatórios de comando hbck com várias regiões não atribuídas

É um problema comum ver “várias regiões terem a atribuição cancelada ou falhas na cadeia de regiões” quando o usuário do HBase executa o comando 'hbase hbck'.

O usuário veria a contagem de regiões ser desequilibrada em todos os servidores de região da interface do usuário do HBase Master. Depois disso, o usuário pode executar o comando 'hbase hbck' e perceberá buracos na cadeia de região.

O usuário deve primeiro corrigir as atribuições, porque as falhas podem ser devido a essas regiões offline. 

Siga as etapas abaixo para exibir as regiões não atribuídas de volta no estado normal:

1. Faça logon em um cluster HDInsight HBase usando SSH.
1. Execute o comando 'hbase zkcli' para conectar-se ao shell do zookeeper.
1. Execute o comando 'rmr /hbase/regions-in-transition' ou 'rmr /hbase-unsecure/regions-in-transition'.
1. Saia do shell 'hbase zkcli' usando o comando 'exit'.
1. Abra a interface do usuário do Ambari e reinicie o serviço HBase Master Ativo do Ambari.
1. Execute o comando 'hbase hbck' novamente (sem mais nenhuma opção).

Verifique a saída do comando na etapa 6 e verifique se todas as regiões estão sendo atribuídas.

---

## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Como fazer para corrigir problemas de tempo limite com comandos hbck atribuições de região

### <a name="probable-cause"></a>Causa provável

A possível causa aqui pode ser várias regiões no estado "em transição" por um longo tempo. Essas regiões podem ser vistas como offline da interface do usuário do HBase Master. Devido ao grande número de regiões tentando fazer a transição, o HBase Master pode atingir tempo limite e não conseguirá colocar essas regiões no estado online novamente.

### <a name="resolution-steps"></a>Etapas de resolução

Abaixo estão as etapas para corrigir o problema de tempo limite de hbck:

1. Faça logon em um cluster HDInsight HBase usando SSH.
1. Execute o comando 'hbase zkcli' para conectar-se ao shell do zookeeper.
1. Execute o comando 'rmr /hbase/regions-in-transition' ou 'rmr /hbase-unsecure/regions-in-transition'.
1. Saia do shell 'hbase zkcli' usando o comando 'exit'.
1. Abra a interface do usuário do Ambari e reinicie o serviço HBase Master Ativo do Ambari.
1. Execute novamente o comando 'hbase hbck -fixAssignments' e ele não deverá mais atingir o tempo limite.

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-an-cluster"></a>Como fazer para forçar a desabilitação do modo de segurança de HDFS em um cluster

### <a name="issue"></a>Problema:

HDFS local está travado no modo de segurança no cluster do HDInsight.   

### <a name="detailed-description"></a>Descrição detalhada:

Falha ao executar o comando HDFS simples da seguinte maneira:

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

O erro encontrado ao tentar executar o comando acima é o seguinte:

```apache
hdiuser@hn0-spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.mkdirs(FSNamesystem.java:4010)
        at org.apache.hadoop.hdfs.server.namenode.NameNodeRpcServer.mkdirs(NameNodeRpcServer.java:1102)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolServerSideTranslatorPB.mkdirs(ClientNamenodeProtocolServerSideTranslatorPB.java:630)
        at org.apache.hadoop.hdfs.protocol.proto.ClientNamenodeProtocolProtos$ClientNamenodeProtocol$2.callBlockingMethod(ClientNamenodeProtocolProtos.java)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Server$ProtoBufRpcInvoker.call(ProtobufRpcEngine.java:640)
        at org.apache.hadoop.ipc.RPC$Server.call(RPC.java:982)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2313)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2309)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:422)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1724)
        at org.apache.hadoop.ipc.Server$Handler.run(Server.java:2307)
        at org.apache.hadoop.ipc.Client.getRpcResponse(Client.java:1552)
        at org.apache.hadoop.ipc.Client.call(Client.java:1496)
        at org.apache.hadoop.ipc.Client.call(Client.java:1396)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Invoker.invoke(ProtobufRpcEngine.java:233)
        at com.sun.proxy.$Proxy10.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolTranslatorPB.mkdirs(ClientNamenodeProtocolTranslatorPB.java:603)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invokeMethod(RetryInvocationHandler.java:278)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:194)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:176)
        at com.sun.proxy.$Proxy11.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.DFSClient.primitiveMkdir(DFSClient.java:3061)
        at org.apache.hadoop.hdfs.DFSClient.mkdirs(DFSClient.java:3031)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1162)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1158)
        at org.apache.hadoop.fs.FileSystemLinkResolver.resolve(FileSystemLinkResolver.java:81)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirsInternal(DistributedFileSystem.java:1158)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirs(DistributedFileSystem.java:1150)
        at org.apache.hadoop.fs.FileSystem.mkdirs(FileSystem.java:1898)
        at org.apache.hadoop.fs.shell.Mkdir.processNonexistentPath(Mkdir.java:76)
        at org.apache.hadoop.fs.shell.Command.processArgument(Command.java:273)
        at org.apache.hadoop.fs.shell.Command.processArguments(Command.java:255)
        at org.apache.hadoop.fs.shell.FsCommand.processRawArguments(FsCommand.java:119)
        at org.apache.hadoop.fs.shell.Command.run(Command.java:165)
        at org.apache.hadoop.fs.FsShell.run(FsShell.java:297)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:90)
        at org.apache.hadoop.fs.FsShell.main(FsShell.java:350)
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

### <a name="probable-cause"></a>Causa provável:

O cluster do HDInsight foi reduzido verticalmente para alguns poucos nós abaixo ou perto do fator de replicação do HDFS.

### <a name="resolution-steps"></a>Etapas de resolução: 

- Relate o status do HDFS no cluster do HDInsight com os seguintes comandos:

```apache
hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
```

```apache
hdiuser@hn0-spark2:~$ hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
Safe mode is ON
Configured Capacity: 3372381241344 (3.07 TB)
Present Capacity: 3138625077248 (2.85 TB)
DFS Remaining: 3102710317056 (2.82 TB)
DFS Used: 35914760192 (33.45 GB)
DFS Used%: 1.14%
Under replicated blocks: 0
Blocks with corrupt replicas: 0
Missing blocks: 0
Missing blocks (with replication factor 1): 0

-------------------------------------------------
Live datanodes (8):

Name: 10.0.0.17:30010 (10.0.0.17)
Hostname: 10.0.0.17
Decommission Status : Normal
Configured Capacity: 421547655168 (392.60 GB)
DFS Used: 5288128512 (4.92 GB)
Non DFS Used: 29087272960 (27.09 GB)
DFS Remaining: 387172253696 (360.58 GB)
DFS Used%: 1.25%
DFS Remaining%: 91.85%
Configured Cache Capacity: 0 (0 B)
Cache Used: 0 (0 B)
Cache Remaining: 0 (0 B)
Cache Used%: 100.00%
Cache Remaining%: 0.00%
Xceivers: 2
Last contact: Wed Apr 05 16:22:00 UTC 2017
...

```
- Você também pode verificar a integridade do HDFS no cluster do HDInsight com os seguintes comandos:

```apache
hdiuser@hn0-spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
```

```apache
Connecting to namenode via http://hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net:30070/fsck?ugi=hdiuser&path=%2F
FSCK started by hdiuser (auth:SIMPLE) from /10.0.0.22 for path / at Wed Apr 05 16:40:28 UTC 2017
....................................................................................................

....................................................................................................
..................Status: HEALTHY
 Total size:    9330539472 B
 Total dirs:    37
 Total files:   2618
 Total symlinks:                0 (Files currently being written: 2)
 Total blocks (validated):      2535 (avg. block size 3680686 B)
 Minimally replicated blocks:   2535 (100.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       0 (0.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     3.0
 Corrupt blocks:                0
 Missing replicas:              0 (0.0 %)
 Number of data-nodes:          8
 Number of racks:               1
FSCK ended at Wed Apr 05 16:40:28 UTC 2017 in 187 milliseconds


The filesystem under path '/' is HEALTHY
```

- Se for determinado que não há blocos ausentes corrompido ou sub-replicados ou se for possível ignorar esses blocos, execute o seguinte comando para colocar o nó de nome fora do modo de segurança:

```apache
hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Como fazer para corrigir problemas de conectividade de JDBC ou sqlline com o Apache Phoenix

### <a name="resolution-steps"></a>Etapas de resolução:

Para conectar-se ao Apache phoenix, você deve fornecer o IP do nó zookeeper ativo. Certifique-se de que o serviço zookeeper ao qual sqlline.py está tentando se conectar esteja em execução.
1. Execute logon SSH no cluster do HDInsight.
1. Tente o comando a seguir:
        
```apache
        "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
```     
    Note: The IP of Active Zooker node can be identified from Ambari UI, by following the links to HBase -> "Quick Links" -> "ZK* (Active)" -> "Zookeeper Info". 

Se o sqlline.py conectar-se ao Apache Phoenix e não exceder o tempo limite, execute o comando a seguir para validar a disponibilidade e a integridade do Apache Phoenix:

```apache
        !tables
        !quit
```      
- Se os comandos acima funcionarem, não haverá nenhum problema. O IP fornecido pelo usuário pode estar incorreto.
   
    No entanto, se o comando for pausado por muito tempo e então lançar o erro mencionado abaixo, continue seguindo o guia de solução de problemas abaixo:

```apache
        Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
```

- Execute o seguinte comandos de um nó principal (hn0) para diagnosticar a condição da tabela phoenix SYSTEM.CATALOG:

```apache
        hbase shell
        
        count 'SYSTEM.CATALOG'
```        
- O comando deve retornar um erro semelhante ao seguinte: 

```apache
        ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
```
- Reinicie o serviço HMaster em todos os nós do zookeeper da interface do usuário do Ambari seguindo as etapas abaixo:

    a. Acesse o link "HBase -> Active HBase mestre" na seção de resumo do HBase. 
    a. Na seção Componentes, reinicie o serviço HBase Master.
    a. Repita as etapas acima para os serviços "Standby HBase Master" restantes. 

Pode levar até cinco minutos para o serviço HBase Master estabilizar e concluir a recuperação. Após alguns minutos de espera, repita os comandos sqlline.py para confirmar que a tabela do catálogo do sistema está ativa e pode ser consultada. 

Quando a tabela 'SYSTEM.CATALOG' voltar ao normal, o problema de conectividade com o Apache Phoenix deverá ser resolvido automaticamente.


## <a name="what-causes-a-master-server-to-fail-to-start"></a>O que faz com o servidor mestre falhar ao iniciar

### <a name="error"></a>Erro: 

Falha de renomeação atômica

### <a name="detailed-description"></a>Descrição detalhada:

Durante o processo de inicialização, o HMaster executa várias etapas de inicialização, incluindo a movimentação de dados da pasta de rascunho (.tmp) para a pasta de dados. Ele também procura na pasta WALs (Logs Write Ahead) para verificar se há algum servidor de região inativo e assim por diante. 

Durante todas essas situações, ele executa um comando básico 'list' nessas pastas. Se, a qualquer momento, ele detectar um arquivo inesperado em qualquer uma dessas pastas, ele gerará uma exceção e, portanto, não iniciará.  

### <a name="probable-cause"></a>Causa provável:

Tente identificar a linha do tempo de criação do arquivo e ver se houve uma falha no processo por volta dessa época nos logs do servidor da região (entre em contato com qualquer membro da equipe do HBase para ajudá-lo a fazer isso). Isso nos ajuda a fornecer mecanismos mais robustos para evitar a ocorrência desse bug e garantir desligamentos de processo normais.

### <a name="resolution-steps"></a>Etapas de resolução:

Em uma situação assim, tente verificar a pilha de chamadas e ver qual pasta pode estar causando o problema (por exemplo, a pasta WALs ou a pasta .tmp). Em seguida, por meio do Cloud Explorer ou de comandos hdfs, tente localizar o arquivo com problema – geralmente um arquivo *-renamePending.json (um arquivo de diário usado para implementar a operação de Renomeação Atômica no driver WASB; devido a bugs nessa implementação, esses arquivos podem ficar para trás em casos de falha no processo etc). Force exclusão disso por meio do Cloud Explorer. 

Além disso, às vezes, também pode haver um arquivo temporário da natureza $$$.$$$ nesse local; isso não pode ser visto por meio do Cloud Explorer, mas somente por meio do comando hdfs ls. Você pode usar o comando "hdfs dfs -rm /<the path>/\$\$\$.\$\$\$" para excluir esse arquivo.  

Depois que isso for feito, o HMaster deve iniciar imediatamente. 

### <a name="error-no-server-address-listed-in"></a>Erro: nenhum endereço de servidor listado no 

Nenhum endereço de servidor listado no hbase: meta para a região xxx

### <a name="detailed-description"></a>Descrição detalhada:

O cliente encontrou um problema no cluster Linux em que a tabela de metadados hbase: não estava online. O bloco hbck em execução relatou que "replicaId 0 da tabela hbase: meta não foi encontrado em nenhuma região". Depois de reiniciar HBase, o sintoma tornou-se que o hmaster não conseguia inicializar. Nos logs do hmaster, foi relatado: "Nenhum endereço de servidor listado no hbase: meta para região hbase: backup <region name>".  

### <a name="resolution-steps"></a>Etapas de resolução:

- Digite o seguinte no shell do HBase (altere os valores reais conforme aplicável).  

```apache
> scan 'hbase:meta'  
```

```apache
> delete 'hbase:meta','hbase:backup <region name>','<column name>'  
```

- Exclua a entrada de hbase: namespace porque o mesmo erro pode ser relatado durante verificação da tabela hbase: namespace.

- Reinicie o HMaster ativo da interface do usuário do Ambari para exibir o HBase em estado de execução.  

- Execute o seguinte comando no shell do HBase para abrir todas as tabelas offline:

```apache 
hbase hbck -ignorePreCheckPermission -fixAssignments 
```

### <a name="further-reading"></a>Leitura adicional:

[Não é possível processar a tabela do HBase](http://stackoverflow.com/questions/4794092/unable-to-access-hbase-table)


### <a name="error"></a>Erro:

O HMaster atinge o tempo limite com uma exceção fatal como java.io.IOException: Timedout 300000ms aguardando a tabela de namespace ser atribuída

### <a name="detailed-description"></a>Descrição detalhada:

Cliente encontrou esse problema quando ele aparentemente tinha muitas tabelas e regiões e não havia feito a liberação ao reiniciar seus serviços HMaster. A reinicialização estava falhando com a mensagem acima. Nenhum outro erro encontrado.  

### <a name="probable-cause"></a>Causa provável:

Esse é um "defeito" conhecido com o HMaster – tarefas de inicialização do cluster geral podem levar muito tempo e o HMaster desliga porque a tabela do namespace ainda não foi atribuída – o que acontece somente nesse cenário, em que há uma grande quantidade de dados não liberados e um tempo limite de cinco minutos não é suficiente
  
### <a name="resolution-steps"></a>Etapas de resolução:

- Acesse a interface do usuário do Ambari, vá para HBase -> Configurações, no hbase-site.xml personalizado e adicione a seguinte configuração: 

```apache
Key: hbase.master.namespace.init.timeout Value: 2400000  
```

- Reinicie os serviços necessários (principalmente HMaster e possivelmente outros serviços do HBase).  



## <a name="what-causes-a-restart-failure-on-a-region-server"></a>O que causa uma falha de reinicialização em um servidor de região

### <a name="probable-cause"></a>Causa provável:

Primeiramente, uma situação assim poderia ser evitada seguindo as práticas recomendadas. É recomendável pausar a atividade com uso intenso de carga de trabalho ao planejar reiniciar Servidores da Região do HBase. Se o aplicativo continuar a conectar-se a servidores da região quando o desligamento estiver em andamento, isso vai desacelerar a operação de reinicialização do servidor da região em vários minutos. Além disso, é aconselhável os usuários liberarem todas as tabelas seguindo [HDInsight HBase: Como aprimorar o tempo de reinicialização do cluster HBase liberando tabelas](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/) como referência.

Se um usuário iniciar a operação de reinicialização no servidor da região HBase da interface do usuário do Ambari. Ele veria imediatamente que os servidores da região ficaram inoperantes e não voltarem a ficar ativos por muito tempo. 

Abaixo está o que acontece nos bastidores: 

1. O agente Ambari enviará uma solicitação de parada para o servidor de região.
1. O agente do Ambari então aguarda 30 segundos para o servidor da região desligar normalmente. 
1. Se o aplicativo do cliente continuar a conectar-se ao servidor da região, ele será não será desligado imediatamente e, portanto, o tempo limite de 30 segundos expirará mais cedo. 
1. Após a expiração de 30 segundos, o agente Ambari enviará um encerramento forçado (kill -9) para o servidor da região. É possível observar isso em um log ambari-agent (no diretório /var do respectivo nó de trabalho) como abaixo:

```apache
         2017-03-21 13:22:09,171 - Execute['/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/current/hbase-regionserver/conf stop regionserver'] {'only_if': 'ambari-sudo.sh  -H -E t
         est -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1', 'on_timeout': '! ( ambari-sudo.sh  -H -E test -
         f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H 
         -E cat /var/run/hbase/hbase-hbase-regionserver.pid`', 'timeout': 30, 'user': 'hbase'}
         2017-03-21 13:22:40,268 - Executing '! ( ambari-sudo.sh  -H -E test -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >
         /dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid`'. Reason: Execution of 'ambari-sudo.sh su hbase -l -s /bin/bash -c 'export  
         PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/var/lib/ambari-agent ; /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/curre
         nt/hbase-regionserver/conf stop regionserver was killed due timeout after 30 seconds
         2017-03-21 13:22:40,285 - File['/var/run/hbase/hbase-hbase-regionserver.pid'] {'action': ['delete']}
         2017-03-21 13:22:40,285 - Deleting File['/var/run/hbase/hbase-hbase-regionserver.pid']
```
Devido a esse desligamento inesperado, a porta associada ao processo não pode ser liberada, embora o processo de servidor da região seja encerrado. Essa situação pode levar à AddressBindException, conforme mostram os logs abaixo ao iniciar o servidor da região. É possível verificar isso em region-server.log no diretório /var/log/hbase nos nós de trabalho em que a inicialização do servidor da região falha. 

```apache

       2017-03-21 13:25:47,061 ERROR [main] regionserver.HRegionServerCommandLine: Region server exiting
       java.lang.RuntimeException: Failed construction of Regionserver: class org.apache.hadoop.hbase.regionserver.HRegionServer
       at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2636)
       at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.start(HRegionServerCommandLine.java:64)
       at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.run(HRegionServerCommandLine.java:87)
       at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
       at org.apache.hadoop.hbase.util.ServerCommandLine.doMain(ServerCommandLine.java:126)
       at org.apache.hadoop.hbase.regionserver.HRegionServer.main(HRegionServer.java:2651)
       
       Caused by: java.lang.reflect.InvocationTargetException
       at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
       at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:57)
       at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
       at java.lang.reflect.Constructor.newInstance(Constructor.java:526)
       at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2634)
       ... 5 more
       
       Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
       at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2497)
       at org.apache.hadoop.hbase.ipc.RpcServer$Listener.<init>(RpcServer.java:580)
       at org.apache.hadoop.hbase.ipc.RpcServer.<init>(RpcServer.java:1982)
       at org.apache.hadoop.hbase.regionserver.RSRpcServices.<init>(RSRpcServices.java:863)
       at org.apache.hadoop.hbase.regionserver.HRegionServer.createRpcServices(HRegionServer.java:632)
       at org.apache.hadoop.hbase.regionserver.HRegionServer.<init>(HRegionServer.java:532)
       ... 10 more
       
       Caused by: java.net.BindException: Address already in use
       at sun.nio.ch.Net.bind0(Native Method)
       at sun.nio.ch.Net.bind(Net.java:463)
       at sun.nio.ch.Net.bind(Net.java:455)
       at sun.nio.ch.ServerSocketChannelImpl.bind(ServerSocketChannelImpl.java:223)
       at sun.nio.ch.ServerSocketAdaptor.bind(ServerSocketAdaptor.java:74)
       at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2495)
       ... 15 more
```

### <a name="resolution-steps"></a>Etapas de Resolução:

Nesses casos, a solução alternativa abaixo pode ser testada: 

- Tente reduzir a carga nos servidores de região do HBase antes de iniciar uma reinicialização. 

- Como alternativa (se etapa acima não ajudar), tente reiniciar manualmente os servidores de região nos nós de trabalho usando os comandos a seguir:

```apache
      sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
      sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
```



