<properties
	pageTitle="Tutorial do Data Factory: primeiro pipeline de dados | Microsoft Azure"
	description="Este tutorial do Azure Data Factory mostra como criar e agendar um data factory que processa os dados usando o script Hive em um cluster Hadoop."
	services="data-factory"
	keywords="tutorial do azure data factory , cluster hadoop, hive do hadoop"
	documentationCenter=""
	authors="spelluru"
	manager=""
	editor=""/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="06/17/2016"
	ms.author="spelluru"/>

# Tutorial: Criar seu primeiro pipeline para processar dados usando cluster Hadoop 
> [AZURE.SELECTOR]
- [Visão geral do tutorial](data-factory-build-your-first-pipeline.md)
- [Como usar o Editor do Data Factory](data-factory-build-your-first-pipeline-using-editor.md)
- [Como usar o Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Usando o PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Como usar o Modelo do Gerenciador de Recursos](data-factory-build-your-first-pipeline-using-arm.md)
- [Usando a API REST](data-factory-build-your-first-pipeline-using-rest-api.md)

Neste tutorial, você cria seu primeiro data factory no Azure com um pipeline de dados que os processa executando o script do Hive em um cluster Azure HDInsight (Hadoop).

Este artigo fornece uma **visão geral** do tutorial e instruções passo a passo para atender aos **pré-requisitos** do tutorial. Depois de concluir as etapas de pré-requisito, você usa um destes procedimentos para fazer o tutorial: Editor do Data Factory no Portal do Azure, Visual Studio, Azure PowerShell e modelo do Azure Resource Manager.

Observe que este artigo não fornece uma visão geral conceitual do Azure Data Factory. Para obter uma visão geral conceitual do serviço, confira [Introdução ao Azure Data Factory](data-factory-introduction.md).

## O que é abordado neste tutorial?	
O **Azure Data Factory** permite compor tarefas de **movimentação** e de **processamento** de dados como fluxos de trabalho de orientados a dados (também chamados de pipelines de dados). Você aprende a criar seu primeiro pipeline de dados com uma tarefa de processamento de dados (ou transformação de dados) que usa um cluster HDInsight do Azure para transformar e analisar logs da Web e agendar o pipeline para execução mensalmente.

Neste tutorial, você executa as seguintes etapas:

1.	Crie o **data factory**. Um data factory pode conter um ou mais pipelines de dados que movem e processam os dados.
2.	Criar os **serviços vinculados**. Crie um serviço vinculado para vincular um armazenamento de dados ou um serviço de computação ao data factory. Um armazenamento de dados, como o Armazenamento do Azure, armazena dados de entrada/saída de atividades no pipeline. Um serviço de computação como o Azure HDInsight processa/transforma dados.
3.	Criar **conjuntos de dados** de entrada e saída. Um conjunto de dados de entrada representa a entrada de uma atividade no pipeline e um conjunto de dados de saída representa a saída da atividade.
3.	Crie o **pipeline**. Um pipeline pode ter uma ou mais atividades, como a Atividade de Cópia para copiar dados de uma origem para um destino (ou) a Atividade do Hive do HDInsight para transformar dados de entrada usando o script do Hive para produzir dados de saída. Este exemplo usa a atividade do Hive do HDInsight que executa um script do Hive. Primeiro, o script cria uma tabela externa que faz referência aos dados brutos de log da Web armazenados no armazenamento de blobs do Azure e então particiona os dados brutos por ano e por mês.

Seu primeiro pipeline, chamado **MyFirstPipeline**, usa uma atividade do Hive para transformar e analisar um log da Web que você carrega na pasta **inputdata** no contêiner **adfgetstarted** (adfgetstarted/inputdata) em seu armazenamento de blobs do Azure.
 
