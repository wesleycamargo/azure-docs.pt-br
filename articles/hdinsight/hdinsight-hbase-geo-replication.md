<properties 
   pageTitle="Configurar a replicação HBase entre dois datacenters | Microsoft Azure" 
   description="Saiba como configurar a replicação HBase entre dois data centers e conheça casos de uso de replicação de cluster." 
   services="hdinsight,virtual-network" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/02/2015"
   ms.author="jgao"/>

# Configurar a replicação geográfica do HBase no HDInsight

> [AZURE.SELECTOR]
- [Configure VPN connectivity](../hdinsight-hbase-geo-replication-configure-VNETs.md)
- [Configure DNS](hdinsight-hbase-geo-replication-configure-DNS.md)
- [Configure HBase replication](hdinsight-hbase-geo-replication.md) 
 
Saiba como configurar a replicação do HBase em dois datacenters. Alguns casos de uso para a replicação em cluster incluem:

- Backup e recuperação de desastres
- Agregação de dados
- Distribuição de dados geográficos
- Inclusão de dados online combinados com análises de dados offline

A replicação de cluster usa uma metodologia de envio de origem. Um cluster HBase pode ser uma fonte, um destino ou pode atender a ambas as funções de uma vez. A replicação é assíncrona e o objetivo da replicação é a consistência eventual. Quando a origem recebe uma edição para uma família de coluna com replicação habilitada, essa edição é propagada para todos os clusters de destino. Quando os dados são replicados de um cluster para outro, o cluster de origem e todos os clusters que já consumiram os dados são rastreados para evitar loops de replicação. Para obter mais informações, neste tutorial, você configurará uma replicação de origem/destino. Para obter outras topologias de cluster, consulte o [Guia de referência do Apache HBase](http://hbase.apache.org/book.html#_cluster_replication).

Esta é a terceira parte da série:

- [Configurar uma conectividade VPN entre duas redes virtuais][hdinsight-hbase-replication-vnet]
- [Configurar o DNS para as redes virtuais][hdinsight-hbase-replication-dns]
- Configure a replicação geográfica de HBase (este tutorial)

O diagrama a seguir ilustra as duas redes virtuais e a conectividade de rede que você criou em [Configurar uma conectividade VPN entre duas redes virtuais][hdinsight-hbase-geo-replication-vnet] e [Configurar DNS para as redes virtuais][hdinsight-hbase-replication-dns]\:

![Diagrama de rede virtual de replicação de HBase do HDInsight][img-vnet-diagram]

## <a id="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Uma estação de trabalho com o PowerShell do Azure**. Consulte [Instalar e usar o PowerShell do Azure](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). Para executar scripts do PowerShell, você deve executar o PowerShell do Azure como administrador e configurar a política de execução como *RemoteSigned*. Consulte Usando o cmdlet Set-ExecutionPolicy.

- **Duas redes virtuais do Azure com conectividade VPN com o DNS configurada**. Para obter instruções, consulte [Configurar uma conexão VPN entre duas redes virtuais do Azure][hdinsight-hbase-replication-vnet] e [Configurar DNS entre duas redes virtuais do Azure][hdinsight-hbase-replication-dns].


	Antes de executar scripts do PowerShell, verifique se você está conectado à sua assinatura do Azure usando o seguinte cmdlet:

		Add-AzureAccount

	Se você tiver várias assinaturas do Azure, use o seguinte cmdlet para definir a assinatura atual:

		Select-AzureSubscription <AzureSubscriptionName>



## Provisionar os clusters do HBase em HDInsight

Em [Configurar uma conexão VPN entre duas redes virtuais do Azure][hdinsight-hbase-replication-vnet], você criou uma rede virtual em um datacenter na Europa e uma rede virtual em um datacenter nos EUA. As duas redes virtuais são conectadas via VPN. Nesta sessão, você provisionará um cluster HBase em cada uma das redes virtuais. Posteriormente neste tutorial, você criará um dos clusters HBase para replicar o cluster HBase.

O Portal Clássico do Azure não dá suporte ao provisionamento de clusters HDInsight com opções de configuração personalizadas. Por exemplo, defina *hbase.replication* para *true*. Se definir o valor no arquivo de configuração depois que um cluster for configurado, você perderá a configuração após o cluster ter tido a sua imagem recriada. Para obter mais informações, consulte [Clusters de provisão do Hadoop no HDInsight][hdinsight-provision]. Uma das opções para provisionar o cluster HDInsight com opções personalizadas é usando o PowerShell do Azure.


**Para provisionar um HBase Cluster em Contoso-VNet-EU**

1. Na sua estação de trabalho, abra o ISE do Windows PowerShell.
2. Defina as variáveis no início do script e, em seguida, execute o script.

		# create hbase cluster with replication enabled
		
		$azureSubscriptionName = "[AzureSubscriptionName]"
		
		$hbaseClusterName = "Contoso-HBase-EU" # This is the HBase cluster name to be used.
		$hbaseClusterSize = 1   # You must provision a cluster that contains at least 3 nodes for high availability of the HBase services.
		$hadoopUserLogin = "[HadoopUserName]"
		$hadoopUserpw = "[HadoopUserPassword]"
		
		$vNetName = "Contoso-VNet-EU"  # This name must match your Europe virtual network name.
		$subNetName = 'Subnet-1' # This name must match your Europe virtual network subnet name.  The default name is "Subnet-1".
		
		$storageAccountName = 'ContosoStoreEU'  # The script will create this storage account for you.  The storage account name doesn't support hyphens. 
		$storageAccountName = $storageAccountName.ToLower() #Storage account name must be in lower case.
		$blobContainerName = $hbaseClusterName.ToLower()  #Use the cluster name as the default container name.
		
		#connect to your Azure subscription
		Add-AzureAccount 
		Select-AzureSubscription $azureSubscriptionName
		
		# Create a storage account used by the HBase cluster
		$location = Get-AzureVNetSite -VNetName $vNetName | %{$_.Location} # use the virtual network location
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location
		
		# Create a blob container used by the HBase cluster
		$storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{$_.Primary}
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		New-AzureStorageContainer -Name $blobContainerName -Context $storageContext
		
		# Create provision configuration object
		$hbaseConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHBaseConfiguration'
		    
		$hbaseConfigValues.Configuration = @{ "hbase.replication"="true" } #this modifies the hbase-site.xml file
		
		# retrieve vnet id based on vnetname
		$vNetID = Get-AzureVNetSite -VNetName $vNetName | %{$_.id}
		
		$config = New-AzureHDInsightClusterConfig `
		                -ClusterSizeInNodes $hbaseClusterSize `
		                -ClusterType HBase `
		                -VirtualNetworkId $vNetID `
		                -SubnetName $subNetName `
		            | Set-AzureHDInsightDefaultStorage `
		                -StorageAccountName $storageAccountName `
		                -StorageAccountKey $storageAccountKey `
		                -StorageContainerName $blobContainerName `
		            | Add-AzureHDInsightConfigValues `
		                -HBase $hbaseConfigValues
		
		# provision HDInsight cluster
		$hadoopUserPassword = ConvertTo-SecureString -String $hadoopUserpw -AsPlainText -Force     
		$credential = New-Object System.Management.Automation.PSCredential($hadoopUserLogin,$hadoopUserPassword)
		
		$config | New-AzureHDInsightCluster -Name $hbaseClusterName -Location $location -Credential $credential



**Para provisionar um HBase Cluster em Contoso-VNet-US**

- Use o mesmo script com os seguintes valores:

		$hbaseClusterName = "Contoso-HBase-US" # This is the HBase cluster name to be used.
		$vNetName = "Contoso-VNet-US"  # This name must match your Europe virtual network name.
		$storageAccountName = 'ContosoStoreUS'	

	Como já se conectou à sua conta do Azure, você não precisa mais executar os comlets a seguir:

		Add-AzureAccount 
		Select-AzureSubscription $azureSubscriptionName




## Configure o encaminhador condicional DNS

Em [Configurar DNS para as redes virtuais][hdinsight-hbase-replication-dns], você configurou os servidores DNS para as duas redes. Os clusters HBase têm sufixos de domínio diferentes. Portanto, você precisa configurar os encaminhadores condicionais DNS adicionais.

Para configurar o encaminhador condicional, você precisa saber os sufixos de domínio dos dois clusters HBase.

**Para localizar os sufixos de domínio dos dois clusters HBase**

1. RDP no **Contoso-HBase-EU**. Para obter instruções, veja [Gerenciar clusters Hadoop no HDInsight usando o Portal Clássico do Azure][hdinsight-manage-portal]. É realmente o headnode0 do cluster.
2. Abra um console do Windows PowerShell ou um prompt de comando.
3. Execute **ipconfig** e anote o **sufixo DNS específico da conexão**.
4. Não feche a sessão RDP. Você precisará dela posteriormente para testar a resolução de nome de domínio.
5. Repita as mesmas etapas para descobrir o **sufixo DNS específico da conexão** do **Contoso-HBase-US**.


**Para configurar os encaminhadores DNS**
 
1.	RDP no **Contoso-DNS-EU**. 
2.	Clique na chave do Windows na parte inferior esquerda.
2.	Clique em **Ferramentas administrativas**.
3.	Clique em **DNS**.
4.	No painel esquerdo, expanda **DSN**, **Contoso-DNS-EU**.
5.	Clique com o botão direito do mouse em **Encaminhadores condicionais** e, em seguida, clique em **Novo encaminhador condicional**. 
5.	Insira as seguintes informações:
	- **Domínio DNS**: insira o sufixo DNS do Contoso-HBase-US. Por exemplo: Contoso-HBase-US.f5.internal.cloudapp.net.
	- **Endereços IP dos servidores mestres**: insira 10.2.0.4, que é o endereço IP do Contoso-DNS-US. Verifique se o IP. O servidor DNS pode ter um endereço IP diferente.
6.	Pressione **ENTER** e, em seguida, clique em **OK**. Agora você poderá resolver o endereço IP do Contoso-DNS-US por meio do Contoso-DNS-EU.
7.	Repita as etapas para adicionar um encaminhador condicional do DNS ao serviço DNS na máquina virtual Contoso-DNS-US com os seguintes valores:
	- **Domínio DNS**: insira o sufixo DNS do Contoso-HBase-EU. 
	- **Endereços IP dos servidores mestres**: insira 10.2.0.4, que é o endereço IP do Contoso-DNS-EU.

**Para testar a resolução de nome de domínio**

1. Alterne para a janela RDP do Contoso-HBase-EU.
2. Abra um prompt de comando.
3. Execute o comando ping:

		ping headnode0.[DNS suffix of Contoso-HBase-US]

	O Protocolo ICM está ativado nos nós de trabalho dos clusters HBase

4. Não feche a sessão RDP. Você ainda precisará dele posteriormente no tutorial.
5. Repita as mesmas etapas para executar o ping headnode0 do Contoso-HBase-EU por meio do Contoso-HBase-US.

>[AZURE.IMPORTANT]O DNS deve funcionar antes de você passar para as próximas etapas.

## Habilitar a replicação entre as tabelas do HBase

Agora, é possível criar uma tabela do HBase de exemplo, habilitar a replicação e, em seguida, testá-lo com alguns dados. A tabela de exemplo usará duas famílias de coluna: Pessoal e do Escritório.

Neste tutorial, você fará o cluster HBase da Europa como o cluster de origem e nos EUA. O cluster HBase como o cluster de destino.

Crie tabelas de HBase com os mesmos nomes e famílias de coluna nos clusters de origem e de destino, para que o cluster de destino saiba onde armazenar os dados que serão exibidos. Para obter mais informações sobre como usar o shell do HBase, consulte [Introdução ao Apache HBase em HDInsight][hdinsight-hbase-get-started].

**Para criar uma tabela do HBase no Contoso-HBase-EU**

1. Alterne para a janela RDP do **Contoso-HBase-EU**.
2. Na área de trabalho, clique em **Linha de Comando do Hadoop**.
2. Altere a pasta para o diretório inicial HBase:

		cd %HBASE_HOME%\bin
3. Abra o shell HBase:

		hbase shell
4. Crie uma tabela do HBase:

		create 'Contacts', 'Personal', 'Office'
5. Não feche a sessão RDP nem a janela da linha de comando do Hadoop. Você ainda precisará deles posteriormente no tutorial.
	
**Para criar uma tabela do HBase no Contoso-HBase-US**

- Repita as mesmas etapas para criar a mesma tabela no Contoso-HBase-US.


**Para adicionar o Contoso-HBase-US como um parceiro de replicação**

1. Alterne para a janela RDP do **Contso HBase\_EU**.
2. Na janela do shell do HBase, adicione o cluster de destino (Contoso-HBase-US) como um par, por exemplo:

		add_peer '1', 'zookeeper0.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper1.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper2.contoso-hbase-us.d4.internal.cloudapp.net:2181:/hbase'

	No exemplo, o sufixo de domínio é *contoso-hbase-us.d4.internal.cloudapp.net*. Você precisa atualizá-lo para coincidir com o sufixo de domínio do cluster no HBase dos EUA. Certifique-se de que não haja nenhum espaço entre os nomes de host.

**Para configurar cada família de coluna a ser replicada no cluster de origem**

1. Na janela do shell de HBase da sessão RDP do **Contso-HBase-EU**, configure cada família de coluna a ser replicada:

		disable 'Contacts'
		alter 'Contacts', {NAME => 'Personal', REPLICATION_SCOPE => '1'}
		alter 'Contacts', {NAME => 'Office', REPLICATION_SCOPE => '1'}
		enable 'Contacts'

**Para carregar dados em massa na tabela do HBase**

Um arquivo de exemplo de dados foi carregado para um contêiner público de BLOBs do Azure com a seguinte URL:

		wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

O conteúdo do arquivo:

		8396	Calvin Raji	230-555-0191	5415 San Gabriel Dr.
		16600	Karen Wu	646-555-0113	9265 La Paz
		4324	Karl Xie	508-555-0163	4912 La Vuelta
		16891	Jonathan Jackson	674-555-0110	40 Ellis St.
		3273	Miguel Miller	397-555-0155	6696 Anchor Drive
		3588	Osarumwense Agbonile	592-555-0152	1873 Lion Circle
		10272	Julia Lee	870-555-0110	3148 Rose Street
		4868	Jose Hayes	599-555-0171	793 Crawford Street
		4761	Caleb Alexander	670-555-0141	4775 Kentucky Dr.
		16443	Terry Chander	998-555-0171	771 Northridge Drive

Você pode carregar o mesmo arquivo de dados em seu cluster HBase e importar os dados a partir daí.

1. Alterne para a janela RDP do **Contoso-HBase-EU**.
2. Na área de trabalho, clique em **Linha de Comando do Hadoop**.
3. Altere a pasta para o diretório inicial HBase:

		cd %HBASE_HOME%\bin

4. carregar os dados:

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:HomePhone, Office:Address" -Dimporttsv.bulk.output=/tmpOutput Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput Contacts


## Verifique se a replicação de dados está sendo executada

Você pode verificar que a replicação está ocorrendo examinando as tabelas de dois clusters com os seguintes comandos de shell do HBase:

		Scan 'Contacts'


## Próximas etapas

Neste tutorial, você aprendeu a configurar a replicação do HBase entre dois datacenters. Para saber mais sobre HDInsight e HBase, consulte:

- [Página do serviço HDInsight](http://azure.microsoft.com/services/hdinsight/)
- [Documentação do HDInsight](http://azure.microsoft.com/documentation/services/hdinsight/)
- [Introdução ao Apache HBase no HDInsight][hdinsight-hbase-get-started]
- [Visão geral de HBase no HDInsight][hdinsight-hbase-overview]
- [Provisionar os clusters do HBase na rede Virtual do Azure][hdinsight-hbase-provision-vnet]
- [Analisar o sentimento do Twitter em tempo real com o HBase][hdinsight-hbase-twitter-sentiment]
- [Analisando dados de sensor com o Storm e o HBase em HDInsight (Hadoop)][hdinsight-sensor-data]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-VNets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: ../install-configure-powershell.md
[hdinsight-hbase-get-started]: ../hdinsight-hbase-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-hbase-replication-vnet]: hdinsight-hbase-geo-replication-configure-VNets.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-get-started]: ../hdinsight-hbase-get-started.md

<!---HONumber=AcomDC_1203_2015-->