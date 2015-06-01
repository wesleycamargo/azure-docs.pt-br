<properties 
	pageTitle="Mover e processar arquivos de log usando o Data Factory do Azure" 
	description="Este tutorial avançado descreve um cenário quase real e implementa o cenário usando o serviço Azure Data Factory." 
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
	ms.date="1/28/2015" 
	ms.author="spelluru"/>

# Tutorial: Mover e processar arquivos de log usando o Data Factory
Este artigo fornece uma passo a passo completo de um cenário canônico de processamento de log usando o Data Factory do Azure para transformar dados de arquivos de log em informações. 

## Cenário
A Contoso é uma empresa de jogos que cria jogos para várias plataformas: consoles de jogos, dispositivos portáteis e computadores pessoais (PCs). Cada um desses jogos produz toneladas de logs. O objetivo da Contoso é coletar e analisar os logs gerados por esses jogos para obter informações sobre o uso, identificar oportunidades de vendas e vendas cruzadas, desenvolver novos recursos atraentes etc. para melhorar os negócios e fornecer a melhor experiência para os clientes.
 
Neste passo a passo, podemos coletar logs de exemplo, processar e enriquecê-los com dados de referência, além de transformar os dados para avaliar a eficiência de uma campanha de marketing que a Contoso lançou recentemente.

## Preparando-se para o tutorial
1.	Leia a [Introdução ao Data Factory do Azure][adfintroduction] para obter uma visão geral do Data Factory do Azure e entender os conceitos de nível superior.
2.	Você deve ter uma assinatura do Azure para ver este tutorial. Para obter informações sobre como adquirir uma assinatura, consulte [Opções de Compra] [azure-purchase-options], [Ofertas para Membros][azure-member-offers] ou [Avaliação Gratuita][azure-free-trial].
3.	Você deve baixar e instalar o [PowerShell do Azure][download-azure-powershell] no seu computador. 
2.	**(recomendado)** Revise e pratique o tutorial no artigo [Introdução ao Data Factory do Azure][adfgetstarted] para ver um tutorial simples e familiarizar-se com o portal e os cmdlets.
3.	**(recomendado)** Revise e pratique o passo a passo no artigo [Usar o Pig e Hive com o Data Factory do Azure][usepigandhive] para ver um passo a passo sobre como criar um pipeline para mover dados da fonte de dados local para um armazenamento de blob do Azure.
4.	Baixe os arquivos [ADFWalkthrough][adfwalkthrough-download] na pasta **C:\ADFWalkthrough** **preservando a estrutura da pasta**:
	- **Pipelines:** inclui  arquivos JSON que contêm a definição dos pipelines.
	- **Tabelas:** inclui  arquivos JSON que contêm a definição das Tabelas.
	- **LinkedServices:** inclui arquivos JSON que contêm a definição do armazenamento e computação de cluster (HDInsight) 
	- **Scripts:** inclui scripts do Hive e Pig que são usados para processar os dados e invocados de pipelines
	- **SampleData:** inclui dados de exemplo para este passo a passo
	- **OnPremises:** inclui arquivos JSON e scripts que são usados para demonstrar o acesso aos seus dados locais
	- **AzureEnvironmentSetup.ps1:** Este é o script do PowerShell para configurar o ambiente do Azure
	- **uploadSampleDataAndScripts.ps1:** Este script carrega os dados de exemplo e scripts no Azure.
5. Verifique se que você criou os seguintes recursos do Azure:			
	- Conta de Armazenamento do Azure.
	- Banco de Dados SQL Azure
	- Cluster HDInsight do Azure versão 3.1 ou superior	
7. Depois que os recursos do Azure são criados, verifique se que você tem as informações necessárias para se conectar a cada um desses recursos.
 	- **Conta de armazenamento do Azure** - Nome da conta e chave de conta.  
	- **Banco de Dados SQL do Azure** - Servidor, banco de dados, nome de usuário e senha.
	- **Cluster HDInsight do Azure** - Nome do cluster HDInsight, nome de usuário, senha e nome da conta e chave de conta para o armazenamento do Azure associado a esse cluster.  
 8. Inicie o **PowerShell do Azure**, navegue até **C:\ADFWalkthrough** e execute o script de preparação.**\AzureEnvironmentSetup.ps1**.
 
		Você deve ter o nome da sua assinatura e a conta da Microsoft associada à assinatura antes de executar o script.

			Você precisa configurar o ambiente do Azure usando o script acima toda vez que você iniciar o  PowerShell do Azure. A configuração não é persistente entre as sessões e precisa ser executada separadamente, mesmo ao usar várias sessões simultaneamente.

		Como alternativa, você pode usar o cmdlet **Add-AzureAccount** para entrar no Azure e selecionar uma assinatura (se você tiver várias assinaturas) usando o cmdlet **Select-AzureSubscription**.
	

## Visão geral
O fluxo de trabalho de ponta a ponta é descrito abaixo:
	![Tutorial End to End Flow][image-data-factory-tutorial-end-to-end-flow]

