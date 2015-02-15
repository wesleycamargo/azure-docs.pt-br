<properties 
	pageTitle="Disponibilidade dos clusters Hadoop no HDInsight | Azure" 
	description="O HDInsight implanta clusters altamente disponíveis e confiáveis." 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="bradsev" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/10/2014" 
	ms.author="bradsev"/>


#Disponibilidade e confiabilidade dos clusters Hadoop em HDInsight

## Introdução ##
Um segundo headnode foi adicionado aos clusters Hadoop implantados pelo HDInsight para aumentar a disponibilidade e confiabilidade do serviço necessário para gerenciar cargas de trabalho. Implementações padrão de clusters Hadoop normalmente têm um único headnode. Esses clusters são projetados para gerenciar falha de nós de trabalho sem problemas, mas qualquer interrupção dos serviços mestres em execução no headnode fará com que o cluster pare de funcionar. 

![](http://i.imgur.com/jrUmrH4.png)

O HDInsight remove esse ponto único de falha, com a adição de um headnode secundário (Head Node1). [Nós ZooKeeper][zookeeper] (ZKs) foram adicionados e são usados para Eleição de Líder de headnodes e para assegurar que os nós de trabalho e gateways (GWS) saibam quando fazer failover no headnode secundário (Head Node1) quando o headnode ativo (HeadNode0) torna-se inativo.


## Como verificar o status do serviço no headnode ativo ##
Para determinar qual headnode está ativo e verificar o estado dos serviços em execução no nó principal, você deve se conectar ao cluster Hadoop usando o protocolo RDP. A capacidade de acesso remoto ao cluster é desabilitada por padrão no Azure, por isso deve ser habilitada primeiro. Para obter instruções sobre como fazer isso no portal, consulte, [Conectar a clusters HDInsight usando o RDP](../hdinsight-administer-use-management-portal/#rdp)
Depois de acessar remotamente o cluster, clique duas vezes no ícone **Status Disponível do Serviço Hadoop** localizado na área de trabalho para obter status sobre qual headnode os serviços Namenode, Jobtracker, Templeton, Oozieservice, Metastore e Hiveserver2 estão executando ou, para HDI 3.0, o Namenode, Resource Manager, History Server, Templeton, Oozieservice, Metastore e Hiveserver2.

![](http://i.imgur.com/MYTkCHW.png)


## Como acessar arquivos de log no headnode secundário ##

Para acessar registros de trabalhos no headnode secundário caso ele tenha se tornado o headnode ativo, a navegação na interface do usuário do JobTracker ainda funciona como o faz no nó ativo primário. Para acessar o Job Tracker, você deve se conectar ao cluster Hadoop usando o protocolo RDP, conforme descrito na seção anterior. Depois de acessar remotamente o cluster, clique duas vezes no ícone **Nó de Nome do Hadoop** localizado na área de trabalho e clique em **Logs de NameNode** para obter o diretório de logs no headnode secundário.

![](http://i.imgur.com/eL6jzgB.png)


## Como configurar o tamanho do headnode ##
Os headnodes são alocados como grandes VMs por padrão. Esse tamanho é adequado para o gerenciamento da maior parte dos trabalhos de Hadoop executados no cluster. Mas há cenários que podem exigir grandes máquinas virtuais para os headnodes. Um exemplo é quando o cluster tem de gerenciar um grande número de pequenos trabalhos de Oozie. 

VMs extragrandes podem ser configuradas usando cmdlets de PowerShell do Azure ou o SDK do HDInsight.

A criação e o provisionamento de um cluster usando o PowerShell estão documentados em [Administrar o HDInsight usando o PowerShell](../hdinsight-administer-use-powershell/). A configuração de um headnote muito grande exige a adição do parâmetro `-HeadNodeVMSize ExtraLarge` ao cmdlet `New-AzureHDInsightcluster` usado neste código.

    # Create a new HDInsight cluster in Azure PowerShell
	# Configured with an ExtraLarge Headnode VM
    New-AzureHDInsightCluster -Name $clusterName -Location $location -HeadNodeVMSize ExtraLarge -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

Para o SDK, a história é semelhante. A criação e o provisionamento de um cluster usando o SDK estão documentados em [Usando o SD .NET do HDInsight](../hdinsight-provision-clusters/#sdk). A configuração de um headnote extra grande exige a adição do parâmetro `HeadNodeSize = NodeVMSize.ExtraLarge` para o método `ClusterCreateParameters()` usado no código.

    # Create a new HDInsight cluster with the HDInsight SDK
	# Configured with an ExtraLarge Headnode VM
    ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
    {
    Name = clustername,
    Location = location,
    HeadNodeSize = NodeVMSize.ExtraLarge,
    DefaultStorageAccountName = storageaccountname,
    DefaultStorageAccountKey = storageaccountkey,
    DefaultStorageContainer = containername,
    UserName = username,
    Password = password,
    ClusterSizeInNodes = clustersize
    };


**Referências**	

- [ZooKeeper][zookeeper]
- [Conectar aos clusters HDInsight usando o RDP](../hdinsight-administer-use-management-portal/#rdp)
- [Usando o SDK do .NET do HDInsight](../hdinsight-provision-clusters/#sdk) 


[zookeeper]: http://zookeeper.apache.org/ 







<!--HONumber=42-->
