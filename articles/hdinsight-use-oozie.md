<properties 
	pageTitle="Usar o Oozie do Hadoop no HDInsight | Azure" 
	description="Usar o Oozie do Hadoop no HDInsight, uma solução de big data. Saiba como definir um fluxo de trabalho do Oozie e enviar um trabalho do Oozie." 
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
	ms.date="03/31/2015" 
	ms.author="jgao"/>


# Usar o Oozie com o Hadoop no HDInsight

##Visão geral
Saiba como usar o Apache Oozie para definir um fluxo de trabalho e executar o fluxo de trabalho no HDInsight. Para saber mais sobre o coordenador do Oozie, confira [Usar o coordenador do Oozie com base em tempo com o HDInsight][hdinsight-oozie-coordinator-time]. Para conhecer a Azure Data Factory, confira [Usar o Pig e o Hive com o Data Factory][azure-data-factory-pig-hive].

##O que é o Oozie?

O Apache Oozie é um sistema de fluxo de trabalho/coordenação que gerencia trabalhos do Hadoop. Ele é integrado com a pilha do Hadoop e oferece suporte a trabalhos do Hadoop para o Apache MapReduce, Apache Pig, Apache Hive e Apache Sqoop. Ele também pode ser usado para agendar trabalhos específicos para um sistema, como programas Java ou scripts de shell.

O fluxo de trabalho que você deve implementar seguindo as instruções neste tutorial contém duas ações:

![Diagrama de fluxo de trabalho][img-workflow-diagram]

1. Uma ação do Hive executa um script do HiveQL para contar as ocorrências de cada tipo de nível de log em um arquivo de log log4j. Cada arquivo log4j consiste em uma linha de campos que contém um campo [LOG LEVEL] para mostrar o tipo e a gravidade, por exemplo:

		2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
		2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
		2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
		...

	A saída do script do Hive é semelhante a:
	
		[DEBUG] 434
		[ERROR] 3
		[FATAL] 1
		[INFO]  96
		[TRACE] 816
		[WARN]  4

	Para obter mais informações sobre o Hive, consulte [Usar o Hive com o HDInsight][hdinsight-use-hive].
	
2.  Uma ação do Sqoop exporta a saída do HiveQL para uma tabela no banco de dados SQL do Azure. Para saber mais sobre o Sqoop, confira [Usar o Hadoop Sqoop com o HDInsight][hdinsight-use-sqoop].

> [AZURE.NOTE]Para obter as versões do Oozie com suporte em clusters HDInsight, confira [Novidades nas versões de clusters fornecidas pelo HDInsight][hdinsight-versions].

> [AZURE.NOTE]Este tutorial funciona em versões do HDInsight 3.0 e 2.1. Este artigo não foi testado no emulador do HDInsight.


##Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Uma estação de trabalho** com o PowerShell do Azure instalado e configurado. Para saber mais, confira [Como instalar e configurar o PowerShell do Azure][powershell-install-configure]. Para executar scripts do Windows PowerShell, você deve executar o PowerShell do Azure como administrador e configurar a política de execução como *RemoteSigned*. Para saber mais, confira [Executar scripts do Windows PowerShell][powershell-script].
- **Um cluster HDInsight**. Para saber mais sobre como criar um cluster HDInsight, confira [Provisionar clusters Hadoop no HDInsight usando opções personalizadas][hdinsight-provision-clusters] ou [Introdução ao Hadoop com o Hive no HDInsight para analisar o uso de fones móveis][hdinsight-get-started]. Você precisará dos seguintes dados para percorrer o tutorial:

	<table border = "1">
<tr><th>Propriedade do cluster</th><th>Nome de variável do Windows PowerShell</th><th>Valor</th><th>Descrição</th></tr>
<tr><td>Nome do cluster HDInsight</td><td>$clusterName</td><td></td><td>O cluster HDInsight em que você executará este tutorial.</td></tr>
<tr><td>Nome de usuário do cluster HDInsight</td><td>$clusterUsername</td><td></td><td>O nome do usuário do cluster HDInsight. </td></tr>
<tr><td>Senha de usuário do cluster HDInsight </td><td>$clusterPassword</td><td></td><td>A senha de usuário do cluster HDInsight.</td></tr>
<tr><td>Nome da Conta de Armazenamento do Azure</td><td>$storageAccountName</td><td></td><td>Uma Conta de Armazenamento do Azure disponível para o cluster HDInsight. Para este tutorial, use a conta de armazenamento padrão especificada durante o processo de provisionamento do cluster.</td></tr>
<tr><td>Nome do contêiner de blob do Azure</td><td>$containerName</td><td></td><td>Para este exemplo, utilize o contêiner de armazenamento de blob usado para o sistema de arquivos do cluster HDInsight padrão. Por padrão, o contêiner tem o mesmo nome do cluster HDInsight.</td></tr>
</table>

