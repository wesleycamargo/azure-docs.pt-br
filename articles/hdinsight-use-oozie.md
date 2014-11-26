<properties urlDisplayName="Use Hadoop Oozie in HDInsight" pageTitle="Usar o Oozie do Hadoop no HDInsight | Azure" metaKeywords="" description="Use Hadoop Oozie in HDInsight, a big data solution. Learn how to define an Oozie workflow, and submit an Oozie job." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Hadop Oozie in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />


# Usar o Oozie com o Hadoop no HDInsight

Saiba como definir um fluxo de trabalho e como executar o fluxo de trabalho no HDInsight. Para saber o coordenador do Oozie, consulte [Usar o Coordenador baseado em tempo do Oozie com o HDInsight][hdinsight-oozie-coordinator-time].



**Tempo estimado para conclusão:** 40 minutos

##Neste artigo

0. [O que é o Oozie](#whatisoozie)
1. [Pré-requisitos](#prerequisites)
2. [Definir arquivo de fluxo de trabalho Oozie](#defineworkflow)
2. [Implantar o projeto Oozie e preparar-se para o tutorial](#deploy)
3. [Executar o fluxo de trabalho](#run)
4. [Próximas etapas](#nextsteps)

##<a id="whatisoozie"></a>O que é o Oozie

O Apache Oozie é um sistema de fluxo de trabalho/coordenação que gerencia trabalhos do Hadoop. É integrado com a pilha do Hadoop e oferece suporte a trabalhos do Hadoop para o Apache MapReduce, Apache Pig, Apache Hive e Apache Sqoop. Também pode ser usado para agendar trabalhos específicos para um sistema, como programas Java ou scripts de shell.

O fluxo de trabalho que você implementará contém duas ações: 

![Workflow diagram][img-workflow-diagram]

1. Uma ação do Hive executa um script do HiveQL para contar as ocorrências de cada tipo de nível de log em um arquivo de log log4j. Cada log log4j consiste em uma linha de campos que contém um campo [NÍVEL DE LOG] para mostrar o tipo e a gravidade. Por exemplo:

		2012-02-03 18:35:34 SampleClass6 [INFO] tudo o que é normal para a id de 577725851
		2012-02-03 18:35:34 SampleClass4 [FATAL] problema no sistema na ID 1991281254
		2012-02-03 18:35:34 SampleClass3 [DEBUG] detalhes da ID 1304807656
		...

	A saída do script do Hive é semelhante a:
	
		[DEBUG] 434
		[ERROR] 3
		[FATAL] 1
		[INFO]  96
		[TRACE] 816
		[WARN]  4

	Para obter mais informações sobre o Hive, consulte [Use o hive com o HDInsight][hdinsight-use-hive].
	
2.  Uma ação do Sqoop exporta a saída da ação do HiveQL para uma tabela no banco de dados SQL do Azure. Para obter mais informações sobre o Sqoop, consulte [Use o Sqoop com o HDInsight][hdinsight-use-sqoop].

> [WACOM.NOTE] Para versões do Oozie com suporte nos clusters HDInsight, consulte [O que há de novo nas versões de cluster fornecidas pelo HDInsight?][hdinsight-versions].

> [WACOM.NOTE] Este tutorial funciona com o cluster HDInsight 2.1 e 3.0. Este artigo não foi testado no emulador do HDInsight.


##<a id="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Uma estação de trabalho** com o PowerShell do Azure instalado e configurado. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure]. Para executar scripts do PowerShell, você deve executar o PowerShell do Azure como administrador e configurar a política de execução como *RemoteSigned*. Consulte [Executar scripts do Windows PowerShell][powershell-script].
- **Um cluster HDInsight**. Para obter informações sobre como criar um cluster HDInsight, consulte [Provisão de clusters HDInsight][hdinsight-provision]ou [Introdução ao HDInsight][hdinsight-get-started]. Você precisará dos seguintes dados para percorrer o tutorial:

	<table border = "1">
	<tr><th>Propriedade do cluster</th><th>Nome de variável do PowerShell</th><th>Valor</th><th>Descrição</th></tr>
	<tr><td>Nome do cluster HDInsight</td><td>$clusterName</td><td></td><td>O cluster HDInsight em que você executará este tutorial.</td></tr>
	<tr><td>Nome de usuário do cluster HDInsight</td><td>$clusterUsername</td><td></td><td>O nome de usuário do cluster HDInsight. </td></tr>
	<tr><td>Senha de usuário do cluster HDInsight </td><td>$clusterPassword</td><td></td><td>A senha de usuário do cluster HDInsight.</td></tr>
	<tr><td>Nome da conta de armazenamento do Azure</td><td>$storageAccountName</td><td></td><td>Uma conta de armazenamento do Azure disponível para o cluster HDInsight. Para este tutorial, use a conta de armazenamento padrão especificada durante o processo de provisionamento do cluster.</td></tr>
	<tr><td>Nome do contêiner de blob do Azure</td><td>$containerName</td><td></td><td>Para este exemplo, utilize o contêiner de armazenamento de blob do Azure usado para o sistema de arquivos do cluster HDInsight padrão. Por padrão, o contêiner tem o mesmo nome do cluster HDInsight.</td></tr>
	</table>

- **Um Banco de Dados SQL Azure**. Você deve configurar uma regra de firewall para o servidor de Banco de Dados SQL para permitir o acesso a partir de sua estação de trabalho. Para obter instruções sobre como criar um banco de dados SQL e configurar o firewall, consulte [Introdução ao uso do banco de dados do SQL Azure][sqldatabase-get-started]. Este artigo fornece um script do PowerShell para criar a tabela do Banco de Dados SQL necessária para este tutorial. 

	<table border = "1">
	<tr><th>Propriedade de banco de dados SQL</th><th>Nome de variável do PowerShell</th><th>Valor</th><th>Descrição</th></tr>
	<tr><td>Nome do servidor de banco de dados SQL</td><td>$sqlDatabaseServer</td><td></td><td>O servidor do Banco de Dados SQL para o qual o Sqoop exportará os dados. </td></tr>
	<tr><td>Nome de logon do banco de dados SQL</td><td>$sqlDatabaseLogin</td><td></td><td>Nome de logon do banco de dados SQL.</td></tr>
	<tr><td>Senha de logon do banco de dados SQL</td><td>$sqlDatabaseLoginPassword</td><td></td><td>Senha de logon do banco de dados SQL.</td></tr>
	<tr><td>Nome do banco de dados SQL</td><td>$sqlDatabaseName</td><td></td><td>O Banco de Dados SQL do Azure para o qual o Sqoop exportará dados. </td></tr>
	</table>

	> [WACOM.NOTE] Por padrão, um Banco de Dados SQL do Azure permite conexões de serviços do Azure, como o Azure HDInsight. Se essa configuração de firewall estiver desabilitada, você deverá habilitá-la no Portal de Gerenciamento do Azure. Para obter instruções sobre como criar um banco de dados SQL e configurar regras do firewall, consulte [Criar e configurar o Banco de Dados SQL][sqldatabase-create-configue]. 


> [WACOM.NOTE] Preencha os valores nas tabelas.  Isso poderá ser útil para percorrer este tutorial.


##<a id="defineworkflow"></a>Definir o fluxo de trabalho do Oozie e o script HiveQL relacionado

As definições de fluxos de trabalho do Oozie são escritas em hPDL (uma Linguagem de Definição de Processo XML). O nome do arquivo do fluxo de trabalho padrão é *workflow.xml*.  Você salvará o arquivo do fluxo de trabalho localmente e o implantará no cluster HDInsight usando o PowerShell do Azure posteriormente neste tutorial.

A ação do Hive no fluxo de trabalho chama um arquivo de script HiveQL. Esse arquivo de script contém três instruções HiveQL:

1. **A instrução DROP TABLE** exclui a tabela Hive log4j caso ela exista.
2. **A instrução CREATE TABLE** cria uma tabela externa Hive log4j apontando para o local do arquivo de log log4j. O delimitador de campo é ",". O delimitador de linha padrão é "\n".  A tabela externa Hive é usada para evitar que o arquivo de dados seja removido do local original, caso você deseje executar o fluxo de trabalho do Oozie várias vezes.
3. **A instrução INSERT OVERWRITE** conta as ocorrências de cada tipo de nível de log da tabela Hive log4j e salva a saída em um local do Armazenamento do Azure - Blob (WASB). 

Há um erro conhecido do caminho do Hive. Você encontrará esse problema ao enviar um trabalho do Oozie. As instruções para corrigir o problema podem ser encontradas no [TechNet Wiki][technetwiki-hive-error].

**Para definir o arquivo de script HiveQL a ser chamado pelo fluxo de trabalho:**

1. Crie um arquivo de texto com o seguinte conteúdo:

		DROP TABLE ${hiveTableName};
		CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
		INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

	Existem três variáveis usadas no script:

	- ${hiveTableName}
	- ${hiveDataFolder}
	- ${hiveOutputFolder}
			
	O arquivo de definição do fluxo de trabalho (workflow.xml neste tutorial) irá passar esses valores para esse script HiveQL em tempo de execução.
		
2. Salve o arquivo como **C:\Tutorials\UseOozie\useooziewf.hql** usando a codificação **ANSI (ASCII)** (use o Bloco de Notas se o seu editor de texto não fornecer a opção). Esse arquivo de script será implantado no cluster HDInsight posteriormente no tutorial.



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

	Existem duas ações definidas no fluxo de trabalho. A ação de início é *RunHiveScript* Se a ação for executada *ok*, a próxima ação será *RunSqoopExport*.

	O RunHiveScript possui várias variáveis. Você irá passar os valores ao enviar o trabalho do Oozie de sua estação de trabalho usando o PowerShell do Azure.

	<table border = "1">
	<tr><th>Variáveis de fluxo de trabalho</th><th>Descrição</th></tr>
	<tr><td>${jobTracker}</td><td>Especifique a URL do controlador do trabalho do Hadoop. Use <strong>jobtrackerhost:9010</strong> nos clusters HDInsight versão 2.0 e 3.0.</td></tr>
	<tr><td>${nameNode}</td><td>Especifique a URL do namenode do Hadoop. Use o endereço WASB do sistema de arquivos padrão. Por exemplo, <i>wasb://&lt;containerName&gt;@&lt;storageAccountName&gt;.blob.core.windows.net</i>.</td></tr>
	<tr><td>${queueName}</td><td>Especifica o queuename ao qual o trabalho será enviado. Use <strong>padrão</strong>.</td></tr>
	</table>

	<table border = "1">
	<tr><th>Variável de ação do Hive</th><th>Descrição</th></tr>
	<tr><td>${hiveDataFolder}</td><td>O diretório de origem do comando Hive Create Table.</td></tr>
	<tr><td>${hiveOutputFolder}</td><td>A pasta de saída da instrução INSERT OVERWRITE.</td></tr>
	<tr><td>${hiveTableName}</td><td>O nome da tabela Hive que faz referência aos arquivos de dados log4j.</td></tr>
	</table>

	<table border = "1">
	<tr><th>Variável de ação do Sqoop</th><th>Descrição</th></tr>
	<tr><td>${sqlDatabaseConnectionString}</td><td>Cadeia de conexão do Banco de Dados SQL.</td></tr>
	<tr><td>${sqlDatabaseTableName}</td><td>A tabela do Banco de Dados SQL para onde os dados serão exportados.</td></tr>
	<tr><td>${hiveOutputFolder}</td><td>A pasta de saída para a instrução Hive INSERT OVERWRITE. Essa é a mesma pasta para export-dir de Exportação do Sqoop.</td></tr>
	</table>

	Para obter mais informações sobre o fluxo de trabalho do Oozie e o uso das ações de fluxo de trabalho, consulte [Documentação do Apache Oozie 4.0][apache-oozie-400] (para a versão 3.0 do cluster HDInsight) ou [Documentação do Oozie Apache 3.3.2][apache-oozie-332] (para a versão 2.1 do cluster HDInsight).

2. Salve o arquivo como **C:\Tutorials\UseOozie\workflow.xml** usando a codificação ANSI (ASCII) (use o Bloco de Notas se o seu editor de texto não fornecer a opção).
	
##<a id="deploy"></a>Implantar o projeto Oozie e preparar-se para o tutorial

Você executará um script do PowerShell do Azure para realizar o seguinte:

- Copiar o armazenamento de Blob do Azure do script HiveQL (useoozie.hql), wasb:///tutorials/useoozie/useoozie.hql.
- Copiar workflow.xml para wasb:///tutorials/useoozie/workflow.xml.
- Copie o arquivo de dados (/example/data/sample.log) para wasb:///tutorials/useoozie/data/sample.log. 
- Criar uma tabela do Banco de Dados SQL para armazenar os dados de exportação do Sqoop.  O nome da tabela é *log4jLogCount*.

**Compreender o armazenamento do HDInsight**

O HDInsight usa o Armazenamento de Blob do Azure para armazenamento de dados.  Ele é chamado *WASB* ou *Armazenamento do Azure - Blob*. O WASB é a implementação do HDFS da Microsoft no Armazenamento de Blob do Azure. Para obter mais informações, consulte [Usar o armazenamento de blobs do Azure com o HDInsight][hdinsight-storage]. 

Ao provisionar um cluster HDInsight, uma conta de Armazenamento do Azure e um contêiner de Armazenamento de Blob específico dessa conta é designado como o sistema de arquivos padrão, exatamente como no HDFS. Além dessa conta de armazenamento, você pode adicionar mais contas de armazenamento da mesma assinatura do Azure ou de diferentes assinaturas do Azure durante o processo de provisionamento. Para obter instruções sobre como adicionar contas de armazenamento adicionais, consulte [Provisão de clusters HDInsight][hdinsight-provision]. Para simplificar o script do PowerShell usado neste tutorial, todos os arquivos são armazenados no contêiner do sistema de arquivos padrão, localizado em */tutoriais/useoozie*. Por padrão, esse contêiner tem o mesmo nome que o nome do cluster HDInsight. 
A sintaxe do WASB é:

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Apenas a sintaxe *wasb://* tem suporte no cluster HDInsight versão 3.0. A sintaxe antiga *asv://* tem suporte nos clusters HDInsight 2.1 e 1.6, mas não tem suporte nos clusters HDInsight 3.0 e não terá suporte em versões posteriores.

> [WACOM.NOTE] O caminho WASB é um caminho virtual.  Para obter mais informações, consulte [Usar o armazenamento de blobs do Azure com o HDInsight][hdinsight-storage]. 

Um arquivo armazenado no contêiner do sistema de arquivos padrão pode ser acessado no HDInsight usando qualquer um dos seguintes URIs (usando workflow.xml como um exemplo):

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
	wasb:///tutorials/useoozie/workflow.xml
	/tutorials/useoozie/workflow.xml

Se você desejar acessar o arquivo diretamente da conta de armazenamento, o nome do blob para o arquivo será:

	tutorials/useoozie/workflow.xml

**Compreender a tabela interna e a tabela externa do Hive**

Existem algumas coisas que você precisa saber sobre a tabela interna e a tabela externa do Hive:

- O comando CREATE TABLE cria uma tabela interna, também conhecida como uma tabela gerenciada. O arquivo de dados deve estar localizado no contêiner padrão.
- O comando CREATE TABLE move o arquivo de dados para a pasta /hive/warehouse/<TableName> no contêiner padrão.
- O comando CREATE EXTERNAL TABLE cria uma tabela externa. O arquivo de dados pode estar localizado fora do contêiner padrão.
- O comando CREATE EXTERNAL TABLE não move o arquivo de dados.
- O comando CREATE EXTERNAL TABLE não permite nenhuma subpasta dentro da pasta especificada na cláusula LOCATION. Essa é a razão pela qual o tutorial faz uma cópia do arquivo sample.log.

Para obter mais informações, consulte [HDInsight: Introdução às tabelas internas e externas do Hive][cindygross-hive-tables].

**Para preparar-se para o tutorial**

1. Abra o ISE do Windows PowerShell (na tela Iniciar do Windows 8, digite **PowerShell_ISE** e clique em **ISE do Windows PowerShell**. Consulte [Iniciar o Windows PowerShell no Windows 8 e no Windows][powershell-start]).
2. No painel inferior, execute o seguinte comando para se conectar à sua assinatura do Azure:

		Add-AzureAccount

	Será solicitado que você insira suas credenciais de conta do Azure. Esse método de adicionar uma conexão de assinatura expira e, depois de 12 horas, você precisará executar o cmdlet novamente. 

	> [WACOM.NOTE] Se você tiver várias assinaturas do Azure e a assinatura padrão não é aquela que deseja usar, use o cmdlet <strong>Select-AzureSubscription</strong> para selecionar a assinatura atual.

3. Copie o script a seguir no painel de script e defina as seis primeiras variáveis
			
		# Variáveis WASB
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<BlobStorageContainerName>"
		
		# Variáveis de banco de dados SQL
		$sqlDatabaseServer = "<SQLDatabaseServerName>"  
		$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
		$sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
		$sqlDatabaseName = "<SQLDatabaseName>"  
		$sqlDatabaseTableName = "log4jLogsCount"
		
		# Arquivos Oozie para o tutorial	
		$workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
		$hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"
		
		# Pasta de WASB para armazenar os arquivos do tutorial do Oozie.
		$destFolder = "tutorials/useoozie"  # NÃO use o caminho longo aqui


	Para obter mais descrições das variáveis, consulte a seção [Pré-requisitos](#prerequisites) neste tutorial. 

3. Acrescente o seguinte ao script no painel de script:
		
		# Criar um objeto de contexto de armazenamento
		$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
		
		function uploadOozieFiles()
		{		
		    Write-Host "Definição de fluxo de trabalho de cópia e o arquivo de script HiveQL..." -ForegroundColor Green
			Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
			Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
		}
				
		function prepareHiveDataFile()
		{
			Write-Host "Faça uma cópia do arquivo sample.log... " -ForegroundColor Green
			Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
		}
				
		function prepareSQLDatabase()
		{
			# Cadeia de caracteres de consulta de SQL para criar tabela log4jLogsCount
			$cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
				    [Nível] [nvarchar](10) NOT NULL,
				    [Total] float,
				CONSTRAINT [PK_$sqlDatabaseTableName] CHAVE PRIMÁRIA EM CLUSTER   
				(
				[Nível] ASC
				)
				)"
				
			#Criar a tabela log4jLogsCount
		    Write-Host "Criar a tabela Log4jLogsCount..." -ForegroundColor Green
			$conn = New-Object System.Data.SqlClient.SqlConnection
			$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
			$conn.open()
			$cmd = New-Object System.Data.SqlClient.SqlCommand
			$cmd.connection = $conn
			$cmd.commandtext = $cmdCreateLog4jCountTable
			$cmd.executenonquery()
				
			$conn.close()
		}
				
		# carregar workflow.xml, coordinator.xml e ooziewf.hql
		uploadOozieFiles;
				
		# fazer uma cópia de example/data/sample.log para example/data/log4j/sample.log
		prepareHiveDataFile;
		
		# criar tabela log4jlogsCount no banco de dados SQL
		prepareSQLDatabase;

4. Clique em **Executar Script** ou pressione **F5** para executar o script. A saída deverá ser similar a:

	![Tutorial preparation output][img-preparation-output]

##<a id="run"></a>Executar o projeto Oozie

Atualmente, o PowerShell do Azure não fornece nenhum cmdlet para definir trabalhos do Oozie. Você pode usar 
o cmdlet Invoke-RestMethod do PowerShell para invocar os serviços web do Oozie. A API de Serviços Web do Oozie é uma API JSON REST HTTP. Para obter mais informações sobre a API de serviços Web do Oozie, consulte [Documentação do Apache Oozie 4.0][apache-oozie-400] (para a versão 3.0 do cluster HDInsight) ou [Documentação do Oozie Apache 3.3.2][apache-oozie-332] (para a versão 2.1 do cluster HDInsight).

**Para enviar um trabalho do Oozie**

1. Abra o ISE do Windows PowerShell (na tela Iniciar do Windows 8, digite **PowerShell_ISE** e clique em **ISE do Windows PowerShell**. Consulte [Iniciar o Windows PowerShell no Windows 8 e no Windows][powershell-start]).

3. Copie o script a seguir no painel de script e defina as dez primeiras variáveis (ignore a 6ª, $storageUri). 

		#Variáveis de cluster #HDInsight
		$clusterName = "<HDInsightClusterName>"
		$clusterUsername = "<HDInsightClusterUsername>"
		$clusterPassword = "<HDInsightClusterUserPassword>"
		
		#Variáveis de armazenamento (WASB) de blob do Azure
		$storageAccountName = "<StorageAccountName>"
		$storageContainerName = "<BlobContainerName>"
		$storageUri="wasb://$storageContainerName@$storageAccountName.blob.core.windows.net"
		
		#Variáveis de banco de dados SQL do Azure
		$sqlDatabaseServer = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
		$sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
		$sqlDatabaseName = "<SQLDatabaseName>"  
		
		#Variáveis do Oozie WF
		$oozieWFPath="$storageUri/tutorials/useoozie"  # O nome padrão é o workflow.xml. E você não precisa especificar o nome do arquivo.
		$waitTimeBetweenOozieJobStatusCheck=10
		
		#Variável de ação do Hive
		$hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
		$hiveTableName = "log4jlogs"
		$hiveDataFolder = "$storageUri/tutorials/useoozie/data"
		$hiveOutputFolder = "$storageUri/tutorials/useoozie/output"
		
		#Variável de ação do Sqoop
		$sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"
		$sqlDatabaseTableName = "log4jLogsCount"

		$passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)


	Para obter mais descrições das variáveis, consulte a seção [Pré-requisitos](#prerequisites) neste tutorial.

3. Acrescente o texto a seguir ao script. Esta parte define a carga do Oozie:
		
		#OoziePayload usado para envio de serviço web Oozie
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
		
4. Acrescente o texto a seguir ao script. Esta parte verifica o status do serviço web do Oozie:	
			
	    Write-Host "Verificando o status do servidor Oozie..." -ForegroundColor Green
	    $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
	    $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus 
	    
	    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	    $oozieServerSatus = $jsonResponse[0].("systemMode")
	    Write-Host "O status do servidor de Oozie é $oozieServerSatus..."
	
5. Acrescente o texto a seguir ao script. Esta parte cria e inicia um trabalho do Oozie:	

	    # criar o trabalho do Oozie
	    Write-Host "Enviar a seguinte carga ao cluster:" -ForegroundColor Green
	    Write-Host "`n--------`n$OoziePayload`n--------"
	    $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
	    $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName #-debug
	
	    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	    $oozieJobId = $jsonResponse[0].("id")
	    Write-Host "A ID do trabalho Oozie é $oozieJobId..."
	
	    # iniciar trabalho do Oozie
	    Write-Host "Iniciando o trabalho do Oozie $oozieJobId..." -ForegroundColor Green
	    $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=start"
	    $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders #-debug
		
6. Acrescente o texto a seguir ao script. Esta parte verifica o status do trabalho do Oozie:		

	    # obter status do trabalho
	    Write-Host "Espera por $waitTimeBetweenOozieJobStatusCheck segundos até que os metadados de trabalho sejam preenchidos no metastore do Oozie..." -ForegroundColor Green
	    Start-Sleep - $waitTimeBetweenOozieJobStatusCheck de segundos
	
	    Write-Host "Obter o status do trabalho e aguardar o trabalho seja concluído..." -ForegroundColor Green
	    $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
	    $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds 
	    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	    $JobStatus = $jsonResponse[0].("status")
	
	    while($JobStatus -notmatch "SUCCEEDED|KILLED")
	    {
	        Write-Host "$(Get-Date-formato 'G'): $oozieJobId está no estado de $JobStatus... espera $waitTimeBetweenOozieJobStatusCheck segundos para que o trabalho termine..."
	        Start-Sleep - $waitTimeBetweenOozieJobStatusCheck de segundos
	        $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds 
	        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	        $JobStatus = $jsonResponse[0].("status")
	    }
	
	    Write-Host "$(Get-Date-formato 'G'): $oozieJobId está em estado $JobStatus!" -ForegroundColor Green

7. Se seu cluster HDinsight for versão 2.1, substitua "https://$clusterName.azurehdinsight.net:443/oozie/v2/" por "https://$clusterName.azurehdinsight.net:443/oozie/v1/". A versão 2.1 do cluster HDInsight não oferece suporte à versão 2 dos serviços web.

8. Clique em **Executar Script** ou pressione **F5** para executar o script. A saída será semelhante a:

	![Tutorial run workflow output][img-runworkflow-output]

8. Conecte-se ao Banco de Dados SQL para ver os dados exportados.

**Para verificar o log de erros do trabalho**

Para solucionar problemas do fluxo de trabalho, o arquivo de log do Oozie pode ser localizado em 
*C:\apps\dist\oozie-3.3.2.1.3.2.0-05\oozie-win-distro\logs\Oozie.log* ou *C:\apps\dist\oozie-4.0.0.2.0.7.0-1528\oozie-win-distro\logs\Oozie.log* do headnode do cluster. Para obter informações sobre o RDP, consulte [Administrando clusters HDInsight usando o Portal de Gerenciamento][hdinsight-admin-portal].

**Para executar o tutorial novamente**

Para executar novamente o fluxo de trabalho, execute o seguinte procedimento:

- exclua o arquivo de saída do script do Hive
- exclua os dados na tabela log4jLogsCount

Este é um exemplo de script do PowerShell que você pode usar:

	$storageAccountName = "<AzureStorageAccountName>"
	$containerName = "<ContainerName>"
	
	# Variáveis de banco de dados SQL
	$sqlDatabaseServer = "<SQLDatabaseServerName>"
	$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
	$sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>"
	$sqlDatabaseName = "<SQLDatabaseName>"
	$sqlDatabaseTableName = "log4jLogsCount"
	
	Write-host "Excluir o arquivo de saída do script do Hive..." -ForegroundColor Green
	$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
	Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName
	
	Write-host "Excluir todos os registros da tabela log4jLogsCount..." -ForegroundColor Green
	$conn = New-Object System.Data.SqlClient.SqlConnection
	$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
	$conn.open()
	$cmd = New-Object System.Data.SqlClient.SqlCommand
	$cmd.connection = $conn
	$cmd.commandtext = "excluir de $sqlDatabaseTableName"
	$cmd.executenonquery()
	
	$conn.close()


##<a id="nextsteps"></a>Próximas etapas
Neste tutorial, você aprendeu como definir um fluxo de trabalho do Oozie e como executar um trabalho do Oozie usando o PowerShell do Azure. Para saber mais, consulte os seguintes artigos:

- [Usar o Coordenador baseado em tempo do Oozie com o HDInsight][hdinsight-oozie-coordinator-time]
- [Introdução ao HDInsight][hdinsight-get-started]
- [Introdução ao emulador do HDInsight][hdinsight-get-started-emulator]
- [Usar o armazenamento de blobs do Azure com o HDInsight][hdinsight-storage]
- [Administrar o HDInsight usando o PowerShell][hdinsight-admin-powershell]
- [Carregar dados no HDInsight][hdinsight-upload-data]
- [Use o Sqoop com o HDInsight][hdinsight-use-sqoop]
- [Use o hive com o HDInsight][hdinsight-use-hive]
- [Use o Pig com o HDInsight][hdinsight-use-pig]
- [Desenvolver trabalhos de streaming do Hadoop em C# para o HDInsight][hdinsight-develop-streaming-jobs]
- [Desenvolver programas Java MapReduce para HDInsight][hdinsight-develop-mapreduce]


[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563



[hdinsight-oozie-coordinator-time]: ../hdinsight-use-oozie-coordinator-time/
[hdinsight-versions]:  ../hdinsight-component-versioning/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/


[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-get-started-emulator]: ../hdinsight-get-started-emulator/

[hdinsight-develop-streaming-jobs]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/

[sqldatabase-create-configue]: ../sql-database-create-configure/
[sqldatabase-get-started]: ../sql-database-get-started/

[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/ 

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/pt-br/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: ../install-and-configure-powershell/
[powershell-start]: http://technet.microsoft.com/pt-br/library/hh847889.aspx
[powershell-script]: http://technet.microsoft.com/pt-br/library/ee176949.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png  
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png 

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
