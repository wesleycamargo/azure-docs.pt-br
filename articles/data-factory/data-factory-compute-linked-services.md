<properties
	pageTitle="Serviços Vinculados de Computação | Microsoft Azure"
	description="Aprenda sobre ambientes de computação que você pode usar em pipelines do Azure Data Factory para transformar/processar dados."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/11/2016"
	ms.author="spelluru"/>

# Serviços vinculados de computação

Este artigo explica diferentes ambientes de computação que você pode usar para processar ou transformar dados. Ele também fornece detalhes sobre as diferentes configurações (sob demanda versus traga a sua própria) com suporte pela Data Factory ao configurar serviços vinculados que vinculam esses ambientes de computação para uma Azure Data Factory.

## Ambiente de computação sob demanda

Nesse tipo de configuração, o ambiente de computação é totalmente gerenciado pelo serviço do Azure Data Factory. Ele é automaticamente criado pelo serviço Data Factory antes de um trabalho ser enviado a fim de processar os dados e é removido após a conclusão do trabalho. Você pode criar um serviço vinculado para o ambiente de computação sob demanda, configurá-lo e controlar as configurações granulares da execução do trabalho, gerenciamento de cluster e ações de inicialização.

> [AZURE.NOTE] A configuração sob demanda tem suporte somente para clusters do Azure HDInsight.

## Serviço vinculado do Azure HDInsight sob demanda
O serviço Azure Data Factory pode criar automaticamente um cluster HDInsight sob demanda baseado em Windows/Linux para processar dados. O cluster é criado na região que é a mesma que a conta de armazenamento (propriedade linkedServiceName em JSON) associada ao cluster.

Observe os seguintes pontos **importantes** sobre o serviço vinculado HDInsight sob demanda:

- Você não verá o cluster HDInsight sob demanda criado na sua assinatura do Azure. O serviço do Azure Data Factory gerencia o cluster HDInsight sob demanda em seu nome.
- Os logs para trabalhos que são executados em um cluster HDInsight sob demanda são copiados para a conta de armazenamento associada ao cluster HDInsight. Você pode acessar esses logs do Portal Clássico do Azure na folha **Detalhes de execução da atividade**. Consulte o artigo [Monitorar e gerenciar pipelines](data-factory-monitor-manage-pipelines.md) para obter detalhes.
- Você será cobrado somente pelo tempo em que o cluster HDInsight estiver ativo e executando trabalhos.

> [AZURE.IMPORTANT] Ele normalmente demora mais do que **15 minutos** para provisionar um cluster Azure HDInsight sob demanda.

### Exemplo
O JSON a seguir define um serviço vinculado HDInsight sob demanda baseado em Linux. O serviço Data Factory cria automaticamente um cluster HDInsight **baseado em Linux** ao processar uma fatia de dados.


	{
	    "name": "HDInsightOnDemandLinkedService",
	    "properties": {
	        "type": "HDInsightOnDemand",
	        "typeProperties": {
	            "clusterSize": 4,
	            "timeToLive": "00:05:00",
	            "osType": "linux",
	            "linkedServiceName": "StorageLinkedService"
	        }
	    }
	}

Para usar um cluster HDInsight baseado em Windows, defina **osType** como **windows** ou não use a propriedade pois o valor padrão é: windows.