- **Um Banco de Dados SQL Azure**. Você deve configurar uma regra de firewall para o servidor de Banco de Dados SQL para permitir o acesso de sua estação de trabalho. Para obter instruções sobre como criar um banco de dados SQL Azure e configurar o firewall, confira [Introdução ao Banco de dados SQL do Microsoft Azure][sqldatabase-get-started]. Este artigo fornece um script do Windows PowerShell para criar a tabela do Banco de Dados SQL do Azure necessária para este tutorial.

	<table border = "1">
<tr><th>Propriedade de banco de dados SQL</th><th>Nome de variável do Windows PowerShell</th><th>Valor</th><th>Descrição</th></tr>
<tr><td>Nome do servidor de banco de dados SQL</td><td>$sqlDatabaseServer</td><td></td><td>Banco de dados SQL do Azure para o qual o Sqoop exportará dados. </td></tr>
<tr><td>Nome de logon do banco de dados SQL</td><td>$sqlDatabaseLogin</td><td></td><td>Nome de logon do banco de dados SQL do Azure.</td></tr>
<tr><td>Senha de logon do banco de dados SQL</td><td>$sqlDatabaseLoginPassword</td><td></td><td>Senha de logon do banco de dados SQL do Azure.</td></tr>
<tr><td>Nome do banco de dados SQL</td><td>$sqlDatabaseName</td><td></td><td>Banco de dados SQL do Azure para o qual o Sqoop exportará dados. </td></tr>
</table>

	> [AZURE.NOTE]Por padrão, um banco de dados SQL do Azure permite conexões de serviços do Azure, como o Azure HDInsight. Se essa configuração de firewall estiver desabilitada, você deverá habilitá-la no Portal do Azure. Para saber mais sobre como criar um Banco de Dados SQL e configurar regras de firewall, confira [Criar e configurar o Banco de Dados SQL do Azure][sqldatabase-create-configue].


> [AZURE.NOTE]O preenchimento dos valores nas tabelas é útil para o uso deste tutorial.


##Definir o fluxo de trabalho do Oozie e o script HiveQL relacionado

As definições de fluxos de trabalho do Oozie são escritas em hPDL (uma Linguagem de Definição de Processo XML). O nome do arquivo do fluxo de trabalho padrão é *workflow.xml*. Você salvará o arquivo do fluxo de trabalho localmente e o implantará no cluster HDInsight usando o Windows PowerShell posteriormente neste tutorial.

A ação do Hive no fluxo de trabalho chama um arquivo de script HiveQL. Esse arquivo de script contém três instruções HiveQL:

1. **A instrução DROP TABLE** exclui a tabela Hive log4j caso ela exista.
2. **A instrução CREATE TABLE** cria uma tabela externa Hive log4j apontando para o local do arquivo de log log4j. O delimitador de campo é ",". O delimitador de linha padrão é "\\n". A tabela externa do Hive é usada para evitar que o arquivo de dados seja removido do local original, caso você deseje executar o fluxo de trabalho do Oozie várias vezes.
3. **A instrução INSERT OVERWRITE** conta as ocorrências de cada tipo de nível de log da tabela ds Hive log4j e salva a saída em um local de armazenamento de blob do Azure. 

Há um erro conhecido do caminho do Hive. Você terá esse problema ao enviar um trabalho do Oozie. As instruções para corrigi-lo podem ser encontradas na Wiki do TechNet: [Erro do Hive no HDInsight: não é possível renomear][technetwiki-hive-error].

**Para definir o arquivo de script HiveQL a ser chamado pelo fluxo de trabalho**