![Exibição de diagrama no tutorial do Data Factory](./media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


Neste tutorial, adfgetstarted (contêiner) => inputdata (pasta) contém um arquivo chamado input.log. Esse arquivo de log tem entradas de três meses: janeiro, fevereiro e março de 2014. Veja as linhas de exemplo para cada mês no arquivo de entrada.

	2014-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
	2014-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
	2014-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871

Quando o arquivo é processado pelo pipeline com a Atividade do Hive do HDInsight, a atividade executa um script do Hive no cluster do HDInsight que particiona dados de entrada por ano e por mês. O script cria três pastas de saída com um arquivo com entradas de cada mês.

	adfgetstarted/partitioneddata/year=2014/month=1/000000_0
	adfgetstarted/partitioneddata/year=2014/month=2/000000_0
	adfgetstarted/partitioneddata/year=2014/month=3/000000_0

Das linhas do exemplo mostradas acima, a primeira delas (com 2014-01-01) é gravada no arquivo 000000\_0 na pasta do mês = 1. Da mesma forma, a segunda é gravada no arquivo na pasta do mês = 2 e a terceira é gravada no arquivo na pasta do mês = 3.


## Pré-requisitos
Antes de iniciar este tutorial, você deverá ter os seguintes pré-requisitos:

1.	**Uma assinatura do Azure** - se você não tiver uma, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Consulte o artigo [Avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) para ver como você pode obter uma conta de avaliação gratuita.

2.	**Armazenamento do Azure** – você usa uma conta de armazenamento do Azure para armazenar os dados neste tutorial. Se você não tiver uma conta de armazenamento do Azure, consulte o artigo [Criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account). Depois de criar a conta de armazenamento, você precisa obter a chave de conta usada para acessar o armazenamento. Consulte [Exibir, copiar e regenerar chaves de acesso de armazenamento](../storage/storage-create-storage-account.md#view-and-copy-storage-access-keys).

## Carregar arquivos no Armazenamento do Azure para seguir o tutorial
Antes de iniciar o tutorial, você precisa preparar o armazenamento do Azure com arquivos necessários para o tutorial.

Nesta seção, você fará o seguinte:

2. Carregue o arquivo de consulta do Hive (HQL) na pasta **script** do contêiner **adfgetstarted**.
3. Carregue o arquivo de entrada na pasta **script** do contêiner **adfgetstarted**.

### Criar arquivo de script HQL 

1. Inicie o **Bloco de Notas** e cole o script HQL a seguir. Esse script do Hive cria duas tabelas: **WebLogsRaw** e **WebLogsPartitioned**. Clique em **Arquivo** no menu e selecione **Salvar como**. Alterne para a pasta **C:\\adfgettingstarted** no disco rígido. Selecione **Todos os Arquivos (*.*)** para o campo **Salvar como tipo**. Insira **partitionweblogs.hql** para o **Nome do arquivo**. Confirme se o campo **Codificação** na parte inferior da caixa de diálogo está definido como **ANSI**. Se não estiver, defina-o como **ANSI**.

		set hive.exec.dynamic.partition.mode=nonstrict;
		
		DROP TABLE IF EXISTS WebLogsRaw; 
		CREATE TABLE WebLogsRaw (
		  date  date,
		  time  string,
		  ssitename string,
		  csmethod  string,
		  csuristem  string,
		  csuriquery string,
		  sport int,
		  susername string,
		  cipcsUserAgent string,
		  csCookie string,
		  csReferer string,
		  cshost  string,
		  scstatus  int,
		  scsubstatus  int,
		  scwin32status  int,
		  scbytes int,
		  csbytes int,
		  timetaken int
		)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
		LINES TERMINATED BY '\n' 
		tblproperties ("skip.header.line.count"="2");
		
		LOAD DATA INPATH '${hiveconf:inputtable}' OVERWRITE INTO TABLE WebLogsRaw;
		
		DROP TABLE IF EXISTS WebLogsPartitioned ; 
		create external table WebLogsPartitioned (  
		  date  date,
		  time  string,
		  ssitename string,
		  csmethod  string,
		  csuristem  string,
		  csuriquery string,
		  sport int,
		  susername string,
		  cipcsUserAgent string,
		  csCookie string,
		  csReferer string,
		  cshost  string,
		  scstatus  int,
		  scsubstatus  int,
		  scwin32status  int,
		  scbytes int,
		  csbytes int,
		  timetaken int
		)
		partitioned by ( year int, month int)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
		STORED AS TEXTFILE 
		LOCATION '${hiveconf:partitionedtable}';
		
		INSERT INTO TABLE WebLogsPartitioned  PARTITION( year , month) 
		SELECT
		  date,
		  time,
		  ssitename,
		  csmethod,
		  csuristem,
		  csuriquery,
		  sport,
		  susername,
		  cipcsUserAgent,
		  csCookie,
		  csReferer,
		  cshost,
		  scstatus,
		  scsubstatus,
		  scwin32status,
		  scbytes,
		  csbytes,
		  timetaken,
		  year(date),
		  month(date)
		FROM WebLogsRaw

No tempo de execução, a Atividade do Hive no pipeline do Data Factory passa valores para os parâmetros inputtable e partitionedtable, conforme mostrado abaixo; nomedacontadearmazenamento é o nome da sua conta de armazenamento do Azure:

		"inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
		"partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
 
### Criar um arquivo de entrada de exemplo
Usando o bloco de notas, crie um arquivo chamado **input.log** na pasta **c:\\adfgetstarted** com o seguinte conteúdo:

	#Software: Microsoft Internet Information Services 8.0
	#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken
	2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46
	2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32
	2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47
	2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step5.png X-ARR-LOG-ID=9b0c14b1-434d-495a-9b0d-46775194257b 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 37931 871 32
	2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step6.png X-ARR-LOG-ID=f99cff81-ec38-4a13-b2fe-21b10adca996 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 27146 871 47
	2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step1.png X-ARR-LOG-ID=af94d3b4-8e05-4871-82c4-638f866d4e83 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 66259 871 140
	2014-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2014-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2014-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2014-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2014-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2014-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2014-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2014-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2014-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2014-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2014-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2014-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
	2014-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47

### Carregar o arquivo de entrada e o arquivo HQL no Armazenamento de Blobs do Azure

Esta seção fornece instruções sobre o uso da ferramenta **AzCopy** para copiar arquivos para o Armazenamento de Blobs do Azure. Você pode usar qualquer ferramenta que desejar (por exemplo: [Gerenciador de Armazenamento do Microsoft Azure](http://storageexplorer.com/), [CloudXPlorer da ClumsyLeaf Software](http://clumsyleaf.com/products/cloudxplorer)) para realizar essa tarefa.
	 
2. Como preparar o armazenamento do Azure para o tutorial:
	1. Baixe a [versão mais recente do **AzCopy**](http://aka.ms/downloadazcopy) ou a [versão de visualização mais recente](http://aka.ms/downloadazcopypr). Consulte o artigo [Como usar o AzCopy](../storage/storage-use-azcopy.md) para obter instruções sobre o uso do utilitário.
	2. Após a instalação do AzCopy, você pode adicioná-lo ao caminho do sistema, executando o seguinte comando no prompt de comando.
	
			set path=%path%;C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy

	3. Navegue até a pasta c:\\adfgettingstarted e execute o seguinte comando para carregar o arquivo **input.log** na conta de armazenamento (o contêiner **adfgetstarted** e a pasta **inputdata**). Substitua **StorageAccountName** pelo nome da sua conta de armazenamento e **Storage Key** pela chave de armazenamento da conta.

			AzCopy /Source:. /Dest:https://<storageaccountname>.blob.core.windows.net/adfgetstarted/inputdata /DestKey:<storagekey>  /Pattern:input.log

		> [AZURE.NOTE] O comando acima cria um contêiner denominado **adfgetstarted** em seu armazenamento de Blobs do Azure e copia o arquivo **partitionweblogs.hql** da sua unidade local para a pasta **inputdata** no contêiner.
	
	5. Depois que o arquivo tiver sido carregado com êxito, você verá a saída semelhante à seguinte do AzCopy.
	
			Finished 1 of total 1 file(s).
			[2015/12/16 23:07:33] Transfer summary:
			-----------------
			Total files transferred: 1
			Transfer successfully:   1
			Transfer skipped:        0
			Transfer failed:         0
			Elapsed time:            00.00:00:01
	1. Execute o comando a seguir para carregar o arquivo **partitionweblogs.hql** para a pasta **script** do contêiner **adfgetstarted**. Veja o código:
	
			AzCopy /Source:. /Dest:https://<storageaccountname>.blob.core.windows.net/adfgetstarted/script /DestKey:<storagekey>  /Pattern:partitionweblogs.hql


Agora você está pronto para começar o tutorial. Clique em uma das guias na parte superior para criar seu primeiro data factory do Azure ou clique em um dos links a seguir.

- [Como usar o Editor do Data Factory](data-factory-build-your-first-pipeline-using-editor.md)
- [Como usar o Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Usando o PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Como usar o Modelo do Gerenciador de Recursos](data-factory-build-your-first-pipeline-using-arm.md)

<!---HONumber=AcomDC_0817_2016-->