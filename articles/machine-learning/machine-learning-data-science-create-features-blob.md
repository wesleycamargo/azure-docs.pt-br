<properties 
	pageTitle="Criar recursos para dados de armazenamento de blob do Azure usando o Panda | Microsoft Azure" 
	description="Como criar recursos para os dados armazenados no contêiner de blob do Azure com o pacote Python Pandas." 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/05/2016" 
	ms.author="fashah;garye;bradsev" />

#Criar recursos para dados de armazenamento de blob do Azure usando o Panda


##Introdução

Este documento aborda como criar recursos para os dados armazenados no contêiner de blob do Azure usando o pacote Python [Pandas](http://pandas.pydata.org/). Depois de descrever como carregar em um quadro de dados Panda, ele mostrará como gerar recursos categóricos com os valores de indicador e a compartimentalização de recursos, ambos usando scripts Python.

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]Os links deste **menu** o levam até os tópicos que descrevem como criar recursos para os dados em vários ambientes. Esta tarefa é uma etapa no [CAP (Processo do Cortana Analytics)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## Pré-requisitos
Este artigo supõe que você:

* Criou uma conta de armazenamento de blob do Azure e armazenou os dados lá. Se você precisar de instruções para configurar uma conta, consulte [Criar uma conta de Armazenamento do Azure](../hdinsight-get-started.md#storage)


## Carregar os dados em um quadro de dados Pandas
Para explorar e manipular um conjunto de dados, eles devem ser baixados da fonte de blob para um arquivo local, que pode então ser carregado em um quadro de dados Pandas. Aqui estão as etapas para este procedimento:

1. Baixe os dados do Blob do Azure com o seguinte código de Python de exemplo e usando o serviço blob a seguir. Substitua a variável no código abaixo pelos valores específicos: 

	    from azure.storage import BlobService
    	import tables
    	
		STORAGEACCOUNTNAME= <storage_account_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		LOCALFILENAME= <local_file_name>		
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

    	#download from blob
    	t1=time.time()
    	blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    	blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    	t2=time.time()
    	print(("It takes %s seconds to download "+blobname) % (t2 - t1))


2. Leia os dados em um quadro de dados Pandas do arquivo baixado.

	    #LOCALFILE is the file path	
    	dataframe_blobdata = pd.read_csv(LOCALFILE)

Agora você está pronto para explorar os dados e gerar recursos neste conjunto de dados.
	
##<a name="blob-featuregen"></a>Geração de recursos
	
As duas seções a seguir mostram como gerar recursos categóricos com valores de indicador e de compartimentalização usando scripts Python.

###<a name="blob-countfeature"></a>Geração de Recursos baseada no valor do indicador

Recursos categóricos podem ser criados da seguinte maneira:

1. Inspecionar a distribuição da coluna categórica:
	
		dataframe_blobdata['<categorical_column>'].value_counts()

2. Gerar valores de indicador para cada um dos valores da coluna

		#generate the indicator column
		dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')

3. Unir a coluna de indicador com o quadro de dados original
 
			#Join the dummy variables back to the original data frame
			dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)

4. Remover a própria variável original:

		#Remove the original column rate_code in df1_with_dummy
		dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)
	
###<a name="blob-binningfeature"></a>Agrupamento da Geração de Recursos

Para gerar recursos compartimentalizados, faça o seguinte:

1. Adicione uma sequência de colunas a ser compartimentalizada a coluna numérica
 
		bins = [0, 1, 2, 4, 10, 40]
		dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
		
2. Converta a compartimentalização em uma sequência de variáveis boolianas

		dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
	
3. Por fim, associe as variáveis fictícias ao quadro de dados original

		dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)	

##<a name="sql-featuregen"></a>Gravar dados de volta ao blob do Azure e consumi-los no Aprendizado de Máquina do Azure

Depois que você já explorou os dados e criou os recursos necessários, pode carregar os dados (amostra ou recurso) para um blob do Azure e consumi-los no Aprendizado de Máquina do Azure usando as seguintes etapas: observe que os recursos adicionais podem ser criados no Estúdio de Aprendizado de Máquina do Azure também. 1. Grave o quadro de dados no arquivo local

		dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Carregue os dados para o blob do Azure da seguinte maneira:

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

3. Agora, os dados podem ser lidos do blob usando o módulo [Leitor](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) do Aprendizado de Máquina do Azure, como mostra a tela abaixo:
 
![blob de leitor](./media/machine-learning-data-science-process-data-blob/reader_blob.png)


 

<!---HONumber=AcomDC_0211_2016-->