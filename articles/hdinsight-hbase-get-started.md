<properties urlDisplayName="Get Started" pageTitle="Configurar e consultar tabelas HBase usando o Hive no HDInsight | Azure" metaKeywords="" description="Comece a usar o HBase com Hadoop no HDInsight. Aprenda a criar tabelas HBase e consultá-los usando o Hive." metaCanonical="" services="hdinsight" documentationCenter="" title="Set up HBase clusters and query them using Hive on Hadoop in HDInsight" authors="bradsev" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/9/2014" ms.author="bradsev" />



# Configurar clusters HBase e consultá-los usando o Hive no Hadoop no HDInsight

Aprenda como criar e consultar tabelas HBase usando o Hive no Hadoop no HDInsight. 

##Neste artigo

* [O que é o HBase?](#hbaseintro)
* [Pré-requisitos](#prerequisites)
* [Provisionar clusters HBase usando o Portal de Gerenciamento do Azure](#create-hbase-cluster)
* [Gerenciar tabelas HBase usando shell do HBase](#create-sample-table)
* [Usar o HiveQL para consultar tabelas HBase](#hive-query)
* [Usar a biblioteca do cliente REST do Microsoft HBase para gerenciar tabelas HBase](#hbase-powershell)
* [Consulte também](#seealso)

##<a name="hbaseintro"></a>O que é o HBase?

O HBase é um banco de dados NoSQL de baixa latência que permite o processamento transacional online de big data. Ele é oferecido como um cluster gerenciado integrado no ambiente do Azure. Os clusters são configurados para armazenar dados diretamente no armazenamento de Blob do Azure, que fornece baixa latência e elasticidade elevada no desempenho e opções de custo. Isso permite que os clientes compilem sites interativos que trabalham com grandes conjuntos de dados, compilem serviços que armazenam dados de sensor e telemetria de milhões de pontos de extremidade e analisem esses dados por meio de trabalhos do Hadoop. Para obter mais informações sobre o HBase e os cenários que ele pode ser usado, consulte a [Visão geral do HBase do HDInsight](http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-hbase-overview/).

> [WACOM.NOTE] O HBase (versão 0.98.0) está disponível para uso somente com clusters HDInsight 3.1 no HDInsight (com base no Apache Hadoop e YARN 2.4.0). Para obter informações sobre a versão, consulte [O que há de novo nas versões de cluster Hadoop fornecidas pelo HDInsight?][hdinsight-versions]

##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Uma assinatura do Azure** Para obter mais informações sobre como obter uma assinatura, consulte [Opções de Compra][azure-purchase-options], [Ofertas para Membros][azure-member-offers] ou [Avaliação Gratuita][azure-free-trial].
- **Uma conta de armazenamento do Azure** Para obter instruções, consulte [Como criar uma conta de armazenamento][azure-create-storageaccount].
- **Uma estação de trabalho** com Visual Studio 2013 instalado. Para obter instruções, consulte [Instalação do Visual Studio](http://msdn.microsoft.com/pt-br/library/e2h7fzkw.aspx).
- Baixe a [Biblioteca do Cliente REST do Microsoft HBase para .NET](https://github.com/hdinsight/hbase-sdk-for-net). 


##<a name="create-hbase-cluster"></a>Provisionar um cluster HBase no portal do Azure

Esta seção descreve como provisionar um cluster HBase usando o portal do Azure.


[WACOM.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Para provisionar um cluster HDInsight no portal do Azure** 


1. Entre no [Portal de Gerenciamento do Azure][azure-management-portal]. 

2. Clique em **HDInsight** à esquerda para listar o status dos clusters em sua conta e depois no ícone **+NOVO** na parte inferior esquerda. 

	![Creating an HBase cluster in HDInsight in the Azure portal.](http://i.imgur.com/PmGynKZ.jpg)

3. Clique no ícone HDInsight na segunda coluna da esquerda e na opção HBase na próxima coluna. Especifique os valores para o CLUSTERNAME e CLUSTER SIZE, o nome da Conta de Armazenamento e uma senha para o novo cluster HBase.
 
	![Choosing and HBase cluster type and entering cluster login credentials.](http://i.imgur.com/ecxbB9K.jpg)

4. Clique no ícone de conferido na parte inferior esquerda para criar o cluster HBase.


##<a name="create-sample-table"></a>Criar uma tabela de amostra HBase por meio do shell HBase
Esta seção descreve como habilitar e usar o Remote Desktop Protocol (RDP) para acessar o shell HBase e usá-lo para criar uma tabela de amostra HBase, incluir linhas e listar as linhas na tabela.

Presume-se que você concluiu o procedimento descrito na primeira seção e, portanto, já criou com sucesso um cluster HBase.

**Habilitar a conexão do RDP com o cluster HBase**

1. Para habilitar uma Conexão de Área de Trabalho Remota com o cluster HDInsight, selecione o cluster HBase que você criou e clique na guia **CONFIGURAÇÃO**. Clique no botão **HABILITAR REMOTO** na parte inferior da página para habilitar a conexão do RDP com o cluster.
2. Forneça as credenciais e a data de expiração no assistente **CONFIGURAR ÁREA DE TRABALHO REMOTA** e clique no círculo marcado na parte inferior direita.(Pode levar alguns minutos até a operação ser concluída.)
3. Para conectar-se ao cluster HDInsight, clique no botão **CONECTAR** na parte inferior da guia **CONFIGURAÇÃO**.

 
**Abrir o Shell HBase**

1. Em sua sessão do RDP, clique no atalho **Prompt de Comando do Hadoop** localizado na Área de Trabalho.

2. Altere a pasta para o diretório inicial HBase:
		
		cd %HBASE_HOME%\bin

3. Abra o shell HBase:

		hbase shell


**Crie uma tabela de amostra, adicione dados e recupere os dados**

1. Crie uma tabela de amostra:

		create 'sampletable', 'cf1'

2. Inclua uma linha na tabela de amostra:

		put 'sampletable', 'row1', 'cf1:col1', 'value1'

3. Liste as linhas na tabela de amostra:
	
		scan 'sampletable'

##<a name="hive-query"></a>Usar o Hive para consultar uma tabela HBase

Agora que você tem um cluster HBase fornecido e criou uma tabela, é possível consultá-la usando o Hive. Esta seção cria uma tabela Hive que mapeia para a tabela HBase e usa-a para consultas nos dados em sua tabela HBase.

**Para abrir o painel do cluster**

1. Entre no [Portal de Gerenciamento do Azure][azure-management-portal]. 
2. Clique em **HDINSIGHT** no painel esquerdo.Você verá uma lista dos clusters criados, incluindo aquele que criou na última seção.
3. Clique no nome do cluster em que deseja executar a tarefa do Hive.
4. Clique em **GERENCIAR CLUSTER** na parte inferior da página para abrir o painel do cluster.   Uma página da Web será aberta em uma guia diferente do navegador.   
5. Insira o nome de usuário e senha da conta do Usuário Hadoop.  O nome de usuário padrão é **admin**, a senha é a que você digitou durante o processo de provisionamento.O painel fica assim:

	![HDInsight cluster dashboard.](http://i.imgur.com/tMwXlj9.jpg)


**Para executar uma consulta do Hive**

1. Para criar uma tabela Hive com um mapeamento para a tabela HBase, insira o script HiveQL abaixo na janela do console Hive e clique no botão **ENVIAR**.Assegure-se de ter criado a tabela de amostra referenciada aqui no HBase usando o Shell HBase antes de executar esta declaração.
 
    	CREATE EXTERNAL TABLE hbasesampletable(rowkey STRING, col1 STRING, col2 STRING)
    	STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    	WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,cf1:col1,cf1:col2')
    	TBLPROPERTIES ('hbase.table.name' = 'sampletable');

 
2. Para executar uma Consulta do Hive nos Dados no HBase, insira o script HiveQL abaixo na janela do console do Hive e clique no botão **ENVIAR**.

     	SELECT count(*) FROM hbasesampletable;
 
4. Para recuperar os resultados da consulta do Hive, clique no link **Exibir Detalhes** na janela **Sessão da Tarefa** quando a execução da tarefa for concluída.


Observação: O link do shell HBase alterna a guia para o **Shell HBase**.



**Para pesquisar o arquivo de saída**

1. No painel do cluster, clique em **Arquivos** na parte superior.
2. Clique em **Templeton-Job-Status**.
3. Clique no número da GUID que possui o último Horário modificado um pouco após o Hora de início da tarefa que você anotou anteriormente. Anote este GUID.  Você precisará dele na próxima seção.
4. O arquivo **stdout** possui os dados necessários para a próxima seção. É possível clicar em **stdout** para baixar uma cópia do arquivo de dados se desejar.

	
##<a name="hbase-powershell"></a>Usar APIs C# da Biblioteca do Cliente REST do HBase para .NET para criar uma tabela HBase e recuperar dados da tabela

O projeto da Biblioteca do Cliente REST do Microsoft HBase deve ser baixado do GitHub e o projeto criado para usar o SDK do .NET do HBase. O procedimento a seguir inclui as instruções para essa etapa.

1. Baixe a [Biblioteca do Cliente REST do Microsoft HBase para .NET](https://github.com/hdinsight/hbase-sdk-for-net) se você ainda não tiver atendido a esse pré-requisito.

2. Abra Marlin.sln no Visual Studio (**Arquivo** -> **Abrir** -> **Projeto/Solução ...**) do local para o qual foi baixado. Selecione **Exibir** -> **Gerenciador de Soluções** para ver a solução 'Marlin' e o seu projeto Microsoft.HBase.Client. Compile o projeto **Marlin** ao clicar com o botão direito do mouse no **Gerenciador de Soluções** e selecionar **Compilar Solução**. 

4. Crie um novo aplicativo de console do Visual C#. Recupere o Microsoft.HBase.Client.dll e protobuf.dll compilados (do diretório ...\bin\debug\Microsoft.HBase.Client) e adicione-os ao seu projeto do C#: Clique com o botão direito do mouse em **Referências**, selecione **Adicionar Referências...**, navegue até os dois assemblies e carregue-os. [protobuf-net](http://code.google.com/p/protobuf-net/) é uma implementação .NET do serializador binário Protocol Buffers da Google usado para comunicação de dados.

5. Adicione o seguinte usando as instruções na parte superior do arquivo:
		
		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. Crie uma nova instância do cliente do HBase usando as credenciais de cluster e recupere a versão do cluster:

		// Create a new instance of an HBase client.
		var creds = new ClusterCredentials(new Uri("https://myclustername.azurehdinsight.net"), "myusername", "mypassword");
		var client = new HBaseClient(creds);

		// Retrieve the cluster version
		var version = client.GetVersion();
		Console.WriteLine(version);

7. Para criar uma nova tabela HBase, use este código:

		// Create a new HBase table.
		var testTableSchema = new TableSchema();
		testTableSchema.name = "mytablename";
		testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
		testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
		client.CreateTable(testTableSchema);

8. Inserir dados em uma tabela com esse código:

		// Insert data into a table.
		var tableName = "mytablename";
		var testKey = "content";
		var testValue = "the force is strong in this column";
		var set = new CellSet();
		var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
		set.rows.Add(row);

		var value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
		row.values.Add(value);
		client.StoreCells(tableName, set);

9. Para recuperar uma célula com a sua chave, use este código. 

		// Retrieve a cell with its key.
		var testKey = "content";
		var tableName = "mytablename";

		var cells = client.GetCells(tableName, testKey);
		// get the first value from the row.
		.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values[0].data));
		// with the previous insert, it should yield: "the force is strong in this column"

10. Examine as linhas em uma tabela com o seguinte código:

		//Scan over rows in a table.
		var creds = new ClusterCredentials(new Uri("https://myclustername.azurehdinsight.net"), "myusername", "mypassword");
		var client = new HBaseClient(creds);

		var tableName = "mytablename";

		// assume the table has integer keys and we want data between keys 25 and 35
		var scanSettings = new Scanner()
		{
    		batch = 10,
    		startRow = BitConverter.GetBytes(25),
    		endRow = BitConverter.GetBytes(35)
		};

		var scannerInfo = client.CreateScanner(tableName, scanSettings);
		CellSet next = null;
		while ((next = client.ScannerGetNext(scannerInfo)) != null)
		{
    		foreach (var row in next.rows)
    		{
        		// ... read the rows
    		}
		}



##<a name="summary"></a>Resumo
Neste tutorial, você aprendeu a provisionar um cluster HBase, criar tabelas e exibir os dados nessas tabelas por meio do shell HBase. Você também aprendeu a usar o Hive para consultar os dados em tabelas HBase e como usar as APIs C# do HBase para criar uma tabela HBase e recuperar dados da tabela.

##<a name="next"></a> O que vem a seguir?

[Visão geral do HBase do HDInsight][hdinsight-hbase-overview]:
O HBase é um banco de dados NoSQL de software livre Apache baseado no Hadoop que fornece acesso aleatório e uma coerência forte para grandes quantidades de dados não estruturados e semiestruturados.

[Provisionar clusters HBase na Rede Virtual do Azure][hdinsight-hbase-provision-vnet]:
Com a integração da rede virtual, os clusters do HBase podem ser implantados na mesma rede virtual que seus aplicativos, de modo que os aplicativos possam se comunicar diretamente com o HBase.

[Analisar dados de sentimento no Twitter com o HBase no HDInsight][hbase-twitter-sentiment]:
Saiba como fazer [análise de sentimento ](http://en.wikipedia.org/wiki/Sentiment_analysis) em tempo real de big data usando o HBase em um Hadoop no HDInsight.

[hdinsight-hbase-overview]: ../hdinsight-hbase-overview/
[hdinsight-hbase-provision-vnet]: ../hdinsight-hbase-provision-vnet
[hdinsight-versions]: ../hdinsight-component-versioning/

[hdinsight-get-started-30]: ../hdinsight-get-started-30/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hbase-twitter-sentiment]: ../hdinsight-hbase-analyze-twitter-sentiment/

[hdinsight-use-hive]: ../hdinsight-use-hive/

[hdinsight-storage]: ../hdinsight-use-blob-storage/



[azure-purchase-options]: http://azure.microsoft.com/pt-br/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pt-br/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pt-br/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/pt-br/documentation/articles/storage-create-storage-account/ 

[apache-hadoop]: http://hadoop.apache.org/

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell/
[powershell-open]: ../install-configure-powershell/#Install


[img-hdi-dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png







<!--HONumber=35.1-->
