<properties 
	pageTitle="Dados de exemplo no Armazenamento de Blobs do Azure| Microsoft Azure" 
	description="Dados de exemplo no Armazenamento de Blobs do Azure" 
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
	ms.date="02/07/2016" 
	ms.author="sunliangms;fashah;garye;bradsev" />

#<a name="heading"></a>Dados de exemplo no armazenamento de blob do Azure

## Introdução

Este documento aborda os dados de amostragem armazenados no Armazenamento do Blobs do Azure, baixando-os por meio de programação e realizando amostragem com um exemplo de código Python. As etapas para isso são os seguintes:

**Por que fazer amostragem dos dados?** Se o conjunto de dados que você deseja analisar é grande, geralmente é uma boa ideia reduzir a amostra de dados para um tamanho menor, mas representativo e mais gerenciável. Isso facilita a compreensão de dados, exploração e engenharia de recursos. Sua função no Processo de Análise do Cortana é habilitar a rápida criação de protótipos de funções de processamento de dados e modelos de aprendizado de máquina.

O **menu** abaixo leva a tópicos que descrevem como obter amostras de dados de vários ambientes de armazenamento.

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

A tarefa de amostragem é uma etapa no [CAP (Processo do Cortana Analytics)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## Baixar e reduzir os dados de exemplo
1. Baixe os dados do Armazenamento do Blobs do Azure usando o serviço blob com o código Python de exemplo a seguir: 

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

2. Leia os dados em um quadro de dados Pandas do arquivo baixado anteriormente.

		import pandas as pd

	    #directly ready from file on disk
    	dataframe_blobdata = pd.read_csv(LOCALFILE)

3. Reduza os dados usando o `random.choice` da `numpy`, da seguinte maneira:

	    # A 1 percent sample
    	sample_ratio = 0.01 
    	sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
    	sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
    	dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

	Agora você pode trabalhar com o quadro de dados acima com a amostra de 1 por cento para exploração e geração de recursos adicional.

##<a name="heading"></a>Carregar os dados e lê-los no Aprendizado de Máquina do Azure

Você pode usar o seguinte código de exemplo para os reduzir os dados de exemplo e usá-los diretamente no AM do Azure:

1. Escrever o quadro de dados em um arquivo local

		dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Carregue o arquivo local para um blob do Azure usando o seguinte código de exemplo:

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
		    print ("Something went wrong with uploading to the blob:"+ BLOBNAME)

3. Leia os dados de blob do Azure usando o [Leitor](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) do AM do Azure como mostra a captura de tela abaixo:
 
![blob de leitor][1]

[1]: ./media/machine-learning-data-science-sample-data-blob/reader_blob.png


<!-- Module References -->
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 

<!---HONumber=AcomDC_0211_2016-->