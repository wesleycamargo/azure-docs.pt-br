<properties 
	pageTitle="Mover e processar arquivos de log usando o Data Factory do Azure" 
	description="Este tutorial avançada descreve um cenário do mundo real quase e implementa o cenário usando o serviço de fábrica de dados do Azure e dados de fábrica do Editor." 
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
	ms.date="05/18/2015" 
	ms.author="spelluru"/>

# Tutorial: Medir a eficiência de uma campanha de marketing  
A Contoso é uma empresa de jogos que cria os jogos para várias plataformas: computadores pessoais (PCs), dispositivos de mão mantido e consoles de jogos. Esses jogos geram muitos registros e objetivo da Contoso é coletar e analisar esses logs para obter informações sobre as preferências do cliente, dados demográficos, comportamento de uso etc. para identificar oportunidades de venda e vendas cruzadas, desenvolver novos recursos atraentes ao crescimento do negócio de unidade e proporcionar uma melhor experiência para os clientes.

Neste tutorial, você irá criar pipelines de fábrica de dados para avaliar a eficiência de uma campanha de marketing Contoso recentemente lançou por coletar logs de exemplo, processamento e aprimorando-los com dados de referência e transformar os dados. Ele tem três tubulações a seguir:

1.	O **PartitionGameLogsPipeline** lê os eventos brutos de jogos do armazenamento de blob e cria partições com base no ano, mês e dia.
2.	O **EnrichGameLogsPipeline** junta eventos jogos particionados com dados de referência de código geográfica e enriquece os dados pelo mapeamento de endereços IP para os locais de geográfica correspondentes.
3.	O **AnalyzeMarketingCampaignPipeline** pipeline aproveita os dados rico e processa-os com os dados de anúncio para criar a saída final que contém a eficácia de campanha de marketing.

