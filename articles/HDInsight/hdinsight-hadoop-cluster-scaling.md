<properties
   pageTitle="Dimensionamento de cluster no HDInsight | Azure"
   description="Altere o número de nós de dados em um cluster que está sendo executado no HDInsight, sem precisar excluir e recriar o cluster."
   services="hdinsight"
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
   ms.date="04/02/2015"
   ms.author="jgao"/>

#Dimensionamento de clusters no HDInsight

O recurso de dimensionamento de clusters permite que você altere o número de nós de dados usado por um cluster em execução no Azure HDInsight sem precisar excluir e recriar o cluster. A operação pode ser executada por meio do PowerShell do Azure, do HDInsight SDK ou do portal do Azure.

## Detalhes do recurso
Esta seção descreve o impacto da alteração do número de nós de dados para cada tipo de cluster com suporte pelo HDInsight:

* O Hadoop
* Storm
* HBase 

## O Hadoop 

### Adicionando nós de dados
Você pode aumentar continuamente o número de nós de dados em um cluster Hadoop que está sendo executado sem afetar os trabalhos em execução ou pendentes. Novos trabalhos também podem ser enviados enquanto a operação está em andamento. Falhas em uma operação de dimensionamento são normalmente manipuladas para que o cluster sempre seja deixado em um estado funcional.

### Removendo nós de dados
Quando um cluster Hadoop é reduzido verticalmente pela diminuição do número de nós de dados, alguns dos serviços no cluster são reiniciados. Isso faz com que todos os trabalhos em execução e pendentes falhem após a conclusão da operação de dimensionamento. Você pode, no entanto, reenviar os trabalhos quando a operação for concluída.

## Storm
Você pode adicionar ou remover nós de dados continuamente para seu cluster Strom enquanto ele é executado. Mas, após a conclusão bem-sucedida da operação de dimensionamento, você precisará redistribuir a topologia.

A redistribuição pode ser feita de duas maneiras:

* Interface da Web Storm
* Ferramenta CLI (interface de linha de comando) 

Consulte a [documentação do Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obter mais detalhes.

A IU da Web do Storm está disponível no cluster HDInsight:

![image1](./media/hdinsight-hadoop-cluster-scaling/StormUI.png)

Aqui está um exemplo de como usar o comando CLI para reequilibrar a topologia do Storm:

	## Reconfigure the topology "mytopology" to use 5 worker processes,
	## the spout "blue-spout" to use 3 executors, and
	## the bolt "yellow-bolt" to use 10 executors

	$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

##HBase
Você pode adicionar ou remover diretamente nós do cluster HBase enquanto ele é executado. Servidores Regionais são equilibrados automaticamente em alguns minutos após o término da operação de dimensionamento. No entanto, você pode equilibrar manualmente os servidores regionais fazendo logon no nó de cabeçalho do cluster e executando os seguintes comandos em uma janela de prompt de comando:

	>pushd %HBASE_HOME%\bin
	>hbase shell
	>balancer

## Pré-requisitos

* Somente clusters HDInsight versão 3.1.3 ou superior são compatíveis. Se não tem certeza da versão de seu cluster, você pode verificar a versão do cluster no portal do Azure, clicando no nome do cluster HDInsight ou executando o comando `Get-AzureHDInsightCluster –name <clustername>` no PowerShell do Azure.

* É necessário o PowerShell do Azure versão 0.8.14 ou superior para executar a operação pelo PowerShell do Azure. Você pode baixar a versão mais recente do PowerShell do Azure na seção **Ferramentas de linha de comando** do site [Downloads do Azure](http://azure.microsoft.com/downloads/). Você pode conferir a versão do PowerShell do Azure que está instalada usando este comando em uma janela do PowerShell do Azure: `(get-module Azure).Version`

## Como usar o dimensionamento de cluster

### Portal do Azure
O tamanho de um cluster em execução pode ser alterado na guia **ESCALA** do painel do cluster no Azure HDInsight.

![](http://i.imgur.com/u5Mewwx.png)

### PowerShell do Azure
Para alterar o tamanho do cluster Hadoop usando o PowerShell do Azure, execute este comando no computador cliente:

	Set-AzureHDInsightClusterSize -ClusterSizeInNodes <NewSize> -name <clustername>	

> [AZURE.NOTE]O computador cliente deve ter o PowerShell do Azure versão 0.8.14 ou posterior instalado para usar esse comando.

### .
Para alterar o tamanho do cluster Hadoop usando o HDInsight SDK, use um dos seguintes métodos:

	ChangeClusterSize(string dnsName, string location, int newSize) 

ou o

	ChangeClusterSizeAsync(string dnsName, string location, int newSize) 


Versões síncronas e assíncronas deste método retornam um objeto [ClusterDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterdetails_properties.aspx).

Aqui está alguns exemplos de código que mostra como usar a versão síncrona desse método:

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.Security.Cryptography.X509Certificates;
	using Microsoft.WindowsAzure.Management.HDInsight;
	using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;

	namespace HDInsightClusterScaling
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            // Friendly name for the certificate your created earlier  
	            string certfriendlyname = "<CertificateFriendlyName>";     
	            string subscriptionid = "<SubscriptionID>";
	            string clustername = "<ClusterDNSName>";
	     		string location = "<ClusterLocation>”";
				int newSize = <NewClusterSize>;
	
	            // Get the certificate object from certificate store by using the friendly name to identify it
	            X509Store store = new X509Store();
	            store.Open(OpenFlags.ReadOnly);
	            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);
	
	            // Create an HDInsightClient object
	            HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
	            var client = HDInsightClient.Connect(creds);
	
	            Console.WriteLine("Rescaling HDInsight cluster ...");
	
	            // Change the cluster size
	     		ClusterDetails cluster = client.ChangeClusterSize(clustername, location, newSize);
	            
	            Console.WriteLine("Cluster Rescaled: {0} \n New Cluster Size = {1}", cluster.ConnectionUrl, cluster.ClusterSizeInNodes);
	            Console.WriteLine("Press ENTER to continue.");
	            Console.ReadKey();
	        }
	    }
	}


Consulte o tópico [Provisionar clusters Hadoop no HDInsight usando opções personalizadas](hdinsight-provision-clusters.md) para obter mais informações sobre como usar o HDInsight .NET SDK.

<!--HONumber=54--> 