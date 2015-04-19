<properties 
   pageTitle="Começar a usar o Hadoop com Hive no HDInsight no Linux| Azure" 
   description="Começar a usar o Hadoop no HDInsight no Linux. Saiba como provisionar clusters em execução do Hadoop do HDInsight no Linux e consultar dados com o Hive" 
   services="hdinsight" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/18/2015"
   ms.author="nitinme"/>

# Começar a usar o Hadoop com Hive no HDInsight no Linux (Preview)

Este tutorial apresenta rapidamente o HDInsight no Linux mostrando como provisionar um cluster Hadoop do HDInsight no Linux e executar uma consulta do Hive para extrair informações significativas de dados não estruturados. Em seguida, você poderá analisar os resultados em uma ferramenta do Business Intelligence (BI), como o Tableau.


> [AZURE.NOTE] Se você é iniciante no Hadoop e em Big Data, leia mais sobre os termos <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>, <a href="http://go.microsoft.com/fwlink/?LinkId=510086" target="_blank">MapReduce</a>, <a href="http://go.microsoft.com/fwlink/?LinkId=510087" target="_blank">HDFS</a> e <a href="http://go.microsoft.com/fwlink/?LinkId=510085" target="_blank">Hive</a>. Para entender como o HDInsight habilita o Hadoop no Azure, consulte [Introdução ao Hadoop no HDInsight](hdinsight-hadoop-introduction.md).


## O que este tutorial aborda? ##

Suponha que você tenha um grande conjunto de dados não estruturado e deseje executar consultas nele para extrair algumas informações importantes. Veja como faremos isso:

   !["Get started using Hadoop with Hive in HDInsight" tutorial steps illustrated: create an account; provision a cluster; query data; and analyze in Tableau.](./media/hdinsight-hadoop-linux-get-started/HDI.Linux.GetStartedFlow.png)


**Pré-requisitos:**

Antes de começar este tutorial, você deve ter o seguinte:


- Uma assinatura do Azure. Para obter mais informações sobre como adquirir uma assinatura, consulte <a href="http://azure.microsoft.com/pricing/purchase-options/" target="_blank">Opções de compra</a>, <a href="http://azure.microsoft.com/pricing/member-offers/" target="_blank">Ofertas para membros</a> ou <a href="http://azure.microsoft.com/pricing/free-trial/" target="_blank">Avaliação gratuita</a>.

**Tempo estimado para concluir:** 30 minutos

## Neste tutorial

