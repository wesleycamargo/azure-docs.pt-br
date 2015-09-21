<properties 
	pageTitle="Mover e processar arquivos de log usando o Azure Data Factory (Portal do Azure)" 
	description="Este tutorial avançado descreve um cenário quase real e implementa o cenário usando o serviço Azure Data Factory e Editor Data Factory no Portal do Azure." 
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
	ms.date="08/25/2015" 
	ms.author="spelluru"/>

# Tutorial: Medindo a eficiência de uma campanha de marketing  
A Contoso é uma empresa de jogos que cria os jogos para várias plataformas: PCs (computadores pessoais), dispositivos de mão e consoles de jogos. Esses jogos geram muitos logs e o objetivo da Contoso é coletar e analisar esses logs para obter informações sobre as preferências do cliente, dados demográficos, comportamento de uso, etc., para identificar oportunidades de venda e vendas cruzadas, desenvolver novos recursos atraentes para fomentar o crescimento do negócio e proporcionar uma melhor experiência para os clientes.

Neste tutorial, você criará pipelines de Data Factory para avaliar a eficiência de uma campanha de marketing que a Contoso lançou recentemente, iniciados pela coleta de logs de exemplo, pelo processamento e enriquecimento desses logs com dados de referência, além da transformação dos dados. Ele tem três os três pipelines a seguir:

1.	O **PartitionGameLogsPipeline** lê os eventos brutos de jogos de um armazenamento de blob e cria partições com base no ano, mês e dia.
2.	O **EnrichGameLogsPipeline** junta eventos de jogos particionados com dados de referência de código geográfico e enriquece os dados pelo mapeamento de endereços IP para os locais geográficos correspondentes.
3.	O pipeline **AnalyzeMarketingCampaignPipeline** aproveita os dados enriquecidos e processa-os com os dados de anúncio para criar a saída final, que contém a eficácia de uma campanha de marketing.

