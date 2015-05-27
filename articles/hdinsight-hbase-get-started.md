<properties
	pageTitle="Configurar e consultar tabelas HBase usando o Hive no HDInsight | Azure"
	description="Comece a usar o HBase com Hadoop no HDInsight. Aprenda a criar tabelas HBase e consultá-los usando o Hive."
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/09/2015"
	ms.author="jgao"/>



# Introdução ao Apache HBase no HDInsight

Aprenda como criar e consultar tabelas HBase usando o Hive no HDInsight.

O HBase é um banco de dados NoSQL de baixa latência que permite o processamento transacional online de Big data. Ele é oferecido como um cluster gerenciado integrado ao ambiente do Azure. Os clusters são configurados para armazenar dados diretamente no armazenamento de blobs do Azure, que proporciona baixa latência e maior flexibilidade para escolhas relacionadas a desempenho e custos. Isso permite que os clientes criem sites interativos que trabalham com grandes conjuntos de dados, de modo a criar serviços que armazenam dados de sensor e telemetria de milhões de pontos de extremidade e analisar esses dados por meio de trabalhos do Hadoop. Para obter mais informações sobre o HBase e os cenários que ele pode ser usado, consulte a [Visão geral do HBase do HDInsight][hdinsight-hbase-overview].

> [AZURE.NOTE]O HBase (versão 0.98.0) está disponível para uso somente com clusters HDInsight 3.1 no HDInsight (com base no Apache Hadoop e YARN 2.4.0). Para obter informações sobre a versão, consulte [O que há de novo nas versões de cluster do Hadoop fornecidas pelo HDInsight?][hdinsight-versions]

## Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Uma assinatura do Azure**: para obter mais informações sobre como obter uma assinatura, consulte [Opções de Compra][azure-purchase-options], [Ofertas para Membros][azure-member-offers] ou [Avaliação Gratuita][azure-free-trial].
- **Uma conta de armazenamento do Azure**: para obter instruções, consulte [Como criar uma conta de armazenamento][azure-create-storageaccount].
- **Uma estação de trabalho** com o Visual Studio 2013 instalado: para obter instruções, consulte [Instalando o Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx).

## Provisionar um cluster HBase

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Para provisionar um cluster HBase usando o Portal do Azure**


1. Entre no [Portal do Azure][azure-management-portal].
2. Clique em **NOVO** na parte inferior esquerda e clique em **SERVIÇOS DE DADOS**, **HDINSIGHT** e **HBASE**.

	Você também pode usar a opção de CRIAÇÃO PERSONALIZADA.
3. Forneça o **NOME DO CLUSTER**, o **TAMANHO DO CLUSTER**, a SENHA DO USUÁRIO DO CLUSTER e uma **CONTA DE ARMAZENAMENTO**.

	![Escolher um tipo de cluster HBase e inserir as credenciais de logon do cluster.][img-hdinsight-hbase-cluster-quick-create]

	O NOME DE USUÁRIO HTTP padrão é admin. Você pode personalizar o nome usando a opção CRIAÇÃO PERSONALIZADA.

	> [AZURE.WARNING]Para alta disponibilidade dos serviços HBase, você deverá provisionar um cluster que contenha pelo menos **três** nós. Isso garante que, se um nó falhar, as regiões de dados do HBase estão disponíveis em outros nós.

4. Clique no ícone de marca de verificação na parte inferior direita para criar o cluster HBase.

>[AZURE.NOTE]Depois que um cluster HBase for excluído, você pode criar outro cluster HBase usando o mesmo blob padrão. O novo cluster selecionará as tabelas HBase criadas por você no cluster original.

## Criar uma tabela de amostra HBase a partir do shell HBase
Esta seção descreve como usar o shell do HBase para criar tabelas HBase, adicionar linhas e listar linhas. Para acessar o shell do HBase, primeiro habilite o protocolo RDP e, em seguida, fazer uma conexão por RDP ao cluster HBase. Para obter instruções, consulte [Gerenciar clusters Hadoop no HDInsight usando o Portal do Azure][hdinsight-manage-portal].


**Para usar o shell HBase**

1. Em sua sessão do RDP, clique no atalho **Linha de Comando do Hadoop** localizado na área de trabalho.
2. Altere a pasta para o diretório inicial HBase:

		cd %HBASE_HOME%\bin
3. Abra o shell HBase:

		hbase shell

4. Crie um HBase com uma família de coluna e insira uma linha:

		create 'sampletable', 'cf1'
		put 'sampletable', 'row1', 'cf1:col1', 'value1'
		scan 'sampletable'

	Para obter mais informações sobre o esquema da tabela HBase, consulte [Introdução ao Design de esquema HBase][hbase-schema]. Para obter mais comandos HBase, consulte [Guia de referência do Apache HBase][hbase-quick-start].
5. Liste as tabelas do HBase:

		list

**Verificar o status do cluster na WebUI do HBase**