1. O **PartitionGameLogsPipeline** lê os eventos brutos de jogos de um armazenamento de blob (RawGameEventsTable) e cria partições com base no ano, mês e dia (PartitionedGameEventsTable).
2. O **EnrichGameLogsPipeline** junta eventos de jogos particionados (tabela PartitionedGameEvents, que é uma saída do PartitionGameLogsPipeline) com o código geográfico (RefGetoCodeDictionaryTable) e enriquece dados mapeando um endereço IP para a localização geográfica correspondente (EnrichedGameEventsTable).
3. O pipeline **AnalyzeMarketingCampaignPipeline** aproveita os dados enriquecidos (EnrichedGameEventTable gerado pelo EnrichGameLogsPipeline) e os processa com os dados de anúncio (RefMarketingCampaignnTable) para criar a saída final da eficiência de campanha de marketing, que é copiada para o banco de dados SQL do Azure (MarketingCampainEffectivensessSQLTable) e um armazenamento de blob do Azure (MarketingCampaignEffectivenessBlobTable) para análise.
    
## Passo a passo: Criando, implantando e monitorando fluxos de trabalho
1. [Etapa 1: Carregar dados de exemplo e scripts](#MainStep1). Nesta etapa, você carregará todos os dados de exemplo (incluindo todos os logs e dados de referência) e scripts do Hive/Pig que serão executadas pelos fluxos de trabalho. Os scripts que você executa também criam um banco de dados SQL do Azure (chamado MarketingCampaigns), tabelas, tipos definidos pelo usuário e procedimentos armazenados.
2. [Etapa 2: Criar um data factory do Azure](#MainStep2). Nesta etapa, você criará uma data factory do Azure chamado LogProcessingFactory.
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


		![PartitionGamesLogs pipeline][image-data-factory-tutorial-partition-game-logs-pipeline]


	- **EnrichGameLogsPipeline**. Este pipeline junta eventos de jogos particionados (tabela PartitionedGameEvents, que é uma saída do PartitionGameLogsPipeline) com o código geográfico (RefGetoCodeDictionaryTable) e enriquece dados mapeando um endereço IP para a localização geográfica correspondente (EnrichedGameEventsTable) 

		![EnrichedGameLogsPipeline][image-data-factory-tutorial-enrich-game-logs-pipeline]

	- **AnalyzeMarketingCampaignPipeline**. Este pipeline aproveita os dados enriquecidos (EnrichedGameEventTable gerado pelo EnrichGameLogsPipeline) e os processa com os dados de anúncio (RefMarketingCampaignnTable) para criar a saída final da eficiência de campanha de marketing campaign, que é copiada para o banco de dados SQL do Azure (MarketingCampainEffectivensessSQLTable) e um armazenamento de blob do Azure (MarketingCampaignEffectivenessBlobTable) para análise


		![MarketingCampaignPipeline][image-data-factory-tutorial-analyze-marketing-campaign-pipeline]


6. [Etapa 6: Monitorar pipelines e fatias de dados](#MainStep6). Nesta etapa, você monitorará as fatias de dados, tabelas e pipelines usando o Portal do Azure.

### <a name="MainStep1"></a>Etapa 1: Carregar dados de exemplo e scripts
Nesta etapa, você carregará todos os dados de exemplo (incluindo todos os logs e dados de referência) e scripts do Hive/Pig que são executadas pelos fluxos de trabalho. Os scripts que você executa também criam um banco de dados SQL do Azure chamado **MarketingCampaigns**, tabelas, tipos definidos pelo usuário e procedimentos armazenados. 

As tabelas, os tipos definidos pelo usuário e procedimentos armazenados são usados ao mover os resultados da Eficácia de Campanha de Marketing do armazenamento de blobs do Azure para o banco de dados SQL do Azure.

1. Abra **uploadSampleDataAndScripts.ps1** da pasta **C:\ADFWalkthrough** (ou a pasta que contém os arquivos extraídos) no seu editor favorito, substitua a parte destacada pelas informações do cluster e salve o arquivo.


		$storageAccount = <storage account name>
		$storageKey = <storage account key>
		$azuresqlServer = <sql azure server>.database.windows.net
		$azuresqlUser = <sql azure user>@<sql azure server>
		$azuresqlPassword = <sql azure password>

 
	> [WACOM.NOTE] Esse script exige que o utilitário sqlcmd esteja instalado no computador. Se você tiver o SQL Server instalado, você já o possui. Caso contrário, [baixe][sqlcmd-install] e instale o utilitário. 
	> Como alternativa, você pode usar os arquivos na pasta: C:\ADFWalkthrough\Scripts para carregar scripts do hive/pig e arquivos de exemplo no contêiner adfwalkthrough no armazenamento de blob e criar a tabela MarketingCampaignEffectiveness no banco de dados SQL do Azure MarketingCamapaigns.   
2. Confirme que seu computador local tem permissão para acessar o Banco de Dados SQL do Azure. Para habilitar o acesso, use o **Portal de Gerenciamento do Azure** ou **sp_set_firewall_rule** no banco de dados mestre para criar uma regra de firewall para o endereço IP do seu computador. Pode levar até cinco minutos para que essa alteração tenha efeito. Consulte [Configurando regras de firewall para SQL Azure][azure-sql-firewall].
3. Inicie o **PowerShell do Azure**. 
4. Navegue até o local onde você extraiu os exemplos (por exemplo: **C:\ADFWalkthrough**)
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
		6/6/2014 11:54:36 AM 3. Created 'MarketingCampaigns' Azure SQL database and tables.
		6/6/2014 11:54:36 AM You are ready to deploy Linked Services, Tables and Pipelines. 


### <a name="MainStep2"></a>Etapa 2: Criar um data factory do Azure
Nesta etapa, você criará um data factory do Azure chamado **LogProcessingFactory**.

1.	Depois de fazer logon no [Portal de Visualização do Azure][azure-preview-portal], clique em **NOVO** no canto inferior esquerdo e clique em **Data Factory** na folha **Novo**. 

	![New->DataFactory][image-data-factory-new-datafactory-menu] 
	
	Se você não vir **Data Factory** na folha **Novo**, role para baixo. 
	
5. Na folha **Novo data factory** insira **LogProcessingFactory** para o **Nome**.

	![Data Factory Blade][image-data-factory-tutorial-new-datafactory-blade]

6. Se você ainda não criou um grupo de recursos do Azure chamado **ADF**, faça o seguinte:
	1. Clique em **NOME DO GRUPO DE RECURSOS** e clique em **Criar um novo grupo de recursos**.
	
		![Resource Group Blade][image-data-factory-tutorial-resourcegroup-blade]
	2. Na folha **Criar grupo de recursos**, insira **ADF** para o nome do grupo de recursos e clique em **OK**.
	
		![Create Resource Group][image-data-factory-tutorial-create-resourcegroup]
7. Selecione **ADF** para o **NOME DO GRUPO DE RECURSOS**.  
8.	Na folha **Novo data factory**, observe que a opção **Adicionar ao Quadro Inicial** está selecionada por padrão. Isso pode adicionar um link ao data factory no quadro inicial (o que você vê quando ao fazer logon no Portal de Visualização do Azure).

	![Create Data Factory Blade][image-data-factory-tutorial-create-datafactory]

9.	Na folha **Novo data factory**, clique em **Criar** para criar o data factory.
10.	Depois que o data factory é criado, você deverá ver a folha **DATA FACTORY** denominada **LogProcessingFactory**.

	![Data Factory Homepage][image-data-factory-tutorial-datafactory-homepage]

	
	Se você não vê-la, siga um destes procedimentos:

	- Clique em **LogProcessingFactory** no **Quadro Inicial** (página inicial)
	- Clique em **PROCURAR** à esquerda, clique em **Tudo**, clique em **Data factories** e clique no data factory.
 

 
### <a name="MainStep3"></a>Etapa 3: Criar serviços vinculados

Nesta etapa, você criará os seguintes serviços vinculados: StorageLinkedService, AzureSqlLinkedService, HDInsightStorageLinkedService e HDInsightLinkedService.

1.	Na folha **LogProcessingFactory**, clique no bloco **Serviços Vinculados**.

	![Linked Services Tile][image-data-factory-tutorial-linkedservice-tile]

2. Na folha **Serviços Vinculados**, clique em **+ Armazenamento de Dados** na barra de comandos.	

	![Linked Services - Add Store][image-data-factory-tutorial-linkedservices-add-datstore]

3. Na folha **Novo armazenamento de dados**, insira **StorageLinkedService** para o **Nome**, clique em **TIPO (configurações necessárias)** e selecione **Conta de armazenamento do Azure**.

	![Data Store Type - Azure Storage][image-data-factory-tutorial-datastoretype-azurestorage]

4. Na folha **Novo armazenamento de dados**, você verá dois novos campos: **Nome da Conta** e **Chave de Conta**. Insira o nome da conta e a chave de conta da sua **Conta de Armazenamento do Azure**.

	![Azure Storage Settings][image-data-factory-tutorial-azurestorage-settings]

	Você pode obter o nome da conta e a chave de conta da sua conta de armazenamento do Azure no portal, conforme mostrado abaixo:

	![Storage Key][image-data-factory-tutorial-storage-key]
  
5. Depois de clicar em **OK** na folha Novo armazenamento de dados, você verá **StorageLinkedService** na lista de **ARMAZENAMENTOS DE DADOS** na folha **Serviços Vinculados**. Verifique o hub **NOTIFICAÇÕES** (à esquerda) para ver todas as mensagens.

	![Linked Services Blade with Storage][image-data-factory-tutorial-linkedservices-blade-storage]
   
6. Repita as **etapas 2-5** para criar outro serviço vinculado chamado: **HDInsightStorageLinkedService**. Esse é o armazenamento usado pelo seu cluster HDInsight.
7. Confirme que você vê **StorageLinkedService** e **HDInsightStorageLinkedService** na lista na folha Serviços Vinculados.
8. Na folha **Serviços Vinculados**, clique em **Adicionar (+) Armazenamento de Dados** na barra de comandos.
9. Insira **AzureSqlLinkedService** para o nome.
10. Clique em **TIPO (configurações necessárias)**, selecione **Banco de Dados SQL do Azure**.
11. Agora, você deve ver os seguintes campos adicionais na folha **Novo armazenamento de dados**. Insira o nome do **servidor** do Banco de Dados SQL do Azure SQL, **nome do** banco de dados, **nome de usuário** e **senha**, e clique em **OK**.
	1. Insira **MarketingCampaigns** para o **banco de dados**. Esse é o banco de dados SQL do Azure criado pelos scripts que você executou na Etapa 1. Você deve confirmar que esse banco de dados foi realmente criado pelos scripts (se ocorreram erros).
		
 		![Azure SQL Settings][image-data-factory-tutorial-azuresql-settings]

		Para obter esses valores no Portal de Gerenciamento do Azure: clique em Exibir cadeias de conexão de Banco de Dados SQL para banco de dados MarketingCampaigns

		![Azure SQL Database Connection String][image-data-factory-tutorial-azuresql-database-connection-string]

12. Certifique-se de que você vê todos os três armazenamentos de dados que você criou: **StorageLinkedService**, **HDInsightStorageLinkedService** e **AzureSqlLinkedService**.
13. Você precisa criar outro serviço vinculado, mas esse é um serviço de computação, especificamente o **cluster HDInsight do Azure**. O portal ainda não da suporte à criação de um serviço de computação vinculada. Portanto, você precisa usar o PowerShell do Azure para criar esse serviço vinculado. 
14. Alterne para o **PowerShell do Azure** se você já o tiver aberto (ou) inicie o **PowerShell do Azure**.
15. Alterne para o modo **AzureResourceManager** pois os cmdlets de Data Factory do Azure estão disponíveis nesse modo.

		Switch-AzureMode AzureResourceManager

16. Navegue até a subpasta **LinkedServices** em **C:\ADFWalkthrough** (ou) da pasta do local onde você extraiu os arquivos.
17. Abra **HDInsightLinkedService.json** em seu editor favorito, substitua a parte destacada pelas suas informações de cluster e salve o arquivo.

        "clusterUri": "https://<hdinsightclustername>.azurehdinsight.net/",
        "userName": "<hdiusername>",
        "location": "<hdiregion>",
        "password": "<hdipassword>",

18. Use o seguinte comando para definir a variável $df com o nome do data factory.

		$df = "LogProcessingFactory"
19. Use o cmdlet **New-AzureDataFactoryLinkedService** para criar um Serviço Vinculado da seguinte maneira. Comece com a conta de armazenamento:

		New-AzureDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightLinkedService.json
 
	Se você estiver usando um nome diferente para o nome de ResourceGroupName, DataFactoryName ou LinkedService, consulte-o no cmdlet anterior. Além disso, forneça o caminho completo do arquivo JSON de Serviço Vinculado se o arquivo não puder ser encontrado.
20. Você deve ver todos os quatro serviços vinculados na folha **Serviços Vinculados**, conforme mostrado abaixo. Se a folha de serviços vinculados não estiver aberta, clique em Serviços Vinculados na página **DATA FACTORY** de **LogProcessingFactory**. Pode levar alguns segundos até a folha de serviços vinculados seja atualizada.

	![Linked Services All][image-data-factory-tutorial-linkedservices-all]
 

### <a name="MainStep4"></a>Etapa 4: Criar tabelas 
Nesta etapa, você criará as seguintes tabelas: 

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Tutorial End-to-End Flow][image-data-factory-tutorial-end-to-end-flow]
 
A figura acima exibe pipelines na linha intermediária e tabelas nas linhas superior e inferior. 

O Portal do Azure não dá suporte à criação de conjuntos de dados e tabelas ainda, portanto, será necessário usar o PowerShell do Azure para criar tabelas nesta versão.

#### Para criar as tabelas

1.	No PowerShell do Azure, navegue até a pasta **Tabelas** (**C:\ADFWalkthrough\Tables**) do local onde você extraiu os exemplos. 
2.	Use o cmdlet **New-AzureDataFactoryTable** para criar as tabelas da seguinte maneira para **RawGameEventsTable**.json	


		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\RawGameEventsTable.json

	Se você estiver usando um nome diferente para o nome de ResourceGroupName e DataFactoryName, consulte-o no cmdlet anterior. Além disso, forneça o caminho completo do arquivo JSON da tabela se o arquivo não puder ser encontrado pelo cmdlet.

3. Repita a etapa anterior para criar as tabelas a seguir:	
		
	- **PartitionedGameEventsTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\PartitionedGameEventsTable.json

	- **RefGeoCodeDictionaryTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\RefGeoCodeDictionaryTable.json

	- **RefMarketingCampaignTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\RefMarketingCampaignTable.json

	- **EnrichedGameEventsTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\EnrichedGameEventsTable.json

	- **MarketingCampaignEffectivenessSQLTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\MarketingCampaignEffectivenessSQLTable.json

	- **MarketingCampaignEffectivenessBlobTable**
			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\MarketingCampaignEffectivenessBlobTable.json



4. No **Portal de Visualização do Azure**, clique em **Conjuntos de Dados** na folha **DATA FACTORY** para **LogProcessingFactory** e confirme que você vê todos os conjuntos de dados (tabelas são conjuntos de dados retangulares). 

	![Data Sets All][image-data-factory-tutorial-datasets-all]

	Você também pode usar o seguinte comando do PowerShell do Azure:
			
		Get-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df

	


### <a name="MainStep5"></a>Etapa 5: Criar e agendar pipelines
Nesta etapa, você criará os seguintes pipelines: PartitionGameLogsPipeline, EnrichGameLogsPipeline e AnalyzeMarketingCampaignPipeline.

1. No **Windows Explorer**, navegue até a subpasta **Pipelines** na pasta **C:\ADFWalkthrough** (ou do local onde você extraiu os exemplos).
2.	Abra **PartitionGameLogsPipeline.json** no seu editor favorito, substitua a parte destacada pela sua conta de armazenamento das informações de conta de armazenamento de dados e salve o arquivo.
			
		"RAWINPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/rawgameevents/",
		"PARTITIONEDOUTPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/partitionedgameevents/",

3. Repita a etapa para criar os seguintes pipelines:
	1. **EnrichGameLogsPipeline**.json (3 ocorrências)
	2. **AnalyzeMarketingCampaignPipeline**.json (3 ocorrências)

	**IMPORTANTE:** Confirme que você substituiu todas as <storageaccountname> pelo nome da sua conta de armazenamento. 
 
4.  No **PowerShell do Azure**, navegue até a subpasta **Pipelines** na pasta **C:\ADFWalkthrough** (ou do local onde você extraiu os exemplos).
5.  Use o cmdlet **New-AzureDataFactoryPipeline** para criar os Pipelines da seguinte maneira para **PartitionGameLogspeline**.json	 
			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\PartitionGameLogsPipeline.json

	Se você estiver usando um nome diferente para o nome de ResourceGroupName, DataFactoryName ou Pipeline, consulte-o no cmdlet anterior. Além disso, forneça o caminho completo do arquivo de Pipeline JSON.
6. Repita a etapa anterior para criar os seguintes pipelines:
	1. **EnrichGameLogsPipeline**
			
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\EnrichGameLogsPipeline.json

	2. **AnalyzeMarketingCampaignPipeline**
				
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\AnalyzeMarketingCampaignPipeline.json

7. Use o cmdlet **Get-AzureDataFactoryPipeline** para obter a lista dos Pipelines.
			
		Get-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df

8. Depois que os pipelines são criados, você pode especificar a duração em que o processamento de dados ocorrerá. Especificando o período ativo de um pipeline, você está definindo a duração de tempo em que as fatias de dados serão processadas com base nas propriedades de Disponibilidade que foram definidas para cada tabela do ADF.

Para especificar o período ativo para o pipeline, você pode usar o cmdlet Set-AzureDataFactoryPipelineActivePeriod. Neste passo a passo, os dados de exemplo são de 05/01 a 05/05. Use 2014-05-01 como StartDateTime. EndDateTime é opcional.
			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name PartitionGameLogsPipeline
  
9. Confirme para definir o período ativo para o pipeline.
			
			Confirmar
			Are you sure you want to set pipeline 'PartitionGameLogsPipeline' active period from '05/01/2014 00:00:00' to '05/05/2014 00:00:00'?
			[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): n

10. Repita as duas etapas anteriores para definir o período ativo para os seguintes pipelines.
	1. **EnrichGameLogsPipeline**
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name EnrichGameLogsPipeline

	2. **AnalyzeMarketingCampaignPipeline** 
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name AnalyzeMarketingCampaignPipeline

11. No **Portal de Visualização do Azure**, clique no bloco **Pipelines** (não nos nomes dos pipelines) na folha **DATA FACTORY** de **LogProcessingFactory**, você verá os pipelines criados.

	![All Pipelines][image-data-factory-tutorial-pipelines-all]

12. Na folha **DATA FACTORY** de **LogProcessingFactory**, clique em **Diagrama**.

	![Diagram Link][image-data-factory-tutorial-diagram-link]

13. Você pode reorganizar o diagrama que você vê, e aqui está um diagrama reorganizado mostrando entradas diretas na parte superior e saídas na parte inferior. Você pode ver que a saída de **PartitionGameLogsPipeline** é passada como entrada para EnrichGameLogsPipeline e a saída de **EnrichGameLogsPipeline** é passada para **AnalyzeMarketingCampaignPipeline**. Clique duas vezes em um título para ver detalhes sobre o artefato que a folha representa.

	![Diagram View][image-data-factory-tutorial-diagram-view]

	**Parabéns!** Você criou com êxito o Data Factory do Azure, os Serviços Vinculados, Pipelines, Tabelas e iniciou o fluxo de trabalho. 


### <a name="MainStep6"></a>Etapa 6: Monitorar pipelines e fatias de dados 

1.	Se você não tiver a folha DATA FACTORY para abrir o LogProcessingFactory, poderá fazer o seguinte:
	1.	Clique em **LogProcessingFactory** no **Quadro Inicial**. Ao criar o data factory, a opção **Adicionar ao Quadro Inicial** foi marcada automaticamente.

		![Monitoring Startboard][image-data-factory-monitoring-startboard]

	2. Clique no hub **PROCURAR** e clique em **TUDO**.
	 	
		![Monitoring Hub Everything][image-data-factory-monitoring-hub-everything]

	3. Na folha **Procurar**, selecione **Data factories** e selecione **LogProcessingFactory** na folha **Data factories**.

	![Monitoring Browse Datafactories][image-data-factory-monitoring-browse-datafactories]
2. Você pode monitorar seu data factory de várias maneiras. Você pode iniciar com pipelines ou conjuntos de dados. Vamos começar com Pipelines e analisar mais. 
3.	Clique em **Pipelines** na folha **DATA FACTORY**. 
4.	Clique em **PartitionGameLogsPipeline** na folha Pipelines. 
5.	Na folha **PIPELINE** de **PartitionGameLogsPipeline**, você verá que o pipeline consume o conjunto de dados **RawGameEventsTable**.  Clique em **RawGameEventsTable**.

	![Pipeline Consumed and Produced][image-data-factory-monitoring-pipeline-consumed-produced]

6. Na folha TABELA de **RawGameEventsTable**, você verá todas as fatias. Na seguinte captura de tela, todas as fatias estão no estado **Pronto** e não há fatias com problema. Isso significa que os dados estão prontos para serem processados.	

	![RawGameEventsTable TABLE blade][image-data-factory-monitoring-raw-game-events-table]
 
7. Agora, na folha **PIPELINE** de **PartiionGameLogsPipeline**, clique em **Produzido**. 
8. Você deverá ver a lista de conjuntos de dados que esse pipeline produz: 
9. Clique na tabela **PartitionedGameEvents** na folha **Conjuntos de dados gerados**. 
10.	Confirme se o **status** de todas as fatias está definido como **Pronto**. 
11.	Clique em uma das fatias que está **Pronta** para ver a folha **FATIA DE DADOS** dessa fatia.

	![RawGameEventsTable DATA SLICE blade][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	Se houver um erro, você verá um status **Falha** aqui.  Você também pode ver as duas fatias com status **Pronto** ou com status **PendingValidation**, dependendo da rapidez com que as fatias são processadas.
 
	Consulte a [Referência do Desenvolvedor do Data Factory do Azure][developer-reference] para obter uma compreensão de todos os status possíveis da fatia.

12.	Na folha **FATIA DE DADOS**, clique na execução da lista **Execuções de Atividade**. Você deve ver a folha Execução de Atividade dessa fatia. Você deve ver a seguinte folha **DETALHES DE EXECUÇÃO DA ATIVIDADE**.

	![Activity Run Details blade][image-data-factory-monitoring-activity-run-details]

13.	Clique em **Download** para baixar os arquivos. Esta tela é especialmente útil quando você estiver solucionando erros de processamento do HDInsight. 
	 
	
Quando todo o pipeline tiver concluído a execução, você poderá examinar **MarketingCampaignEffectivenessTable** no banco de dados SQL do Azure **MarketingCampaigns** para exibir os resultados. 

**Parabéns!** Agora você pode monitorar e solucionar problemas de fluxos de trabalho. Você aprendeu a usar o Data Factory do Azure para processar dados e obter a análise.

	 

## Passo a passo: Usando dados locais

**(recomendado)** Revise e pratique o passo a passo no artigo [Habilitar seus pipelines para trabalhar com dados locais][useonpremisesdatasources] para ver um passo a passo sobre como criar um pipeline para mover dados do SQL Server local para um armazenamento de blob do Azure.


Neste passo a passo, você aprenderá a configurar o ambiente para habilitar o pipeline para trabalhar com dados locais.
 
Na última etapa do cenário de processamento de log do primeiro passo a passo com Partição -> Enriquecer -> Analisar fluxo de trabalho, a saída de eficácia de campanha marketing foi copiada para um banco de dados SQL do Azure. Você também pode mover esses dados para o SQL Server local para análise dentro da sua organização.
 
Para copiar dados de eficácia de campanha de marketing do Blob do Azure para o SQL Server local, você precisa criar Serviço Vinculado local, Tabela e Pipeline adicionais usando o mesmo conjunto de cmdlets introduzidos no primeiro passo a passo.

Neste tutorial, você executará as seguintes etapas: 

1. [Etapa 1: Criar um gateway de gerenciamento de dados](#OnPremStep1). O Gateway de Gerenciamento de Dados  é um agente cliente que fornece acesso a fontes de dados locais de sua organização na nuvem. O gateway permite que a transferência de dados entre um SQL Server local e armazenamentos de dados do Azure.	

	Você deve ter pelo menos um gateway instalado no seu ambiente corporativo, bem como registrá-lo com o Data Factory do Azure antes de adicionar o banco de dados SQL Server local como um serviço vinculado a um data factory do Azure.

2. [Etapa 2: Criar um serviço vinculado para o SQL Server local](#OnPremStep2). Nesta etapa, você primeiro cria um banco de dados e uma tabela no computador local do SQL Server e, em seguida, cria o serviço vinculado: **OnPremSqlLinkedService**.  
3. [Etapa 3: Criar tabela e pipeline](#OnPremStep3). Nesta etapa, você criará uma tabela **MarketingCampaignEffectivenessOnPremSQLTable** e um pipeline **EgressDataToOnPremPipeline**. 

4. [Etapa 4: Monitorar o pipeline e exibir o resultado](#OnPremStep4). Nesta etapa, você monitorará as fatias de dados, tabelas e pipelines usando o Portal do Azure.


### <a name="OnPremStep1"></a>Etapa 1: Criar um Gateway de Gerenciamento de Dados

O Gateway de Gerenciamento de Dados é um agente cliente que fornece acesso a fontes de dados locais de sua organização na nuvem. O gateway permite que a transferência de dados entre um SQL Server local e armazenamentos de dados do Azure.
  
Você deve ter pelo menos um gateway instalado no seu ambiente corporativo, bem como registrá-lo com o Data Factory do Azure antes de adicionar o banco de dados SQL Server local como um serviço vinculado a um data factory do Azure.

Se você tiver um gateway de dados existente que você possa usar, ignore esta etapa.

1.	Crie um gateway de dados lógicos. No **Portal de Visualização do Azure**, clique em **Serviços Vinculados** na folha **DATA FACTORY**.
2.	Clique em **Adicionar (+) Gateway de Dados** na barra de comandos.  
3.	Na folha **Novo gateway de dados**, clique em **CRIAR**.
4.	Na folha **Criar**, insira **MyGateway** para o **nome** do gateway de dados.
5.	Clique em **ESCOLHER UMA REGIÃO** e altere-a se necessário. 
6.	Clique em **OK** na folha **Criar**. 
7.	Você deve ver a folha **Configurar**. 
8.	Na folha **Configurar**, clique em **Instalar diretamente neste computador**. Isso vai baixar, instalar e configurar o gateway em seu computador e registrar o gateway com o serviço. Se você tiver um gateway existente instalado no computador que você deseja vincular a esse gateway lógico no portal, use a chave nessa folha para registrar novamente o gateway usando a ferramenta Gerenciador de Configuração de Gateway de gerenciamento de dados (visualização).

	![Data Management Gateway Configuration Manager][image-data-factory-datamanagementgateway-configuration-manager]

9. Clique em **OK** para fechar a folha **Configurar** e em **OK** para fechar a folha **Criar**. Aguarde até que o status de **MyGateway** na folha **Serviços Vinculados** mude para **BOM**. Você também pode iniciar a ferramenta **Gerenciador de Configuração de Gateway de gerenciamento de dados (visualização)** para confirmar se o nome do gateway corresponde ao nome no portal e o **status** é **Registrado**. Você terá que fechar e reabrir a folha Serviços Vinculados para ver o status mais recente. Pode levar alguns minutos antes que a tela seja atualizada com o status mais recente.	

### <a name="OnPremStep2"></a>Etapa 2: Criar um serviço vinculado para o SQL Server local

Nesta etapa, você primeiro cria o banco de dados e a tabela necessários no computador local do SQL Server e, em seguida, cria o serviço vinculado.

#### Preparar tabela e banco de dados local

Para começar, você precisa criar o banco de dados SQL Server, a tabela, os tipos definidos pelo usuário e procedimentos armazenados. Esses serão usados para mover os resultados **MarketingCampaignEffectiveness** do blob do Azure para o banco de dados SQL Server.

1.	No **Windows Explorer**, navegue até a subpasta **OnPremises** na pasta **C:\ADFWalkthrough** (ou do local onde você extraiu os exemplos).
2.	Abra **prepareOnPremDatabase&Table.ps1** em seu editor favorito, substitua a parte destacada pelas informações do SQL Server e salve o arquivo (forneça os detalhes de **autenticação do SQL**). Para o tutorial, habilite a autenticação do SQL para o banco de dados. 
			
		$dbServerName = "<servername>"
		$dbUserName = "<username>"
		$dbPassword = "<password>"

3. No **PowerShell do Azure**, navegue até a pasta **C:\ADFWalkthrough\OnPremises**.
4.	Execute **prepareOnPremDatabase&Table.ps1** **(entre aspas duplas ou conforme mostrado abaixo)**.
			
		& '.\prepareOnPremDatabase&Table.ps1'

5. Once the script executes successfully, you will see the following:	
			
		PS E:\ADF\ADFWalkthrough\OnPremises> & '.\prepareOnPremDatabase&Table.ps1'
		6/10/2014 10:12:33 PM Script to create sample on-premises SQL Server Database and Table
		6/10/2014 10:12:33 PM Creating the database [MarketingCampaigns], table and stored procedure on [.]...
		6/10/2014 10:12:33 PM Connecting as user [sa]
		6/10/2014 10:12:33 PM Summary:
		6/10/2014 10:12:33 PM 1. Database 'MarketingCampaigns' created.
		6/10/2014 10:12:33 PM 2. 'MarketingCampaignEffectiveness' table and stored procedure 


#### Criar o serviço vinculado

1.	No **Portal de Visualização do Azure**, clique no bloco **Serviços Vinculados** na folha **DATA FACTORY** de **LogProcessingFactory**.
2.	Na folha **Serviços Vinculados**, clique em **Adicionar (+) Armazenamento de Dados**.
3.	Na folha **Novo armazenamento de dados** insira **OnPremSqlLinkedService** para o **Nome**. 
4.	Clique em **Tipo (Configurações necessárias)** e selecione **SQL Server**. Agora você deve ver as configurações de **GATEWAY DE DADOS**, **Servidor**, **Banco de dados** e **CREDENCIAIS** na folha **Novo armazenamento de dados**. 
5.	Clique em **GATEWAY DE DADOS (definir configurações necessárias)** e selecione o **MyGateway** criado anteriormente. 
6.	Insira o **nome** do servidor de banco de dados que hospeda o banco de dados **MarketingCampaigns**. 
7.	Insira **MarketingCampaigns** para o banco de dados. 
8.	Clique em **CREDENCIAIS**. 
9.	Na folha **Credenciais**, clique em **clique aqui para definir credenciais com segurança**.
10.	Ela instala um aplicativo em um único clique pela primeira vez e inicia a caixa de diálogo **Definindo Credenciais**. 
11.	Na caixa de diálogo **Definindo Credenciais**, insira **Nome de Usuário** e **Senha**, e clique em **OK**. Aguarde até que a caixa de diálogo seja fechada. 
12.	Clique em **OK** na folha **Novo armazenamento de dados**. 
13.	Na folha **Serviços Vinculados**, confirme se **OnPremSqlLinkedService** aparece na lista e o **status** do serviço vinculado é **Bom**.

### <a name="OnPremStep3"></a>Etapa 3: Criar tabela e pipeline

#### Criar a tabela lógica local

1.	No **PowerShell do Azure**, alterne para a pasta **C:\ADFWalkthrough\OnPremises**. 
2.	Use o cmdlet **New-AzureDataFactoryTable** para criar as Tabelas da seguinte maneira para **MarketingCampaignEffectivenessOnPremSQLTable.json**.

			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\MarketingCampaignEffectivenessOnPremSQLTable.json
	 
#### Criar o pipeline para copiar os dados do Blob do Azure para o SQL Server

1.	Use o cmdlet **New-AzureDataFactoryPipeline** para criar o Pipeline da seguinte maneira para **EgressDataToOnPremPipeline.json**.

			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\EgressDataToOnPremPipeline.json
	 
2. Use o cmdlet **Set-AzureDataFactoryPipelineActivePeriod** para especificar o período ativo de **EgressDataToOnPremPipeline**.

			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name EgressDataToOnPremPipeline

	Pressione **'Y'** para continuar.
	
### <a name="OnPremStep4"></a>Etapa 4: Monitorar o pipeline e exibir o resultado

Agora você pode usar as mesmas etapas introduzidas na [Etapa 6: Monitorando tabelas e pipelines](#MainStep6)  para monitorar o novo pipeline e as fatias de dados para a nova tabela ADF local.
 
Quando você vir o status de uma fatia da tabela **MarketingCampaignEffectivenessOnPremSQLTable** mudar para Pronto, isso significa que o pipeline concluiu a execução da fatia. Para exibir os resultados, consulte a tabela **MarketingCampaignEffectiveness** no banco de dados **MarketingCampaigns** no seu SQL Server.
 
Parabéns! Você verificou com êxito o passo a passo para usar seus dados locais. 
 
## Consulte também

Artigo | Descrição
------ | ---------------
[Habilitar seus pipelines para trabalhar com dados locais][useonpremisesdatasources] | Este artigo tem um passo a passo que mostra como copiar dados de um banco de dados SQL Server local para um blob do Azure.
[Usar o Pig e o Hive com o Data Factory][usepigandhive] | Este artigo tem um passo a passo que mostra como usar a Atividade de HDInsight para executar um script do hive/pig para processar dados de entrada a fim de gerar dados de saída. 
[Usar atividades personalizadas em um Data Factory][use-custom-activities] | Este artigo fornece um passo a passo com instruções para criar uma atividade personalizada e usá-la em um pipeline. 
[Monitorar e gerenciar o Data Factory do Azure usando o PowerShell][monitor-manage-using-powershell] | Este artigo descreve como monitorar um Data Factory do Azure usando cmdlets do PowerShell do Azure. Você pode testar os exemplos neste artigo sobre ADFTutorialDataFactory.
[Solucionar problemas de Data Factory][troubleshoot] | Este artigo descreve como solucionar problemas do Data Factory do Azure. Você pode testar o passo a passo neste artigo no ADFTutorialDataFactory introduzindo um erro (excluindo a tabela no Banco de Dados SQL do Azure). 
[Referência do Desenvolvedor do Data Factory do Azure][developer-reference] | A Referência do Desenvolvedor tem o conteúdo de referência abrangente de cmdlets, script JSON, funções, etc...
[Referência de cmdlet do Data Factory do Azure][cmdlet-reference] | Este conteúdo de referência apresenta detalhes sobre todos os cmdlets do Data Factory. 

[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[use-custom-activities]: ../data-factory-use-custom-activities
[troubleshoot]: ../data-factory-troubleshoot
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: ../data-factory-get-started
[adfintroduction]: ../data-factory-introduction
[useonpremisesdatasources]: ../data-factory-use-onpremises-datasources
[usepigandhive]: ../data-factory-pig-hive-activities

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/pt-br/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx

[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908


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

<!--HONumber=46--> 
