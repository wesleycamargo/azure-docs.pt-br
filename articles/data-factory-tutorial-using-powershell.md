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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Tutorial: Mover e processar os arquivos de log usando o alocador de dados [PowerShell]
Este artigo fornece uma passo a passo completo de um cenário canônico de processamento de log usando o Data Factory do Azure para transformar dados de arquivos de log em informações.

## Cenário
A Contoso é uma empresa de jogos que cria os jogos para várias plataformas: computadores pessoais (PCs), dispositivos de mão mantido e consoles de jogos. Cada um desses jogos produz toneladas de logs. O objetivo da Contoso é coletar e analisar os logs gerados por esses jogos para obter informações sobre o uso, identificar oportunidades de vendas e vendas cruzadas, desenvolver novos recursos atraentes etc. para melhorar os negócios e fornecer a melhor experiência para os clientes.
 
Neste passo a passo, podemos coletar logs de exemplo, processar e enriquecê-los com dados de referência, além de transformar os dados para avaliar a eficiência de uma campanha de marketing que a Contoso lançou recentemente.

## Preparando-se para o tutorial
1.	Leitura [Introdução ao Azure dados Factory][adfintroduction] para obter uma visão geral da fábrica de dados do Azure e compreensão dos conceitos de nível superior.
2.	Você deve ter uma assinatura do Azure para ver este tutorial. Para obter informações sobre como adquirir uma assinatura, consulte [Opções de compra][azure-purchase-options], [Ofertas para membros][azure-member-offers] ou [Avaliação gratuita][azure-free-trial].
3.	Você deve baixar e instalar [PowerShell do Azure][download-azure-powershell] em seu computador. 
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
O fluxo de trabalho de ponta a ponta é descrito abaixo: ![Tutorial fluxo de ponta a ponta][image-data-factory-tutorial-end-to-end-flow]

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

1.	Após fazer logon na [Azure Preview Portal][azure-preview-portal], clique em **novo** do canto inferior esquerdo e clique em **Data Factory** no **novo** lâmina. 

	![Novo -> DataFactory][image-data-factory-new-datafactory-menu]
	
	Se você não vir **Data Factory** sobre o **novo** lâmina, role para baixo.
	
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

> [AZURE.NOTE]Este artigo usa o PowerShell do Azure para criar pipelines, tabelas e serviços vinculados. Consulte [Tutorial usando dados de fábrica de Editor][adftutorial-using-editor] se você deseja executar este tutorial usando o Portal do Azure, especificamente o Editor de fábrica de dados.

Nesta etapa, você criará os seguintes serviços vinculados: StorageLinkedService, AzureSqlLinkedService, HDInsightStorageLinkedService e HDInsightLinkedService.


1.	No **LogProcessingFactory** lâmina, clique em **Serviços vinculados** lado a lado.

	![Bloco serviços vinculados][image-data-factory-tutorial-linkedservice-tile]

2. No **Serviços vinculados** lâmina, clique em **+ Data Store** na barra de comandos.

	![Serviços vinculados - adicionar armazenamento][image-data-factory-tutorial-linkedservices-add-datstore]

3. No **novo armazenamento de dados** lâmina, digite **StorageLinkedService** para o **nome**, clique em **(configurações necessárias) do tipo**, e selecione **conta de armazenamento do Azure**.

	![Tipo de repositório de dados - armazenamento do Azure][image-data-factory-tutorial-datastoretype-azurestorage]

4. No **novo armazenamento de dados** lâmina, você verá dois novos campos: **nome da conta** e **chave de conta**. Insira o nome da conta e chave de conta para o **conta de armazenamento do Azure**.

	![Configurações de armazenamento do Azure][image-data-factory-tutorial-azurestorage-settings]

	Você pode obter o nome da conta e a chave de conta da sua conta de armazenamento do Azure no portal, conforme mostrado abaixo:

	![Chave de armazenamento][image-data-factory-tutorial-storage-key]
  
