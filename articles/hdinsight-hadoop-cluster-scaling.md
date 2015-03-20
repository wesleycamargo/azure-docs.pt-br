<properties
   pageTitle="Dimensionamento de cluster no HDInsight | Azure"
   description="Altere o número de nós de dados em um cluster que está sendo executado no HDInsight, sem precisar excluir e recriar o cluster."
   services="hdinsight"
   documentationCenter=""
   authors="bradsev"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="bradsev"/>

#Dimensionamento de cluster no HDInsight

O recurso de dimensionamento do cluster permite que você altere o número de nós de dados usado por um cluster que está sendo executado no HDInsight, sem precisar excluir e recriar o cluster. A operação pode ser executada usando o PowerShell, o SDK do HDInsight ou pelo portal do Azure.

> [WACOM.NOTE] Somente os tipos de cluster Hadoop e Strom têm suporte na versão atual. O suporte para clusters HBase será adicionado em breve. 

## Detalhes do recurso
Esta seção descreve o impacto da alteração do número de nós de dados para cada tipo de cluster com suporte pelo HDInsight:

* Hadoop
* Storm
* HBase 

## Hadoop 

### Adicionando nós de dados
Você pode aumentar continuamente o número de nós de dados em um cluster Hadoop que está sendo executado sem afetar os trabalhos em execução ou pendentes. Novos trabalhos também podem ser enviados enquanto a operação está em andamento. Falhas em uma operação de dimensionamento são normalmente manipuladas para que o cluster sempre seja deixado em um estado funcional.

### Removendo nós de dados
Quando um cluster Hadoop é diminuído reduzindo o número de nós de dados, alguns dos serviços no cluster são reiniciados. Isso faz com que todos os trabalhos em execução e pendentes falhem após a conclusão da operação de dimensionamento. Você pode, no entanto, reenviar os trabalhos quando a operação for concluída.

## Storm
Você pode adicionar ou remover nós de dados continuamente para seu cluster Strom enquanto ele é executado. Mas, após a conclusão bem-sucedida da operação de dimensionamento, você precisará balancear novamente a topologia.

O rebalanceamento pode ser feito de duas maneiras usando:

* Interface da Web Storm
* Ferramenta CLI 

Consulte a [documentação do Apache Strom](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obter mais detalhes.

A interface do usuário da Web Storm está disponível no cluster HDInsight:

![image1](./media/hdinsight-hadoop-cluster-scaling/StormUI.png)

Aqui está um exemplo de como usar o comando CLI para reequilibrar a topologia do Storm:

	## Reconfigure the topology "mytopology" to use 5 worker processes,
	## the spout "blue-spout" to use 3 executors and
	## the bolt "yellow-bolt" to use 10 executors.

	$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

##HBase
A operação de dimensionamento do cluster não tem suporte para clusters de tipo HBase neste momento.

## Pré-requisitos:

* Somente clusters com um HDInsight versão 3.1.3 ou superior são suportados. Se você não tiver certeza da versão do seu cluster, é possível verificar a versão do cluster no Portal do Azure clicando no nome do cluster HDInsight ou executando o comando  `Get-AzureHDInsightCluster -name <clustername>` no PowerShell do Azure.

* O PowerShell do Azure versão 0.8.14 ou superior é necessário para executar a operação do PowerShell. Você pode baixar a versão mais recente do PowerShell na seção de ferramentas de linha de comando no site [Downloads do Azure](http://azure.microsoft.com/downloads/). Você pode verificar a versão do PowerShell do Azure que estiver instalada com o seguinte comando em uma janela do PowerShell: `(get-module Azure).Version`

## Como usar o dimensionamento de cluster

### Portal do Azure
O tamanho de um cluster em execução pode ser alterado na guia **Dimensionamento** do painel do cluster HDInsight do Azure.

![](http://i.imgur.com/u5Mewwx.png)

### PowerShell
Para alterar o tamanho do cluster Hadoop usando o PowerShell, execute o seguinte comando em um computador cliente:

	Set-AzureHDInsightClusterSize -ClusterSizeInNodes <NewSize> -name <clustername>	

> [WACOM.NOTE] O computador cliente deve ter o PowerShell do Azure versão 0.8.14 ou posterior instalado para usar esse comando.

### SDK
Para alterar o tamanho do cluster Hadoop usando o SDK do HDInsight, use um dos dois métodos a seguir: 

	ChangeClusterSize(string dnsName, string location, int newSize) 

ou 

	ChangeClusterSizeAsync(string dnsName, string location, int newSize) 


As versões síncronas e assíncronas desse método retornam o objeto [ClusterDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterdetails_properties.aspx).

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
	     		string location = "<ClusterLocation>"";
				int newSize = <NewClusterSize>;
	
	            // Get the certificate object from certificate store using the friendly name to identify it
	            X509Store store = new X509Store();
	            store.Open(OpenFlags.ReadOnly);
	            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);
	
	            // Create an HDInsightClient object
	            HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
	            var client = HDInsightClient.Connect(creds);
	
	            Console.WriteLine("Rescaling HDInsight cluster ...");
	
	            // Change cluster size
	     		ClusterDetails cluster = client.ChangeClusterSize(clustername, location, newSize);
	            
	            Console.WriteLine("Cluster Rescaled: {0} \n New Cluster Size = {1}", cluster.ConnectionUrl, cluster.ClusterSizeInNodes);
	            Console.WriteLine("Press ENTER to continue.");
	            Console.ReadKey();
	        }
	    }
	}


Consulte o tópico [Provisionar clusters Hadoop no HDInsight usando opções de personalização](http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/) para obter mais informações sobre como usar o SDK do .NET do HDInsight.
<!--HONumber=47-->