1. Crie um arquivo de texto com o seguinte conteúdo:

		DROP TABLE ${hiveTableName};
		CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
		INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

	Existem três variáveis usadas no script:

	- ${hiveTableName}
	- ${hiveDataFolder}
	- ${hiveOutputFolder}
			
	O arquivo de definição do fluxo de trabalho (workflow.xml neste tutorial) irá passar esses valores para o script HiveQL em tempo de execução.
		
2. Salve o arquivo como **C:\\Tutorials\\UseOozie\\useooziewf.hql** usando a codificação **ANSI (ASCII)**. (Use o Bloco de Notas se o seu editor de texto não fornecer essa opção.) Esse arquivo de script será implantado no cluster HDInsight posteriormente no tutorial.



**Para definir um fluxo de trabalho**

1. Crie um arquivo de texto com o seguinte conteúdo:

		<workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
		    <start to = "RunHiveScript"/> 
			
		    <action name="RunHiveScript">
		        <hive xmlns="uri:oozie:hive-action:0.2">
		            <job-tracker>${jobTracker}</job-tracker>
		            <name-node>${nameNode}</name-node>
		            <configuration>
		                <property>
		                    <name>mapred.job.queue.name</name>
		                    <value>${queueName}</value>
		                </property>
		            </configuration>
		            <script>${hiveScript}</script>
			    	<param>hiveTableName=${hiveTableName}</param>
		            <param>hiveDataFolder=${hiveDataFolder}</param>
		            <param>hiveOutputFolder=${hiveOutputFolder}</param>
		        </hive>
		        <ok to="RunSqoopExport"/>
		        <error to="fail"/>
		    </action>
		
		    <action name="RunSqoopExport">
		        <sqoop xmlns="uri:oozie:sqoop-action:0.2">
		            <job-tracker>${jobTracker}</job-tracker>
		            <name-node>${nameNode}</name-node>
		            <configuration>
		                <property>
		                    <name>mapred.compress.map.output</name>
		                    <value>true</value>
		                </property>
		            </configuration>
			    <arg>export</arg>
			    <arg>--connect</arg> 
			    <arg>${sqlDatabaseConnectionString}</arg> 
			    <arg>--table</arg>
			    <arg>${sqlDatabaseTableName}</arg> 
			    <arg>--export-dir</arg> 
			    <arg>${hiveOutputFolder}</arg> 
			    <arg>-m</arg> 
			    <arg>1</arg>
			    <arg>--input-fields-terminated-by</arg>
			    <arg>"\001"</arg>
		        </sqoop>
		        <ok to="end"/>
		        <error to="fail"/>
		    </action>
		
		    <kill name="fail">
		        <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
		    </kill>
		
		   <end name="end"/>
		</workflow-app>

	Existem duas ações definidas no fluxo de trabalho. A ação de início é *RunHiveScript* Se a ação for executada, a próxima ação será *RunSqoopExport*.

	O RunHiveScript possui várias variáveis. Você irá passar os valores ao enviar o trabalho do Oozie de sua estação de trabalho usando o Windows PowerShell.

	<table border = "1">
<tr><th>Variáveis de fluxo de trabalho</th><th>Descrição</th></tr>
<tr><td>${jobTracker}</td><td>Especifica a URL do controlador do trabalho do Hadoop. Use <strong>jobtrackerhost: 9010</strong> nas versões 3.0 e 2.1 do HDInsight.</td></tr>
<tr><td>${nameNode}</td><td>Especifica a URL do name node do Hadoop. Use o endereço padrão do sistema de arquivos, por exemplo, <i>wasb://&lt;NomeContêiner>@&lt;NomeContaArmazenamento>.blob.core.windows.net</i>.</td></tr>
<tr><td>${queueName}</td><td>Especifica o nome da fila para a qual o trabalho será enviado. Use o <strong>padrão</strong>.</td></tr>
</table><table border = "1">
<tr><th>Variável de ação do Hive</th><th>Descrição</th></tr>
<tr><td>${hiveDataFolder}</td><td>Especifica o diretório de origem do comando Hive Create Table.</td></tr>
<tr><td>${hiveOutputFolder}</td><td>Especifica a pasta de saída da instrução INSERT OVERWRITE.</td></tr>
<tr><td>${hiveTableName}</td><td>O nome da tabela Hive que faz referência aos arquivos de dados log4j.</td></tr>
</table><table border = "1">
<tr><th>Variável de ação do Sqoop</th><th>Descrição</th></tr>
<tr><td>${sqlDatabaseConnectionString}</td><td>Especifica a cadeia de conexão do Banco de Dados SQL do Azure.</td></tr>
<tr><td>${sqlDatabaseTableName}</td><td>A tabela do Banco de Dados SQL do Azure para onde os dados serão exportados.</td></tr>
<tr><td>${hiveOutputFolder}</td><td>Especifica a pasta de saída para a instrução Hive INSERT OVERWRITE. Essa é a mesma pasta para a exportação do Sqoop (export-dir).</td></tr>
</table>Para saber mais sobre o fluxo de trabalho do Oozie e sobre como usar ações de fluxo de trabalho, confira a [documentação do Apache Oozie 4.0][apache-oozie-400] (para a versão 3.0 do cluster HDInsight) ou a [documentação do Oozie Apache 3.3.2][apache-oozie-332] (para a versão 2.1 do cluster HDInsight).

