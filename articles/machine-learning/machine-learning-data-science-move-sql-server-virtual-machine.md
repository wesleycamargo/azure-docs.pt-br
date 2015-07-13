<properties 
	pageTitle="Mover dados para o SQL Server no Azure| Microsoft Azure" 
	description="Mover dados para o SQL Server no Azure" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="msolhab" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="fashah;garye;mohabib;bradsev" />

#Mover dados para o SQL Server no Azure

Este documento aborda a movimentação de dados de arquivos simples (csv/tsv) ou um SQL Server local para um SQL Server no Azure. Essa tarefa faz parte do ADAPT (Processo e Tecnologia de Análise Avançada) fornecido pelo Aprendizado de Máquina do Azure.


<table>

<tr>
<td><b>ORIGEM</b></td>
<td colspan="2" align="center"><b>DESTINO</b></td>
</tr>

<tr>
  <td></td>
  <td><b>VM do SQL Server no Azure</b></td>
</tr>

<tr>
  <td><b>Arquivo simples</b></td>  
  <td>
    1. <a href="#insert-tables-bcp">Utilitário de cópia em massa da linha de comando (BCP) </a><br>
    2. <a href="#insert-tables-bulkquery">Consulta SQL de inserção em massa </a><br>
    3. <a href="#sql-builtin-utilities">Utilitários gráficos internos no SQL Server </a>
  </td>
</tr>
<tr>
  <td><b>SQL Server local</b></td>
  <td>
    1. <a href="#export-flat-file">Exportar para um arquivo simples </a><br>
    2. <a href="#sql-migration">Assistente de Migração de Banco de Dados SQL </a> <br>    
    3. <a href="#sql-backup">Backup e restauração de banco de dados </a> <br>
  </td>
</tr>
</table>

Observe que este documento pressupõe que os comandos SQL sejam executados no SQL Server Management Studio ou no Gerenciador de Banco de Dados do Visual Studio.