> [AZURE.IMPORTANT] 
O cluster HDInsight cria um **contêiner padrão** no armazenamento de blobs especificado em JSON (**linkedServiceName**). O HDInsight não exclui esse contêiner quando o cluster é excluído. Esse comportamento é intencional. Com o serviço vinculado HDInsight sob demanda, um cluster HDInsight é criado sempre que uma fatia precisa ser processada, a menos que haja um cluster ativo existente (**timeToLive**) e que ele seja excluído quando o processamento é realizado.
> 
> À medida que mais e mais fatias forem processadas, você verá muitos contêineres no armazenamento de blobs do Azure. Se você não precisa deles para solução de problemas dos trabalhos, convém excluí-los para reduzir o custo de armazenamento. O nome desses contêineres segue um padrão: "adf**yourdatafactoryname**-**linkedservicename**-datetimestamp". Use ferramentas como o [Gerenciador de Armazenamento da Microsoft](http://storageexplorer.com/) para excluir contêineres do armazenamento de blobs do Azure.

### Propriedades

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
type | A propriedade de tipo deve ser configurada como **HDInsightOnDemand**. | Sim
clusterSize | O tamanho do cluster sob demanda. Especifica quantos nós devem estar neste cluster sob demanda. | Sim
timetolive | O tempo ocioso permitido para o cluster HDInsight sob demanda. Especifica quanto tempo o cluster HDInsight sob demanda permanecerá ativo após a conclusão de uma atividade executada se não houver nenhum outro trabalho ativo no cluster.<br/><br/>Por exemplo, se uma execução de atividade demora 6 minutos e o timetolive é definido como 5 minutos, o cluster fica ativo durante 5 minutos após a execução de 6 minutos de execução da atividade. Se outra atividade é executada com a janela de 6 minutos, ela é processada pelo mesmo cluster.<br/><br/>Criar um cluster HDInsight sob demanda é uma operação custosa (pode demorar um pouco), então use essa configuração, conforme o necessário, para melhorar o desempenho de uma fábrica de dados com a reutilização de um cluster HDInsight sob demanda.<br/><br/>Se você definir o valor de timetolive como 0, o cluster é excluído assim que a atividade executada é processada. Por outro lado, se você definir um valor alto, o cluster pode permanecer ocioso desnecessariamente resultando em altos custos. Portanto, é importante que você defina o valor apropriado com base em suas necessidades.<br/><br/>Vários pipelines podem compartilhar a mesma instância do cluster do HDInsight sob demanda se o valor da propriedade timetolive estiver definido corretamente | Sim
versão | Versão do cluster HDInsight O valor padrão é 3.1 para cluster do Windows e 3.2 para o cluster do Linux. | Não
linkedServiceName | O armazenamento de blob a ser usado pelo cluster sob demanda para armazenar e processar dados. | Sim
additionalLinkedServiceNames | Especifica as contas de armazenamento adicionais para o serviço vinculado do HDInsight para que o serviço do Data Factory possa registrá-los em seu nome. | Não
osType | Tipo do sistema operacional. Valores permitidos são: Windows (padrão) e Linux | Não
hcatalogLinkedServiceName | O nome do serviço vinculado do SQL Azure que aponta para o banco de dados HCatalog. O cluster HDInsight sob demanda será criado usando o banco de dados SQL do Azure como o metastore. | Não


#### Exemplo de JSON additionalLinkedServiceNames

    "additionalLinkedServiceNames": [
        "otherLinkedServiceName1",
		"otherLinkedServiceName2"
  	]
 
### Propriedades avançadas

Você também pode especificar as seguintes propriedades para a configuração granular do cluster HDInsight sob demanda.

Propriedade | Descrição | Obrigatório
:-------- | :----------- | :--------
coreConfiguration | Especifica os parâmetros de configuração principal (como core-site. xml) para o cluster HDInsight a ser criado. | Não
hBaseConfiguration | Especifica os parâmetros de configuração HBase (hbase-site.xml) para o cluster HDInsight. | Não
hdfsConfiguration | Especifica os parâmetros de configuração HDFS (hdfs-site.xml) para o cluster HDInsight. | Não
hiveConfiguration | Especifica os parâmetros de configuração do hive (hive-site.xml) para o cluster HDInsight. | Não
mapReduceConfiguration | Especifica os parâmetros de configuração do MapReduce (mapred-site.xml) para o cluster HDInsight. | Não
oozieConfiguration | Especifica os parâmetros de configuração do Oozie (oozie-site.xml) para o cluster HDInsight. | Não
stormConfiguration | Especifica os parâmetros de configuração do Storm (storm-site.xml) para o cluster HDInsight. | Não
yarnConfiguration | Especifica os parâmetros de configuração do Yarn (yarn-site.xml) para o cluster HDInsight. | Não

#### Exemplo – configuração de cluster HDInsight sob demanda com propriedades avançadas

	{
	  "name": " HDInsightOnDemandLinkedService",
	  "properties": {
	    "type": "HDInsightOnDemand",
	    "typeProperties": {
	      "clusterSize": 16,
	      "timeToLive": "01:30:00",
	      "linkedServiceName": "adfods1",
	      "coreConfiguration": {
	        "templeton.mapper.memory.mb": "5000"
	      },
	      "hiveConfiguration": {
	        "templeton.mapper.memory.mb": "5000"
	      },
	      "mapReduceConfiguration": {
	        "mapreduce.reduce.java.opts": "-Xmx4000m",
	        "mapreduce.map.java.opts": "-Xmx4000m",
	        "mapreduce.map.memory.mb": "5000",
	        "mapreduce.reduce.memory.mb": "5000",
	        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
	      },
	      "yarnConfiguration": {
	        "yarn.app.mapreduce.am.resource.mb": "5000",
	        "mapreduce.map.memory.mb": "5000"
	      },
	      "additionalLinkedServiceNames": [
	        "datafeeds",
	        "adobedatafeed"
	      ]
	    }
	  }
	}

### Tamanhos dos nós
Você pode especificar os tamanhos de nós de dados, principais e zookeeper usando as seguintes propriedades.

Propriedade | Descrição | Obrigatório
:-------- | :----------- | :--------
headNodeSize | Especificar o tamanho do nó principal O valor padrão é: Grande. Veja a seção **Especificando tamanhos dos nós** abaixo para obter detalhes. | Não
dataNodeSize | Especifica o tamanho do nó principal O valor padrão é: Grande | Não
zookeeperNodeSize | Especifica o tamanho do nó Zoo Keeper. O valor padrão é: "Small". | Não
 
#### Especificar tamanhos de nós
Veja o artigo [Tamanhos de máquinas virtuais](../virtual-machines/virtual-machines-size-specs.md#size-tables) para obter valores de cadeia de caracteres que você precisa especificar para as propriedades acima. Os valores precisam estar em conformidade com os **CMDLETs e as APIS** mencionadas no artigo. Como você pode ver neste artigo, o nó de dados de tamanho grande (padrão) tem 7 GB de memória, que pode não ser suficiente para seu cenário.

Se quiser criar nós de cabeçalho e nós de trabalho em tamanho D4, você precisa especificar **Standard\_D4** como o valor das propriedades headNodeSize e dataNodeSize.

	"headNodeSize": "Standard_D4",	
	"dataNodeSize": "Standard_D4",

Se especificar um valor incorreto para essas propriedades, você pode receber o seguinte **erro:** falha ao criar o cluster. Exceção: Não foi possível concluir operação de criação do cluster. Falha na operação com o código ‘400’. Cluster deixou para trás estado: ‘Erro’. Mensagem: “PreClusterCreationValidationFailure”. Quando receber esse erro, verifique se está usando o nome **CMDLET e APIS** da tabela no artigo acima.



## Traga seu próprio ambiente de computação

Nesse tipo de configuração, os usuários podem registrar um ambiente de computação já existente como um serviço vinculado no Data Factory. O ambiente de computação é gerenciado pelo usuário e o serviço Data Factory o utiliza para executar as atividades.

Esse tipo de configuração tem suporte para os ambientes de computação a seguir:

- Azure HDInsight
- Lote do Azure
- Aprendizado de Máquina do Azure.

## Serviço vinculado do Azure HDInsight

Você pode criar um serviço vinculado Azure HDInsight para registrar seu próprio cluster HDInsight com o Data Factory.

### Exemplo

	{
	  "name": "HDInsightLinkedService",
	  "properties": {
	    "type": "HDInsight",
	    "typeProperties": {
	      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
	      "userName": "admin",
	      "password": "<password>",
	      "location": "WestUS",
	      "linkedServiceName": "MyHDInsightStoragelinkedService"
	    }
	  }
	}

### Propriedades

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
type | A propriedade de tipo deve ser configurada como **HDInsight**. | Sim
clusterUri | A URI do cluster HDInsight. | Sim
Nome de Usuário | Especifique o nome do usuário a ser usado para se conectar a um cluster HDInsight existente. | Sim
Senha | Especifique a senha para a conta de usuário. | Sim
location | Especifique o local do cluster do HDInsight (por exemplo, WestUS). | Sim
linkedServiceName | Nome do serviço vinculado para o armazenamento de blob usado por esse cluster HDInsight. | Sim

## Serviço vinculado de Lote do Azure

Você pode criar um serviço vinculado de Lote do Azure para registrar um pool de lote de máquinas virtuais (VMs) para uma fábrica de dados. Você pode executar atividades personalizadas do .NET usando o Lote do Azure ou o Azure HDInsight.

Veja os tópicos a seguir se você for novo no serviço de Lote do Azure:


- [Noções básicas do Lote do Azure](../batch/batch-technical-overview.md) para obter uma visão geral do serviço de Lote do Azure.
- Cmdlet [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) para criar uma conta do Lote do Azure (ou) [Portal Clássico do Azure](../batch/batch-account-create-portal.md) para criar a conta do Lote do Azure usando o Portal Clássico do Azure. Veja o tópico [Usando o PowerShell para gerenciar a Conta do Lote do Azure](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) para obter instruções detalhadas sobre como usar este cmdlet.
- Cmdlet [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) para criar um pool do Lote do Azure.

### Exemplo

	{
	  "name": "AzureBatchLinkedService",
	  "properties": {
	    "type": "AzureBatch",
	    "typeProperties": {
	      "accountName": "<Azure Batch account name>",
	      "accessKey": "<Azure Batch account key>",
	      "poolName": "<Azure Batch pool name>",
	      "linkedServiceName": "<Specify associated storage linked service reference here>"
	    }
	  }
	}

Acrescente "**.<region name**" ao nome da sua conta do Lote para a propriedade **accountName**. Exemplo:

			"accountName": "mybatchaccount.eastus"

Outra opção é fornecer o ponto de extremidade batchUri, conforme mostrado abaixo.

			"accountName": "adfteam",
			"batchUri": "https://eastus.batch.azure.com",

### Propriedades

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
type | A propriedade de tipo deve ser definida como **AzureBatch**. | Sim
accountName | Nome da conta do Lote do Azure. | Sim
accessKey | Tecla de acesso para a conta do Lote do Azure. | Sim
poolName | Nome do pool de máquinas virtuais. | Sim
linkedServiceName | Nome do serviço vinculado do Armazenamento do Azure associado ao serviço vinculado de Lote do Azure. Esse serviço vinculado é usado para arquivos de teste necessários para executar a atividade e armazenar os logs de execução da atividade. | Sim


## Serviço Vinculado de Aprendizado de Máquina do Azure

Criar um serviço vinculado de Aprendizado de Máquina do Azure para registrar um ponto de extremidade de pontuação do lote de Aprendizado de Máquina a uma fábrica de dados.

### Exemplo

	{
	  "name": "AzureMLLinkedService",
	  "properties": {
	    "type": "AzureML",
	    "typeProperties": {
	      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
	      "apiKey": "<apikey>"
	    }
	  }
	}

### Propriedades

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
Tipo | A propriedade de tipo deve ser configurada como **AzureML**. | Sim
mlEndpoint | A URL de pontuação do lote. | Sim
apiKey | A API do modelo de espaço de trabalho publicada. | Sim


## Serviço Vinculado da Análise Azure Data Lake
Você cria um serviço vinculado da **Análise Azure Data Lake** para vincular um serviço de computação da Análise do Azure Data Lake a um Azure Data Factory antes de usar a atividade do [U-SQL da Análise Data Lake](data-factory-usql-activity.md) em um pipeline.

O exemplo a seguir fornece uma definição de JSON para um serviço vinculado da Análise Azure Data Lake.

	{
	    "name": "AzureDataLakeAnalyticsLinkedService",
	    "properties": {
	        "type": "AzureDataLakeAnalytics",
	        "typeProperties": {
	            "accountName": "adftestaccount",
	            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
	            "authorization": "<authcode>",
				"sessionId": "<session ID>",
	            "subscriptionId": "<subscription id>",
	            "resourceGroupName": "<resource group name>"
	        }
	    }
	}


A tabela a seguir fornece as descrições das propriedades usadas na definição de JSON.

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
Tipo | A propriedade de tipo deve ser definida como: **AzureDataLakeAnalytics**. | Sim
accountName | Nome da conta da Análise Azure Data Lake. | Sim
dataLakeAnalyticsUri | URI da Análise Azure Data Lake. | Não
autorização | O código de autorização é recuperado automaticamente depois de clicar no botão **Autorizar** no Editor do Data Factory e concluir o logon OAuth. | Sim
subscriptionId | ID de assinatura do Azure | Não (se não for especificado, a assinatura do Data Factory é usada).
resourceGroupName | Nome do grupo de recursos do Azure | Não (se não for especificado, o grupo de recursos do Data Factory é usado).
sessionId | ID da sessão de autorização OAuth. Cada ID da sessão é exclusiva e pode ser usado somente uma vez. Isso é gerado automaticamente no Editor Data Factory. | Sim

O código de autorização gerado usando o botão **Autorizar** expira após algum tempo. Confira a tabela a seguir para ver os tempos de expiração para os diferentes tipos de contas de usuário. Talvez você veja a mensagem de erro a seguir quando o **token de autenticação expirar**: erro na operação da credencial: invalid\_grant - AADSTS70002: erro ao validar as credenciais. AADSTS70008: a concessão de acesso fornecida expirou ou foi revogada. ID do Rastreamento: d18629e8-af88-43c5-88e3-d8419eb1fca1 ID da Correlação: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Carimbo de data/hora: 2015-12-15 21:09:31Z

| Tipo de usuário | Expira após |
| :-------- | :----------- | 
| Contas de usuários NÃO gerenciadas pelo Azure Active Directory (@hotmail.com, @live.com, etc.) | 12 horas |
| Contas de usuários gerenciadas pelo AAD (Azure Active Directory) | 14 dias após a última execução da fatia. <br/>90 dias, se uma fatia com base em serviços vinculados do OAuth for executada pelo menos uma vez a cada 14 dias.<br/> |
 
Para evitar/resolver este erro, você precisará autorizar novamente usando o botão **Autorizar** quando o **token expirar** e reimplantar o serviço vinculado. Você também pode gerar valores para as propriedades sessionId e authorization programaticamente usando o código na seção a seguir.

### Para gerar valores sessionId e authorization programaticamente 
O código a seguir gera os valores **sessionId** e **authorization**.

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

Consulte os tópicos [Classe AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [Classe AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) e [Classe AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) para obter detalhes sobre as classes do Data Factory usadas no código. Você precisa adicionar uma referência a: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll para a classe WindowsFormsWebAuthenticationDialog.
 

## Serviço Vinculado do SQL do Azure

Você pode criar um serviço vinculado SQL do Azure e usá-lo com a [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado de um pipeline do Data Factory. Confira o artigo [Conector SQL do Azure](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) para saber mais sobre esse serviço vinculado.

<!---HONumber=AcomDC_0316_2016-->