2. Salve o arquivo como **C:\\Tutorials\\UseOozie\\workflow.xml** usando a codificação ANSI (ASCII). (Use o Bloco de Notas se o seu editor de texto não fornecer essa opção.)
	
##Implantar o projeto Oozie e preparar-se para o tutorial

Você executará um script do Windows PowerShell para realizar o seguinte:

- Copie o script HiveQL (useoozie.hql) para o Armazenamento do Azure (wasb:///tutorials/useoozie/useoozie.hql).
- Copie workflow.xml para wasb:///tutorials/useoozie/workflow.xml.
- Copie o arquivo de dados (/ example/data/sample.log) para wasb:///tutorials/useoozie/data/sample.log. 
- Criar uma tabela do Banco de Dados SQL para armazenar os dados de exportação do Sqoop. O nome da tabela é *log4jLogCount*.

**Compreender o armazenamento do HDInsight**

O HDInsight usa o Armazenamento de Blob do Azure para armazenamento de dados. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][hdinsight-storage].

Ao provisionar um cluster HDInsight, uma conta de Armazenamento do Azure e um contêiner de blob específico dessa conta são designados como o sistema de arquivos padrão, exatamente como no HDFS. Além dessa conta de armazenamento, você pode adicionar mais contas de armazenamento da mesma assinatura do Azure ou de diferentes assinaturas do Azure durante o processo de provisionamento. Para saber mais sobre como adicionar mais contas de armazenamento, confira [Provisionar clusters Hadoop no HDInsight][hdinsight-provision]. Para simplificar o script do Windows PowerShell usado neste tutorial, todos os arquivos são armazenados no contêiner do sistema de arquivos padrão, localizado em */tutoriais/useoozie*. Por padrão, esse contêiner tem o mesmo nome do cluster HDInsight. A sintaxe do é:

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE]No cluster HDInsight versão 3.0, há suporte apenas para a sintaxe *wasb://*. A antiga sintaxe *asv://* tem suporte em clusters HDInsight 2.1 e 1.6, mas não tem suporte em clusters HDInsight 3.0.

> [AZURE.NOTE]O caminho wasb:// é um caminho virtual. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][hdinsight-storage].

Um arquivo armazenado no contêiner do sistema de arquivos padrão pode ser acessado no HDInsight usando qualquer um dos seguintes URIs (usando workflow.xml como um exemplo):

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
	wasb:///tutorials/useoozie/workflow.xml
	/tutorials/useoozie/workflow.xml

Se você desejar acessar o arquivo diretamente da conta de armazenamento, o nome do blob para o arquivo será:

	tutorials/useoozie/workflow.xml

**Compreender a tabela interna e a tabela externa do Hive**

Existem algumas coisas que você precisa saber sobre as tabelas interna e externa do Hive:

- O comando CREATE TABLE cria uma tabela interna, também conhecida como uma tabela gerenciada. O arquivo de dados deve estar localizado no contêiner padrão.
- O comando CREATE TABLE move o arquivo de dados para a pasta /hive/warehouse/<TableName> no contêiner padrão.
- O comando CREATE EXTERNAL TABLE cria uma tabela externa. O arquivo de dados pode estar localizado fora do contêiner padrão.
- O comando CREATE EXTERNAL TABLE não move o arquivo de dados.
- O comando CREATE EXTERNAL TABLE não permite nenhuma subpasta dentro da pasta especificada na cláusula LOCATION. Essa é a razão pela qual o tutorial faz uma cópia do arquivo sample.log.