5. Depois de clicar em **OK** nos novos dados armazenar lâmina, você deverá ver **StorageLinkedService** na lista de **ARMAZENAMENTOS de dados** no **Serviços vinculados** lâmina. Verificar **notificações** Hub (à esquerda) para ver todas as mensagens.

	![Lâmina de serviços vinculados com armazenamento][image-data-factory-tutorial-linkedservices-blade-storage]
   
6. Repita **as etapas 2 a 5** criar outro vinculado nomeado: **HDInsightStorageLinkedService**. Esse é o armazenamento usado pelo seu cluster HDInsight.
7. Confirme que você vê dois **StorageLinkedService** e **HDInsightStorageLinkedService** na lista na lâmina de serviços vinculados.
8. No **Serviços vinculados** lâmina, clique em **Adicionar (+) de armazenamento de dados** na barra de comandos.
9. Digite **AzureSqlLinkedService** para o nome.
10. Clique em **(configurações necessárias) do tipo**, selecione **banco de dados do SQL Azure**.
11. Agora, você deve os seguintes campos adicionais sobre o **novo armazenamento de dados** lâmina. Insira o nome do banco de dados do SQL Azure **server**, **banco de dados** nome **nome de usuário**, e **senha**, e clique em **OK**.
	1. Digite **MarketingCampaigns** para o **banco de dados**. Isso é o banco de dados do SQL Azure criado pelos scripts que você executou na etapa 1. Você deve confirmar que esse banco de dados foi realmente criado pelos scripts (se houve erros).
		
 		![Configurações do SQL Azure][image-data-factory-tutorial-azuresql-settings]

		Para obter esses valores no Portal de gerenciamento do Azure: clique em cadeias de conexão de banco de dados do modo de exibição SQL para banco de dados MarketingCampaigns

		![Cadeia de conexão de banco de dados do SQL Azure][image-data-factory-tutorial-azuresql-database-connection-string]

12. Confirme que você vê todos os armazenamentos de dados de três você criou: **StorageLinkedService**, **HDInsightStorageLinkedService**, e **AzureSqlLinkedService**.
13. Você precisa criar outro serviço vinculado, mas esse é um serviço de computação, especificamente **cluster Azure HDInsight**. O portal ainda não da suporte à criação de um serviço de computação vinculada. Portanto, você precisa usar o PowerShell do Azure para criar esse serviço vinculado. 
14. Alternar para **PowerShell do Azure** se você tiver já aberto (ou) inicialização **PowerShell do Azure**. Se você tiver fechado e reaberto PowerShell do Azure, você precisa executar os seguintes comandos: 
	- Executar **Add-AzureAccount** e insira o nome de usuário e senha que você usa para entrar no portal de visualização do Azure.  
	- Executar **Get-AzureSubscription** para exibir todas as assinaturas para essa conta.
	- Executar **Select-AzureSubscription** para selecionar a assinatura que você deseja trabalhar. Esta assinatura deve ser a mesma que você usou no Portal de Visualização do Azure. 
15. Alterne para o **AzureResourceManager** o modo como os cmdlets de fábrica de dados do Azure estão disponíveis nesse modo.

		Switch-AzureMode AzureResourceManager

16. Navegue até o **LinkedServices** subpasta em **C:\ADFWalkthrough** (ou) da pasta do local onde você instalou os arquivos.
17. Abra **HDInsightLinkedService.json** em seu editor favorito e observe que o tipo é definido como **HDInsightOnDemandLinkedService**.


	> [AZURE.NOTE]O serviço Data Factory do Azure dá suporte à criação de um cluster sob demanda e o usa para processar entrada a fim de gerar dados de saída. Você também pode usar seu próprio cluster para fazer isso. Quando você usa o cluster HDInsight sob demanda, um cluster é criado para cada fatia. Ao passo que, ao utilizar seu próprio cluster de HDInsight, o cluster está pronto para processar a fatia imediatamente. Portanto, quando você usar cluster sob demanda, não verá os dados de saída mais rapidamente que quando usa seu próprio cluster. Para fins de exemplo, vamos usar um cluster sob demanda. O HDInsightLinkedService vincula um cluster de HDInsight sob demanda para o alocador de dados. Para usar seu próprio cluster HDInsight, atualize a seção de propriedades do arquivo HDInsightLinkedService.json, conforme mostrado a seguir (substituir clustername, nome de usuário e senha com valores apropriados):
	> 
			"Properties": 
			{
        		"Type": "HDInsightBYOCLinkedService",
	        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
    	    	"UserName": "<username>",
    	    	"Password": "<password>",
    	    	"LinkedServiceName": "HDInsightStorageLinkedService"
    		}
		

