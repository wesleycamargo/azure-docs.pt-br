<properties 
   pageTitle="Use o Apache Phoenix e o SQuirreL no HDInsight | Microsoft Azure" 
   description="Saiba como usar o Apache Phoenix no HDInsight e como instalar e configurar o SQuirreL na sua estação de trabalho para se conectar a um cluster HBase no HDInsight." 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/02/2016"
   ms.author="jgao"/>

# Usar o Apache Phoenix com clusters do HBase baseados em Linux no HDInsight  

Saiba como usar o [Apache Phoenix](http://phoenix.apache.org/) no HDInsight e como usar o SQLLine. Para obter mais informações sobre o Phoenix, consulte [Phoenix em 15 minutos ou menos](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Para conhecer a gramática de Phoenix, consulte [Gramática do Phoenix](http://phoenix.apache.org/language/index.html).

>[AZURE.NOTE] Para obter as informações de versão do Phoenix no HDInsight, consulte [O que há de novo nas versões de cluster Hadoop fornecidas pelo HDInsight?][hdinsight-versions].

##Usar o SQLLine
[SQLLine](http://sqlline.sourceforge.net/) é um utilitário de linha de comando para executar o SQL.

###Pré-requisitos
Antes de poder usar o SQLLine, você deve ter o seguinte:

- **Um cluster HBase no HDInsight**. Para obter informações sobre como provisionar o cluster HBase, consulte [Introdução ao Apache HBase no HDInsight][hdinsight-hbase-get-started].
- **Conectar-se ao cluster HBase por meio do protocolo de área de trabalho remoto**. Para obter instruções, veja [Gerenciar clusters Hadoop no HDInsight usando o Portal Clássico do Azure][hdinsight-manage-portal].


Ao se conectar a um cluster HBase, você precisará se conectar a um dos Zookeepers. Cada cluster HDInsight possui 3 Zookeepers.

**Para descobrir o nome de host do Zookeeper**

1. Abra o Ambari navegando até **https://<ClusterName>.azurehdinsight.net**.
2. Insira o nome de usuário HTTP (cluster) e a senha para fazer logon.
3. Clique em **ZooKeeper** no menu à esquerda. Você deverá ver três **Servidores do ZooKeeper** listados.
4. Clique em um dos **Servidores do ZooKeeper** listados. No painel Resumo, encontre o **Nome do host**. Ele é semelhante a *zk1-jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Usar SQLLine**

1. Conecte-se ao cluster usando o SSH. Para obter instruções, confira o artigo [Use SSH com Hadoop baseado em Linux no HDInsight dos Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md) ou [Usar SSH com Hadoop baseado em Linux no HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md), dependendo do sistema operacional do seu computador cliente.

2. No SSH, execute os seguintes comandos para executar o SQLLine:

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ClusterName>:2181:/hbase-unsecure

2. Execute os seguintes comandos para criar uma tabela do HBase e insira alguns dados:

		CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));
	
		!tables
		
		UPSERT INTO Company VALUES(1, 'Microsoft');
		
		SELECT * FROM Company;
        
        !quit

Para obter mais informações, consulte o [Manual SQLLine](http://sqlline.sourceforge.net/#manual) e [Gramática do Phoenix](http://phoenix.apache.org/language/index.html).


 
##Próximas etapas
Neste artigo, você aprendeu a usar o Apache Phoenix no HDInsight. Para obter mais informações, consulte:

- [Visão geral do HBase do HDInsight][hdinsight-hbase-overview]\: o HBase é um banco de dados NoSQL de software livre Apache baseado no Hadoop que fornece acesso aleatório e uma sólida consistência para grandes quantidades de dados não estruturados e semiestruturados.
- [Provisionar clusters do HBase na Rede Virtual do Azure][hdinsight-hbase-provision-vnet]\: com a integração da rede virtual, os clusters do HBase podem ser implantados na mesma rede virtual que seus aplicativos, de modo que os aplicativos possam se comunicar diretamente com o HBase.
- [Configurar replicação HBase no HDInsight](hdinsight-hbase-geo-replication.md): saiba como configurar a replicação do HBase entre dois datacenters do Azure.
- [Analisar sentimentos do Twitter com o HBase no HDInsight][hbase-twitter-sentiment]\: saiba como fazer a [análise de sentimento](http://en.wikipedia.org/wiki/Sentiment_analysis) em tempo real de grandes volumes de dados usando o HBase em um cluster do Hadoop no HDInsight.

[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png


 

<!---HONumber=AcomDC_0914_2016-->