## Preparando-se para o tutorial
1.	Leia a [Introdução ao Azure Data Factory][adfintroduction] para obter uma visão geral do Azure Data Factory e entender os conceitos de nível superior.
2.	Você deve ter uma assinatura do Azure para ver este tutorial. Para obter informações sobre como adquirir uma assinatura, consulte [Opções de compra](http://azure.microsoft.com/pricing/purchase-options/), [Ofertas para membros](http://azure.microsoft.com/pricing/member-offers/) ou [Avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/).
3.	Você deve baixar e instalar o [PowerShell do Azure][download-azure-powershell] no seu computador. Você vai executar cmdlets da Data Factory para carregar dados de exemplo e scripts de pig/hive para seu armazenamento de blob. 
2.	**(recomendado)** Examine e pratique o tutorial no artigo [Introdução ao Azure Data Factory][adfgetstarted] para ver um tutorial simples e familiarizar-se com o portal e os cmdlets.
3.	**(recomendado)** Examine e pratique o passo a passo no artigo [Usar o Pig e Hive com o Azure Data Factory][usepigandhive] para ver um passo a passo sobre como criar um pipeline para mover dados da fonte de dados local para um armazenamento de blob do Azure.
4.	Baixe os arquivos [ADFWalkthrough][adfwalkthrough-download] na pasta **C:\\ADFWalkthrough** **preservando a estrutura da pasta**:
	- **Pipelines:** inclui arquivos JSON que contêm a definição dos pipelines.
	- **Tables:** inclui arquivos JSON que contêm a definição das tabelas.
	- **LinkedServices:** inclui arquivos JSON que contêm a definição do seu cluster de armazenamento e computação (HDInsight) 
	- **Scripts:** inclui scripts do Hive e Pig que são usados para processar os dados e são invocados por meio de pipelines
	- **SampleData:** inclui dados de exemplo para este passo a passo
	- **OnPremises:** inclui arquivos JSON e scripts que são usados para demonstrar o acesso aos seus dados locais
	- **uploadSampleDataAndScripts.ps1:** este script carrega os dados de exemplo e scripts no Azure.
5. Verifique se que você criou os seguintes recursos do Azure:			
	- Conta de Armazenamento do Azure.
	- Banco de Dados SQL do Azure
	- Cluster de HDInsight do Azure versão 3.1 ou superior (ou use um cluster HDInsight sob demanda que o serviço Data Factory criará automaticamente)	
7. Depois que os recursos do Azure são criados, verifique se que você tem as informações necessárias para se conectar a cada um desses recursos.
 	- **Conta de armazenamento do Azure** - Nome da conta e chave de conta.  
	- **Banco de Dados SQL do Azure** - Servidor, banco de dados, nome de usuário e senha.
	- **Cluster HDInsight do Azure** - Nome do cluster HDInsight, nome de usuário, senha e nome da conta e chave de conta para o armazenamento do Azure associado a esse cluster. Se você quiser usar um cluster de HDInsight sob demanda em vez de seu próprio cluster HDInsight, você pode ignorar esta etapa.  
8. Execute o **PowerShell do Azure** e os comandos a seguir. Mantenha a janela do PowerShell do Azure aberta. Se você fechá-la e reabri-la, precisará executar esses comandos novamente.
	- Execute **Add-AzureAccount** e insira o nome de usuário e a senha que você usa para entrar no Portal de Visualização do Azure.  
	- Execute **Get-AzureSubscription** para exibir todas as assinaturas dessa conta.
	- Execute **Select-AzureSubscription** para selecionar a assinatura com a qual deseja trabalhar. Esta assinatura deve ser a mesma que você usou no Portal de Visualização do Azure.	

## Visão geral
O fluxo de trabalho de ponta a ponta é descrito abaixo:

![Tutorial de fluxo de ponta a ponta][image-data-factory-tutorial-end-to-end-flow]

1. O **PartitionGameLogsPipeline** lê os eventos brutos de jogos de um armazenamento de blob (RawGameEventsTable) e cria partições com base no ano, mês e dia (PartitionedGameEventsTable).
2. O **EnrichGameLogsPipeline** junta eventos de jogos particionados (tabela PartitionedGameEvents, que é uma saída do PartitionGameLogsPipeline) com o código geográfico (RefGetoCodeDictionaryTable) e enriquece dados mapeando um endereço IP para a localização geográfica correspondente (EnrichedGameEventsTable).
3. O pipeline **AnalyzeMarketingCampaignPipeline** aproveita os dados enriquecidos (EnrichedGameEventTable gerado pelo EnrichGameLogsPipeline) e os processa com os dados de anúncio (RefMarketingCampaignnTable) para criar a saída final da eficiência de campanha de marketing, que é copiada para o banco de dados SQL do Azure (MarketingCampainEffectivensessSQLTable) e um armazenamento de blob do Azure (MarketingCampaignEffectivenessBlobTable) para análise.
    
## Passo a passo: Criando, implantando e monitorando fluxos de trabalho
1. [Etapa 1: Carregar dados e scripts de exemplo](#MainStep1). Nesta etapa, você carregará todos os dados de exemplo (incluindo todos os logs e dados de referência) e scripts do Hive/Pig que serão executadas pelos fluxos de trabalho. Os scripts que você executa também criam um banco de dados SQL do Azure (chamado MarketingCampaigns), tabelas, tipos definidos pelo usuário e procedimentos armazenados.
2. [Etapa 2: criar uma data factory do Azure](#MainStep2). Nesta etapa, você criará uma data factory do Azure chamado LogProcessingFactory.
3. [Etapa 3: Criar serviços vinculados](#MainStep3). Nesta etapa, você criará os seguintes serviços vinculados: 
	
	- 	**StorageLinkedService**. Vincula o local de armazenamento do Azure que contém eventos brutos de jogos, eventos de jogos particionados, eventos de jogos enriquecidos, informações de eficácia de campanha de marketing, dados de referência do código geográfico e dados de referência da campanha de marketing para o LogProcessingFactory   
	- 	**AzureSqlLinkedService**. Vincula um banco de dados SQL do Azure que contém informações de eficácia de campanha de marketing. 
	- 	**HDInsightStorageLinkedService**. Vincula um armazenamento de blob do Azure que está associado ao cluster HDInsight ao qual HDInsightLinkedService se refere. 
	- 	**HDInsightLinkedService**. Vincula um cluster HDInsight do Azure ao LogProcessingFactory. Este cluster é usado para executar o processamento do pig/hive nos dados. 
 		
4. [Etapa 4: Criar tabelas](#MainStep4). Nesta etapa, você criará as seguintes tabelas:
	
	- **RawGameEventsTable**. Esta tabela especifica o local dos dados de eventos de jogos brutos dentro do armazenamento de blob do Azure definido pelo StorageLinkedService (adfwalkthrough/logs/rawgameevents/). 
	- **PartitionedGameEventsTable**. Esta tabela especifica o local dos dados de eventos de jogos particionados dentro do armazenamento de blob do Azure definido pelo StorageLinkedService (adfwalkthrough/logs/partitionedgameevents/). 
	- **RefGeoCodeDictionaryTable**. Esta tabela especifica o local dos dados de referência do código geográfico dentro do armazenamento de blob do Azure definido pelo StorageLinkedService (adfwalkthrough/refdata/refgeocodedictionary/).
	- **RefMarketingCampaignTable**. Esta tabela especifica o local dos dados de referência da campanha de marketing dentro do armazenamento de blob do Azure definido pelo StorageLinkedService (adfwalkthrough/refdata/refmarketingcampaign/).
	- **EnrichedGameEventsTable**. Esta tabela especifica o local dos dados enriquecidos de eventos de jogos dentro do armazenamento de blob do Azure definido pelo StorageLinkedService (adfwalkthrough/logs/enrichedgameevents/).
	- **MarketingCampaignEffectivenessSQLTable**. Esta tabela especifica a tabela SQL (MarketingCampaignEffectiveness) no Banco de Dados SQL do Azure definido pelo AzureSqlLinkedService que contém os dados de eficácia de campanha de marketing. 
	- **MarketingCampaignEffectivenessBlobTable**. Esta tabela especifica o local dos dados de eficácia da campanha de marketing dentro do armazenamento de blob do Azure definido pelo StorageLinkedService (adfwalkthrough/marketingcampaigneffectiveness/). 

	
5. [Etapa 5: Criar e agendar pipelines](#MainStep5). Nesta etapa, você criará os seguintes pipelines:
	- **PartitionGameLogsPipeline**. Este pipeline lê os eventos brutos de jogos de um armazenamento de blob (RawGameEventsTable) e cria partições com base no ano, mês e dia (PartitionedGameEventsTable). 


		![Pipeline PartitionGamesLogs][image-data-factory-tutorial-partition-game-logs-pipeline]


	- **EnrichGameLogsPipeline**. Este pipeline junta eventos de jogos particionados (tabela PartitionedGameEvents, que é uma saída do PartitionGameLogsPipeline) com o código geográfico (RefGetoCodeDictionaryTable) e enriquece dados mapeando um endereço IP para a localização geográfica correspondente (EnrichedGameEventsTable)

		![EnrichedGameLogsPipeline][image-data-factory-tutorial-enrich-game-logs-pipeline]

	- **AnalyzeMarketingCampaignPipeline**. Este pipeline aproveita os dados enriquecidos (EnrichedGameEventTable gerado pelo EnrichGameLogsPipeline) e os processa com os dados de anúncio (RefMarketingCampaignnTable) para criar a saída final da eficiência de campanha de marketing campaign, que é copiada para o banco de dados SQL do Azure (MarketingCampainEffectivensessSQLTable) e um armazenamento de blob do Azure (MarketingCampaignEffectivenessBlobTable) para análise


		![MarketingCampaignPipeline][image-data-factory-tutorial-analyze-marketing-campaign-pipeline]


6. [Etapa 6: Monitorar pipelines e fatias de dados](#MainStep6). Nesta etapa, você monitorará as fatias de dados, tabelas e pipelines usando o Portal do Azure.

## <a name="MainStep1"></a> Etapa 1: Carregar dados e scripts de exemplo
Nesta etapa, você carregará todos os dados de exemplo (incluindo todos os logs e dados de referência) e scripts do Hive/Pig que são executadas pelos fluxos de trabalho. Os scripts que você executa também criam um banco de dados SQL do Azure chamado **MarketingCampaigns**, tabelas, tipos definidos pelo usuário e procedimentos armazenados.

As tabelas, os tipos definidos pelo usuário e procedimentos armazenados são usados ao mover os resultados da Eficácia de Campanha de Marketing do armazenamento de blobs do Azure para o banco de dados SQL do Azure.

1. Abra **uploadSampleDataAndScripts.ps1** da pasta **C:\\ADFWalkthrough** (ou a pasta que contém os arquivos extraídos) no seu editor favorito, substitua a parte destacada pelas informações do cluster e salve o arquivo.


		$storageAccount = <storage account name>
		$storageKey = <storage account key>
		$azuresqlServer = <sql azure server>.database.windows.net
		$azuresqlUser = <sql azure user>@<sql azure server>
		$azuresqlPassword = <sql azure password>

 
	Esse script exige que o utilitário sqlcmd esteja instalado no computador. Se você tiver o SQL Server instalado, você já o possui. Caso contrário, [baixe][sqlcmd-install] e instale o utilitário.
	
	Alternativamente, você pode usar os arquivos na pasta: C:\\ADFWalkthrough\\Scripts para carregar scripts do hive/pig e arquivos de exemplo no contêiner adfwalkthrough no armazenamento de blob e criar a tabela MarketingCampaignEffectiveness no banco de dados SQL do Azure MarketingCamapaigns.
   
2. Confirme que seu computador local tem permissão para acessar o Banco de Dados SQL do Azure. Para habilitar o acesso, use o **Portal de Gerenciamento do Azure** ou **sp\_set\_firewall\_rule** no banco de dados mestre para criar uma regra de firewall para o endereço IP do seu computador. Pode levar até cinco minutos para que essa alteração tenha efeito. Consulte [Configurando regras de firewall para Azure SQL][azure-sql-firewall].
4. No PowerShell do Azure, navegue até o local onde você extraiu os exemplos (por exemplo: **C:\\ADFWalkthrough**)
5. Execute **uploadSampleDataAndScripts.ps1** 
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

## <a name="MainStep2"></a> Etapa 2: criar uma data factory do Azure
Nesta etapa, você criará uma data factory do Azure chamada **LogProcessingFactory**.

1.	Após fazer logon no [Portal de Visualização do Azure][azure-preview-portal], clique em **NOVO** no canto inferior esquerdo, clique em **Análises de dados** na folha **Criar** e clique em **Data Factory** na folha **Análises de dados**. 

	![Novo -> DataFactory][image-data-factory-new-datafactory-menu]

5. Na folha **Nova data factory**, insira **LogProcessingFactory** como o **Nome**.

	![Folha Data Factory][image-data-factory-tutorial-new-datafactory-blade]

6. Se você ainda não criou um grupo de recursos do Azure chamado **ADF**, faça o seguinte:
	1. Clique em **NOME DO GRUPO DE RECURSOS** e clique em **Criar um novo grupo de recursos**.
	
		![Folha Grupo de recursos][image-data-factory-tutorial-resourcegroup-blade]
	2. Na folha **Criar grupo de recursos**, insira **ADF** como nome do grupo de recursos e clique em **OK**.
	
		![Criar grupo de recursos][image-data-factory-tutorial-create-resourcegroup]
7. Selecione **ADF** como o **NOME DO GRUPO DE RECURSOS**.  
8.	Na folha **Nova data factory**, observe que a opção **Adicionar ao Quadro Inicial** é selecionada por padrão. Isso pode adicionar um link ao data factory no quadro inicial (o que você vê quando ao fazer logon no Portal de Visualização do Azure).

	![Folha Criar Data Factory][image-data-factory-tutorial-create-datafactory]

9.	Na folha **Nova data factory**, clique em **Criar** para criar a data factory.
10.	Depois que a data factory é criada, você deverá ver a folha **DATA FACTORY** denominada **LogProcessingFactory**.

	![Página inicial da Data Factory][image-data-factory-tutorial-datafactory-homepage]

	
	Se você não vê-la, siga um destes procedimentos:

	- Clique em **LogProcessingFactory** no **Quadro Inicial** (página inicial)
	- Clique em **PROCURAR** à esquerda, clique em **Tudo**, clique em **Data factories** e clique na data factory.
 
	O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro: **O nome de data factory "LogProcessingFactory" não está disponível**, altere o nome (por exemplo, seunomeLogProcessingFactory). Use esse nome em vez de LogProcessingFactory ao executar as etapas neste tutorial.
 
## <a name="MainStep3"></a> Etapa 3: Criar serviços vinculados

> [AZURE.NOTE]Este artigo usa o Portal do Azure, especificamente o Editor Data Factory, para criar pipelines, tabelas e serviços vinculados. Consulte [Tutorial usando o PowerShell do Azure][adftutorial-using-powershell] para executar este tutorial usando o PowerShell do Azure.

Nesta etapa, você criará os seguintes serviços vinculados:

- StorageLinkedService
- AzureSqlLinkedService
- HDInsightStorageLinkedService
- HDInsightLinkedService. 

### Crie StorageLinkedService e HDInsightStorageLinkedService

1.	Na folha **DATA FACTORY**, clique no bloco **Criar e implantar** para iniciar o **Editor** da data factory.

	![Bloco Criar e implantar][image-author-deploy-tile]

	Consulte o tópico [Editor Data Factory][data-factory-editor] para obter a visão geral detalhada do Editor Data Factory.

2.  No **Editor**, clique no botão **Novo armazenamento de dados** na barra de ferramentas e selecione **Armazenamento do Azure** no menu suspenso. Você deve ver o modelo JSON para criar um serviço de armazenamento vinculado do Azure no painel à direita.
	
	![Botão Novo repositório de dados do editor][image-editor-newdatastore-button]

3. Substitua **accountname** e **accountkey** pelos valores de nome e de chave da sua conta de armazenamento do Azure.

	![JSON do armazenamento de Blob do editor][image-editor-blob-storage-json]
	
	Consulte a [Referência de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971) para obter detalhes sobre as propriedades JSON.

4. Clique em **Implantar** na barra de ferramentas para implantar o StorageLinkedService. Confirme que você vê a mensagem **SERVIÇO VINCULADO CRIADO COM ÊXITO** na barra de título.

	![Implantar armazenamento de Blob do editor][image-editor-blob-storage-deploy]

5. Repita as etapas para criar outro serviço vinculado de armazenamento do Azure nomeado **HDInsightStorageLinkedService** para o armazenamento associado ao seu cluster HDInsight. No script JSON para o serviço vinculado, altere o valor da propriedade **name** para **HDInsightStorageLinkedService**.

### Criar AzureSqlLinkedService
1. No **Editor Data Factory**, clique no botão **Novo armazenamento de dados** na barra de ferramentas e selecione **Banco de dados SQL do Azure** no menu suspenso. Você deve ver o modelo JSON para criar o serviço vinculado do SQL do Azure no painel à direita.
2. Substitua **servername**, ****username@servername** e **password** pelos nomes de seu servidor SQL do Azure, do banco de dados, da conta de usuário e a senha.
3. Substitua **databasename** por **MarketingCampaigns**. Esse é o banco de dados SQL do Azure criado pelos scripts que você executou na Etapa 1. Você deve confirmar se esse banco de dados foi realmente criado pelos scripts (caso tenham ocorrido erros). 
3. Clique em **Implantar** na barra de ferramentas para implantar o AzureSqlLinkedService.

### Criar HDInsightLinkedService
O serviço Data Factory do Azure dá suporte à criação de um cluster sob demanda e o usa para processar entrada a fim de gerar dados de saída. Você também pode usar seu próprio cluster para fazer isso. Quando você usa o cluster HDInsight sob demanda, um cluster é criado para cada fatia. Ao passo que, ao utilizar seu próprio cluster de HDInsight, o cluster está pronto para processar a fatia imediatamente. Portanto, quando você usar cluster sob demanda, não verá os dados de saída mais rapidamente que quando usa seu próprio cluster. Para fins de exemplo, vamos usar um cluster sob demanda.

#### Para usar um cluster HDInsight sob demanda
1. Clique em **Nova computação** na barra de comandos e selecione **Cluster HDInsight sob demanda** no menu.
2. No script JSON, faça o seguinte: 
	1. Para a propriedade **clusterSize**, especifique o tamanho do cluster do HDInsight.
	2. Para a propriedade **jobsContainer**, especifique o nome do contêiner padrão onde os logs de cluster serão armazenados. Para fins deste tutorial, especifique **adfjobscontainer**.
	3. Para a propriedade **timeToLive**, especifique quanto tempo o cliente pode ficar ocioso antes de ser excluído. 
	4. Para a propriedade **version**, especifique a versão do HDInsight que você deseja usar. Se você excluir essa propriedade, a versão mais recente será usada.  
	5. Para o **linkedServiceName**, especifique **HDInsightStorageLinkedService** que você criou no tutorial do guia de Introdução. 

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

		Observe que o **type** do serviço vinculado é definido como **HDInsightOnDemandLinkedService**.

2. Clique em **Implantar** na barra de comandos para implantar o serviço vinculado.
   
   
#### Para usar seu próprio cluster HDInsight: 

1. Clique em **Nova computação** na barra de comandos e selecione **Cluster HDInsight** no menu.
2. No script JSON, faça o seguinte: 
	1. Para a propriedade **clusterUri**, insira a URL para seu HDInsight. Por exemplo: https://<clustername>.azurehdinsight.net/     
	2. Para a propriedade **UserName**, digite o nome de usuário que tem acesso ao cluster HDInsight.
	3. Para a propriedade **Password**, especifique a senha para o usuário. 
	4. Para a propriedade **LinkedServiceName**, digite **StorageLinkedService**. Este é o serviço vinculado que você criou no tutorial do guia de Introdução. 

	Observe que o **type** do serviço vinculado é definido como **HDInsightBYOCLinkedService** (BYOC significa traga seu próprio Cluster).

2. Clique em **Implantar** na barra de comandos para implantar o serviço vinculado.


## <a name="MainStep4"></a> Etapa 4: Criar tabelas
 
Nesta etapa, você criará as seguintes tabelas d Data Factory:

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Tutorial de fluxo de ponta a ponta][image-data-factory-tutorial-end-to-end-flow]
 
A figura acima exibe pipelines na linha intermediária e tabelas nas linhas superior e inferior.

### Para criar as tabelas
	
1. No **Editor** para Data Factory, clique no botão **Novo conjunto de dados** na barra de ferramentas e clique em **Armazenamento de Blob do Azure** no menu suspenso. 
2. Substitua JSON no painel direito pelo script JSON do arquivo **RawGameEventsTable.json** contido na pasta **C:\\ADFWalkthrough\\Tables**.
3. Clique em **Implantar** na barra de ferramentas para implantar a tabela. Confirme que você vê a mensagem **TABELA CRIADA COM ÊXITO** na barra de título do Editor.
4. Repita as etapas 1 a 3 com o conteúdo dos arquivos a seguir: 
	1. PartitionedGameEventsTable.json
	2. RefGeoCodeDictionaryTable.json
	3. RefMarketingCampaignTable.json
	4. EnrichedGameEventsTable.json
	5. MarketingCampaignEffectivenessBlobTable.json 
5. Repita as etapas 1 a 3 com o conteúdo do arquivo a seguir. MAS selecione **Sql Azure** depois de clicar em **Novo conjunto de dados**.
	1. MarketingCampaignEffectivenessSQLTable.json
	

## <a name="MainStep5"></a> Etapa 5: Criar e agendar pipelines
Nesta etapa, você criará os seguintes pipelines:

- PartitionGameLogsPipeline
- EnrichGameLogsPipeline
- AnalyzeMarketingCampaignPipeline

### Para criar pipelines

1. No **Editor do Data Factory**, clique no botão **Novo pipeline** na barra de ferramentas. Clique em **... (Elipse)** na barra de ferramentas se você não enxergar o botão. Como alternativa, você pode clicar com o botão direito em **Pipelines** no modo de exibição de árvore e clicar em **Novo pipeline**.
2. Substitua JSON no painel direito pelo script JSON do arquivo **PartitionGameLogsPipeline.json** contido na pasta **C:\\ADFWalkthrough\\Pipelines**.
3. Adicione uma **vírgula (,)** no final da **colchete de fechamento (])** em JSON e, em seguida, adicione as três linhas a seguir após o colchete de fechamento. 

        "start": "2014-05-01T00:00:00Z",
        "end": "2014-05-05T00:00:00Z",
        "isPaused": false

	Observe que os momentos de início e término são definidos como 01/05/2014 e 05/05/2014, porque os dados de exemplo neste passo a passo são de 01/05/2014 a 05/05/2014.
 
3. Clique em **Implantar** na barra de ferramentas para implantar o pipeline. Confirme que você vê a mensagem **PIPELINE CRIADO COM ÊXITO** na barra de título do Editor.
4. Repita as etapas 1 a 3 com o conteúdo dos arquivos a seguir: 
	1. EnrichGameLogsPipeline.json
	2. AnalyzeMarketingCampaignPipeline.json
4. Feche as folhas do Data Factory pressionando **X** (canto superior direito) para ver a página inicial (folha **DATA FACTORY) do seu Data Factory.

### Exibição de diagrama

1. Na folha **DATA FACTORY** de **LogProcessingFactory**, clique em **Diagrama**. 

	![Link do diagrama][image-data-factory-tutorial-diagram-link]

2. Você pode reorganizar o diagrama que você vê, e aqui está um diagrama reorganizado mostrando entradas diretas na parte superior e saídas na parte inferior. Você pode ver que a saída de **PartitionGameLogsPipeline** é passada como entrada para EnrichGameLogsPipeline e a saída de **EnrichGameLogsPipeline** é passada para **AnalyzeMarketingCampaignPipeline**. Clique duas vezes em um título para ver detalhes sobre o artefato que a folha representa.

	![Exibição de diagrama][image-data-factory-tutorial-diagram-view]

3. Clique com o botão direito em **AnalyzeMarketingCampaignPipeline** e clique em **Abrir Pipeline**. Você deve ver todas as atividades no pipeline junto com conjuntos de dados de entrada e saída para as atividades.
 
	![Pipeline aberto](./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline-OpenPipeline.png)

	Clique em **Data factory** na trilha do canto superior esquerdo para voltar para o modo de exibição de diagrama com todos os pipelines.


**Parabéns!** Você criou com êxito o Data Factory do Azure, os Serviços Vinculados, Pipelines, Tabelas e iniciou o fluxo de trabalho.


## <a name="MainStep6"></a> Etapa 6: Monitorar pipelines e fatias de dados 

1.	Se você não tiver a folha **DATA FACTORY** para abrir o **LogProcessingFactory**, poderá fazer o seguinte:
	1.	Clique em **LogProcessingFactory** no **Quadro Inicial**. Ao criar a data factory, a opção **Adicionar ao Quadro Inicial** foi marcada automaticamente.

		![Monitorando Quadro Inicial][image-data-factory-monitoring-startboard]

	2. Clique no hub **PROCURAR** e clique em **Tudo**.
	 	
		![Monitorando Hub Tudo][image-data-factory-monitoring-hub-everything]

		Na folha **Procurar**, selecione **Data factories** e selecione **LogProcessingFactory** na folha **Data factories**.

		![Monitorando Procurar Data Factories][image-data-factory-monitoring-browse-datafactories]
2. Você pode monitorar seu data factory de várias maneiras. Você pode iniciar com pipelines ou conjuntos de dados. Vamos começar com Pipelines e analisar mais. 
3.	Clique em **Pipelines** na folha **DATA FACTORY**. 
4.	Clique em **PartitionGameLogsPipeline** na folha Pipelines. 
5.	Na folha **PIPELINE** de **PartitionGameLogsPipeline**, você verá que o pipeline consume o conjunto de dados **RawGameEventsTable**. Clique em **RawGameEventsTable**.

	![Pipeline consumido e produzido][image-data-factory-monitoring-pipeline-consumed-produced]

6. Na folha TABELA de **RawGameEventsTable**, você verá todas as fatias. Na seguinte captura de tela, todas as fatias estão no estado **Pronto** e não há fatias com problema. Isso significa que os dados estão prontos para serem processados.

	![Folha TABELA RawGameEventsTable][image-data-factory-monitoring-raw-game-events-table]

	Ambas as listas **Fatias atualizadas recentemente** e **Fatias com falha recente** são classificadas segundo o **HORÁRIO DA ÚLTIMA ATUALIZAÇÃO**. A hora de atualização de uma fatia é alterada nas situações a seguir.

	-  Você atualiza o status da fatia manualmente, por exemplo, usando o **Set-AzureDataFactorySliceStatus** (ou) clicando em **EXECUTAR** na folha **FATIA** da fatia.
	-  A fatia é alterada devido a uma execução (por exemplo, uma execução iniciada, uma execução finalizada e com falha, uma execução finalizada e bem-sucedida, etc).
 
	Clique no título das listas ou em **... (reticências)** para ver a lista maior de fatias. Clique em **Filtrar** na barra de ferramentas para filtrar as fatias.
	
	Para exibir as fatias de dados classificadas pelas horas de início/término da fatia, clique no bloco **Fatias de dados (por hora da fatia)**.

	![Fatias de dados por horário da fatia][DataSlicesBySliceTime]
 
7. Agora, na folha **PIPELINE** para **PartiionGameLogsPipeline**, clique em **Produzido**.
8. Você deverá ver a lista de conjuntos de dados que esse pipeline produz: 
9. Clique na tabela **PartitionedGameEvents** na folha **Conjuntos de dados gerados**. 
10.	Confirme se o **status** de todas as fatias está definido como **Pronto**. 
11.	Clique em uma das fatias que está **Pronta** para ver a folha **FATIA DE DADOS** dessa fatia.

	![Folha FATIA DE DADOS RawGameEventsTable][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	Se houver um erro, você verá um status **Falha** aqui. Você também pode ver as duas fatias com status **Pronto** ou com status **PendingValidation**, dependendo da rapidez com que as fatias são processadas.

	Se a fatia não está no estado **Pronto**, você pode ver as fatias upstream que não estão Prontas e estão impedindo a execução da fatia atual na lista **Fatias upstream que não estão prontas**.
 
	Consulte a [Referência do Desenvolvedor do Azure Data Factory][developer-reference] para obter uma compreensão de todos os status possíveis da fatia.

12.	Na folha **FATIA DE DADOS**, clique na execução da lista **Execuções de Atividade**. Você deve ver a folha Execução de Atividade dessa fatia. Você deve ver a seguinte folha **DETALHES DE EXECUÇÃO DA ATIVIDADE**.

	![Folha Detalhes da execução de atividade][image-data-factory-monitoring-activity-run-details]

13.	Clique em **Download** para baixar os arquivos. Esta tela é especialmente útil quando você estiver solucionando erros de processamento do HDInsight.
	 
	
Quando todo o pipeline tiver concluído a execução, você poderá examinar **MarketingCampaignEffectivenessTable** no banco de dados SQL do Azure **MarketingCampaigns** para exibir os resultados.

**Parabéns!** Agora você pode monitorar e solucionar problemas de fluxos de trabalho. Você aprendeu a usar o Data Factory do Azure para processar dados e obter a análise.

## Estender o tutorial para usar dados locais
Na última etapa do cenário de processamento de log do passo a passo neste artigo, a saída de eficácia de campanha marketing foi copiada para um banco de dados SQL do Azure. Você também pode mover esses dados para o SQL Server local para análise dentro da sua organização.
 
Para copiar dados de eficácia de campanha de marketing do Blob do Azure para o SQL Server local, você precisa criar Serviço Vinculado local, Tabela e Pipeline adicionais introduzidos no passo a passo deste artigo.

Pratique o [Guia passo a passo: usando a fonte de dados local][tutorial-onpremises] para aprender a criar um pipeline para copiar dados de eficácia de campanha de marketing para um banco de dados do SQL Server local.

## Enviar comentários
Apreciamos muito seus comentários sobre este artigo. Reserve alguns minutos para enviar seus comentários por meio de [email](mailto:adfdocfeedback@microsoft.com?subject=data-factory-tutorial.md).

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[data-factory-editor]: data-factory-editor.md

[adfsamples]: data-factory-samples.md
[adfgetstarted]: data-factory-get-started.md
[adftutorial-using-powershell]: data-factory-tutorial-using-powershell.md
[adfintroduction]: data-factory-introduction.md
[usepigandhive]: data-factory-data-transformation-activities.md
[tutorial-onpremises]: data-factory-tutorial-extend-onpremises.md
[download-azure-powershell]: ../powershell-install-configure.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: https://azure.microsoft.com/documentation/articles/sql-database-configure-firewall-settings/


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

<!---HONumber=Sept15_HO2-->