* [Criar uma conta de armazenamento do Azure](#storage)
* [Provisionar um cluster HDInsight Linux](#provision)
* [Enviar um trabalho do Hive](#hivequery)
* [Próximas etapas](#nextsteps)

## <a name="storage"></a>Criar uma conta de Armazenamento do Azure

O HDInsight usa o Armazenamento de Blob do Azure para armazenar dados. Ele é chamado de  *WASB* ou  *Armazenamento do Azure - Blob*. O WASB é a implementação do HDFS da Microsoft no Armazenamento de Blob do Azure. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight](hdinsight-use-blob-storage.md).

Ao provisionar um cluster HDInsight, você especifica uma conta de Armazenamento do Azure. Um contêiner de armazenamento de Blob específico dessa conta é designado como o sistema de arquivos padrão, exatamente como no HDFS. O cluster HDInsight, por padrão, é provisionado no mesmo data center que a conta de armazenamento especificada.

Além dessa conta de armazenamento, você pode adicionar mais contas de armazenamento ao configurar um cluster HDInsight de modo personalizado. Essa conta de armazenamento adicional pode ser da mesma assinatura do Azure ou de assinaturas diferentes do Azure. Para obter instruções, consulte [Provisionar clusters do HDInsight Linux usando opções personalizadas](hdinsight-hadoop-provision-linux-clusters.md). 

Para simplificar este tutorial, somente o contêiner de blob padrão e a conta de armazenamento padrão são usados. Na prática, os arquivos de dados geralmente são armazenados em uma conta de armazenamento designada.

**Para criar uma conta de Armazenamento do Azure**

1. Entre no <a href="https://manage.windowsazure.com/" target="_blank">Portal de Gerenciamento do Azure</a>.
2. Clique em **NOVO** no canto inferior esquerdo, aponte para **SERVIÇOS DE DADOS**, aponte para **ARMAZENAMENTO** e, em seguida, clique em **CRIAÇÃO RÁPIDA**.

	![Azure portal where you can use Quick Create to set up a new storage account.](./media/hdinsight-hadoop-linux-get-started/HDI.StorageAccount.QuickCreate.png)

3. Digite **URL**, **LOCAL** e **REPLICAÇÃO** e, em seguida, clique em **CRIAR CONTA DE ARMAZENAMENTO**. Não há suporte para grupos de afinidade. Você verá a nova conta de armazenamento na lista de armazenamento.

	>[AZURE.NOTE]  A opção de criação rápida para provisionar um cluster HDInsight Linux, como a que usamos neste tutorial, não solicita um local durante o provisionamento do cluster. Por padrão, ela coloca o cluster no mesmo data center da conta de armazenamento. Desse modo, verifique se você criou a conta de armazenamento nos locais com suporte para o cluster, que são:  **Ásia Oriental**, **Sudeste da Ásia**, **Norte da Europa**, **Oeste da Europa**, **Leste dos EUA**, **Oeste dos EUA**, **Leste dos EUA** e **Centro-Sul dos EUA**.

4. Aguarde até que o **STATUS** da nova conta de armazenamento seja alterado para **Online**.
5. Selecione a nova conta de armazenamento na lista e clique em **GERENCIAR CHAVES DE ACESSO** na parte inferior da página.
7. Tome nota do **NOME DA CONTA DE ARMAZENAMENTO** e da **CHAVE DE ACESSO PRIMÁRIA** (ou a **CHAVE DE ACESSO SECUNDÁRIA**. As duas chaves funcionam).  Você precisará deles mais tarde no tutorial.


Para obter mais informações, consulte
[Como criar uma conta de armazenamento](storage-create-storage-account.md) e [Usar o Armazenamento de Blob do Azure com o HDInsight](hdinsight-use-blob-storage.md).
	
## <a name="provision"></a>Provisionar um cluster HDInsight no Linux

Ao provisionar um cluster HDInsight, você provisiona recursos de computação do Azure que contêm o Hadoop e aplicativos relacionados. Nesta seção você provisiona um cluster HDInsight no Linux usando a opção quick-create. Essa opção usa nomes de usuário padrão e contêineres de armazenamento do Azure e configura um cluster com o HDInsight versão 3.2 (Hadoop versão 2.5, HDP versão 2.2) em execução no Ubuntu 12.04 LTS. Para obter informações sobre versões diferentes do HDInsight e o SLA, consulte a página [Controle de versão do componente do HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/).

>[AZURE.NOTE]  Você também pode criar clusters de Hadoop executando o sistema operacional Windows Server. Para obter instruções, consulte [Introdução ao HDInsight no Windows](hdinsight-get-started.md).


**Para provisionar um cluster HDInsight**

1. Entre no <a href="https://manage.windowsazure.com/" target="_blank">Portal de Gerenciamento do Azure</a>. 

2. Clique em **NOVO** no lado inferior esquerdo, em **Serviços de Dados**, em **HDInsight** e em **Hadoop no Linux**.

	![Creation of a Hadoop cluster in HDInsight.](./media/hdinsight-hadoop-linux-get-started/HDI.QuickCreateCluster.png)

4. Digite ou selecione os valores a seguir:

	<table border="1">
	<tr><th>Nome</th><th>Valor</th></tr>
	<tr><td>Nome do cluster</td><td>Nome do cluster</td></tr>
	<tr><td>Tamanho do cluster</td><td>Número de nós de dados que você deseja implantar. O valor padrão é 4. No entanto, a opção de usar 1 ou 2 nós de dados também está disponível no menu suspenso. Qualquer número de nós do cluster pode ser especificado ao utilizar a opção <strong>Criação Personalizada</strong>. Os detalhes de preço referente às taxas de cobrança para diversos tamanhos de cluster estão disponíveis. Clique no símbolo <strong>?</strong> logo acima da caixa suspensa e siga o link no pop-up.</td></tr>
	<tr><td>Senha</td><td>A senha para a conta <i>HTTP</i> (nome de usuário padrão: admin) e a conta <i>SSH</i> (nome de usuário padrão: hdiuser). Observe que essas NÃO são contas de administrador para as VMs em que os clusters são provisionados. </td></tr>
	
	<tr><td>Conta de Armazenamento</td><td>Selecione a conta de armazenamento criada na caixa suspensa. <br/>

	Assim que for escolhida, a conta de armazenamento não poderá ser alterada. Se a conta de armazenamento for removida, o cluster não estará disponível para uso.

	O cluster HDInsight está colocalizado no mesmo data center que a conta de armazenamento. 
	</td></tr>
	</table>

	Mantenha uma cópia do nome do cluster. Você precisará dela mais tarde no tutorial.

	
5. Clique em **Criar Cluster HDInsight**. Quando o provisionamento for concluído, a coluna de status mostrará **Em execução**.

	>[AZURE.NOTE] O procedimento acima cria um cluster do Linux com a opção quick-create que usa o nome de usuário SSH padrão e contêineres de armazenamento do Azure. Para criar um cluster com opções personalizadas, como o uso da chave SSH para autenticação ou o uso de contas de armazenamento adicionais, consulte [Provisionar clusters HDInsight Linux usando opções personalizadas](hdinsight-hadoop-provision-linux-clusters.md).


## <a name="hivequery"></a>Enviar um trabalho Hive no cluster
Agora que você tem um cluster HDInsight Linux provisionado, a próxima etapa é executar um trabalho Hive de exemplo para consultar dados de exemplo (sample.log) fornecido com clusters HDInsight. Os dados de exemplo contêm informações de log, incluindo rastreamento, avisos, info, erros, etc. Podemos consultar esses dados para recuperar todos os logs de erro com uma severidade específica. Você deve executar as seguintes etapas para executar uma consulta Hive em um cluster HDInsight Linux.

- Conectar-se a um cluster Linux
- Executar um trabalho do Hive



### Para se conectar a um cluster

Você pode se conectar a um cluster HDInsight no Linux de um computador Linux ou um computador Windows usando SSH.

**Para conectar-se de um computador Linux**

1. Abra um terminal e digite o seguinte comando:

		ssh <username>@<clustername>-ssh.azurehdinsight.net

	Como você provisionou um cluster com a opção de criação rápida, o nome de usuário SSH padrão é **hdiuser**. Portanto, o comando deve ser

		ssh hdiuser@myhdinsightcluster-ssh.azurehdinsight.net

2. Quando solicitado, digite a senha que você forneceu durante o provisionamento do cluster. Depois que você for conectado com êxito, o prompt será alterado para o seguinte:

		hdiuser@headnode-0:~$


**Para conectar-se de um computador Windows**

1. Baixe **PuTTY** para clientes Windows. Ele está disponível em <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>

2. Abra o **PuTTY**. Em **Categoria**, clique em **Sessão**. Na tela **Opções básicas para sua sessão PuTTY**, digite o endereço SSH do seu servidor HDInsight no campo **Nome do host (ou endereço IP)**. O endereço do SSH é seu nome de cluster, seguido por**-ssh.azurehdinsight.net**. Por exemplo, **myhdinsightcluster-ssh.azurehdinsight.net**.

	![Connect to an HDInsight cluster on Linux using PuTTY](./media/hdinsight-hadoop-linux-get-started/HDI.linux.connect.putty.png)

3. Para salvar as informações de conexão para uso futuro, digite um nome para essa conexão em **Sessões Salvas** e clique em **Salvar**. A conexão será adicionada à lista de sessões salvas.

4. Clique em **Abrir** para se conectar ao cluster. Quando for solicitado o nome de usuário, insira  *hdiuser*. Para a senha, digite a senha especificada durante o provisionamento do cluster. Depois que você for conectado com êxito, o prompt será alterado para o seguinte:

		hdiuser@headnode-0:~$

### Para executar um trabalho do Hive

Quando você estiver conectado ao cluster usando SSH, use os seguintes comandos para executar uma consulta Hive.

1. Inicie a Interface da Linha de Comando (CLI) do Hive usando o seguinte comando no prompt:

		hive

2. Usando a CLI, digite as instruções a seguir para criar uma nova tabela chamada log4jLogs usando os dados de exemplo já disponíveis no cluster.

		DROP TABLE log4jLogs;
		CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) 
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
		STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
		SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

	As instruções executam as seguintes ações.

	- **DROP TABLE** - exclui a tabela e o arquivo de dados, caso a tabela já exista.
	- **CREATE EXTERNAL TABLE** - cria uma nova tabela "externa" no Hive. As tabelas externas armazenam apenas a definição da tabela no Hive; os dados são deixados no local original.
	- **ROW FORMAT** - informa ao Hive como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.
	- **STORED AS TEXTFILE LOCATION** - informa ao Hive onde os dados são armazenados (o diretório de exemplos/dados) e que estão armazenados como texto.
	- **SELECT** - seleciona uma conta entre todas as linhas em que a coluna t4 contém o valor [ERROR]. 

	>[WACOM.NOTE] As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa, como um processo automático de carregamento de dados, ou outra operação MapReduce, mas sempre quer que as consultas Hive utilizem os dados mais recentes. Descartar uma tabela externa **não** exclui os dados, apenas a definição da tabela.

	Isso retorna a saída a seguir.

		Query ID = hdiuser_20150116000202_cceb9c6b-4356-4931-b9a7-2c373ebba493
		Total jobs = 1
		Launching Job 1 out of 1
		Number of reduce tasks not specified. Estimated from input data size: 1
		In order to change the average load for a reducer (in bytes):
		  set hive.exec.reducers.bytes.per.reducer=<number>
		In order to limit the maximum number of reducers:
		  set hive.exec.reducers.max=<number>
		In order to set a constant number of reducers:
		  set mapreduce.job.reduces=<number>
		Starting Job = job_1421200049012_0006, Tracking URL = <URL>:8088/proxy/application_1421200049012_0006/
		Kill Command = /usr/hdp/2.2.1.0-2165/hadoop/bin/hadoop job  -kill job_1421200049012_0006
		Hadoop job information for Stage-1: number of mappers: 1; number of reducers: 1
		2015-01-16 00:02:40,823 Stage-1 map = 0%,  reduce = 0%
		2015-01-16 00:02:55,488 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 3.32 sec
		2015-01-16 00:03:05,298 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 5.62 sec
		MapReduce Total cumulative CPU time: 5 seconds 620 msec
		Ended Job = job_1421200049012_0006
		MapReduce Jobs Launched: 
		Stage-Stage-1: Map: 1  Reduce: 1   Cumulative CPU: 5.62 sec   HDFS Read: 0 HDFS Write: 0 SUCCESS
		Total MapReduce CPU Time Spent: 5 seconds 620 msec
		OK
		[ERROR]    3
		Time taken: 60.991 seconds, Fetched: 1 row(s)

	Observe que a saída contém **[ERROR] 3**, pois há três linhas que contêm esse valor.

3. Use as instruções a seguir para criar uma nova tabela "interna" chamada **errorLogs**.

		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';


	As instruções executam as seguintes ações.

	- **CREATE TABLE IF NOT EXISTS** - cria uma tabela, se ela ainda não existir. Como a palavra-chave EXTERNAL não é utilizada, essa é uma tabela "interna", que é armazenada no depósito de dados do Hive e é gerenciada totalmente pelo Hive. Diferentemente da tabela **EXTERNAL**, o descarte de uma tabela interna excluirá os dados subjacentes também.
	- **STORED AS ORC** - armazena os dados no formato de linha de otimização Colunar (ORC). Esse é um formato altamente otimizado e eficiente para o armazenamento de dados do Hive.
	- **INSERT OVERWRITE ... SELECT** - seleciona linhas da tabela **log4jLogs** que contêm [ERROR] e insere os dados na tabela **errorLogs**

4. Para verificar se apenas as linhas que contêm **[ERROR]** na coluna t4 foram armazenadas na tabela **errorLogs**, use a seguinte instrução para retornar todas as linhas de errorLogs.

		SELECT * from errorLogs;

	The following output should be displayed on the console.

		2012-02-03	18:35:34	SampleClass0	[ERROR]	 incorrect		id
		2012-02-03	18:55:54	SampleClass1	[ERROR]	 incorrect		id
		2012-02-03	19:25:27	SampleClass4	[ERROR]	 incorrect		id
		Time taken: 0.987 seconds, Fetched: 3 row(s)

	The returned data should all correspond to [ERROR] logs.


## <a name="nextsteps"></a>Próximas etapas
Nesse tutorial, você aprendeu a provisionar um cluster do Hadoop Linux com o HDInsight e executar uma consulta do Hive nele usando o SSH. Para saber mais, consulte os seguintes artigos:

- [Provisionar HDInsight no Linux usando opções personalizadas](hdinsight-hadoop-provision-linux-clusters.md)
- [Trabalhando com o HDInsight no Linux](hdinsight-hadoop-linux-information.md)
- [Gerenciar clusters HDInsight usando Ambari](hdinsight-hadoop-manage-ambari.md)
- [Usar o MapReduce com HDInsight][hdinsight-use-mapreduce]
- [Usar o hive com o HDInsight][hdinsight-use-hive]
- [Usar o Pig com o HDInsight][hdinsight-use-pig]
- [Usar o armazenamento de blobs do Azure com o HDInsight](hdinsight-use-blob-storage.md)
- [Carregar dados no HDInsight][hdinsight-upload-data]


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell/
[powershell-open]: ../install-configure-powershell/#Install

[img-hdi-dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-get-started/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
<!--HONumber=47-->