> [AZURE.TIP]Como alternativa, você pode usar o [Azure Factory](https://azure.microsoft.com/pt-br/services/data-factory/) para criar e agendar um pipeline que move dados para uma VM do SQL Server no Azure. Para obter mais informações, consulte [Copiar dados com o Azure Data Factory (Atividade de Cópia)](../data-factory/data-factory-copy-activity.md).


## <a name="sqlonazurevm"></a>Movendo seus dados para uma VM do SQL Server no Azure

Esta seção documenta o processo de movimentar dados para uma VM do SQL Server no Azure. Se você não configurou a VM do SQL Server, provisione uma nova máquina virtual do SQL Server para análises avançadas, conforme descrito em [Configurar uma máquina virtual do SQL Server no Azure como um servidor IPython Notebook para análises avançadas](machine-learning-data-science-setup-sql-server-virtual-machine.md).

Este documento descreve a movimentação de dados das seguintes fontes de dados:
  
1. [De arquivos simples](#filesource_to_sqlonazurevm) 
2. [De um SQL Server local](#sqlonprem_to_sqlonazurevm)


### <a name="filesource_to_sqlonazurevm"></a>Origem do arquivo

Se os dados estiverem em um arquivo simples (organizado em um formato de linha/coluna), ele pode ser movido para a VM do SQL Server no Azure pelos métodos a seguir:

1. [Utilitário de cópia em massa de linha de comando (BCP)](#insert-tables-bcp) 
2. [Consulta SQL de inserção em massa ](#insert-tables-bulkquery)
3. [Utilitários gráficos internos no SQL Server (Importar/Exportar, SSIS)](#sql-builtin-utilities)


### <a name="insert-tables-bcp"></a>Utilitário de cópia em massa de linha de comando (BCP)

O BCP é um utilitário de linha de comando instalado com o SQL Server e é uma das maneiras mais rápidas para mover os dados. Ele funciona em todas as três variantes do SQL Server (SQL Server local, SQL Azure e VM do SQL Server no Azure).

> [AZURE.NOTE]**Onde os dados devem estar para o BCP?** Embora não seja necessário, ter arquivos que contêm dados de origem localizados no mesmo computador que o SQL Server de destino possibilita transferências mais rápidas (velocidade rede em comparação com velocidade de E/S do disco local). Você pode mover os arquivos simples que contêm dados para máquina em que o SQL Server está instalado usando várias ferramentas de cópia de arquivo, como [AZCopy](../storage-use-azcopy.md), [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) ou copiar/colar do Windows via protocolo RDP.

1. Certifique-se de que o banco de dados e as tabelas foram criados no banco de dados do SQL Server de destino. Aqui está um exemplo de como fazer isso usando os comandos `Create Database` e `Create Table`:

		CREATE DATABASE <database_name>
	
		CREATE TABLE <tablename>
		(
			<columnname1> <datatype> <constraint>,
			<columnname2> <datatype> <constraint>,
			<columnname3> <datatype> <constraint>
		) 
	
2. Gere o arquivo de formato que descreve o esquema da tabela emitindo o comando a seguir na linha de comando do computador onde o bcp está instalado.

	`bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`

3. Insira os dados no banco de dados usando o comando bcp da seguinte maneira. Isso deve funcionar na linha de comando, supondo que o SQL Server está instalado no mesmo computador:

	`bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attemp -t \t -r \n`

> **Otimizando inserções de BCP** Consulte o artigo a seguir, ['Diretrizes para otimizar a importação de massa'](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx), para otimizar essas inserções.

#### <a name="insert-tables-bulkquery-parallel"></a>Paralelização de inserções para movimentação de dados mais rápida

Se os dados que você está movendo forem grandes, você pode acelerar as coisas executando simultaneamente vários comandos BCP em paralelo em um Script do PowerShell.

> [AZURE.NOTE]**Ingestão de big data** Para otimizar o carregamento de dados para conjuntos de dados grandes e muito grandes, particione suas tabelas de banco de dados lógicas e físicas usando várias tabelas de partição e grupos de arquivos. Para obter mais informações sobre como criar e carregar dados em tabelas de partição, consulte [Tabelas de partição do SQL de carga paralela](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).


O script do PowerShell de exemplo abaixo demonstra inserções paralelas usando bcp:
	
	$NO_OF_PARALLEL_JOBS=2

	 Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
	 # Define what each job does
	   $ScriptBlock = {
		   param($partitionnumber)

		   #Explictly using SQL username password
		   bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

			#Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
			#bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n 
	  }
	

	# Background processing of all partitions
	for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
	{
	  Write-Debug "Submit loading partition # $i"
	  Start-Job $ScriptBlock -Arg $i	  
	}
	

	# Wait for it all to complete
	While (Get-Job -State "Running")
	{
	  Start-Sleep 10
	  Get-Job
	}
	
	# Getting the information back from the jobs
	Get-Job | Receive-Job
	Set-ExecutionPolicy Restricted #reset the execution policy


### <a name="insert-tables-bulkquery"></a>Consulta SQL de inserção em massa

A [Consulta SQL de inserção em massa](https://msdn.microsoft.com/library/ms188365) pode ser usada para importar dados para o banco de dados de arquivos com base em linha/coluna (os tipos com suporte são abordados [aqui](https://msdn.microsoft.com/library/ms188609)).

Aqui estão alguns comandos de exemplo para inserção em massa:

1. Analise seus dados e defina as opções personalizadas antes da importação para certificar-se de que o banco de dados do SQL Server entende o mesmo formato para campos especiais, tal como datas. Aqui está um exemplo de como definir o formato de data como ano-mês-dia (se seus dados contiverem a data no formato de ano-mês-dia):

		SET DATEFORMAT ymd;	
	
2. Importe dados usando as instruções de importação em massa:

    	BULK INSERT <tablename>
    	FROM	
    	'<datafilename>'
    	WITH 
    	(
		FirstRow=2,
    	FIELDTERMINATOR =',', --this should be column separator in your data
    	ROWTERMINATOR ='\n'   --this should be the row separator in your data
    	)
 	  

### <a name="sql-builtin-utilities"></a>Utilitários internos no SQL Server

Você pode usar o SSIS (Serviços de Integrações do SQL Server) para importar dados para a VM do SQL Server no Azure por meio de um arquivo simples. O SSIS está disponível em dois ambientes de estúdio. Para obter detalhes, consulte [Ambientes do Integration Services (SSIS) e Estúdio](https://technet.microsoft.com/library/ms140028.aspx):

- Para obter detalhes sobre SQL Server Data Tools, consulte [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx)  
- Para obter detalhes sobre o Assistente de Importação/Exportação, consulte [Assistente de Importação/Exportação do SQL Server](https://msdn.microsoft.com/library/ms141209.aspx)

### <a name="sqlonprem_to_sqlonazurevm"></a>Movimentação de dados do SQL Server local

Os dados podem ser movidos de um SQL Server local da seguinte maneira:

1. [Exportar para arquivo simples](#export-flat-file) 
2. [Assistente de Migração de Banco de Dados SQL](#sql-migration)
3. [Backup e restauração de banco de dados](#sql-backup)

Descrevemos cada um deles abaixo:

#### <a name="export-flat-file"></a>Exportar para arquivo simples

Vários métodos podem ser usados para exportar dados em massa de um SQL Server local, conforme documentado [aqui](https://msdn.microsoft.com/library/ms175937.aspx). Este documento abordará o BCP (Programa de Cópia em Massa) como um exemplo. Depois que os dados são exportados para um arquivo simples, ele pode ser importado para outro SQL Server usando a importação em massa.

1. Exporte os dados do SQL Server local para um arquivo usando o utilitário bcp da seguinte maneira

	`bcp dbname..tablename out datafile.tsv -S	servername\sqlinstancename -T -t \t -t \n -c`

2. Crie o banco de dados e a tabela na VM do SQL Server no Azure usando `create database` e `create table` para o esquema de tabela exportado na etapa 1.

3. Crie um arquivo de formato para descrever o esquema da tabela de dados que está sendo importado/exportado. Detalhes do formato de arquivo são descritos [aqui](https://msdn.microsoft.com/library/ms191516.aspx).

	Geração de arquivo de formato ao executar o BCP no computador do SQL Server

		bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n 

	Geração de arquivo de formato ao executar o BCP remotamente em um SQL Server

		bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
		
	
4. Use qualquer um dos métodos descritos na seção [Movendo dados da origem do arquivo](#filesource_to_sqlonazurevm) para mover os dados em arquivos simples para o SQL Server.

#### <a name="sql-migration"></a>Assistente de Migração de Banco de Dados SQL

O [Assistente de Migração de Banco de Dados do SQL Server](http://sqlazuremw.codeplex.com/) fornece uma maneira amigável de mover dados entre duas instâncias do SQL Server. Ele permite que o usuário mapeie o esquema de dados entre as tabelas de origem e destino, escolha os tipos de coluna e vários outros recursos. Ele usa BCP (cópia em massa) nos bastidores. Abaixo está uma captura de tela da tela de boas-vindas para o Assistente de Migração de Banco de Dados SQL.

![Assistente de Migração do SQL Server][2]

#### <a name="sql-backup"></a>Backup e restauração de banco de dados

O SQL Server dá suporte a:

1. [Funcionalidade de backup e restauração de banco de dados](https://msdn.microsoft.com/library/ms187048.aspx) (para um arquivo local ou exportação de bacpac para blob) e [Aplicativos de Camada de Dados](https://msdn.microsoft.com/library/ee210546.aspx) (usando bacpac). 
2. Capacidade de criar VMs do SQL Server diretamente no Azure com um banco de dados copiado ou copiar um banco de dados do SQL do Azure existente. Para obter mais detalhes, consulte [Usar o Assistente para Cópia de Banco de Dados](https://msdn.microsoft.com/library/ms188664.aspx). 

Abaixo está uma captura de tela das opções de backup/restauração de banco de dados do Estúdio de Gerenciamento do SQL Server.

![Ferramenta de Importação do SQL Server][1]


[1]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/database_migration_wizard.png

 

<!---HONumber=July15_HO1-->