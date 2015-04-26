<properties 
	pageTitle="Configurar e consultar tabelas HBase usando o Hive no HDInsight | Azure" 
	description="Comece a usar o HBase com Hadoop no HDInsight. Aprenda a criar tabelas HBase e consultá-los usando o Hive." 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="1/7/2015" 
	ms.author="bradsev"/>



# Configurar clusters HBase e consultá-los usando o Hive no Hadoop no HDInsight

Aprenda como criar e consultar tabelas HBase usando o Hive no Hadoop no HDInsight. 

##Neste artigo

* [O que é o HBase?](#hbaseintro)
* [Pré-requisitos](#prerequisites)
* [Provisionar clusters HBase usando o Portal de Gerenciamento do Azure](#create-hbase-cluster)
* [Gerenciar tabelas HBase usando shell do HBase](#create-sample-table)
* [Usar o HiveQL para consultar tabelas HBase](#hive-query)
* [Usar a biblioteca do cliente REST do Microsoft HBase para gerenciar tabelas HBase](#hbase-powershell)
* [Confira também](#seealso)

##<a name="hbaseintro"></a>O que é o HBase?

O HBase é um banco de dados NoSQL de baixa latência que permite o processamento transacional online de big data. Ele é oferecido como um cluster gerenciado integrado no ambiente do Azure. Os clusters são configurados para armazenar dados diretamente no armazenamento de Blob do Azure, que fornece baixa latência e elasticidade elevada no desempenho e opções de custo. Isso permite que os clientes compilem sites interativos que trabalham com grandes conjuntos de dados, compilem serviços que armazenam dados de sensor e telemetria de milhões de pontos de extremidade e analisem esses dados por meio de trabalhos do Hadoop. Para obter mais informações sobre o HBase e os cenários que ele pode ser usado, consulte a [Visão geral do HBase do HDInsight][hdinsight-hbase-overview].

> [AZURE.NOTE] O HBase (versão 0.98.0) está disponível para uso somente com clusters HDInsight 3.1 no HDInsight (com base no Apache Hadoop e YARN 2.4.0). Para obter informações sobre a versão, consulte [O que há de novo nas versões de cluster do Hadoop fornecidas pelo HDInsight?][hdinsight-versions]

##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Uma assinatura do Azure** Para obter mais informações sobre como obter uma assinatura, consulte [Opções de Compra][azure-purchase-options], [Ofertas para Membros][azure-member-offers] ou [Avaliação Gratuita][azure-free-trial].
- **Uma conta de armazenamento do Azure** Para obter instruções, consulte [Como criar uma conta de armazenamento][azure-create-storageaccount].
- **Uma estação de trabalho** com Visual Studio 2013 instalado. Para obter instruções, consulte [Instalação do Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx).

##<a name="create-hbase-cluster"></a>Provisionar um cluster HBase no portal do Azure

Esta seção descreve como provisionar um cluster HBase usando o Portal de Gerenciamento do Azure.


[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Para provisionar um cluster do HDInsight no portal de Gerenciamento do Azure** 


1. Entre no [Portal de Gerenciamento do Azure][azure-management-portal]. 
2. Clique em **NOVO** na parte inferior esquerda e clique em **SERVIÇOS DE DADOS**, **HDINSIGHT**, **HBASE**. 
3. Forneça o **NOME DO CLUSTER**, o **TAMANHO DO CLUSTER**, a SENHA DO USUÁRIO DO CLUSTER e uma **CONTA DE ARMAZENAMENTO**.
 
	![Choosing and HBase cluster type and entering cluster login credentials.][img-hdinsight-hbase-cluster-quick-create]

4. Clique no ícone de conferido na parte inferior esquerda para criar o cluster HBase.


##<a name="create-sample-table"></a>Criar uma tabela de amostra HBase por meio do shell HBase
Esta seção descreve como habilitar e usar o Remote Desktop Protocol (RDP) para acessar o shell HBase e usá-lo para criar uma tabela de amostra HBase, incluir linhas e listar as linhas na tabela.

Presume-se que você concluiu o procedimento descrito na primeira seção e, portanto, já criou com sucesso um cluster HBase.

**Para habilitar a conexão do RDP com o cluster HBase**

1. No portal de gerenciamento, clique em **HDINSIGHT** na esquerda para exibir a lista de clusters existentes.
2. Clique no cluster HBase em que você deseja abrir o Shell HBase.
3. Clique em **CONFIGURAÇÃO**, no topo da página.
4. Clique em **HABILITAR CONEXÃO REMOTA** na parte inferior da página.
5. Digite um nome de usuário de RDP e uma senha.  O nome de usuário deve ser diferente do nome de usuário do cluster usado ao provisionar o cluster. Os dados em **EXPIRA EM** podem ser para até sete dias a partir de hoje.
6. Clique na marcação no canto inferior direito para habilitar a área de trabalho remota.
7. Depois que o RPD estiver habilitado, clique em **CONECTAR** da parte inferior da guia **CONFIGURAÇÃO** e siga as instruções.

 
**Para abrir o Shell HBase**

1. Em sua sessão do RDP, clique no atalho **Prompt de Comando do Hadoop** localizado na Área de Trabalho.

2. Altere a pasta para o diretório inicial HBase:
		
		cd %HBASE_HOME%\bin

3. Abra o shell HBase:

		hbase shell


**Para criar uma tabela de amostra, adicione dados e recupere os dados**

1. Crie uma tabela de amostra:

		create 'sampletable', 'cf1'

2. Inclua uma linha na tabela de amostra:

		put 'sampletable', 'row1', 'cf1:col1', 'value1'

3. Liste as linhas na tabela de amostra:
	
		scan 'sampletable'

**Verificar o status do cluster no WebUI do HBase**
	
O HBase também é fornecido com um WebUI que ajuda a monitorar seu cluster, por exemplo, fornecendo informações sobre regiões ou estatísticas de solicitação. No cluster HBase você pode encontrar o WebUI sob o endereço do nó zookeeper.


	http://zookeepernode:60010/master-status
	
Em um cluster de alta disponibilidade (HA), você encontrará um link para o nó mestre HBase ativo atual hospedando o WebUI.

**Carregamento em massa de uma tabela de exemplo**

1. Crie o arquivo_de_amostra1.txt contendo os dados a seguir, e carregue no armazenamento de Blob do Azure para /tmp/arquivo_de_amostra1.txt:

		row1	c1	c2
		row2	c1	c2
		row3	c1	c2
		row4	c1	c2
		row5	c1	c2
		row6	c1	c2
		row7	c1	c2
		row8	c1	c2
		row9	c1	c2
		row10    c1	c2

2. Altere a pasta para o diretório inicial HBase:
		
		cd %HBASE_HOME%\bin

3. Execute ImportTsv:

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,a:b,a:c" -Dimporttsv.bulk.output=/tmpOutput sampletable2 /tmp/samplefile1.txt

4. Carregue a saída do comando anterior no HBase:

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput sampletable2



##<a name="hive-query"></a>Usar o Hive para consultar uma tabela HBase

Agora que você tem um cluster HBase provisionado e criou uma tabela do HBase, você pode consultá-la usando o Hive. Esta seção cria uma tabela Hive que mapeia para a tabela HBase e usa-a para consultas nos dados em sua tabela HBase.

**Para abrir o painel do cluster**

1. Entre no [Portal de Gerenciamento do Azure][azure-management-portal]. 
2. Clique em **HDINSIGHT** no painel esquerdo. Você verá uma lista dos clusters criados, incluindo aquele que criou na última seção.
3. Clique no nome do cluster em que deseja executar a tarefa do Hive.
4. Clique em **CONSOLE DE CONSULTA** na parte inferior da página para abrir o painel do cluster. Uma página da Web será aberta em uma guia diferente do navegador.   
5. Insira o nome de usuário e senha da conta do Usuário Hadoop.  O nome de usuário padrão é **admin**, a senha é o que você digitou durante o processo de provisionamento. Uma nova guia do navegador é aberta. 
6. Clique em **Editor Hive** no topo da página. O Editor de Hive é semelhante a:

	![HDInsight cluster dashboard.][img-hdinsight-hbase-hive-editor]





























**Para executar consultas de Hive**

1. Insira o script HiveQL abaixo no Editor de Hive e clique em **ENVIAR** para criar um mapeamento de tabela Hive para a tabela do HBase. Assegure-se de ter criado a tabela de amostra referenciada aqui no HBase usando o Shell HBase antes de executar esta declaração.
 
    	CREATE EXTERNAL TABLE hbasesampletable(rowkey STRING, col1 STRING, col2 STRING)
    	STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    	WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,cf1:col1,cf1:col2')
    	TBLPROPERTIES ('hbase.table.name' = 'sampletable');

	Aguarde até o **Status** ser atualizado para **concluído**.
 
2. Insira o script HiveQL abaixo no Editor de Hive e, em seguida, clique no botão **ENVIAR**. A consulta de Hive consulta os dados na tabela do HBase:

     	SELECT count(*) FROM hbasesampletable;
 
4. Para recuperar os resultados da consulta do Hive, clique no link **Visualizar Detalhes** na janela **Sessão da Tarefa** quando a execução da tarefa for concluída. A saída do trabalho deve ser 1 porque você coloca apenas um registro na tabela HBase.




**Para pesquisar o arquivo de saída**

1. No Console de Consulta, clique **Navegador de Arquivos** no topo da página.
2. Clique na Conta de Armazenamento do Azure usada como o sistema de arquivos padrão para o cluster HBase.
3. Clique no nome do cluster HBase. O contêiner padrão de conta de armazenamento do Azure usa o nome do cluster.
4. Clique em **usuário**.
5. Clique em **admin**. Esse é o nome de usuário do Hadoop.
6. Clique no nome do trabalho com o horário da **Última modificação** correspondente ao horário de execução da consulta SELECIONAR do Hive.
4. Clique em **stdout**. Salve o arquivo e abra-o com o bloco de notas. A saída deverá ser 1.

	![HDInsight HBase Hive Editor File Browser][img-hdinsight-hbase-file-browser]
	
##<a name="hbase-powershell"></a>Usar APIs C# da Biblioteca do Cliente REST do HBase para .NET para criar uma tabela HBase e recuperar dados da tabela

O projeto da Biblioteca do Cliente REST do Microsoft HBase deve ser baixado do GitHub e o projeto criado para usar o SDK do .NET do HBase. O procedimento a seguir inclui as instruções para essa etapa.

1. Crie um novo aplicativo C# do Visual Studio de Área de Trabalho do Windows.
2. Abra o console do Gerenciador de Pacotes NuGet clicando no menu **FERRAMENTAS**, **Gerenciador de Pacotes NuGet**, **Console do Gerenciador de Pacotes**.
3. Execute o comando NuGet a seguir no console:

	Install-Package Microsoft.HBase.Client

5. Adicione o seguinte usando as instruções na parte superior do arquivo:
		
		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. Substitua a função Main por:

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

7. Defina as primeiras três variáveis na função Main.
8. Pressione **F5** para executar o aplicativo.



##<a name="next"></a> O que vem a seguir?
Neste tutorial, você aprendeu a provisionar um cluster HBase, criar tabelas e exibir os dados nessas tabelas por meio do shell HBase. Você também aprendeu a usar o Hive para consultar os dados em tabelas HBase e como usar as APIs C# do HBase para criar uma tabela HBase e recuperar dados da tabela. 

Para obter mais informações, consulte:

- [Visão geral do HBase do HDInsight][hdinsight-hbase-overview]:
O HBase é um banco de dados NoSQL de código aberto Apache baseado no Hadoop que fornece acesso aleatório e uma sólida consistência para grandes quantidades de dados não estruturados e semiestruturados.
- [Provisionar clusters do HBase na Rede Virtual do Azure][hdinsight-hbase-provision-vnet]:
Com a integração da rede virtual, os clusters do HBase podem ser implantados na mesma rede virtual que seus aplicativos, de modo que os aplicativos possam se comunicar diretamente com o HBase.
- [Analisar dados de sentimento no Twitter com o HBase no HDInsight][hbase-twitter-sentiment]:
Saiba como fazer a [análise de sentimento](http://en.wikipedia.org/wiki/Sentiment_analysis) em tempo real do Big Data, usando o HBase em um cluster Hadoop no HDInsight.

[hdinsight-hbase-overview]: ../hdinsight-hbase-overview/
[hdinsight-hbase-provision-vnet]: ../hdinsight-hbase-provision-vnet
[hdinsight-versions]: ../hdinsight-component-versioning/
[hbase-twitter-sentiment]: ../hdinsight-hbase-analyze-twitter-sentiment/
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/ 

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-file-browser.png









<!--HONumber=42-->