## Preparando-se para o tutorial
1.	Leitura [Introdução ao Azure dados Factory][adfintroduction] para obter uma visão geral da fábrica de dados do Azure e compreensão dos conceitos de nível superior.
2.	Você deve ter uma assinatura do Azure para ver este tutorial. Para obter informações sobre como adquirir uma assinatura, consulte [Opções de compra](http://azure.microsoft.com/pricing/purchase-options/), [Ofertas para membros](http://azure.microsoft.com/pricing/member-offers/) ou [Avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/).
3.	Você deve baixar e instalar [PowerShell do Azure][download-azure-powershell] em seu computador. Você vai executar cmdlets de fábrica de dados para carregar dados de exemplo e scripts de pig/hive para seu armazenamento de blob. 
2.	**(recomendado)** revisão e pratique o tutorial a [Introdução ao Azure Data Factory][adfgetstarted] artigo para obter um tutorial simple para se familiarizar com o portal e cmdlets.
3.	**(recomendado)** revisão e praticar o passo a passo no [usar o Pig e Hive com o Azure dados Factory][usepigandhive] artigo para obter uma explicação sobre como criar um pipeline para mover os dados da fonte de dados local para um armazenamento de BLOBs do Azure.
4.	Baixe [ADFWalkthrough][adfwalkthrough-download] arquivos **C:\ADFWalkthrough** pasta **preservar a estrutura de pastas**:
	- **Pipelines:** inclui arquivos JSON que contém a definição dos pipelines.
	- **Tabelas:** inclui arquivos JSON que contém a definição das tabelas.
	- **LinkedServices:** inclui arquivos JSON que contém a definição do armazenamento e computação (HDInsight) cluster 
	- **Scripts:** inclui scripts de Hive e Pig que são usados para processar os dados e invocados a partir de pipelines
	- **SampleData:** inclui dados de exemplo para essa explicação passo a passo
	- **OnPremises:** inclui arquivos JSON e scripts que são usados para demonstrar o acesso aos seus dados locais
	- **uploadSampleDataAndScripts.ps1:** esse script carrega os dados de exemplo e scripts para o Azure.
5. Verifique se que você criou os seguintes recursos do Azure:			
	- Conta de Armazenamento do Azure.
	- Banco de Dados SQL do Azure
	- Cluster de HDInsight do Azure da versão 3.1 ou acima (ou use uma demanda HDInsight cluster que o serviço de dados fábrica criará automaticamente)	
7. Depois que os recursos do Azure são criados, verifique se que você tem as informações necessárias para se conectar a cada um desses recursos.
 	- **Conta de armazenamento do azure** - o nome da conta e chave de conta.  
	- **Banco de dados do SQL azure** -servidor, banco de dados, nome de usuário e senha.
	- **Azure HDInsight Cluster**.-nome do cluster do HDInsight, nome de usuário, senha e nome da conta e chave de conta para o armazenamento do Azure associado a esse cluster. Se você quiser usar um cluster de HDInsight sob demanda em vez de seu próprio cluster HDInsight, você pode ignorar esta etapa.  
8. Iniciar **PowerShell do Azure** e execute os seguintes comandos. Mantenha o Azure PowerShell aberta. Se você fechar e reabrir, você precisará executar esses comandos novamente.
	- Executar **Add-AzureAccount** e insira o nome de usuário e senha que você usa para entrar no portal de visualização do Azure.  
	- Executar **Get-AzureSubscription** para exibir todas as assinaturas para essa conta.
	- Executar **Select-AzureSubscription** para selecionar a assinatura que você deseja trabalhar. Esta assinatura deve ser a mesma que você usou no Portal de Visualização do Azure.	

## Visão geral
O fluxo de trabalho de ponta a ponta é descrito abaixo:

![Tutorial fluxo de ponta a ponta][image-data-factory-tutorial-end-to-end-flow]

1. O **PartitionGameLogsPipeline** lê os eventos brutos de jogos de um armazenamento de blob (RawGameEventsTable) e cria partições com base no ano, mês e dia (PartitionedGameEventsTable).
2. O **EnrichGameLogsPipeline** junções eventos particionados jogos (PartitionedGameEvents tabela, que é uma saída do PartitionGameLogsPipeline) com código geográfica (RefGetoCodeDictionaryTable) e enriquece dados mapeando um endereço IP para a correspondente localização geográfica (EnrichedGameEventsTable).
3. O **AnalyzeMarketingCampaignPipeline** pipeline aproveita os dados rico (EnrichedGameEventTable produzido pelo EnrichGameLogsPipeline) e processa-os com os dados de anúncio (RefMarketingCampaignnTable) para criar a saída final da eficiência de campanha de marketing que são copiados para o banco de dados do SQL Azure (MarketingCampainEffectivensessSQLTable) e um armazenamento de BLOBs do Azure (MarketingCampaignEffectivenessBlobTable) para análise.
    
## Passo a passo: Criar, implantar e monitorar fluxos de trabalho
1. [Etapa 1: carregar dados de exemplo e scripts](#MainStep1). Nesta etapa, você carregará todos os dados de exemplo (incluindo todos os logs e dados de referência) e scripts do Hive/Pig que serão executadas pelos fluxos de trabalho. Os scripts que você executa também criam um banco de dados SQL do Azure (chamado MarketingCampaigns), tabelas, tipos definidos pelo usuário e procedimentos armazenados.
2. [Etapa 2: criar uma fábrica de dados do Azure](#MainStep2). Nesta etapa, você criará uma data factory do Azure chamado LogProcessingFactory.
3. [Etapa 3: criar serviços vinculados](#MainStep3). Nesta etapa, você criará os seguintes serviços vinculados: 
	
	- 	**StorageLinkedService**. Vincula o local de armazenamento do Azure que contém eventos brutos de jogos, eventos de jogos particionados, eventos de jogos enriquecidos, informações de eficácia de campanha de marketing, dados de referência do código geográfico e dados de referência da campanha de marketing para o LogProcessingFactory   
	- 	**AzureSqlLinkedService**. Vincula um banco de dados SQL do Azure que contém informações de eficácia de campanha de marketing. 
	- 	**HDInsightStorageLinkedService**. Vincula um armazenamento de blob do Azure que está associado ao cluster HDInsight ao qual HDInsightLinkedService se refere. 
	- 	**HDInsightLinkedService**. Vincula um cluster HDInsight do Azure ao LogProcessingFactory. Este cluster é usado para executar o processamento do pig/hive nos dados. 
 		
4. [Etapa 4: criar tabelas](#MainStep4). Nesta etapa, você criará as seguintes tabelas:
	
	- **RawGameEventsTable**. Esta tabela especifica o local dos dados de eventos de jogos brutos dentro do armazenamento de blob do Azure definido pelo StorageLinkedService (adfwalkthrough/logs/rawgameevents/). 
	- **PartitionedGameEventsTable**. Esta tabela especifica o local dos dados de eventos de jogos particionados dentro do armazenamento de blob do Azure definido pelo StorageLinkedService (adfwalkthrough/logs/partitionedgameevents/). 
	- **RefGeoCodeDictionaryTable**. Esta tabela especifica o local dos dados de referência do código geográfico dentro do armazenamento de blob do Azure definido pelo StorageLinkedService (adfwalkthrough/refdata/refgeocodedictionary/).
	- **RefMarketingCampaignTable**. Esta tabela especifica o local dos dados de referência da campanha de marketing dentro do armazenamento de blob do Azure definido pelo StorageLinkedService (adfwalkthrough/refdata/refmarketingcampaign/).
	- **EnrichedGameEventsTable**. Esta tabela especifica o local dos dados enriquecidos de eventos de jogos dentro do armazenamento de blob do Azure definido pelo StorageLinkedService (adfwalkthrough/logs/enrichedgameevents/).
	- **MarketingCampaignEffectivenessSQLTable**. Esta tabela especifica a tabela SQL (MarketingCampaignEffectiveness) no banco de dados de SQL Azure definido pelo AzureSqlLinkedService que contém dados de eficácia de campanha de marketing. 
	- **MarketingCampaignEffectivenessBlobTable**. Esta tabela especifica o local dos dados de eficácia da campanha de marketing dentro do armazenamento de blob do Azure definido pelo StorageLinkedService (adfwalkthrough/marketingcampaigneffectiveness/). 

	
5. [Etapa 5: criar e agendar pipelines](#MainStep5). Nesta etapa, você criará os seguintes pipelines:
	- **PartitionGameLogsPipeline**. Este pipeline lê os eventos brutos de jogos de um armazenamento de blob (RawGameEventsTable) e cria partições com base no ano, mês e dia (PartitionedGameEventsTable). 


		![Pipeline de PartitionGamesLogs][image-data-factory-tutorial-partition-game-logs-pipeline]


	- **EnrichGameLogsPipeline**. Este pipeline junta eventos de jogos particionados (tabela PartitionedGameEvents, que é uma saída do PartitionGameLogsPipeline) com o código geográfico (RefGetoCodeDictionaryTable) e enriquece dados mapeando um endereço IP para a localização geográfica correspondente (EnrichedGameEventsTable)

		![EnrichedGameLogsPipeline][image-data-factory-tutorial-enrich-game-logs-pipeline]

	- **AnalyzeMarketingCampaignPipeline**. Este pipeline aproveita os dados enriquecidos (EnrichedGameEventTable gerado pelo EnrichGameLogsPipeline) e os processa com os dados de anúncio (RefMarketingCampaignnTable) para criar a saída final da eficiência de campanha de marketing campaign, que é copiada para o banco de dados SQL do Azure (MarketingCampainEffectivensessSQLTable) e um armazenamento de blob do Azure (MarketingCampaignEffectivenessBlobTable) para análise


		![MarketingCampaignPipeline][image-data-factory-tutorial-analyze-marketing-campaign-pipeline]


6. [Etapa 6: monitorar fatias de dados e pipelines](#MainStep6). Nesta etapa, você monitorará as fatias de dados, tabelas e pipelines usando o Portal do Azure.

## <a name="MainStep1"></a> Etapa 1: Carregar dados de exemplo e scripts
Nesta etapa, você carregará todos os dados de exemplo (incluindo todos os logs e dados de referência) e scripts do Hive/Pig que são executadas pelos fluxos de trabalho. Os scripts que você execute também criam um banco de dados do SQL Azure chamado **MarketingCampaigns**, tabelas, tipos definidos pelo usuário e procedimentos armazenados.

As tabelas, os tipos definidos pelo usuário e procedimentos armazenados são usados ao mover os resultados da Eficácia de Campanha de Marketing do armazenamento de blobs do Azure para o banco de dados SQL do Azure.

1. Abra **uploadSampleDataAndScripts.ps1** de **C:\ADFWalkthrough** pasta (ou a pasta que contém os arquivos extraídos) em seu editor favorito, substitua o realçado com suas informações de cluster e salve o arquivo.


		$storageAccount = <storage account name>
		$storageKey = <storage account key>
		$azuresqlServer = <sql azure server>.database.windows.net
		$azuresqlUser = <sql azure user>@<sql azure server>
		$azuresqlPassword = <sql azure password>

 
	> [AZURE.NOTE][baixar][sqlcmd-install]   
2. Confirme que seu computador local tem permissão para acessar o Banco de Dados SQL do Azure. Para habilitar o acesso, use o **Portal de gerenciamento** ou **sp_set_firewall_rule** no banco de dados mestre para criar uma regra de firewall para o endereço IP do seu computador. Pode levar até cinco minutos para que essa alteração tenha efeito. Consulte [Definindo regras de firewall para SQL Azure][azure-sql-firewall].
4. No PowerShell do Azure, navegue até o local onde você extraiu os exemplos (por exemplo: **C:\ADFWalkthrough**)
5. Executar **uploadSampleDataAndScripts.ps1** 
6. Depois que o script for executado com êxito, você verá o seguinte:

		$storageAccount = <storage account name>
		PS C:\ ADFWalkthrough> & '.\uploadSampleDataAndScripts.ps1'

		Name			PublicAccess		LastModified
		-----			--------		------
		ADFWalkthrough		off			6/6/2014 6:53:34 PM +00:00
	
		Uploading sample data and script files to the storage container [adfwalkthrough]

		Container Uri: https://<yourblobstorage>.blob.core.windows.net/adfwalkthrough

		Name                        BlobType   Length   ContentType               LastModified                        
		----                        --------   ------   -----------               ------------                        
		logs/rawgameevents/raw1.csv  BlockBlob  12308   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw2.csv  BlockBlob  16119   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw3.csv  BlockBlob  16062   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw4.csv  BlockBlob  16245   application/octet-stream  6/6/2014 6:54:35 PM 
		refdata/refgeocodedictiona.. BlockBlob  18647   application/octet-stream  6/6/2014 6:54:36 PM 
		refdata/refmarketingcampai.. BlockBlob  8808    application/octet-stream  6/6/2014 6:54:36 PM
		scripts/partitionlogs.hql    BlockBlob  2449    application/octet-stream  6/6/2014 6:54:36 PM 
		scripts/enrichlogs.pig       BlockBlob  1631    application/octet-stream  6/6/2014 6:54:36 PM
		scripts/transformdata.hql    BlockBlob  1753    application/octet-stream  6/6/2014 6:54:36 PM

		6/6/2014 11:54:36 AM Summary
		6/6/2014 11:54:36 AM 1. Uploaded Sample Data Files to blob container.
		6/6/2014 11:54:36 AM 2. Uploaded Sample Script Files to blob container.
		6/6/2014 11:54:36 AM 3. Created ‘MarketingCampaigns’ Azure SQL database and tables.
		6/6/2014 11:54:36 AM You are ready to deploy Linked Services, Tables and Pipelines. 

## <a name="MainStep2"></a> Etapa 2: Criar uma fábrica de dados do Azure
Nesta etapa, você cria uma fábrica de dados do Azure denominada **LogProcessingFactory**.

1.	Após fazer logon na [Azure Preview Portal][azure-preview-portal], clique em **novo** no canto inferior esquerdo, clique em **análises de dados** no **criar** blade e clique em **Data Factory** no **análises de dados** blade. 

	![Novo -> DataFactory][image-data-factory-new-datafactory-menu]

5. No **nova fábrica de dados** lâmina, digite **LogProcessingFactory** para o **nome**.

	![Blade de fábrica de dados][image-data-factory-tutorial-new-datafactory-blade]

6. Se você não criou um grupo de recursos do Azure denominado **ADF** faça o seguinte:
	1. Clique em **nome do grupo de recursos**, e clique em **criar um novo grupo de recursos**.
	
		![Lâmina do grupo de recursos][image-data-factory-tutorial-resourcegroup-blade]
	2. No **Criar grupo de recursos** lâmina, digite **ADF** para o nome do grupo de recursos e clique em **OK**.
	
		![Criar grupo de recursos][image-data-factory-tutorial-create-resourcegroup]
7. Selecione **ADF** para o **nome do grupo de recursos**.  
8.	No **nova fábrica de dados** lâmina, observe que **Adicionar ao quadro inicial** é selecionada por padrão. Isso pode adicionar um link ao data factory no quadro inicial (o que você vê quando ao fazer logon no Portal de Visualização do Azure).

	![Criar dados lâmina de fábrica][image-data-factory-tutorial-create-datafactory]

9.	No **nova fábrica de dados** lâmina, clique em **criar** para criar a fábrica de dados.
10.	Depois que a fábrica de dados é criada, você deverá ver o **DATA FACTORY** lâmina intitulada **LogProcessingFactory**.

	![Home page de fábrica de dados][image-data-factory-tutorial-datafactory-homepage]

	
	Se você não vê-la, siga um destes procedimentos:

	- Clique em **LogProcessingFactory** sobre o **Startboard** (página inicial)
	- Clique em **Procurar** à esquerda, clique em **tudo**, clique em **fábricas de dados**, e clique na fábrica de dados.
 
	> [AZURE.NOTE]O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro: **dados fábrica nome "LogProcessingFactory" não está disponível**, altere o nome (por exemplo, yournameLogProcessingFactory). Use esse nome em vez de LogProcessingFactory ao executar as etapas neste tutorial.
 
## <a name="MainStep3"></a> Etapa 3: Criar serviços vinculados

> [AZURE.NOTE]Este artigo usa o Portal do Azure, especificamente o Editor de fábrica de dados, para criar pipelines, tabelas e serviços vinculados. Consulte [Tutorial usando o PowerShell Azure][adftutorial-using-powershell] para realizar este tutorial usando o PowerShell do Azure.

Nesta etapa, você criará os seguintes serviços vinculados:

- StorageLinkedService
- AzureSqlLinkedService
- HDInsightStorageLinkedService
- HDInsightLinkedService. 

### Criar StorageLinkedService e HDInsightStorageLinkedService

1.	No **DATA FACTORY** lâmina, clique em **autor e implantar** lado a lado para iniciar o **Editor** da fábrica de dados.

	![Bloco Criar e implantar][image-author-deploy-tile]

	> [AZURE.NOTE]Consulte [dados fábrica de Editor][data-factory-editor] tópico de visão geral detalhada do editor de fábrica de dados.

2.  No **Editor**, clique em **novo armazenamento de dados** botão na barra de ferramentas e selecione **armazenamento do Azure** no menu suspenso. Você deve ver o modelo JSON para criar um serviço de armazenamento do Azure vinculado no painel à direita.
	
	![Botão de repositório de dados novo editor][image-editor-newdatastore-button]

3. Substitua **accountname** e **accountkey** com o nome de conta e valores de chave de conta para sua conta de armazenamento do Azure.

	![Armazenamento de Blob do Editor de JSON][image-editor-blob-storage-json]
	
	> [AZURE.NOTE]Consulte [referência de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971) para obter detalhes sobre as propriedades JSON.

4. Clique em **Deploy** na barra de ferramentas para implantar o StorageLinkedService. Confirme que você vê a mensagem **VINCULADO serviço criado com êxito** na barra de título.

	![Implantar o armazenamento de Blob do Editor][image-editor-blob-storage-deploy]

5. Repita as etapas para criar outro armazenamento do Azure vinculado nomeado: **HDInsightStorageLinkedService** do armazenamento associada ao cluster HDInsight. No script JSON para o serviço vinculado, altere o valor da **nome** propriedade **HDInsightStorageLinkedService**.

### Criar AzureSqlLinkedService
1. No **dados fábrica de Editor** , clique em **novo armazenamento de dados** botão na barra de ferramentas e selecione **banco de dados do SQL Azure** no menu suspenso. Você deve ver o modelo JSON para criar o serviço do SQL Azure vinculado no painel à direita.
2. Substitua **servername**, **username@servername**, e **senha** com nomes de seu servidor do SQL Azure, conta de usuário e senha. 3. Substitua **databasename** com **MarketingCampaigns**. Isso é o banco de dados do SQL Azure criado pelos scripts que você executou na etapa 1. Você deve confirmar que esse banco de dados foi realmente criado pelos scripts (se houve erros). 
3. Clique em **Deploy** na barra de ferramentas para criar e implantar o AzureSqlLinkedService.

### Criar HDInsightLinkedService
O serviço Data Factory do Azure dá suporte à criação de um cluster sob demanda e o usa para processar entrada a fim de gerar dados de saída. Você também pode usar seu próprio cluster para fazer isso. Quando você usa o cluster HDInsight sob demanda, um cluster é criado para cada fatia. Ao passo que, ao utilizar seu próprio cluster de HDInsight, o cluster está pronto para processar a fatia imediatamente. Portanto, quando você usar cluster sob demanda, não verá os dados de saída mais rapidamente que quando usa seu próprio cluster. Para fins de exemplo, vamos usar um cluster sob demanda.

#### Para usar um cluster HDInsight sob demanda
1. Clique em **nova computação** da barra de comandos e selecione **cluster HDInsight sob demanda** no menu.
2. Faça o seguinte no script JSON: 
	1. Para o **clusterSize** propriedade, especifique o tamanho do cluster do HDInsight.
	2. Para o **jobsContainer** propriedade, especifique o nome do recipiente padrão onde os logs de cluster serão armazenados. Para fins deste tutorial, especifique **adfjobscontainer**.
	3. Para o **timeToLive** propriedade, especifique quanto tempo o cliente pode ficar ocioso antes de serem excluído. 
	4. Para o **versão** propriedade, especifique a versão do HDInsight que você deseja usar. Se você excluir essa propriedade, a versão mais recente é usada.  
	5. Para o **linkedServiceName**, especifique **HDInsightStorageLinkedService** que você tivesse criado em Get tutorial de Introdução. 

			{
		    	"name": "HDInsightLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "HDInsightStorageLinkedService"
		    	}
			}

		Observe que o **tipo** de serviço vinculado é definido como **HDInsightOnDemandLinkedService**.

2. Clique em **Deploy** na barra de comandos para implantar o serviço vinculado.
   
   
#### Para usar seu próprio cluster HDInsight: 

1. Clique em **nova computação** da barra de comandos e selecione **cluster HDInsight** no menu.
2. Faça o seguinte no script JSON: 
	1. Para o **clusterUri** propriedade, digite a URL para o HDInsight. Por exemplo: https://<clustername>.azurehdinsight.net/     
	2. Para o **nome de usuário** propriedade, digite o nome de usuário que tenha acesso ao cluster HDInsight.
	3. Para o **senha** propriedade, digite a senha do usuário. 
	4. Para o **LinkedServiceName** propriedade, digite **StorageLinkedService**. Este é o serviço vinculado que você tivesse criado no tutorial do guia de Introdução. 

	Norar que o **tipo** de serviço vinculado é definido como **HDInsightBYOCLinkedService** (BYOC significa colocar seu próprio Cluster).

2. Clique em **Deploy** na barra de comandos para implantar o serviço vinculado.


## <a name="MainStep4"></a> Etapa 4: Criar tabelas
 
Nesta etapa, você criará tabelas de fábrica de dados a seguir:

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Tutorial fluxo de ponta a ponta][image-data-factory-tutorial-end-to-end-flow]
 
A figura acima exibe pipelines na linha intermediária e tabelas nas linhas superior e inferior.

### Para criar as tabelas
	
1. No **Editor** da fábrica de dados, clique em **novo conjunto de dados** botão na barra de ferramentas e clique em **armazenamento de BLOBs do Azure** no menu suspenso. 
2. Substituir JSON no painel direito, com o script JSON a **RawGameEventsTable.json** arquivo o **C:\ADFWalkthrough\Tables** pasta.
3. Clique em **Deploy** na barra de ferramentas para criar e implantar a tabela. Confirme que você vê o **tabela CRIADA com êxito** mensagem na barra de título do Editor.
4. Repita as etapas 1 a 3 com o conteúdo dos seguintes arquivos: 
	1. PartitionedGameEventsTable.json
	2. RefGeoCodeDictionaryTable.json
	3. RefMarketingCampaignTable.json
	4. EnrichedGameEventsTable.json
	5. MarketingCampaignEffectivenessBlobTable.json 
5. Repita as etapas 1 a 3 com o conteúdo do seguinte arquivo. MAS selecione **Sql Azure** depois de clicar em **novo conjunto de dados**.
	1. MarketingCampaignEffectivenessSQLTable.json
	

## <a name="MainStep5"></a> Etapa 5: Criar e agendar pipelines
Nesta etapa, você criará os seguintes pipelines:

- PartitionGameLogsPipeline
- EnrichGameLogsPipeline
- AnalyzeMarketingCampaignPipeline

### Para criar pipelines

1. No **Editor do Data Factory**, clique no botão **Novo pipeline** na barra de ferramentas. Clique em **... (Elipse)** na barra de ferramentas se você não enxergar o botão. Como alternativa, você pode clicar com o botão direito em **Pipelines** no modo de exibição de árvore e clicar em **Novo pipeline**.
2. Substituir JSON no painel direito, com o script JSON a **PartitionGameLogsPipeline.json** arquivo o **C:\ADFWalkthrough\Pipelines** pasta.
3. Adicione uma **vírgula (,)** no final da **colchete de fechamento (])** em JSON e, em seguida, adicione as três linhas a seguir após o colchete de fechamento. 

        "start": "2014-05-01T00:00:00Z",
        "end": "2014-05-05T00:00:00Z",
        "isPaused": false

	[AZURE.NOTE]Observe que os momentos de início e término são definidos como 01/05/2014 e 05/05/2014, porque os dados de exemplo neste passo a passo são de 01/05/2014 a 05/05/2014.
 
3. Clique em **Implantar** na barra de ferramentas para implantar o pipeline. Confirme que você vê a mensagem **PIPELINE CRIADO COM ÊXITO** na barra de título do Editor.
4. Repita as etapas 1 a 3 com o conteúdo dos seguintes arquivos: 
	1. EnrichGameLogsPipeline.json
	2. AnalyzeMarketingCampaignPipeline.json
4. Feche os blades dados fábrica pressionando **X** (canto superior direito) para ver a home page (* * DATA FACTORY **lâmina) para dados de fábrica. 
### Exibição de diagrama

1. No **DATA FACTORY** lâmina para o **LogProcessingFactory**, clique em **diagrama**. 

	![Link do diagrama][image-data-factory-tutorial-diagram-link]

2. Você pode reorganizar o diagrama que você vê, e aqui está um diagrama reorganizado mostrando entradas diretas na parte superior e saídas na parte inferior. Você pode ver que a saída do **PartitionGameLogsPipeline** é passado como entrada para o EnrichGameLogsPipeline e a saída do **EnrichGameLogsPipeline** é passado para o **AnalyzeMarketingCampaignPipeline**. Clique duas vezes em um título para ver detalhes sobre o artefato que a folha representa.

	![Exibição de diagrama][image-data-factory-tutorial-diagram-view]

3. Clique **AnalyzeMarketingCampaignPipeline**, e clique em **Abrir Pipeline**. Você deve ver todas as atividades no pipeline com conjuntos de dados de entrada e saídos para as atividades.
 
	![Pipeline aberto](./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline-OpenPipeline.png)

	Clique em **Data factory** na navegação estrutural no canto superior esquerdo para voltar ao modo de exibição de diagrama com todos os pipelines.


**Parabéns!** Você criou com êxito o Data Factory do Azure, os Serviços Vinculados, Pipelines, Tabelas e iniciou o fluxo de trabalho.


## <a name="MainStep6"></a> Etapa 6: Monitorar fatias pipelines e dados 

1.	Se você não tiver o **DATA FACTORY** lâmina para o **LogProcessingFactory** aberto, siga um destes procedimentos:
	1.	Clique em **LogProcessingFactory** sobre o **Startboard**. Ao criar a fábrica de dados, o **Adicionar ao quadro inicial** opção foi marcada automaticamente.

		![Monitoramento de quadro inicial][image-data-factory-monitoring-startboard]

	2. Clique em **Procurar** hub e clique em **tudo**.
	 	
		![Monitoramento de Hub tudo][image-data-factory-monitoring-hub-everything]

		No **Procurar** lâmina, selecione **fábricas de dados** e selecione **LogProcessingFactory** no **fábricas de dados** lâmina.

		![Monitoramento Datafactories procurar][image-data-factory-monitoring-browse-datafactories]
2. Você pode monitorar seu data factory de várias maneiras. Você pode iniciar com pipelines ou conjuntos de dados. Vamos começar com Pipelines e analisar mais. 
3.	Clique em **Pipelines** no **DATA FACTORY** lâmina. 
4.	Clique em **PartitionGameLogsPipeline** na lâmina Pipelines. 
5.	No **PIPELINE** lâmina para **PartitionGameLogsPipeline**, você verá que o pipeline consome **RawGameEventsTable** conjunto de dados. Clique em **RawGameEventsTable**.

	![Pipeline consumidos e produzidos][image-data-factory-monitoring-pipeline-consumed-produced]

6. Na lâmina de tabela para **RawGameEventsTable**, verá todas as fatias. Na seguinte captura de tela, todas as fatias estão no **pronto** estado e não há nenhuma fatia do problema. Isso significa que os dados estão prontos para serem processados.

	![Blade de tabela RawGameEventsTable][image-data-factory-monitoring-raw-game-events-table]

	Ambos **atualizado recentemente fatias** e **recentemente falha fatias** listas são classificadas pelo **hora da última atualização**. O tempo de atualização de uma fatia é alterado nas seguintes situações.

	-  Você atualiza o status da fatia manualmente, por exemplo, usando o **conjunto AzureDataFactorySliceStatus** (ou) clicando em **executar** no **FATIA** blade da fatia.
	-  A fatia é alterado devido a uma execução (por exemplo, uma execução de Introdução, uma execução finalizada e falha, uma execução terminou e foi bem-sucedida, etc).
 
	Clique no título da lista ou **... (reticências)** Para ver a lista de intervalos de maior. Clique em **filtro** na barra de ferramentas para filtrar as fatias.
	
	Para exibir as fatias de dados classificadas pelas horas de início/término fatia em vez disso, clique em **fatias de dados (por hora fatia)** lado a lado.

	![Fatias de dados por intervalo de tempo][DataSlicesBySliceTime]
 
7. Agora, sob o **PIPELINE** lâmina para **PartiionGameLogsPipeline**, clique em **Produced**.
8. Você deverá ver a lista de conjuntos de dados que esse pipeline produz: 
9. Clique em **PartitionedGameEvents** na tabela de **produzido datasets** blade. 
10.	Confirme se o **status** de todas as fatias é definido como **pronto**. 
11.	Clique em uma das fatias é **pronto** para ver o **FATIA de dados** lâmina para essas fatias.

	![A FATIA de dados RawGameEventsTable lâmina][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	Se houver um erro, você verá um **Falha **status aqui. Você também pode encontrar qualquer um dos dois fatias com status **pronto**, ou ambos, com status **PendingValidation**, dependendo da rapidez as fatias são processadas.

	Se a fatia não está no **pronto** estado, você pode ver as fatias upstream que não estão prontos e estão bloqueando a fatia atual da execução no **fatias Upstream que não estão prontas** lista.
 
	Consulte o [referência do desenvolvedor do Azure dados fábrica][developer-reference] para obter uma compreensão de todos os status possíveis fatia.

12.	No **FATIA de dados** lâmina, clique em Executar do **atividade executa** lista. Você deve ver a folha Execução de Atividade dessa fatia. Você verá o seguinte **detalhes de execução da atividade** lâmina.

	![Blade de detalhes da execução de atividade][image-data-factory-monitoring-activity-run-details]

13.	Clique em **baixar** para baixar os arquivos. Esta tela é especialmente útil quando você estiver solucionando erros de processamento do HDInsight.
	 
	
Quando concluir todo o pipeline de execução, você pode examinar o **MarketingCampaignEffectivenessTable** no **MarketingCampaigns** banco de dados do SQL Azure para exibir os resultados.

**Parabéns!** Agora você pode monitorar e solucionar problemas de fluxos de trabalho. Você aprendeu a usar o Data Factory do Azure para processar dados e obter a análise.

## Estender o tutorial para usar dados locais
Na última etapa do cenário do passo a passo neste artigo de processamento de log, a saída de eficácia de campanha marketing foi copiada para um banco de dados do SQL Azure. Você também pode mover esses dados para o SQL Server local para análise dentro da sua organização.
 
Para copiar dados de eficácia de campanha de marketing de BLOBs do Azure para SQL Server local, você precisa criar serviço vinculado locais adicionais, tabela e Pipeline introduziram no passo a passo neste artigo.

Prática de [passo a passo: usando o fonte de dados no local][tutorial-onpremises] para aprender a criar um pipeline para copiar dados de eficácia de campanha de marketing para um banco de dados do SQL Server local.


[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[data-factory-editor]: data-factory-editor.md

[adfsamples]: data-factory-samples.md
[adfgetstarted]: data-factory-get-started.md
[adftutorial-using-powershell]: data-factory-tutorial-using-powershell.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-use-onpremises-datasources.md
[usepigandhive]: data-factory-pig-hive-activities.md
[tutorial-onpremises]: data-factory-tutorial-extend-onpremises.md
[download-azure-powershell]: ../powershell-install-configure.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx


[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[DataSlicesBySliceTime]: ./media/data-factory-tutorial/DataSlicesBySliceTime.png
[image-author-deploy-tile]: ./media/data-factory-tutorial/author-deploy-tile.png
[image-editor-newdatastore-button]: ./media/data-factory-tutorial/editor-newdatastore-button.png
[image-editor-blob-storage-json]: ./media/data-factory-tutorial/editor-blob-storage-json.png
[image-editor-blob-storage-deploy]: ./media/data-factory-tutorial/editor-blob-storage-deploy.png

[image-data-factory-tutorial-end-to-end-flow]: ./media/data-factory-tutorial/EndToEndWorkflow.png

[image-data-factory-tutorial-partition-game-logs-pipeline]: ./media/data-factory-tutorial/PartitionGameLogsPipeline.png

[image-data-factory-tutorial-enrich-game-logs-pipeline]: ./media/data-factory-tutorial/EnrichGameLogsPipeline.png

[image-data-factory-tutorial-analyze-marketing-campaign-pipeline]: ./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline.png


[image-data-factory-tutorial-egress-to-onprem-pipeline]: ./media/data-factory-tutorial/EgreeDataToOnPremPipeline.png

[image-data-factory-tutorial-set-firewall-rules-azure-db]: ./media/data-factory-tutorial/SetFirewallRuleForAzureDatabase.png

[image-data-factory-tutorial-portal-new-everything]: ./media/data-factory-tutorial/PortalNewEverything.png

[image-data-factory-tutorial-datastorage-cache-backup]: ./media/data-factory-tutorial/DataStorageCacheBackup.png

[image-data-factory-tutorial-dataservices-blade]: ./media/data-factory-tutorial/DataServicesBlade.png

[image-data-factory-tutorial-new-datafactory-blade]: ./media/data-factory-tutorial/NewDataFactoryBlade.png

[image-data-factory-tutorial-resourcegroup-blade]: ./media/data-factory-tutorial/ResourceGroupBlade.png

[image-data-factory-tutorial-create-resourcegroup]: ./media/data-factory-tutorial/CreateResourceGroup.png

[image-data-factory-tutorial-datafactory-homepage]: ./media/data-factory-tutorial/DataFactoryHomePage.png

[image-data-factory-tutorial-create-datafactory]: ./media/data-factory-tutorial/CreateDataFactory.png

[image-data-factory-tutorial-linkedservice-tile]: ./media/data-factory-tutorial/LinkedServiceTile.png

[image-data-factory-tutorial-linkedservices-add-datstore]: ./media/data-factory-tutorial/LinkedServicesAddDataStore.png

[image-data-factory-tutorial-datastoretype-azurestorage]: ./media/data-factory-tutorial/DataStoreTypeAzureStorageAccount.png

[image-data-factory-tutorial-azurestorage-settings]: ./media/data-factory-tutorial/AzureStorageSettings.png

[image-data-factory-tutorial-storage-key]: ./media/data-factory-tutorial/StorageKeyFromAzurePortal.png

[image-data-factory-tutorial-linkedservices-blade-storage]: ./media/data-factory-tutorial/LinkedServicesBladeWithAzureStorage.png

[image-data-factory-tutorial-azuresql-settings]: ./media/data-factory-tutorial/AzureSQLDatabaseSettings.png

[image-data-factory-tutorial-azuresql-database-connection-string]: ./media/data-factory-tutorial/DatabaseConnectionString.png

[image-data-factory-tutorial-linkedservices-all]: ./media/data-factory-tutorial/LinkedServicesAll.png

[image-data-factory-tutorial-datasets-all]: ./media/data-factory-tutorial/DataSetsAllTables.png

[image-data-factory-tutorial-pipelines-all]: ./media/data-factory-tutorial/AllPipelines.png

[image-data-factory-tutorial-diagram-link]: ./media/data-factory-tutorial/DataFactoryDiagramLink.png

[image-data-factory-tutorial-diagram-view]: ./media/data-factory-tutorial/DiagramView.png

[image-data-factory-monitoring-startboard]: ./media/data-factory-tutorial/MonitoringStartBoard.png

[image-data-factory-monitoring-hub-everything]: ./media/data-factory-tutorial/MonitoringHubEverything.png

[image-data-factory-monitoring-browse-datafactories]: ./media/data-factory-tutorial/MonitoringBrowseDataFactories.png

[image-data-factory-monitoring-pipeline-consumed-produced]: ./media/data-factory-tutorial/MonitoringPipelineConsumedProduced.png

[image-data-factory-monitoring-raw-game-events-table]: ./media/data-factory-tutorial/MonitoringRawGameEventsTable.png

[image-data-factory-monitoring-raw-game-events-table-dataslice-blade]: ./media/data-factory-tutorial/MonitoringPartitionGameEventsTableDataSliceBlade.png

[image-data-factory-monitoring-activity-run-details]: ./media/data-factory-tutorial/MonitoringActivityRunDetails.png

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial/DataManagementGatewayConfigurationManager.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-tutorial/NewDataFactoryMenu.png

[image-data-factory-new-datafactory-create-button]: ./media/data-factory-tutorial/DataFactoryCreateButton.png

<!---HONumber=GIT-SubDir--> 