Para saber mais, confira [HDInsight: Introdução às tabelas internas e externas do Hive][cindygross-hive-tables].

**Para preparar-se para o tutorial**

1. Abra o ISE do Windows PowerShell. (Na tela Iniciar do Windows 8, digite **PowerShell_ISE** e, em seguida, clique em **Windows PowerShell ISE**. Confira [Iniciar o Windows PowerShell no Windows 8 e no Windows][powershell-start]) para saber mais.
2. No painel inferior, execute o seguinte comando para se conectar à sua assinatura do Azure:

		Add-AzureAccount

	Será solicitado que você insira suas credenciais de conta do Azure. Esse método de adicionar uma conexão de assinatura expira e, depois de 12 horas, você precisará executar o cmdlet novamente.

	> [AZURE.NOTE]Se você tiver várias assinaturas do Azure e a assinatura padrão não for a que você deseja usar, use o cmdlet <strong>Select-AzureSubscription</strong> para selecionar a assinatura atual.

3. Copie o seguinte script no painel de script e defina as seis primeiras variáveis:
			
		# WASB variables
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<BlobStorageContainerName>"
		
		# SQL database variables
		$sqlDatabaseServer = "<SQLDatabaseServerName>"  
		$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
		$sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
		$sqlDatabaseName = "<SQLDatabaseName>"  
		$sqlDatabaseTableName = "log4jLogsCount"
		
		# Oozie files for the tutorial	
		$workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
		$hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"
		
		# WASB folder for storing the Oozie tutorial files.
		$destFolder = "tutorials/useoozie"  # Do NOT use the long path here


	Para obter mais descrições das variáveis, consulte a seção [Pré-requisitos](#prerequisites) deste tutorial.

3. Acrescente o seguinte ao script no painel de script:
		
		# Create a storage context object
		$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
		
		function uploadOozieFiles()
		{		
		    Write-Host "Copy workflow definition and HiveQL script file ..." -ForegroundColor Green
			Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
			Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
		}
				
		function prepareHiveDataFile()
		{
			Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
			Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
		}
				
		function prepareSQLDatabase()
		{
			# SQL query string for creating log4jLogsCount table
			$cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
				    [Level] [nvarchar](10) NOT NULL,
				    [Total] float,
				CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
				(
				[Level] ASC
				)
				)"
				
			#Create the log4jLogsCount table
		    Write-Host "Create Log4jLogsCount table ..." -ForegroundColor Green
			$conn = New-Object System.Data.SqlClient.SqlConnection
			$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
			$conn.open()
			$cmd = New-Object System.Data.SqlClient.SqlCommand
			$cmd.connection = $conn
			$cmd.commandtext = $cmdCreateLog4jCountTable
			$cmd.executenonquery()
				
			$conn.close()
		}
				
		# upload workflow.xml, coordinator.xml, and ooziewf.hql
		uploadOozieFiles;
				
		# make a copy of example/data/sample.log to example/data/log4j/sample.log
		prepareHiveDataFile;
		
		# create log4jlogsCount table on SQL database
		prepareSQLDatabase;

4. Clique em **Executar Script** ou pressione **F5** para executar o script. A saída será semelhante a:

	![Saída de preparação do tutorial][img-preparation-output]

##Executar o projeto Oozie

Atualmente, o PowerShell do Azure não fornece nenhum cmdlet para definir trabalhos do Oozie. Você pode usar o cmdlet **Invoke-RestMethod** para invocar os serviços Web do Oozie. A API de Serviços Web do Oozie é uma API REST HTTP JSON. Para saber mais sobre a API de Serviços Web do Oozie, confira a [documentação do Apache Oozie 4.0][apache-oozie-400] (para a versão 3.0 do cluster HDInsight) ou a [documentação do Oozie Apache 3.3.2][apache-oozie-332] (para a versão 2.1 do cluster HDInsight).

**Para enviar um trabalho do Oozie**

1. Abra o ISE do Windows PowerShell. (Na tela Iniciar do Windows 8, digite **PowerShell_ISE** e, em seguida, clique em **Windows PowerShell ISE**. Confira [Iniciar o Windows PowerShell no Windows 8 e no Windows][powershell-start]) para saber mais.