O HBase também é fornecido com uma WebUI que você pode usar para ajudar a monitorar o cluster. Por exemplo, você pode solicitar estatísticas ou informações sobre regiões. No cluster HBase, você pode encontrar a WebUI sob o endereço do nó do zookeeper:


	http://zookeepernode:60010/master-status

Em um cluster de alta disponibilidade, você encontrará um link para o nó mestre HBase ativo atual hospedando a WebUI.

**Carregamento em massa de uma tabela de exemplo**

1. No shell do HBase, crie uma tabela do HBase com duas famílias de coluna:

		create 'Contacts', 'Personal', 'Office'


3. Criar uma lista de contatos que contém os dados a seguir e carregá-los em uma pasta chamada /tmp/contacts.txt, no armazenamento de Blob do Azure. Para obter instruções, consulte [Carregar dados para trabalhos de Hadoop no HDInsight][hdinsight-upload-data].

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

2. Em uma linha de comando do Hadoop, altere a pasta para o diretório inicial do HBase:

		cd %HBASE_HOME%\bin

3. Execute o ImportTsv. O ImportTsv é uma ferramenta que vai carregar dados em formato TSV no HBase. Ele tem dois usos distintos: carregamento de dados do formato TSV no sistema de arquivos distribuído do Hadoop (HDFS) no HBase e preparação dos arquivos a serem carregados. Para obter mais informações, consulte [Guia de referência do Apache HBase][hbase-reference].

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:HomePhone, Office:Address" -Dimporttsv.bulk.output=/tmpOutput Contacts /tmp/contacts.txt

4. Carregue a saída do comando anterior no HBase:

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput Contacts

## Usar o Hive para consultar uma tabela HBase

Agora que você provisionou um cluster HBase e criou uma tabela do HBase, poderá consultar os dados na tabela usando o Hive. Esta seção cria uma tabela Hive que faz o mapeamento para a tabela do HBase e usa-a para consultar os dados em sua tabela do HBase.

**Para abrir o painel do cluster**

1. Entre no [Portal do Azure][azure-management-portal].
2. Clique em **HDINSIGHT** no painel à esquerda. Você verá uma lista dos clusters criados, incluindo aquele que criou na última seção.
3. Clique no nome do cluster em que deseja executar a tarefa do Hive.
4. Clique em **CONSOLE DE CONSULTA** na parte inferior da página para abrir o painel do cluster. Ele abre uma página da Web em uma guia diferente do navegador.
5. Insira o nome de usuário e senha da conta do usuário do Hadoop. O nome de usuário padrão é **admin**, a senha é a que você digitou durante o processo de provisionamento. Uma nova guia do navegador é aberta.
6. Clique em **Editor Hive** na parte superior da página. O Editor Hive é semelhante a:

	![Painel do cluster HDInsight.][img-hdinsight-hbase-hive-editor]





























**Para executar consultas de Hive**

1. Insira o script HiveQL a seguir no Editor de Hive e clique em **ENVIAR** para criar uma tabela Hive que faz mapeamento para a tabela do HBase. Assegure-se de ter criado a tabela de amostra referenciada anteriormente neste tutorial, usando o Shell HBase antes de executar esta instrução.

		CREATE EXTERNAL TABLE hbasecontactstable(rowkey STRING, name STRING, homephone STRING, office STRING)
		STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
		WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:HomePhone,Office:Address')
		TBLPROPERTIES ('hbase.table.name' = 'Contacts');

	Aguarde até o **Status** ser atualizado para **Concluído**.

2. Insira o script HiveQL a seguir no Editor de Hive e, em seguida, clique no botão **ENVIAR**. A consulta de Hive consulta os dados na tabela do HBase:

     	SELECT count(*) FROM hbasecontactstable;

4. Para recuperar os resultados da consulta do Hive, clique no link **Exibir Detalhes** na janela **Sessão da Tarefa** quando a execução da tarefa for concluída. Haverá apenas um arquivo de saída do trabalho, porque você coloca um registro na tabela HBase.




**Para pesquisar o arquivo de saída**

1. No Console de Consulta, clique em **Navegador de Arquivos**.
2. Clique na Conta de Armazenamento do Azure usada como o sistema de arquivos padrão para o cluster HBase.
3. Clique no nome do cluster HBase. O contêiner padrão de conta de armazenamento do Azure usa o nome do cluster.
4. Clique em **Usuário** e, em seguida, clique em **Admin**. (Esse é o nome de usuário do Hadoop.)
6. Clique no nome do trabalho com o horário da **Última modificação** correspondente ao horário de execução da consulta SELECIONAR do Hive.
4. Clique em **stdout**. Salve o arquivo e abra-o com o bloco de notas. Haverá um arquivo de saída.

	![Navegador de arquivos do Editor Hive no HBase em HDInsight][img-hdinsight-hbase-file-browser]