18. Use o seguinte comando para definir a variável $df com o nome do data factory.

		$df = “LogProcessingFactory”
19. Use o cmdlet **novo AzureDataFactoryLinkedService** para criar um serviço vinculado da seguinte maneira. Comece com a conta de armazenamento:

		New-AzureDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightLinkedService.json
 
	Se você estiver usando um nome diferente para o nome de ResourceGroupName, DataFactoryName ou LinkedService, consulte-o no cmdlet anterior. Além disso, forneça o caminho completo do arquivo JSON de Serviço Vinculado se o arquivo não puder ser encontrado.
20. Você deve ver todos os quatro serviços de vinculado no **Serviços vinculados** lâmina conforme mostrado abaixo. Se a lâmina de serviços vinculados não estiver aberta, clique em serviços vinculados no **DATA FACTORY** página **LogProcessingFactory**. Pode levar alguns segundos até a folha de serviços vinculados seja atualizada.

	![Vinculado serviços todos][image-data-factory-tutorial-linkedservices-all]
 

## <a name="MainStep4"></a> Etapa 4: Criar tabelas 
Nesta etapa, você criará as seguintes tabelas:

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Tutorial fluxo de ponta a ponta][image-data-factory-tutorial-end-to-end-flow]
 
A figura acima exibe pipelines na linha intermediária e tabelas nas linhas superior e inferior.

O Portal do Azure não dá suporte à criação de conjuntos de dados e tabelas ainda, portanto, será necessário usar o PowerShell do Azure para criar tabelas nesta versão.

### Para criar as tabelas

1.	No PowerShell do Azure, navegue até o **tabelas** pasta (* * C:\ADFWalkthrough\Tables**) do local onde você extraiu os exemplos. 
2.	Use o cmdlet **novo AzureDataFactoryTable** para criar as tabelas da seguinte maneira para **RawGameEventsTable**. JSON	


		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RawGameEventsTable.json

	Se você estiver usando um nome diferente para o nome de ResourceGroupName e DataFactoryName, consulte-o no cmdlet anterior. Além disso, forneça o caminho completo do arquivo JSON da tabela se o arquivo não puder ser encontrado pelo cmdlet.

3. Repita a etapa anterior para criar as tabelas a seguir:
		
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionedGameEventsTable.json
		
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RefGeoCodeDictionaryTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RefMarketingCampaignTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichedGameEventsTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessSQLTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessBlobTable.json



4. No **Azure Preview Portal**, clique em **conjuntos de dados** no **DATA FACTORY** lâmina para **LogProcessingFactory** e confirme que você vê todos os conjuntos de dados (tabelas são conjuntos de dados retangulares).

	![Todos os conjuntos de dados][image-data-factory-tutorial-datasets-all]

	Você também pode usar o seguinte comando do PowerShell do Azure:
			
		Get-AzureDataFactoryTable –ResourceGroupName ADF –DataFactoryName $df

	


## <a name="MainStep5"></a> Etapa 5: Criar e agendar pipelines
Nesta etapa, você criará os seguintes pipelines: PartitionGameLogsPipeline, EnrichGameLogsPipeline e AnalyzeMarketingCampaignPipeline.

1. Em **Windows Explorer**, navegue até o **Pipelines** subpasta em **C:\ADFWalkthrough** pasta (ou do local de onde você extraiu os exemplos).
2.	Abra **PartitionGameLogsPipeline.json** em seu editor favorito, substitua o realçado com sua conta de armazenamento para as informações de conta de armazenamento de dados e salve o arquivo.
			
		"RAWINPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/rawgameevents/",
		"PARTITIONEDOUTPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/partitionedgameevents/",

