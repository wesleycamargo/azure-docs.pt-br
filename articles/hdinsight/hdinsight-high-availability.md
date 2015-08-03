<properties 
	pageTitle="Disponibilidade dos clusters Hadoop no HDInsight | Microsoft Azure" 
	description="O HDInsight implanta clusters altamente disponíveis e confiáveis com um nó principal adicional." 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="mumian" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="07/10/2015" 
	ms.author="jgao"/>


#Disponibilidade e confiabilidade dos clusters Hadoop em HDInsight


O HDInsight permite que os clientes implantem uma variedade de tipos de cluster, para diferentes cargas de trabalho de análise de dados. Os tipos de cluster oferecidos atualmente são clusters Hadoop para consulta e cargas de trabalho de análise, clusters HBase para cargas de trabalho NoSQL e clusters Storm para cargas de trabalho de processamento de eventos em tempo real. Dentro de um determinado tipo de cluster, há funções diferentes para vários nós. Por exemplo:



- Os clusters Hadoop para HDInsight são implantados com duas funções:
	- Nó principal (2 nós)
	- Nó de dados (pelo menos 1 nó)

- Clusters HBase para HDInsight são implantados com três funções:
	- Servidores de cabeça (2 nós)
	- Servidores de região (pelo menos 1 nó)
	- Nós mestre/Zookeeper (3 nós)

- Clusters Storm para HDInsight são implantados com três funções:
	- Nós Nimbus (2 nós)
	- Servidores de Supervisor (pelo menos 1 nó)
	- Nós Zookeeper (3 nós)
 
Implementações padrão de clusters Hadoop normalmente têm um único nó principal. O HDInsight remove esse único ponto de falha com a adição de um nó principal secundário/servidor principal/nó Nimbus para aumentar a disponibilidade e a confiabilidade do serviço necessário para gerenciar cargas de trabalho. Esses nós principais/servidores principais/nós Nimbus são projetados para gerenciar sem problemas eventuais falha de nós de trabalho, mas qualquer interrupção dos serviços mestres em execução no nó principal fará com que o cluster pare de funcionar.


Nós [ZooKeeper](http://zookeeper.apache.org/) (ZKs) foram adicionados e são usados para eleição de líder de nós principais, e para assegurar que os nós de trabalho e gateways (GWS) saibam quando fazer failover no nó principal secundário (Nó Principal1) quando o nó principal ativo (Node0) tornar-se inativo.

![Diagrama de nós principais altamente confiáveis na implementação do Hadoop HDInsight.](http://i.imgur.com/jrUmrH4.png)




## Verificar o status de serviço do nó principal ativo
Para determinar qual nó principal está ativo e verificar o estado dos serviços em execução no nó principal, você deve se conectar ao cluster Hadoop usando o protocolo RDP. Para obter instruções de RDP, consulte [Gerenciar clusters Hadoop no HDInsight usando o Portal do Azure](hdinsight-administer-use-management-portal.md/#connect-to-hdinsight-clusters-by-using-rdp). Depois de acessar remotamente o cluster, clique duas vezes no ícone **Disponível do Serviço Hadoop** localizado na área de trabalho para obter status sobre qual nó principal os serviços Namenode, Jobtracker, Templeton, Oozieservice, Metastore e Hiveserver2 estão executando; ou então, para o HDI 3.0, os serviços Namenode, Resource Manager, History Server, Templeton, Oozieservice, Metastore e Hiveserver2.

![](./media/hdinsight-high-availability/Hadoop.Service.Availability.Status.png)

Na captura de tela, o nó principal ativo é *headnode0*.

## Acessar arquivos de log no nó principal secundário

Para acessar registros de trabalhos no nó principal secundário caso ele tenha se tornado o nó principal ativo, a navegação na interface do usuário do JobTracker ainda funciona como o faz no nó ativo primário. Para acessar o JobTracker, você deve se conectar ao cluster Hadoop usando o protocolo RDP, conforme descrito na seção anterior. Depois de acessar remotamente o cluster, clique duas vezes no ícone **Status do Nó de Nome do Hadoop** localizado na área de trabalho e clique em **Logs de NameNode** para obter o diretório de logs no nó principal secundário.

![](./media/hdinsight-high-availability/Hadoop.Head.Node.Log.Files.png)


## Configurar o tamanho do nó principal
Os nós principais são alocados como grandes VMs (máquinas virtuais) por padrão. Esse tamanho é adequado para o gerenciamento da maior parte dos trabalhos de Hadoop executados no cluster. Mas há cenários que podem exigir máquinas virtuais extragrandes para os nós principais. Um exemplo é quando o cluster tem de gerenciar um grande número de pequenos trabalhos de Oozie.

VMs extragrandes podem ser configuradas usando cmdlets do PowerShell do Azure ou o SDK do HDInsight.

A criação e o provisionamento de um cluster usando o PowerShell do Azure estão documentados em [Administrar o HDInsight usando o PowerShell](hdinsight-administer-use-powershell.md). A configuração de um nó principal extragrande exige a adição do parâmetro `-HeadNodeVMSize ExtraLarge` para o cmdlet `New-AzureHDInsightcluster` usado neste código.

    # Create a new HDInsight cluster in Azure PowerShell
	# Configured with an ExtraLarge head-node VM
    New-AzureHDInsightCluster -Name $clusterName -Location $location -HeadNodeVMSize ExtraLarge -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

Para o SDK, a história é semelhante. A criação e o provisionamento de um cluster usando o SDK estão documentados em [Usando o SDK .NET do HDInsight](hdinsight-provision-clusters.md#sdk). A configuração de um nó principal extra grande exige a adição do parâmetro `HeadNodeSize = NodeVMSize.ExtraLarge` para o método `ClusterCreateParameters()` usado neste código.

    # Create a new HDInsight cluster with the HDInsight SDK
	# Configured with an ExtraLarge head-node VM
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

- [ZooKeeper](http://zookeeper.apache.org/)
- [Conectar ao clusters HDInsight usando o RDP](hdinsight-administer-use-management-portal.md#rdp)
- [Usando o SDK .NET do HDInsight](hdinsight-provision-clusters.md#sdk) 







 

<!---HONumber=July15_HO4-->