3. Copie o script a seguir no painel de script e defina as dez primeiras variáveis (ignore a variável $storageUri).

		#HDInsight cluster variables
		$clusterName = "<HDInsightClusterName>"
		$clusterUsername = "<HDInsightClusterUsername>"
		$clusterPassword = "<HDInsightClusterUserPassword>"
		
		#Azure Blob storage variables
		$storageAccountName = "<StorageAccountName>"
		$storageContainerName = "<BlobContainerName>"
		$storageUri="wasb://$storageContainerName@$storageAccountName.blob.core.windows.net"
		
		#Azure SQL database variables
		$sqlDatabaseServer = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
		$sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
		$sqlDatabaseName = "<SQLDatabaseName>"  
		
		#Oozie WF variables
		$oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
		$waitTimeBetweenOozieJobStatusCheck=10
		
		#Hive action variables
		$hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
		$hiveTableName = "log4jlogs"
		$hiveDataFolder = "$storageUri/tutorials/useoozie/data"
		$hiveOutputFolder = "$storageUri/tutorials/useoozie/output"
		
		#Sqoop action variables
		$sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"
		$sqlDatabaseTableName = "log4jLogsCount"

		$passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)


	Para obter mais descrições das variáveis, consulte a seção [Pré-requisitos](#prerequisites) deste tutorial.

3. Acrescente o texto a seguir ao script. Esse script define a carga do Oozie.
		
		#OoziePayload used for Oozie web service submission
		$OoziePayload =  @"
		<?xml version="1.0" encoding="UTF-8"?>
		<configuration>
		
		   <property>
		       <name>nameNode</name>
		       <value>$storageUrI</value>
		   </property>
		
		   <property>
		       <name>jobTracker</name>
		       <value>jobtrackerhost:9010</value>
		   </property>
		
		   <property>
		       <name>queueName</name>
		       <value>default</value>
		   </property>
		
		   <property>
		       <name>oozie.use.system.libpath</name>
		       <value>true</value>
		   </property>
		
		   <property>
		       <name>hiveScript</name>
		       <value>$hiveScript</value>
		   </property>
		
		   <property>
		       <name>hiveTableName</name>
		       <value>$hiveTableName</value>
		   </property>
		
		   <property>
		       <name>hiveDataFolder</name>
		       <value>$hiveDataFolder</value>
		   </property>
		
		   <property>
		       <name>hiveOutputFolder</name>
		       <value>$hiveOutputFolder</value>
		   </property>
		
		   <property>
		       <name>sqlDatabaseConnectionString</name>
		       <value>&quot;$sqlDatabaseConnectionString&quot;</value>
		   </property>
		
		   <property>
		       <name>sqlDatabaseTableName</name>
		       <value>$SQLDatabaseTableName</value>
		   </property>
		
		   <property>
		       <name>user.name</name>
		       <value>$clusterUsername</value>
		   </property>
		
		   <property>
		       <name>oozie.wf.application.path</name>
		       <value>$oozieWFPath</value>
		   </property>
		
		</configuration>
		"@
		
4. Acrescente o texto a seguir ao script. Esse script verifica o status do serviço Web do Oozie.
			
	    Write-Host "Checking Oozie server status..." -ForegroundColor Green
	    $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
	    $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus 
	    
	    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	    $oozieServerSatus = $jsonResponse[0].("systemMode")
	    Write-Host "Oozie server status is $oozieServerSatus..."
	
5. Acrescente o texto a seguir ao script. Esta parte cria e inicia um trabalho do Oozie:

	    # create Oozie job
	    Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
	    Write-Host "`n--------`n$OoziePayload`n--------"
	    $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
	    $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName #-debug
	
	    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	    $oozieJobId = $jsonResponse[0].("id")
	    Write-Host "Oozie job id is $oozieJobId..."
	
	    # start Oozie job
	    Write-Host "Starting the Oozie job $oozieJobId..." -ForegroundColor Green
	    $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=start"
	    $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders #-debug
		
6. Acrescente o texto a seguir ao script. Esse script verifica o status do trabalho do Oozie.

	    # get job status
	    Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
	    Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
	
	    Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
	    $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
	    $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds 
	    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	    $JobStatus = $jsonResponse[0].("status")
	
	    while($JobStatus -notmatch "SUCCEEDED|KILLED")
	    {
	        Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
	        Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
	        $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds 
	        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	        $JobStatus = $jsonResponse[0].("status")
	    }
	
	    Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!" -ForegroundColor Green

7. Se seu cluster HDinsight for versão 2.1, substitua "https://$clusterName.azurehdinsight.net:443/oozie/v2/" por "https://$clusterName.azurehdinsight.net:443/oozie/v1/". A versão 2.1 do cluster HDInsight não oferece suporte à versão 2 dos serviços web.

8. Clique em **Executar Script** ou pressione **F5** para executar o script. A saída será semelhante a:

	![Saída do fluxo de trabalho da execução do tutorial][img-runworkflow-output]

8. Conecte-se ao Banco de Dados SQL do Azure para ver os dados exportados.

**Para verificar o log de erros do trabalho**

Para solucionar problemas de um fluxo de trabalho, o arquivo de log Oozie pode ser encontrado em *C:\\apps\\dist\\oozie-3.3.2.1.3.2.0-05\\oozie-win-distro\\logs\\Oozie.log* ou em *C:\\apps\\dist\\oozie-4.0.0.2.0.7.0-1528\\oozie-win-distro\\logs\\Oozie.log* no nó principal do cluster. Para informações sobre RDP, confira [Gerenciar clusters Hadoop no HDInsight usando o Portal do Azure][hdinsight-admin-portal].

**Para executar o tutorial novamente**

Para executar novamente o fluxo de trabalho, execute o seguinte procedimento:

- Exclua o arquivo de saída do script do Hive
- Exclua os dados na tabela log4jLogsCount

Este é um exemplo de script do Windows PowerShell que você pode usar:

	$storageAccountName = "<AzureStorageAccountName>"
	$containerName = "<ContainerName>"
	
	#SQL database variables
	$sqlDatabaseServer = "<SQLDatabaseServerName>"
	$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
	$sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>"
	$sqlDatabaseName = "<SQLDatabaseName>"
	$sqlDatabaseTableName = "log4jLogsCount"
	
	Write-host "Delete the Hive script output file ..." -ForegroundColor Green
	$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
	Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName
	
	Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
	$conn = New-Object System.Data.SqlClient.SqlConnection
	$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
	$conn.open()
	$cmd = New-Object System.Data.SqlClient.SqlCommand
	$cmd.connection = $conn
	$cmd.commandtext = "delete from $sqlDatabaseTableName"
	$cmd.executenonquery()
	
	$conn.close()


##Próximas etapas
Neste tutorial, você aprendeu a definir um fluxo de trabalho do Oozie e a executar um trabalho do Oozie usando o Windows PowerShell. Para saber mais, consulte os seguintes artigos:

- [Usar o Coordenador baseado em tempo do Oozie com o HDInsight][hdinsight-oozie-coordinator-time]
- [Introdução ao uso do Hadoop com o Hive no HDInsight para analisar o uso de fone de celular][hdinsight-get-started]
- [Introdução ao emulador do HDInsight][hdinsight-get-started-emulator]
- [Usar o armazenamento de blobs do Azure com o HDInsight][hdinsight-storage]
- [Administrar o HDInsight usando o PowerShell][hdinsight-admin-powershell]
- [Carregar dados para trabalhos do Hadoop no HDInsight][hdinsight-upload-data]
- [Usar Sqoop com Hadoop no HDInsight][hdinsight-use-sqoop]
- [Usar o Hive com Hadoop no HDInsight][hdinsight-use-hive]
- [Usar o Pig com Hadoop no HDInsight][hdinsight-use-pig]
- [Desenvolver trabalhos de streaming do Hadoop em C# para o HDInsight][hdinsight-develop-streaming-jobs]
- [Desenvolver programas Java MapReduce para o HDInsight][hdinsight-develop-mapreduce]


[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563



[azure-data-factory-pig-hive]: ./data-factory/data-factory-pig-hive-activities.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: hdinsight-get-started-emulator.md

[hdinsight-develop-streaming-jobs]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md

[sqldatabase-create-configue]: sql-database-create-configure.md
[sqldatabase-get-started]: sql-database-get-started.md

[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: powershell-install-configure.md
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/pt-br/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

<!--HONumber=54-->