3. Repita a etapa para criar os seguintes pipelines:
	1. **EnrichGameLogsPipeline**. JSON (3 ocorrências)
	2. **AnalyzeMarketingCampaignPipeline**. JSON (3 ocorrências)

	**Importante:** confirmar que ter substituído todos <storageaccountname> com o nome da sua conta de armazenamento.
 
4.  Em **PowerShell do Azure**, navegue até o **Pipelines** subpasta em **C:\ADFWalkthrough** pasta (ou do local de onde você extraiu os exemplos).
5.  Use o cmdlet **AzureDataFactoryPipeline novo** criar os Pipelines da seguinte maneira para **PartitionGameLogspeline**. JSON	 
			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionGameLogsPipeline.json

	Se você estiver usando um nome diferente para o nome de ResourceGroupName, DataFactoryName ou Pipeline, consulte-o no cmdlet anterior. Além disso, forneça o caminho completo do arquivo de Pipeline JSON.
6. Repita a etapa anterior para criar os seguintes pipelines:
	1. **EnrichGameLogsPipeline**
			
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichGameLogsPipeline.json

	2. **AnalyzeMarketingCampaignPipeline**
				
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\AnalyzeMarketingCampaignPipeline.json

7. Use o cmdlet **Get-AzureDataFactoryPipeline** para obter a lista dos Pipelines.
			
		Get-AzureDataFactoryPipeline –ResourceGroupName ADF –DataFactoryName $df

8. Depois que as pipelines são criadas, você pode especificar a duração em que o processamento de dados ocorrerá. Especificando o período ativo de um pipeline, você está definindo a duração de tempo em que as fatias de dados serão processadas com base nas propriedades de Disponibilidade que foram definidas para cada tabela do ADF.

Para especificar o período ativo para o pipeline, você pode usar o cmdlet Set-AzureDataFactoryPipelineActivePeriod. Neste passo a passo, os dados de exemplo são de 05/01 05/05. Use 2014-05-01 como o StartDateTime. EndDateTime é opcional.
			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name PartitionGameLogsPipeline
  
9. Confirme para definir o período ativo para o pipeline.
			
			Confirm
			Are you sure you want to set pipeline 'PartitionGameLogsPipeline' active period from '05/01/2014 00:00:00' to '05/05/2014 00:00:00'?
			[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): n

10. Repita as duas etapas anteriores para definir o período ativo para os seguintes pipelines.
	1. **EnrichGameLogsPipeline**
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z –EndDateTime 2014-05-05Z –Name EnrichGameLogsPipeline

	2. **AnalyzeMarketingCampaignPipeline**
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name AnalyzeMarketingCampaignPipeline

11. No **Azure Preview Portal**, clique em **Pipelines** lado a lado (não nos nomes dos pipelines) no **DATA FACTORY** lâmina para o **LogProcessingFactory**, você deve ver os pipelines que você criou.

	![Todos os canais][image-data-factory-tutorial-pipelines-all]

12. No **DATA FACTORY** lâmina para o **LogProcessingFactory**, clique em **diagrama**.

	![Link do diagrama][image-data-factory-tutorial-diagram-link]

13. Você pode reorganizar o diagrama que você vê, e aqui está um diagrama reorganizado mostrando entradas diretas na parte superior e saídas na parte inferior. Você pode ver que a saída do **PartitionGameLogsPipeline** é passado como entrada para o EnrichGameLogsPipeline e a saída do **EnrichGameLogsPipeline** é passado para o **AnalyzeMarketingCampaignPipeline**. Clique duas vezes em um título para ver detalhes sobre o artefato que a folha representa.

	![Exibição de diagrama][image-data-factory-tutorial-diagram-view]

	**Parabéns!** Você criou com êxito o Data Factory do Azure, os Serviços Vinculados, Pipelines, Tabelas e iniciou o fluxo de trabalho.


## <a name="MainStep6"></a> Etapa 6: Monitorar fatias pipelines e dados 

1.	Se você não tiver a folha DATA FACTORY para abrir o LogProcessingFactory, poderá fazer o seguinte:
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
 
