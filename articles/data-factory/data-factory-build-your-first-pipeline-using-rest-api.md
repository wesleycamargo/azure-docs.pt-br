<properties
	pageTitle="Compilar seu primeiro data factory (REST) | Microsoft Azure"
	description="Neste tutorial, você cria um pipeline de exemplo do Azure Data Factory usando a API REST do Data Factory."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"
/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="08/16/2016"
	ms.author="spelluru"/>

# Tutorial: Criar seu primeiro data factory do Azure usando a API REST do Data Factory
> [AZURE.SELECTOR]
- [Portal do Azure](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Modelo do Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
- [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)

[AZURE.INCLUDE [data-factory-tutorial-prerequisites](../../includes/data-factory-tutorial-prerequisites.md)]

## Pré-requisitos adicionais
- Instale o [Curl](https://curl.haxx.se/dlwiz/) em seu computador. Você pode usar a ferramenta CURL com comandos REST para criar um data factory.
- Siga as instruções [deste artigo](../resource-group-create-service-principal-portal.md) para:
	1. Crie um aplicativo Web chamado **ADFGetStartedApp** no Azure Active Directory.
	2. Obtenha a **ID do cliente** e a **chave secreta**.
	3. Obtenha a **ID do locatário**.
	4. Atribua o aplicativo **ADFGetStartedApp** à função **Colaborador do Data Factory**.
- Instale o [Azure PowerShell](../powershell-install-configure.md).
- Inicie o **PowerShell** e execute o comando a seguir. Mantenha o Azure PowerShell aberto até o fim deste tutorial. Se você fechar e reabrir, precisará executar os comandos novamente.
	1. Execute **Login-AzureRmAccount** e insira o nome de usuário e a senha que você usa para entrar no Portal do Azure.
	2. Execute **Get-AzureRmSubscription** para exibir todas as assinaturas dessa conta.
	3. Execute **Get-AzureRmSubscription -SubscriptionName NameOfAzureSubscription| Set-AzureRmContext** to select the subscription that you want to work with. Replace **NameOfAzureSubscription** with the name of your Azure subscription. 
3. Crie um grupo de recursos do Azure denominado **ADFTutorialResourceGroup** executando o comando a seguir no PowerShell.

		New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Algumas das etapas neste tutorial supõem que você utilize o grupo de recursos denominado ADFTutorialResourceGroup. Se você utilizar um grupo de recursos diferente, precisará usar o nome do seu grupo de recursos no lugar de ADFTutorialResourceGroup neste tutorial.

## Criar definições JSON
Crie os arquivos JSON a seguir na pasta onde curl.exe está localizado.

### datafactory.json 
> [AZURE.IMPORTANT] O nome deve ser exclusivo e, portanto, convém colocar um prefixo/sufixo no ADFCopyTutorialDF para torná-lo um nome exclusivo.

	{  
	    "name": "FirstDataFactoryREST",  
	    "location": "WestUS"
	}  

### azurestoragelinkedservice.json
> [AZURE.IMPORTANT] Substitua **nome da conta** e **chave da conta** pelo nome e pela chave da sua conta de armazenamento do Azure. Para saber como obter sua chave de acesso de armazenamento, confira [Exibir, copiar e regenerar chaves de acesso de armazenamento](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

	{
	    "name": "AzureStorageLinkedService",
	    "properties": {
	        "type": "AzureStorage",
	        "typeProperties": {
	            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	        }
	    }
	}


### hdinsightondemandlinkedservice.json

	{
		"name": "HDInsightOnDemandLinkedService",
		"properties": {
			"type": "HDInsightOnDemand",
			"typeProperties": {
				"version": "3.2",
				"clusterSize": 1,
				"timeToLive": "00:30:00",
				"linkedServiceName": "AzureStorageLinkedService"
			}
		}
	}

A tabela a seguir fornece descrições das propriedades de JSON usadas no trecho de código:

| Propriedade | Descrição |
| :------- | :---------- |
| Versão | Especifica a versão do HDInsight criada como a 3.2. | 
| ClusterSize | Tamanho do cluster HDInsight. | 
| TimeToLive | Especifica que o tempo ocioso do cluster HDInsight antes de ser excluído. |
| linkedServiceName | Especifica a conta de armazenamento usada para armazenar os logs gerados pelo HDInsight |

Observe o seguinte:

- O Data Factory cria um cluster HDInsight **baseado no Windows** para você com o JSON acima. Você também pode fazer com que ele crie um cluster HDInsight **baseado em Linux**. Confira [Serviço vinculado do HDInsight sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes.
- Você pode usar **seu próprio cluster do HDInsight** em vez de usar um cluster do HDInsight sob demanda. Confira [Serviço vinculado do HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) para obter detalhes.
- O cluster HDInsight cria um **contêiner padrão** no armazenamento de blobs especificado em JSON (**linkedServiceName**). O HDInsight não exclui esse contêiner quando o cluster é excluído. Este comportamento ocorre por design. Com o serviço vinculado HDInsight sob demanda, um cluster HDInsight é criado sempre que uma fatia é processada, a menos que haja um cluster ativo existente (**timeToLive**), e é excluído quando o processamento é concluído.

	Quanto mais fatias forem processadas, você verá muitos contêineres no armazenamento de blobs do Azure. Se você não precisa deles para solução de problemas dos trabalhos, convém excluí-los para reduzir o custo de armazenamento. Os nomes desses contêineres seguem um padrão: "adf**nomedafábricadedados**-**nomedoserviçovinculado**-carimbodedatahora". Use ferramentas como o [Gerenciador de Armazenamento da Microsoft](http://storageexplorer.com/) para excluir contêineres do armazenamento de blobs do Azure.

Confira [Serviço vinculado do HDInsight sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes.

### inputdataset.json

	{
		"name": "AzureBlobInput",
		"properties": {
			"type": "AzureBlob",
			"linkedServiceName": "AzureStorageLinkedService",
			"typeProperties": {
				"fileName": "input.log",
				"folderPath": "adfgetstarted/inputdata",
				"format": {
					"type": "TextFormat",
					"columnDelimiter": ","
				}
			},
			"availability": {
				"frequency": "Month",
				"interval": 1
			},
			"external": true,
			"policy": {}
		}
	}


O JSON define um conjunto de dados chamado **AzureBlobInput**, que representa dados de entrada para uma atividade no pipeline. Além disso, ele especifica que os dados de entrada estão localizados no contêiner de blob denominado **adfgetstarted** e na pasta chamada **inputdata**.

A tabela a seguir fornece descrições das propriedades de JSON usadas no trecho de código:

| Propriedade | Descrição |
| :------- | :---------- |
| type | A propriedade type é definida como AzureBlob porque os dados residem no armazenamento de blobs do Azure. |  
| linkedServiceName | refere-se ao StorageLinkedService que você criou anteriormente. |
| fileName | Essa propriedade é opcional. Se você omitir essa propriedade, todos os arquivos de folderPath serão selecionados. Nesse caso, somente o input.log será processado. |
| type | Os arquivos de log estão em formato de texto, então utilizaremos TextFormat. | 
| columnDelimiter | as colunas nos arquivos de log são delimitadas por um caractere de vírgula (,) |
| frequência/intervalo | a frequência é definida como Mês e o intervalo como 1, o que significa que as fatias de entrada estão disponíveis mensalmente. | 
| externo | essa propriedade será definida como true se os dados de entrada não forem gerados pelo serviço Data Factory. | 

### outputdataset.json

	{
		"name": "AzureBlobOutput",
		"properties": {
			"type": "AzureBlob",
			"linkedServiceName": "AzureStorageLinkedService",
			"typeProperties": {
				"folderPath": "adfgetstarted/partitioneddata",
				"format": {
					"type": "TextFormat",
					"columnDelimiter": ","
				}
			},
			"availability": {
				"frequency": "Month",
				"interval": 1
			}
		}
	}

O JSON define um conjunto de dados chamado **AzureBlobOutput**, que representa dados de saída para uma atividade no pipeline. Além disso, ele especifica que os resultados são armazenados no contêiner de blob denominado **adfgetstarted** e na pasta chamada **partitioneddata**. A seção **availability** especifica que o conjunto de dados de saída é produzido mensalmente.

### pipeline.json
> [AZURE.IMPORTANT] Substitua **storageaccountname** pelo nome da sua conta de armazenamento do Azure.


	{
		"name": "MyFirstPipeline",
		"properties": {
			"description": "My first Azure Data Factory pipeline",
			"activities": [{
				"type": "HDInsightHive",
				"typeProperties": {
					"scriptPath": "adfgetstarted/script/partitionweblogs.hql",
					"scriptLinkedService": "AzureStorageLinkedService",
					"defines": {
						"inputtable": "wasb://adfgetstarted@<stroageaccountname>.blob.core.windows.net/inputdata",
						"partitionedtable": "wasb://adfgetstarted@<stroageaccountname>t.blob.core.windows.net/partitioneddata"
					}
				},
				"inputs": [{
					"name": "AzureBlobInput"
				}],
				"outputs": [{
					"name": "AzureBlobOutput"
				}],
				"policy": {
					"concurrency": 1,
					"retry": 3
				},
				"scheduler": {
					"frequency": "Month",
					"interval": 1
				},
				"name": "RunSampleHiveActivity",
				"linkedServiceName": "HDInsightOnDemandLinkedService"
			}],
			"start": "2016-07-10T00:00:00Z",
			"end": "2016-07-11T00:00:00Z",
			"isPaused": false
		}
	}

No trecho de JSON, você cria um pipeline que consiste de uma única atividade que usa o Hive para processar dados em um cluster HDInsight.

O arquivo de script do Hive, **partitionweblogs.hql**, é armazenado na conta de armazenamento do Azure (especificada pelo scriptLinkedService chamado **StorageLinkedService**) e na pasta **script** no contêiner **adfgetstarted**.

A seção **defines** especifica as configurações de tempo de execução passadas para o script do hive como valores de configuração do Hive (por exemplo, ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

As propriedades **start** e **end** do pipeline especificam o período ativo do pipeline.

Na atividade de JSON, você especifica que o script do Hive deve ser executado na computação especificada pelo **linkedServiceName** – **HDInsightOnDemandLinkedService**.

> [AZURE.NOTE] Confira [Anatomia de um Pipeline](data-factory-create-pipelines.md#anatomy-of-a-pipeline) para obter detalhes sobre as propriedades JSON usadas no exemplo acima.

## Definir variáveis globais

No Azure PowerShell, execute os comandos a seguir depois de substituir os valores pelos seus próprios:

> [AZURE.IMPORTANT] Veja a seção [Pré-requisitos](#prerequisites) para obter instruções sobre como obter a ID do cliente, o segredo do cliente, a ID do locatário e ID da assinatura.

	$client_id = "<client ID of application in AAD>"
	$client_secret = "<client key of application in AAD>"
	$tenant = "<Azure tenant ID>";
	$subscription_id="<Azure subscription ID>";

	$rg = "ADFTutorialResourceGroup"
	$adf = "FirstDataFactoryREST"



## Autenticar com o AAD

	$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
	$responseToken = Invoke-Command -scriptblock $cmd;
	$accessToken = (ConvertFrom-Json $responseToken).access_token;
	
	(ConvertFrom-Json $responseToken) 



## Criar um data factory

Nesta etapa, você criará um Azure Data Factory chamado **FirstDataFactoryREST**. Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline em um data factory pode ter uma ou mais atividades. Por exemplo, uma Atividade de Cópia para copiar dados de um armazenamento de dados de origem para um de destino e uma atividade do Hive do HDInsight para executar o script do Hive para transformar dados. Execute os seguintes comandos para criar o data factory:

1. Atribua o comando à variável chamada **cmd**.

	Confirme se o nome do data factory especificado aqui (ADFCopyTutorialDF) corresponde ao nome especificado no **datafactory.json**.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/FirstDataFactoryREST?api-version=2015-10-01};
2. Execute o comando usando **Invoke-Command**.

		$results = Invoke-Command -scriptblock $cmd;
3. Exiba os resultados. Se o data factory foi criado com êxito, você verá o JSON para o data factory nos **resultados**. Caso contrário, você verá uma mensagem de erro.

		Write-Host $results

Observe o seguinte:
 
- O nome do Azure Data Factory deve ser globalmente exclusivo. Se você vir o erro nos resultados: **O nome de Data Factory "FirstDataFactoryREST" não está disponível**, faça o seguinte:
	1. Altere o nome (por exemplo, yournameFirstDataFactoryREST) no arquivo **datafactory.json**. Consulte o tópico [Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura para artefatos de Data Factory.
	2. No primeiro comando em que a variável **$cmd** é atribuída um valor, substitua FirstDataFactoryREST pelo novo nome e execute o comando.
	3. Execute os próximos dois comandos para invocar a API REST a fim de criar o data factory e imprima os resultados da operação.
- Para criar instâncias do Data Factory, você precisa ser um colaborador/administrador da assinatura do Azure
- O nome do data factory pode ser registrado futuramente como um nome DNS e tornar-se visível publicamente.
- Se você receber o erro: "**Esta assinatura não está registrada para usar o namespace Microsoft.DataFactory**", siga um destes procedimentos e tente publicar novamente:

	- No Azure PowerShell, execute o comando a seguir para registrar o provedor do Data Factory.
		
			Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
	
		Você pode executar o comando a seguir para confirmar se o provedor do Data Factory está registrado.
	
			Get-AzureRmResourceProvider
	- Faça logon no [portal do Azure](https://portal.azure.com) usando a assinatura do Azure e navegue até uma folha do Data Factory (ou) crie um data factory no portal do Azure. Essa ação registra automaticamente o provedor para você.

Antes de criar um pipeline, primeiro você precisará criar algumas entidades do Data Factory. Primeiro, você cria serviços vinculados para vincular repositórios de dados/cálculos para seu repositório de dados e define conjuntos de dados de entrada e saída para representar dados em repositórios de dados vinculados.

## Criar serviços vinculados 
Nesta etapa, você vinculará sua conta do Armazenamento do Azure e um cluster do HDInsight do Azure sob demanda ao data factory. A conta do Armazenamento do Azure manterá os dados de entrada e de saída para o pipeline neste exemplo. O serviço vinculado do HDInsight é usado para executar o script do Hive especificado na atividade do pipeline neste exemplo.

### Criar o serviço vinculado do armazenamento do Azure
Nesta etapa, você vincula a conta do Armazenamento do Azure ao data factory. Neste tutorial, use a mesma conta do Armazenamento do Azure para armazenar dados de entrada/saída e o arquivo do script do HQL.

1. Atribua o comando à variável chamada **cmd**.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azurestoragelinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
2. Execute o comando usando **Invoke-Command**.
 
		$results = Invoke-Command -scriptblock $cmd;
3. Exiba os resultados. Se o serviço vinculado tiver sido criado com êxito, você verá o JSON do serviço vinculado nos **resultados**. Caso contrário, você verá uma mensagem de erro.
  
		Write-Host $results

### Criar o serviço vinculado do Azure HDInsight
Nesta etapa, você vincula um cluster do HDInsight sob demanda ao seu data factory. O cluster do HDInsight é automaticamente criado no tempo de execução e excluído após a conclusão do processamento, ficando ocioso durante o período especificado. Você pode usar seu próprio cluster do HDInsight em vez de usar um cluster do HDInsight sob demanda. Veja [Serviços vinculados de computação](data-factory-compute-linked-services.md) para obter detalhes.

1. Atribua o comando à variável chamada **cmd**.
 
		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@hdinsightondemandlinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/hdinsightondemandlinkedservice?api-version=2015-10-01};
2. Execute o comando usando **Invoke-Command**.

		$results = Invoke-Command -scriptblock $cmd;
3. Exiba os resultados. Se o serviço vinculado tiver sido criado com êxito, você verá o JSON do serviço vinculado nos **resultados**. Caso contrário, você verá uma mensagem de erro.

		Write-Host $results

## Criar conjuntos de dados
Nesta etapa, você cria conjuntos de dados para representar dados de entrada e de saída para o processamento do Hive. Esses conjuntos de dados fazem referência ao **StorageLinkedService** que você criou anteriormente neste tutorial. O serviço vinculado aponta para uma conta do Armazenamento do Azure e os conjuntos de dados especificam o contêiner, a pasta e o nome do arquivo no armazenamento que contém os dados de entrada e de saída.

### Criar conjunto de dados de entrada
Nesta etapa, você cria o conjunto de dados de entrada para representar os dados de entrada armazenados no armazenamento de Blobs do Azure.

1. Atribua o comando à variável chamada **cmd**.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
2. Execute o comando usando **Invoke-Command**.

		$results = Invoke-Command -scriptblock $cmd;
3. Exiba os resultados. Se o conjunto de dados tiver sido criado com êxito, você verá o JSON do conjunto de dados nos **resultados**. Caso contrário, você verá uma mensagem de erro.
  
		Write-Host $results
### Criar conjunto de dados de saída
Nesta etapa, você cria o conjunto de dados de saída para representar os dados de saída armazenados no armazenamento de Blobs do Azure.

1. Atribua o comando à variável chamada **cmd**.
 
		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobOutput?api-version=2015-10-01};
2. Execute o comando usando **Invoke-Command**.

		$results = Invoke-Command -scriptblock $cmd;
3. Exiba os resultados. Se o conjunto de dados tiver sido criado com êxito, você verá o JSON do conjunto de dados nos **resultados**. Caso contrário, você verá uma mensagem de erro.
  
		Write-Host $results 

## Criar um pipeline
Nesta etapa, você cria seu primeiro pipeline com a atividade **HDInsightHive**. A fatia de entrada está disponível mensalmente (frequência: mês, intervalo: 1), a fatia de saída é produzida mensalmente e a propriedade do agendador para a atividade também é definida como mensal. As configurações para o conjunto de dados de saída e o agendador de atividades devem corresponder. Atualmente, o conjunto de dados de saída é o que aciona a agenda, então você deve criar um conjunto de dados de saída, mesmo que a atividade não produza qualquer saída. Se a atividade não receber entradas, ignore a criação de conjunto de dados de entrada.

Confirme que vê o arquivo **input.log** na pasta **adfgetstarted/inputdata** no armazenamento de blobs do Azure e execute o comando a seguir para implantar o pipeline. Como as horas de **início** e de **término** são definidas no passado e **isPaused** está definido como false, o pipeline (a atividade no pipeline) é imediatamente executado após a implantação.

1. Atribua o comando à variável chamada **cmd**.
 
		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
2. Execute o comando usando **Invoke-Command**.

		$results = Invoke-Command -scriptblock $cmd;
3. Exiba os resultados. Se o conjunto de dados tiver sido criado com êxito, você verá o JSON do conjunto de dados nos **resultados**. Caso contrário, você verá uma mensagem de erro.

		Write-Host $results
5. Parabéns, você criou com sucesso seu primeiro pipeline usando o Azure PowerShell!

## Monitorar o pipeline
Nesta etapa, você pode usar a API REST do Data Factory para monitorar fatias produzidas pelo pipeline.

	$ds ="AzureBlobOutput"

	$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

	$results2 = Invoke-Command -scriptblock $cmd;

	IF ((ConvertFrom-Json $results2).value -ne $NULL) {
	    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
	} else {
    	    (convertFrom-Json $results2).RemoteException
	}

Execute Invoke-Command e o próximo até ver a fatia no estado **Pronto** ou **Falha**. Quando a fatia estiver no estado Pronto, verifique a pasta **partitioneddata** no contêiner **adfgetstarted** em seu armazenamento de blobs para os dados de saída. A criação de um cluster do HDInsight sob demanda geralmente leva algum tempo.

![dados de saída](./media/data-factory-build-your-first-pipeline-using-rest-api/three-ouptut-files.png)

> [AZURE.IMPORTANT] O arquivo de entrada é excluído quando a fatia é processada com êxito. Portanto, se você quiser executar novamente a fatia ou fazer o tutorial novamente, carregue o arquivo de entrada (input.log) na pasta inputdata do contêiner adfgetstarted.

Você também pode usar o portal do Azure para monitorar fatias e solucionar problemas. Veja os detalhes em [Monitorar pipelines usando o portal do Azure](data-factory-build-your-first-pipeline-using-editor.md##monitor-pipeline).

## Resumo 
Neste tutorial, você criou uma data factory do Azure para processar dados ao executar o script Hive em um cluster hadoop do HDInsight. Você usou o Data Factory Editor no portal do Azure para executar as seguintes etapas:

1.	Foi criada uma **data factory** do Azure.
2.	Foram criados dois **serviços vinculados**:
	1.	O serviço vinculado **Armazenamento do Azure** para vincular seu armazenamento de blobs do Azure que contém os arquivos de entrada/saída para a data factory.
	2.	O serviço vinculado **Azure HDInsight** sob demanda para vincular um cluster Hadoop do HDInsight sob demanda à data factory. O Azure Data Factory cria um cluster Hadoop do HDInsight just-in-time para processar dados de entrada e gerar dados de saída.
3.	Foram criados dois **conjuntos de dados** que descrevem dados de entrada e de saída para a atividade Hive do HDInsight no pipeline.
4.	Foi criado um **pipeline** com uma atividade **Hive do HDInsight**.

## Próximas etapas
Neste artigo, você criou um pipeline com uma atividade de transformação (atividade do HDInsight) que executa um script Hive em um cluster do HDInsight do Azure sob demanda. Para saber como usar uma Atividade de Cópia para copiar dados de um Blob do Azure para o SQL do Azure, confira [Tutorial: Copiar dados de um blob do Azure para o SQL do Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## Consulte também
| Tópico | Descrição |
| :---- | :---- |
| [Referência de API REST do Data Factory](https://msdn.microsoft.com/library/azure/dn906738.aspx) | Consulte a documentação abrangente sobre os cmdlets do Data Factory |
| [Atividades de transformação de dados](data-factory-data-transformation-activities.md) | Este artigo fornece uma lista de atividades de transformação de dados (como a transformação do Hive do HDInsight usado neste tutorial) com suporte do Azure Data Factory. |
| [Planejamento e execução](data-factory-scheduling-and-execution.md) | Este artigo explica os aspectos de agendamento e execução do modelo de aplicativo do Azure Data Factory. |
| [Pipelines](data-factory-create-pipelines.md) | Este artigo o ajuda a compreender pipelines e atividades no Azure Data Factory e como usá-los para construir fluxos de trabalho orientados a dados de ponta a ponta para seu cenário ou negócio. |
| [Conjunto de dados](data-factory-create-datasets.md) | Este artigo o ajuda a entender os conjuntos de dados no Azure Data Factory.
| [Monitorar e gerenciar pipelines usando as folhas do portal do Azure](data-factory-monitor-manage-pipelines.md) | Este artigo descreve como monitorar, gerenciar e depurar seus pipelines usando as folhas do portal do Azure. |
| [Monitorar e gerenciar pipelines usando o Aplicativo de Monitoramento](data-factory-monitor-manage-app.md) | Este artigo descreve como monitorar, gerenciar e depurar seus pipelines usando o Aplicativo de Monitoramento e Gerenciamento. 

<!---HONumber=AcomDC_0914_2016-->