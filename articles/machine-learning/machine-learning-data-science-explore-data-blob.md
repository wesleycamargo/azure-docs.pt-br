<properties 
	pageTitle="Explorar dados no repositório de blob do Azure com o Pandas | Microsoft Azure" 
	description="Como explorar dados armazenados no contêiner de blob do Azure usando o Pandas." 
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
	ms.date="05/10/2016" 
	ms.author="fashah;garye;bradsev" />

#Explorar dados no repositório de blob do Azure com o Pandas

Este documento aborda como explorar dados armazenados no contêiner de blob do Azure usando o pacote Python [Pandas](http://pandas.pydata.org/).

O **menu** abaixo leva a tópicos que descrevem como usar ferramentas para explorar dados de vários ambientes de armazenamento. Esta tarefa é uma etapa no Processo de Análise da Cortana (CAP).

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]


## Pré-requisitos
Este artigo supõe que você:

* Criou uma conta de armazenamento do Azure. Se precisar de instruções, consulte [Criar uma conta de Armazenamento do Azure](../hdinsight-get-started.md#storage)
* Armazenar seus dados em uma conta de armazenamento de blob do Azure.

## Carregar os dados em um quadro de dados Pandas
Para explorar e manipular um conjunto de dados, eles devem ser baixados da fonte de blob para um arquivo local, que pode então ser carregado em um quadro de dados Pandas. Aqui estão as etapas para este procedimento:

1. Baixe os dados do blob do Azure com o seguinte código Python de exemplo usando o serviço blob. Substitua a variável no código abaixo pelos valores específicos: 

	    from azure.storage.blob import BlobService
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

##<a name="blob-dataexploration"></a>Exemplos de exploração de dados usando o Pandas

Veja estão alguns exemplos de maneiras de explorar dados usando Pandas:

1. Inspecionar o **número de linhas e colunas** 

		print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape

2. **Inspecione** as primeira ou últimas **linhas** no conjunto de dados, conforme mostrado a seguir:

		dataframe_blobdata.head(10)
		
		dataframe_blobdata.tail(10)

3. Verifique o **tipo de dados** indicado para cada coluna importada usando o seguinte código de exemplo
 	
		for col in dataframe_blobdata.columns:
		    print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype

4. Verifique as **estatísticas básicas** para as colunas no conjunto de dados da seguinte maneira
 
		dataframe_blobdata.describe()
	
5. Veja o número de entradas para cada valor de coluna da seguinte maneira

		dataframe_blobdata['<column_name>'].value_counts()

6. **Conte os valores ausentes** em comparação com o número real de entradas em cada coluna usando o seguinte código de exemplo

		miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
		print miss_num
	 
7.	Se você tiver **valores ausentes** para uma coluna específica nos dados, poderá removê-los da seguinte maneira:

		dataframe_blobdata_noNA = dataframe_blobdata.dropna()
		dataframe_blobdata_noNA.shape

	Outra maneira de substituir valores ausentes é com a função de modo:
	
		dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})		

8. Crie um gráfico de **histograma** usando um número variável de compartimentos para plotar a distribuição de uma variável
	
		dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
		
		np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
	
9. Examine **correlações** entre variáveis usando um gráfico disperso ou a função interna de correlação

		#relationship between column_a and column_b using scatter plot
		plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
		
		#correlation between column_a and column_b
		dataframe_blobdata[['<column_a>', '<column_b>']].corr()

<!---HONumber=AcomDC_0518_2016-->