## Usar a biblioteca do cliente da API REST do HBase para .NET C# para criar uma tabela HBase e recuperar dados por meio dessa tabela

Você deve baixar a biblioteca de cliente API REST do HBase para .NET do GitHub e compilar o projeto para que você possa usar o SDK do .NET HBase. O procedimento a seguir inclui as instruções para essa etapa.

1. Crie um novo aplicativo C# do Visual Studio de Área de Trabalho do Windows.
2. Abra o console do Gerenciador de Pacotes NuGet clicando no menu **FERRAMENTAS**, **Gerenciador de Pacotes NuGet**, **Console do Gerenciador de Pacotes**.
3. Execute o comando NuGet a seguir no console:

		Install-Package Microsoft.HBase.Client

5. Adicione as seguintes instruções **using** à parte superior do arquivo:

		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. Substitua a função **Main** pelo seguinte:

        static void Main(string[] args)
        {
            string clusterURL = "https://<yourHBaseClusterName>.azurehdinsight.net";
            string hadoopUsername= "<yourHadoopUsername>";
            string hadoopUserPassword = "<yourHadoopUserPassword>";

            string hbaseTableName = "sampleHbaseTable";

            // Create a new instance of an HBase client.
            ClusterCredentials creds = new ClusterCredentials(new Uri(clusterURL), hadoopUsername, hadoopUserPassword);
            HBaseClient hbaseClient = new HBaseClient(creds);

            // Retrieve the cluster version
            var version = hbaseClient.GetVersion();
            Console.WriteLine("The HBase cluster version is " + version);

            // Create a new HBase table.
            TableSchema testTableSchema = new TableSchema();
            testTableSchema.name = hbaseTableName;
            testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
            testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
            hbaseClient.CreateTable(testTableSchema);

            // Insert data into the HBase table.
            string testKey = "content";
            string testValue = "the force is strong in this column";
            CellSet cellSet = new CellSet();
            CellSet.Row cellSetRow = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
            cellSet.rows.Add(cellSetRow);

            Cell value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
            cellSetRow.values.Add(value);
            hbaseClient.StoreCells(hbaseTableName, cellSet);

            // Retrieve a cell by its key.
            cellSet = hbaseClient.GetCells(hbaseTableName, testKey);
            Console.WriteLine("The data with the key '" + testKey + "' is: " + Encoding.UTF8.GetString(cellSet.rows[0].values[0].data));
            // with the previous insert, it should yield: "the force is strong in this column"

		    //Scan over rows in a table. Assume the table has integer keys and you want data between keys 25 and 35.
		    Scanner scanSettings = new Scanner()
		    {
    		    batch = 10,
    		    startRow = BitConverter.GetBytes(25),
    		    endRow = BitConverter.GetBytes(35)
		    };

		    ScannerInformation scannerInfo = hbaseClient.CreateScanner(hbaseTableName, scanSettings);
		    CellSet next = null;
            Console.WriteLine("Scan results");

            while ((next = hbaseClient.ScannerGetNext(scannerInfo)) != null)
		    {
    		    foreach (CellSet.Row row in next.rows)
    		    {
                    Console.WriteLine(row.key + " : " + Encoding.UTF8.GetString(row.values[0].data));
    		    }
		    }

            Console.WriteLine("Press ENTER to continue ...");
            Console.ReadLine();
        }

7. Defina as primeiras três variáveis na função **Main**.
8. Pressione **F5** para executar o aplicativo.



## O que vem a seguir?
Neste tutorial, você aprendeu a provisionar um cluster HBase, criar tabelas e exibir os dados nessas tabelas por meio do shell HBase. Você também aprendeu a usar o Hive para consultar os dados em tabelas HBase e como usar as APIs REST C# do HBase para criar uma tabela HBase e recuperar dados da tabela.

Para obter mais informações, consulte:

- [Visão geral do HBase do HDInsight][hdinsight-hbase-overview]\: o HBase é um banco de dados NoSQL de software livre Apache baseado no Hadoop que fornece acesso aleatório e uma sólida consistência para grandes quantidades de dados não estruturados e semiestruturados.
- [Provisionar clusters do HBase na Rede Virtual do Azure][hdinsight-hbase-provision-vnet]\: com a integração da rede virtual, os clusters do HBase podem ser implantados na mesma rede virtual que seus aplicativos, de modo que os aplicativos possam se comunicar diretamente com o HBase.
- [Configurar replicação HBase no HDInsight](hdinsight-hbase-geo-replication.md): saiba como configurar a replicação do HBase entre dois datacenters do Azure. 
- [Analisar sentimentos do Twitter com o HBase no HDInsight][hbase-twitter-sentiment]\: saiba como fazer a [análise de sentimento](http://en.wikipedia.org/wiki/Sentiment_analysis) em tempo real de grandes volumes de dados usando o HBase em um cluster do Hadoop no HDInsight.

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-file-browser.png

<!--HONumber=54-->