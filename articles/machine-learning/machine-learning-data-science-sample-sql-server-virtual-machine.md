<properties 
	pageTitle="Dados de exemplo no SQL Server no Azure| Microsoft Azure"
	description="Dados de exemplo no SQL Server no Azure"
	services="machine-learning"
	documentationCenter=""
	authors="fashah"
	manager="paulettm"
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2015"
	ms.author="fashah;garye;bradsev"/>

#<a name="heading"></a>Dados de exemplo no SQL Server no Azure

Este documento aborda os dados de exemplo armazenados no SQL Server no Azure usando o SQL e a linguagem de programação Python.

>[AZURE.NOTE]O código de exemplo SQL neste documento pressupõe que os dados estejam em um SQL Server no Azure. Se não estiverem, confira o tópico [Mover dados para o SQL Server no Azure](machine-learning-data-science-move-sql-server-virtual-machine.md) no [Guia de Processo Avançado de Dados](machine-learning-data-science-advanced-data-processing.md) para obter instruções e mover os dados para um SQL Server no Azure.

##<a name="SQL"></a>Usando o SQL

Esta seção descreve vários métodos usando SQL para executar uma amostragem aleatória simples em relação aos dados no banco de dados. Escolha um método com base no tamanho e na distribuição dos seus dados.

Os dois itens a seguir mostram como usar newid no SQL Server para executar a amostra. O método escolhido depende do quão aleatório você deseja que o exemplo seja (pk\_id no código de exemplo abaixo é considerado como uma chave primária gerada automaticamente).

1. Menos rígido do exemplo aleatório

	    select  * from <table_name> where <primary_key> in 
    	(select top 10 percent <primary_key> from <table_name> order by newid())

2. Exemplo mais aleatório

	    SELECT * FROM <table_name>
    	WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

TABLESAMPLE pode ser usado para amostragem conforme demonstrado a seguir. Isso pode ser uma abordagem melhor se o tamanho de dados for grande (supondo que os dados em diferentes páginas não estejam correlacionados) e para a consulta terminar em um tempo razoável.

	SELECT *
	FROM <table_name> 
	TABLESAMPLE (10 PERCENT)

>[AZURE.NOTE]Você pode explorar e gerar recursos por meio desses dados amostrados armazenando-os em uma nova tabela


###<a name="sql-aml"></a>Conectando ao Aprendizado de Máquina do Azure

Usar diretamente as consultas de exemplo acima no módulo do leitor Azure ML para buscar os dados dinamicamente e colocá-los em um experimento ML do Azure. Uma captura de tela usando o módulo do leitor para ler os dados de amostra é mostrada abaixo:
   
![sql leitor][1]

##<a name="python"></a>Usando a linguagem de programação Python 

Esta seção demonstra como usar a biblioteca pyodbc para se conectar ao banco de dados do SQL Server no Python. A cadeia de conexão do banco de dados é a seguinte (substitua o nome do servidor, o nome do banco de dados, o nome de usuário e a senha pela sua configuração):

	#Set up the SQL Azure connection
	import pyodbc	
	conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A biblioteca [Pandas](http://pandas.pydata.org/) no Python fornece um conjunto avançado de estruturas de dados e ferramentas de análise de dados para manipulação de dados em programação Python. O código a seguir lê um exemplo de 0,1% dos dados por meio de uma tabela no banco de dados SQL do Azure em um dado Pandas:

	import pandas as pd

	# Query database and load the returned results in pandas data frame
	data_frame = pd.read_sql('''select column1, cloumn2... from <table_name> tablesample (0.1 percent)''', conn)

Agora você pode trabalhar com os dados de amostra no quadro de dados Pandas.

###<a name="python-aml"></a>Conectando ao Aprendizado de Máquina do Azure

Você pode usar o código de exemplo a seguir para salvar os dados convertidos em um arquivo e carregá-los para um blob do Azure. Os dados no blob podem ser lidos diretamente em um experimento de AM do Azure usando o *Modulo Leitor*. As etapas são as seguintes:

1. Gravar o quadro de dados Pandas em um arquivo local

		dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Carregar o arquivo local no blob do Azure

		from azure.storage import BlobService
    	import tables

		STORAGEACCOUNTNAME= <storage_account_name>
		LOCALFILENAME= <local_file_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

	    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
	    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
	    
	    try:
	   
	    #perform upload
	    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
	    
	    except:	        
		    print ("Something went wrong with uploading blob:"+BLOBNAME)

3. Leia dados do blob do Azure usando o *Módulo Leitor* do AM do Azure, como mostra a captura de tela abaixo:
 
![blob de leitor][2]

## Exemplo de ADAPT (Processo de Análise Avançada e Tecnologia) em ação

Para obter um exemplo passo a passo de ponta a ponta do ADAPT (Processo de Análise Avançada e Tecnologia) usando um conjunto de dados público, confira [Processo de Análise Avançada do Azure e Tecnologia em Ação: usando o SQL Sever](machine-learning-data-science-process-sql-walkthrough.md).

[1]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_blob.png

 

<!---HONumber=September15_HO1-->