7. Agora, sob o **PIPELINE** lâmina para **PartiionGameLogsPipeline**, clique em **Produced**.
8. Você deverá ver a lista de conjuntos de dados que esse pipeline produz: 
9. Clique em **PartitionedGameEvents** na tabela de **produzido datasets** blade. 
10.	Confirme se o **status** de todas as fatias é definido como **pronto**. 
11.	Clique em uma das fatias é **pronto** para ver o **FATIA de dados** lâmina para essas fatias.

	![A FATIA de dados RawGameEventsTable lâmina][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	Se houver um erro, você verá um **Falha **status aqui. Você também pode encontrar qualquer um dos dois fatias com status **pronto**, ou ambos, com status **PendingValidation**, dependendo da rapidez as fatias são processadas.
 
	Consulte o [referência do desenvolvedor do Azure dados fábrica][developer-reference] para obter uma compreensão de todos os status possíveis fatia.

12.	No **FATIA de dados** lâmina, clique em Executar do **atividade executa** lista. Você deve ver a folha Execução de Atividade dessa fatia. Você verá o seguinte **detalhes de execução da atividade** lâmina.

	![Blade de detalhes da execução de atividade][image-data-factory-monitoring-activity-run-details]

13.	Clique em **baixar** para baixar os arquivos. Esta tela é especialmente útil quando você estiver solucionando erros de processamento do HDInsight.
	 
	
Quando concluir todo o pipeline de execução, você pode examinar o **MarketingCampaignEffectivenessTable** no **MarketingCampaigns** banco de dados do SQL Azure para exibir os resultados.

**Parabéns!** Agora você pode monitorar e solucionar problemas de fluxos de trabalho. Você aprendeu a usar o Data Factory do Azure para processar dados e obter a análise.

## Estender o tutorial para usar dados locais
Na última etapa do cenário do passo a passo neste artigo de processamento de log, a saída de eficácia de campanha marketing foi copiada para um banco de dados do SQL Azure. Você também pode mover esses dados para o SQL Server local para análise dentro da sua organização.
 
Para copiar dados de eficácia de campanha de marketing de BLOBs do Azure para SQL Server local, você precisa criar serviço vinculado locais adicionais, tabela e Pipeline introduziram no passo a passo neste artigo.

Prática de [passo a passo: usando o fonte de dados no local][tutorial-onpremises-using-powershell] para aprender a criar um pipeline para copiar dados de eficácia de campanha de marketing para um banco de dados do SQL Server local.
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[adftutorial-using-editor]: data-factory-tutorial.md

[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-use-onpremises-datasources.md
[usepigandhive]: data-factory-pig-hive-activities.md
[tutorial-onpremises-using-powershell]: data-factory-tutorial-extend-onpremises-using-powershell.md
[download-azure-powershell]: powershell-install-configure.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx


[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908


[image-data-factory-tutorial-end-to-end-flow]: ./media/data-factory-tutorial-using-powershell/EndToEndWorkflow.png

[image-data-factory-tutorial-partition-game-logs-pipeline]: ./media/data-factory-tutorial-using-powershell/PartitionGameLogsPipeline.png

[image-data-factory-tutorial-enrich-game-logs-pipeline]: ./media/data-factory-tutorial-using-powershell/EnrichGameLogsPipeline.png

[image-data-factory-tutorial-analyze-marketing-campaign-pipeline]: ./media/data-factory-tutorial-using-powershell/AnalyzeMarketingCampaignPipeline.png


[image-data-factory-tutorial-egress-to-onprem-pipeline]: ./media/data-factory-tutorial-using-powershell/EgreeDataToOnPremPipeline.png

[image-data-factory-tutorial-set-firewall-rules-azure-db]: ./media/data-factory-tutorial-using-powershell/SetFirewallRuleForAzureDatabase.png

[image-data-factory-tutorial-portal-new-everything]: ./media/data-factory-tutorial-using-powershell/PortalNewEverything.png

[image-data-factory-tutorial-datastorage-cache-backup]: ./media/data-factory-tutorial-using-powershell/DataStorageCacheBackup.png

[image-data-factory-tutorial-dataservices-blade]: ./media/data-factory-tutorial-using-powershell/DataServicesBlade.png

[image-data-factory-tutorial-new-datafactory-blade]: ./media/data-factory-tutorial-using-powershell/NewDataFactoryBlade.png

[image-data-factory-tutorial-resourcegroup-blade]: ./media/data-factory-tutorial-using-powershell/ResourceGroupBlade.png

[image-data-factory-tutorial-create-resourcegroup]: ./media/data-factory-tutorial-using-powershell/CreateResourceGroup.png

[image-data-factory-tutorial-datafactory-homepage]: ./media/data-factory-tutorial-using-powershell/DataFactoryHomePage.png

[image-data-factory-tutorial-create-datafactory]: ./media/data-factory-tutorial-using-powershell/CreateDataFactory.png

[image-data-factory-tutorial-linkedservice-tile]: ./media/data-factory-tutorial-using-powershell/LinkedServiceTile.png

[image-data-factory-tutorial-linkedservices-add-datstore]: ./media/data-factory-tutorial-using-powershell/LinkedServicesAddDataStore.png

[image-data-factory-tutorial-datastoretype-azurestorage]: ./media/data-factory-tutorial-using-powershell/DataStoreTypeAzureStorageAccount.png

[image-data-factory-tutorial-azurestorage-settings]: ./media/data-factory-tutorial-using-powershell/AzureStorageSettings.png

[image-data-factory-tutorial-storage-key]: ./media/data-factory-tutorial-using-powershell/StorageKeyFromAzurePortal.png

[image-data-factory-tutorial-linkedservices-blade-storage]: ./media/data-factory-tutorial-using-powershell/LinkedServicesBladeWithAzureStorage.png

[image-data-factory-tutorial-azuresql-settings]: ./media/data-factory-tutorial-using-powershell/AzureSQLDatabaseSettings.png

[image-data-factory-tutorial-azuresql-database-connection-string]: ./media/data-factory-tutorial-using-powershell/DatabaseConnectionString.png

[image-data-factory-tutorial-linkedservices-all]: ./media/data-factory-tutorial-using-powershell/LinkedServicesAll.png

[image-data-factory-tutorial-datasets-all]: ./media/data-factory-tutorial-using-powershell/DataSetsAllTables.png

[image-data-factory-tutorial-pipelines-all]: ./media/data-factory-tutorial-using-powershell/AllPipelines.png

[image-data-factory-tutorial-diagram-link]: ./media/data-factory-tutorial-using-powershell/DataFactoryDiagramLink.png

[image-data-factory-tutorial-diagram-view]: ./media/data-factory-tutorial-using-powershell/DiagramView.png

[image-data-factory-monitoring-startboard]: ./media/data-factory-tutorial-using-powershell/MonitoringStartBoard.png

[image-data-factory-monitoring-hub-everything]: ./media/data-factory-tutorial-using-powershell/MonitoringHubEverything.png

[image-data-factory-monitoring-browse-datafactories]: ./media/data-factory-tutorial-using-powershell/MonitoringBrowseDataFactories.png

[image-data-factory-monitoring-pipeline-consumed-produced]: ./media/data-factory-tutorial-using-powershell/MonitoringPipelineConsumedProduced.png

[image-data-factory-monitoring-raw-game-events-table]: ./media/data-factory-tutorial-using-powershell/MonitoringRawGameEventsTable.png

[image-data-factory-monitoring-raw-game-events-table-dataslice-blade]: ./media/data-factory-tutorial-using-powershell/MonitoringPartitionGameEventsTableDataSliceBlade.png

[image-data-factory-monitoring-activity-run-details]: ./media/data-factory-tutorial-using-powershell/MonitoringActivityRunDetails.png

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial-using-powershell/DataManagementGatewayConfigurationManager.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-tutorial-using-powershell/NewDataFactoryMenu.png

[image-data-factory-new-datafactory-create-button]: ./media/data-factory-tutorial-using-powershell/DataFactoryCreateButton.png

<!---HONumber=GIT-SubDir-->