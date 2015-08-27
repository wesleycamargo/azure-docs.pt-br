<properties
   pageTitle="Tutorial do Linux: Introdução ao Hadoop e ao Hive | Microsoft Azure"
   description="Siga este tutorial do Linux para começar a usar o Hadoop no HDInsight. Saiba como configurar clusters do Linux e consultar dados com o Hive."
   services="hdinsight"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/11/2015"
   ms.author="nitinme"/>

# Tutorial do Hadoop: Introdução ao uso do Hadoop com o Hive no HDInsight no Linux (visualização)

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-tutorial-get-started-windows.md)
- [Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

Este tutorial de Hadoop apresenta rapidamente o Azure HDInsight em Linux, mostrando como provisionar um cluster do Hadoop no Linux e executar uma consulta de Hive.


> [AZURE.NOTE]Se for iniciante em Hadoop e Big Data, você pode ler mais sobre os termos <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>, <a href="http://go.microsoft.com/fwlink/?LinkId=510086" target="_blank">MapReduce</a>, <a href="http://go.microsoft.com/fwlink/?LinkId=510087" target="_blank">HDFS (Sistema de Arquivos Distribuído do Hadoop)</a> e <a href="http://go.microsoft.com/fwlink/?LinkId=510085" target="_blank">Hive</a>. Para entender como o HDInsight habilita o Hadoop no Azure, consulte [Introdução ao Hadoop no HDInsight](hdinsight-hadoop-introduction.md).


## O que este tutorial faz? 

Suponha que você tenha um grande conjunto de dados não estruturado e deseje executar consultas nele para extrair algumas informações importantes. Veja como fazer isso:

   ![Etapas do tutorial do Hadoop: criar uma conta de armazenamento; provisionar um cluster do Hadoop; consultar dados com o Hive.](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.Linux.GetStartedFlow.png)


## Pré-requisitos

Antes de começar este tutorial do Linux para Hadoop, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

**Tempo estimado para concluir:** 30 minutos

## Neste tutorial

* [Criar uma conta do Armazenamento do Azure](#storage)
* [Provisionar um cluster HDInsight em Linux](#provision)
* [Enviar um trabalho de Hive no cluster](#hivequery)
* [Próximas etapas](#nextsteps)

## <a name="storage"></a>Criar uma conta do Armazenamento do Azure

O HDInsight usa o armazenamento de Blob do Azure para armazenar dados. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight](../hdinsight-use-blob-storage.md).

Ao provisionar um cluster HDInsight, você especifica uma conta de Armazenamento do Azure. Um contêiner de armazenamento de Blob específico dessa conta é designado como o sistema de arquivos padrão, exatamente como no HDFS. Por padrão, o cluster HDInsight é provisionado no mesmo datacenter da conta do Armazenamento que você especificou.

Além dessa conta do Armazenamento, você pode adicionar outras contas do Armazenamento ao configurar um cluster HDInsight de forma personalizada. Essas contas adicionais do Armazenamento podem ser da mesma assinatura do Azure ou assinaturas diferentes do Azure. Para obter instruções, consulte [Provisionar clusters HDInsight em Linux usando opções personalizadas](hdinsight-hadoop-provision-linux-clusters.md).

Para simplificar este tutorial, vamos usar apenas o contêiner de Blob padrão e a conta padrão do Armazenamento. Na prática, os arquivos de dados geralmente são armazenados em uma conta de Armazenamento designada.

**Para criar uma conta do Armazenamento do Azure**

1. Entre no <a href="https://manage.windowsazure.com/" target="_blank">portal do Azure</a>.
2. Clique em **NOVO** no canto inferior esquerdo, aponte para **SERVIÇOS DE DADOS**, aponte para **ARMAZENAMENTO** e clique em **CRIAÇÃO RÁPIDA**.

	![Portal do Azure, em que você pode usar a Criação Rápida para configurar uma nova conta de Armazenamento.](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.StorageAccount.QuickCreate.png)

3. Insira as informações de **URL**, **LOCAL** e **REPLICAÇÃO** e clique em **CRIAR CONTA DE ARMAZENAMENTO**. Não há suporte para grupos de afinidade. Você verá a nova conta de armazenamento na lista de armazenamento.

	>[AZURE.NOTE]A opção Criação Rápida para provisionar um cluster HDInsight em Linux, como a que usamos neste tutorial, não solicitará um local ao provisionar o cluster. Por padrão, ela coloca o cluster no mesmo datacenter da conta do Armazenamento.

4. Aguarde até que o **STATUS** da nova conta do Armazenamento mude para **Online**.
5. Selecione a nova conta do Armazenamento na lista e clique em **GERENCIAR CHAVES DE ACESSO** na parte inferior da página.
7. Anote as informações de **NOME DA CONTA DE ARMAZENAMENTO** e **CHAVE DE ACESSO PRIMÁRIA** (ou **CHAVE DE ACESSO SECUNDÁRIA**; qualquer uma das chaves funciona). Você precisará deles mais tarde no tutorial.


Para obter mais informações, consulte [Como criar uma conta de armazenamento](../storage-create-storage-account.md) e [Usar o armazenamento de Blob do Azure com o HDInsight](../hdinsight-use-blob-storage.md).

## <a name="provision"></a>Provisionar um cluster HDInsight no Linux

Ao provisionar um cluster HDInsight, você provisiona recursos de computação do Azure que contêm o Hadoop e aplicativos relacionados. Nesta seção, você provisiona um cluster HDInsight no Linux usando a opção Criação Rápida. Essa opção usa nomes de usuário padrão e contêineres de do Armazenamento do Azure e configura um cluster com o HDInsight versão 3.2 (Hadoop versão 2.6, Hortonworks Data Platform versão 2.2) executado em Ubuntu 12.04 LTS. Para obter informações sobre as diferentes versões do HDInsight e seus contratos de nível de serviço, consulte a página [Controle de versão de componentes do HDInsight](hdinsight-component-versioning.md).

>[AZURE.NOTE]Você também pode criar clusters Hadoop que executam o sistema operacional Windows Server. Para obter instruções, consulte [Introdução ao HDInsight](../hdinsight-get-started.md).


**Para provisionar um cluster HDInsight**

1. Entre no <a href="https://manage.windowsazure.com/" target="_blank">portal do Azure</a>.

2. Clique em **NOVO** no canto inferior esquerdo, depois em **SERVIÇOS DE DADOS**, clique em **HDINSIGHT** e, então, em **HADOOP NO LINUX**.

	![Criação de um cluster Hadoop no HDInsight.](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.QuickCreateCluster.png)

4. Digite ou selecione os valores a seguir:

	<table border="1">
	<tr><th>Nome</th><th>Valor</th></tr>
	<tr><td>Nome do cluster</td><td>Nome do cluster.</td></tr>
	<tr><td>Tamanho do cluster</td><td>Número de nós de dados que você deseja implantar. O valor padrão é 4. No entanto, a opção de usar 1 ou 2 nós de dados também está disponível no menu suspenso. Qualquer número de nós do cluster pode ser especificado ao utilizar a opção <strong>Criação Personalizada</strong>. Detalhes de preço nas taxas de cobrança para diversos tamanhos de cluster estão disponível. Clique no símbolo <strong>?</strong> acima da caixa suspensa e siga o link no pop-up.</td></tr>
	<tr><td>Senha</td><td>A senha da conta <i>HTTP</i> (nome de usuário padrão: admin) e da conta <i>SSH</i> (nome de usuário padrão: hdiuser). Observe que NÃO são as contas de administrador das máquinas virtuais em que os clusters são provisionados. </td></tr>

	<tr><td>Conta de armazenamento</td><td>Na caixa suspensa, selecione a conta do Armazenamento que você criou. <br/>

	Depois de escolhida a conta do Armazenamento, ela não pode mais ser alterada. Se a conta do Armazenamento for removida, o cluster ficará indisponível para uso. O cluster HDInsight está localizado no mesmo datacenter que a conta do Armazenamento.
	</td></tr>
	</table>
	Mantenha uma cópia do nome do cluster. Você precisará dela mais tarde no tutorial.


5. Clique em **CRIAR CLUSTER HDINSIGHT**. Quando o provisionamento termina, a coluna de tus mostra **Em execução**.

	>[AZURE.NOTE]O procedimento acima cria um cluster do Linux com a opção Criação Rápida, que usa o nome de usuário de SSH padrão e contêineres do Armazenamento do Azure. Para criar um cluster com opções personalizadas, como usar uma chave SSH para autenticação ou usar contas adicionais do Armazenamento, consulte [Provisionar clusters HDInsight em Linux usando opções personalizadas](hdinsight-hadoop-provision-linux-clusters.md).


## <a name="hivequery"></a>Enviar um trabalho de Hive no cluster
Agora que você tem um cluster HDInsight Linux provisionado, a próxima etapa é executar um trabalho Hive de exemplo para consultar dados de exemplo (sample.log) fornecido com clusters HDInsight. Os dados de exemplo contêm informações de log, inclusive rastreamento, avisos, informações e erros. Podemos consultar esses dados para recuperar todos os logs de erro com uma severidade específica. Você deve executar as seguintes etapas para executar uma consulta Hive em um cluster HDInsight em Linux:

- Conectar-se a um cluster Linux
- Executar um trabalho do Hive



### Para se conectar a um cluster

Você pode se conectar a um cluster HDInsight no Linux de um computador Linux ou de um computador baseado em Windows, usando SSH.

**Para conectar-se de um computador Linux**

1. Abra um terminal e digite o seguinte comando:

		ssh <username>@<clustername>-ssh.azurehdinsight.net

	Como você provisionou um cluster com a opção Criação Rápida, o nome de usuário SSH padrão é **hdiuser**. Portanto, o comando deve ser:

		ssh hdiuser@myhdinsightcluster-ssh.azurehdinsight.net

2. Quando solicitado, digite a senha que você forneceu durante o provisionamento do cluster. Depois que você for conectado com êxito, o prompt será alterado para o seguinte:

		hdiuser@headnode-0:~$


**Para conectar-se de um computador baseado em Windows**

1. Baixe o <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">PuTTY</a> para clientes baseados em Windows.

2. Abra o PuTTY. Em **Categoria**, clique em **Sessão**. Na tela **Opções básicas para sua sessão PuTTY**, insira o endereço SSH do seu servidor HDInsight no campo **Nome de Host (ou Endereço IP)**. O endereço do SSH é o nome do cluster, seguido de **-ssh.azurehdinsight.net**. Por exemplo, **myhdinsightcluster-ssh.azurehdinsight.net**.

	![Conectar-se a um cluster HDInsight no Linux usando PuTTY](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.linux.connect.putty.png)

3. Para salvar as informações de conexão para uso futuro, insira um nome para essa conexão em **Sessões Salvas** e clique em **Salvar**. A conexão será adicionada à lista de sessões salvas.

4. Clique em **Abrir** para se conectar ao cluster. Quando o nome de usuário for solicitado, insira **hdiuser**. Como senha, insira a senha que você especificou durante o provisionamento do cluster. Depois que você for conectado com êxito, o prompt será alterado para o seguinte:

		hdiuser@headnode-0:~$

### Para executar um trabalho do Hive

Quando estiver conectado ao cluster via SSH, use os comandos a seguir para executar uma consulta Hive.

1. Inicie a CLI (interface de linha de comando) do Hive usando o seguinte comando no prompt:

		hive

2. Usando a CLI, insira as instruções a seguir para criar uma nova tabela, chamada **log4jLogs**, usando os dados de exemplo já disponíveis no cluster:

		DROP TABLE log4jLogs;
		CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
		STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
		SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

	Essas instruções executam as seguintes ações:

	- **DROP TABLE** - exclui a tabela e o arquivo de dados caso a tabela já exista.
	- **CREATE EXTERNAL TABLE**: cria uma nova tabela "externa" no Hive. As tabelas externas armazenam apenas a definição da tabela no Hive; os dados são deixados no local original.
	- **ROW FORMAT** - informa ao Hive como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.
	- **STORED AS TEXTFILE LOCATION** - informa ao Hive onde os dados são armazenados (o diretório de exemplos/dados) e que estão armazenados como texto.
	- **SELECT** - Seleciona uma contagem de todas as linhas em que a coluna t4 contém o valor [ERROR].

	>[AZURE.NOTE]Use tabelas externas se os dados subjacentes tiverem que ser atualizados por uma origem externa, como um processo automatizado de carregamento de dados, ou por outra operação MapReduce, mas você quiser que as consultas Hive sempre usem os dados mais recentes. Remover uma tabela externa *não* exclui os dados, somente a definição de tabela.

	Isso retorna a seguinte saída:

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

	A saída contém **ERROR 3**, pois há três linhas que contêm esse valor.

3. Use as instruções a seguir para criar uma nova tabela "interna" chamada **errorLogs**:

		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';


	Essas instruções executam as seguintes ações:

	- **CREATE TABLE IF NOT EXISTS** - cria uma tabela, se ela ainda não existir. Como a palavra-chave **EXTERNAL** não é usada, esta é uma tabela interna, que é armazenada no data warehouse do Hive e totalmente gerenciada pelo Hive. Ao contrário das tabelas externas, remover uma tabela interna excluirá também os dados subjacentes.
	- **STORES AS ORC**: armazena os dados no formato ORC (Optimized Row Columnar). Esse é um formato altamente otimizado e eficiente para o armazenamento de dados do Hive.
	- **INSERT OVERWRITE ... SELECT** - Seleciona linhas da tabela **log4jLogs** que contêm [ERROR] e insere os dados na tabela **errorLogs**.

4. Para verificar se apenas as linhas que contêm ERROR na coluna t4 foram armazenadas na tabela **errorLogs**, use a seguinte instrução para retornar todas as linhas de **errorLogs**.

		SELECT * from errorLogs;

	A saída a seguir deve ser exibida no console:

		2012-02-03	18:35:34	SampleClass0	[ERROR]	 incorrect		id
		2012-02-03	18:55:54	SampleClass1	[ERROR]	 incorrect		id
		2012-02-03	19:25:27	SampleClass4	[ERROR]	 incorrect		id
		Time taken: 0.987 seconds, Fetched: 3 row(s)

	Os dados retornados devem corresponder aos logs [ERROR].


## <a name="nextsteps"></a>Próximas etapas
Neste tutorial do Linux, você aprendeu a provisionar um cluster do Hadoop no Linux com o HDInsight e a executar nele uma consulta do Hive usando SSH. Para saber mais, consulte os seguintes artigos:

- [Provisionar HDInsight em Linux usando opções personalizadas](hdinsight-hadoop-provision-linux-clusters.md)
- [Trabalhando com o HDInsight no Linux](hdinsight-hadoop-linux-information.md)
- [Gerenciar clusters HDInsight usando o Ambari](hdinsight-hadoop-manage-ambari.md)
- [Usar o MapReduce com o HDInsight][hdinsight-use-mapreduce]
- [Usar o Hive com o HDInsight][hdinsight-use-hive]
- [Usar o Pig com o HDInsight][hdinsight-use-pig]
- [Usar o armazenamento de Blob do Azure com o HDInsight](../hdinsight-use-blob-storage.md)
- [Carregar dados no HDInsight][hdinsight-upload-data]


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell.md
[powershell-open]: ../install-configure-powershell.md#Install

[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png
 

<!----HONumber=August15_HO6-->