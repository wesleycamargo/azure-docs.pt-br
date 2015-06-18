<properties 
	pageTitle="Dados de exemplo no SQL Server no Azure| Azure" 
	description="Dados de exemplo no SQL Server no Azure" 
	services="machine-learning" 
	documentationCenter="" 
	authors="fashah" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="fashah,garye" /> 

#<a name="heading"></a>Dados de exemplo no SQL Server no Azure

Este documento aborda os dados de amostragem armazenados no SQL Server no Azure das seguintes maneiras:

1. [Usando o SQL](#sql)
2. [Usando a linguagem de programação Python](#python) 

**Observação**
>O código de exemplo SQL neste documento pressupõe que os dados estejam em um SQL Server no Azure.  Se não estiver, consulte o mapa do processo de ciência de dados na nuvem para obter instruções para mover os dados para um SQL Server no Azure.

###<a name="SQL"></a>Usando o SQL

Esta seção descreve vários métodos usando SQL para executar uma amostragem aleatória simples em relação aos dados no banco de dados.  Escolha um método com base no tamanho e na distribuição dos seus dados.

Os dois itens a seguir mostram como usar newid no SQL Server para executar a amostra.  O método escolhido depende do quão aleatório você deseja que o exemplo seja (pk_id no código de exemplo abaixo é considerado como uma chave primária gerada automaticamente).

1. Menos rígido do exemplo aleatório

	    select  * from <table_name> where <primary_key> in 
    	(select top 10 percent <primary_key> from <table_name> order by newid())

2. Exemplo mais aleatório 

	    SELECT * FROM <table_name>
    	WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

TABLESAMPLE pode ser usado para amostragem conforme demonstrado a seguir.  Isso pode ser uma abordagem melhor se o tamanho de dados for grande (supondo que os dados em diferentes páginas não estejam correlacionados) e para a consulta terminar em um tempo razoável.

	SELECT *
	FROM <table_name> 
	TABLESAMPLE (10 PERCENT)

**Observação**
> Você pode explorar e gerar recursos por meio desses dados amostrados armazenando-os em uma nova tabela


####<a name="sql-aml"></a>Conectar ao Aprendizado de Máquina do Azure

Usar diretamente as consultas de exemplo acima no módulo do leitor Azure ML para buscar os dados dinamicamente e colocá-los em um experimento ML do Azure.  Uma captura de tela usando o módulo do leitor para ler os dados de amostra é mostrada abaixo:
   
![reader sql][1]

###<a name="python"></a>Usando a linguagem de programação Python 

Esta seção demonstra como usar a biblioteca pyodbc para se conectar ao banco de dados do SQL Server no Python.  A cadeia de conexão de banco de dados é a seguinte:  (substitua servername, dbname, username e password pela sua configuração):

	#Set up the SQL Azure connection
	import pyodbc	
	conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A Biblioteca [Pandas](http://pandas.pydata.org/) no Python fornece um rico conjunto de estruturas de dados e ferramentas de análise de dados para manipulação de dados para a programação Python.  O código a seguir lê um exemplo de 0,1% dos dados por meio de uma tabela no banco de dados SQL do Azure em um dado Pandas:

	import pandas as pd

	# Query database and load the returned results in pandas data frame
	data_frame = pd.read_sql('''select column1, cloumn2... from <table_name> tablesample (0.1 percent)''', conn)

Agora você pode trabalhar com os dados de amostra no quadro de dados Pandas. 

####<a name="python-aml"></a>Conectar ao Aprendizado de Máquina do Azure

Você pode usar o código de exemplo a seguir para salvar os dados convertidos em um arquivo e carregá-los para um blob do Azure.  Os dados no blob podem ser lidos diretamente em uma experiência de ML do Azure usando o *Reader Module*.  As etapas são as seguintes: 

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

3. Ler dados do blob do Azure usando o Azure ML *Reader Module* como mostra a captura de tela abaixo:
 
![reader blob][2]

### Exemplo da Ciência de Dados do Azure em Ação

Para obter um exemplo passo a passo e ponta a ponta do Processo de Ciência de Dados do Azure usando um conjunto de dados público, consulte [Processo de Ciência de Dados do Azure em ação](machine-learning-data-science-process-sql-walkthrough.md).

[1]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_blob.png


<!--HONumber=49--> 