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
	ms.date="08/04/2015" 
	ms.author="spelluru"/>

# Serviços vinculados de computação

Este artigo explica diferentes ambientes de computação que você pode usar para processar ou transformar dados. Ele também fornece detalhes sobre as diferentes configurações (sob demanda versus traga a sua própria) com suporte pela Data Factory ao configurar serviços vinculados que vinculam esses ambientes de computação para uma Azure Data Factory.

## Ambiente de computação sob demanda

Nesse tipo de configuração, o ambiente de computação é totalmente gerenciado pelo serviço do Azure Data Factory. Ele é automaticamente criado pelo serviço Data Factory antes de um trabalho ser enviado a fim de processar os dados e é removido após a conclusão do trabalho. Você pode criar um serviço vinculado para o ambiente de computação sob demanda, configurá-lo e controlar as configurações granulares da execução do trabalho, gerenciamento de cluster e ações de inicialização.

> [AZURE.NOTE]A configuração sob demanda tem suporte somente para clusters do Azure HDInsight.

## Serviço vinculado do Azure HDInsight sob demanda

O cluster HDInsight sob demanda é automaticamente criado pelo serviço do Azure Data Factory para processar dados. O cluster é criado na região que é a mesma que a conta de armazenamento (propriedade linkedServiceName em JSON) associada ao cluster.

Observe os seguintes pontos **importantes** sobre o serviço vinculado HDInsight sob demanda:

- Você não verá o cluster HDInsight sob demanda criado na sua assinatura do Azure. O serviço do Azure Data Factory gerencia o cluster HDInsight sob demanda em seu nome.
- Os logs para trabalhos que são executados em um cluster HDInsight sob demanda são copiados para a conta de armazenamento associada ao cluster HDInsight. Você pode acessar esses logs do Portal do Azure na folha **Detalhes de execução da atividade**. Consulte o artigo [Monitorar e gerenciar pipelines](data-factory-monitor-manage-pipelines.md) para obter detalhes. 
- Você será cobrado somente pelo tempo em que o cluster HDInsight estiver ativo e executando trabalhos.

> [AZURE.IMPORTANT]Ele normalmente demora mais do que **15 minutos** para provisionar um cluster Azure HDInsight sob demanda.

### Exemplo

	{
	  "name": "HDInsightOnDemandLinkedService",
	  "properties": {
	    "type": "HDInsightOnDemand",
	    "typeProperties": {
	      "clusterSize": 4,
	      "jobsContainer": "adfjobs",
	      "timeToLive": "00:05:00",
	      "version": "3.1",
	      "linkedServiceName": "MyBlobStore"
	      "additionalLinkedServiceNames": [
	        "otherLinkedServiceName1",
	        "otherLinkedServiceName2"
	      ]
	    }
	  }
	}

### Propriedades

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
type | A propriedade de tipo deve ser configurada como **HDInsightOnDemand**. | Sim
clusterSize | O tamanho do cluster sob demanda. Especifica quantos nós devem estar neste cluster sob demanda. | Sim 
jobscontainer | O contêiner de blob que contém os dados usados por trabalhos de hive/pig/pacote e onde os logs de cluster serão armazenados. | Sim
timetolive | <p>O tempo ocioso permitido para o cluster HDInsight sob demanda. Especifica quanto tempo o cluster HDInsight sob demanda permanecerá ativo após a conclusão de uma atividade executada se não houver nenhum outro trabalho ativo no cluster.</p><p>Por exemplo, se uma execução de atividade demora 6 minutos e o timetolive é definido como 5 minutos, o cluster fica ativo durante 5 minutos após a execução de 6 minutos de execução da atividade. Se outra atividade é executada com a janela de 6 minutos, ela é processada pelo mesmo cluster.</p><p>Criar um cluster HDInsight sob demanda é uma operação custosa (pode demorar um pouco), então use essa configuração, conforme o necessário, para melhorar o desempenho de uma fábrica de dados com a reutilização de um cluster HDInsight sob demanda.</p><p>Se você definir o valor de timetolive como 0, o cluster é excluído assim que a atividade executada é processada. Por outro lado, se você definir um valor alto, o cluster pode permanecer ocioso desnecessariamente resultando em altos custos. Portanto, é importante que você defina o valor apropriado com base em suas necessidades.</p><p>Vários pipelines podem compartilhar a mesma instância do cluster do HDInsight sob demanda se o valor da propriedade timetolive estiver definido corretamente</p> | Sim
versão | Versão do cluster HDInsight | Não
linkedServiceName | O armazenamento de blob a ser usado pelo cluster sob demanda para armazenar e processar dados. | Sim
additionalLinkedServiceNames | Especifica as contas de armazenamento adicionais para o serviço vinculado do HDInsight para que o serviço do Data Factory possa registrá-los em seu nome. | Não

### Propriedades avançadas

Você também pode especificar as seguintes propriedades para a configuração granular do cluster HDInsight sob demanda.

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
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
	      "jobsContainer": "adfjobs",
	      "timeToLive": "01:30:00",
	      "version": "3.1",
	      "linkedServiceName": "adfods1",
	      "coreConfiguration": {
	        "templeton.mapper.memory.mb": "5000"
	      },
	      "hiveConfiguration": {
	        "templeton.mapper.memory.mb": "5000"
	      },
	      "mapReduceConfiguration": {
	        "mapreduce.reduce.java.opts": "-Xmx8000m",
	        "mapreduce.map.java.opts": "-Xmx8000m",
	        "mapreduce.map.memory.mb": "5000",
	        "mapreduce.reduce.memory.mb": "5000",
	        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
	      },
	      "yarnConfiguration": {
	        "yarn.app.mapreduce.am.resource.mb": "5000"
	      },
	      "additionalLinkedServiceNames": [
	        "datafeeds",
	        "adobedatafeed"
	      ]
	    }
	  }
	}

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
 

- [Visão geral técnica do Lote do Azure](../batch/batch-technical-overview.md) para uma visão geral do serviço de Lote do Azure.
- Cmdlet [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) para criar uma conta do Lote do Azure (ou) [Portal de Gerenciamento do Azure](../batch/batch-technical-overview.md) para criar a conta do Lote do Azure usando o Portal de Gerenciamento do Azure. Consulte o tópico [Usando o PowerShell para gerenciar a conta do Lote do Azure](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) para obter instruções detalhadas sobre como usar este cmdlet.
- Cmdlet [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) para criar um pool de Lote do Azure.

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

			accountName: "adfteam",
			batchUri: "https://eastus.batch.azure.com",

### Propriedades

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
type | A propriedade de tipo deve ser configurada como **AzureBatch**. | Sim
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


## Serviço Vinculado do SQL do Azure

Você pode criar um serviço vinculado do SQL do Azure e usá-lo com a [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado de um pipeline de Data Factory. Consulte o artigo [Conector SQL do Azure](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) para obter detalhes sobre esse serviço vinculado.


  



     
 
   

<!---HONumber=August15_HO7-->