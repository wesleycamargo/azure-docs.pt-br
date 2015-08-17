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
	ms.date="06/04/2015" 
	ms.author="spelluru"/>

# Tutorial: Mover e processar arquivos de log usando o [PowerShell] do Data Factory
Este artigo fornece uma passo a passo completo de um cenário canônico de processamento de log usando o Data Factory do Azure para transformar dados de arquivos de log em informações.

## Cenário
A Contoso é uma empresa de jogos que cria os jogos para várias plataformas: PCs (computadores pessoais), dispositivos de mão e consoles de jogos. Cada um desses jogos produz toneladas de logs. O objetivo da Contoso é coletar e analisar os logs gerados por esses jogos para obter informações sobre o uso, identificar oportunidades de vendas e vendas cruzadas, desenvolver novos recursos atraentes etc. para melhorar os negócios e fornecer a melhor experiência para os clientes.
 
Neste passo a passo, podemos coletar logs de exemplo, processar e enriquecê-los com dados de referência, além de transformar os dados para avaliar a eficiência de uma campanha de marketing que a Contoso lançou recentemente.

## Preparando-se para o tutorial
1.	Leia a [Introdução ao Azure Data Factory][adfintroduction] para obter uma visão geral do Azure Data Factory e entender os conceitos de nível superior.
2.	Você deve ter uma assinatura do Azure para ver este tutorial. Para obter informações sobre como adquirir uma assinatura, consulte [Opções de compra][azure-purchase-options], [Ofertas para membros][azure-member-offers] ou [Avaliação gratuita][azure-free-trial].
3.	Você deve baixar e instalar o [PowerShell do Azure][download-azure-powershell] no seu computador. 
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
O fluxo de trabalho de ponta a ponta é descrito abaixo: ![Tutorial de fluxo de ponta a ponta][image-data-factory-tutorial-end-to-end-flow]

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

1.	Após fazer logon no [Portal de Visualização do Azure][azure-preview-portal], clique em **NOVO** no canto inferior esquerdo e clique em **Data Factory** na folha **Novo**. 

	![Novo -> DataFactory][image-data-factory-new-datafactory-menu]
	
	Se você não vir **Data Factory** na folha **Novo**, role para baixo.
	
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

> [AZURE.NOTE]Este artigo usa o PowerShell do Azure para criar pipelines, tabelas e serviços vinculados. Consulte [Tutorial usando Editor Data Factory][adftutorial-using-editor] se você deseja executar este tutorial usando o Portal do Azure, especificamente o Editor Data Factory.

Nesta etapa, você criará os seguintes serviços vinculados: StorageLinkedService, AzureSqlLinkedService, HDInsightStorageLinkedService e HDInsightLinkedService.


1.	Na folha **LogProcessingFactory**, clique no bloco **Serviços Vinculados**.

	![Bloco Serviços vinculados][image-data-factory-tutorial-linkedservice-tile]

2. Na folha **Serviços Vinculados**, clique em **+ Armazenamento de Dados** na barra de comandos.

	![Serviços vinculados - Adicionar armazenamento][image-data-factory-tutorial-linkedservices-add-datstore]

3. Na folha **Novo armazenamento de dados**, insira **StorageLinkedService** como o **Nome**, clique em **TIPO (configurações necessárias)** e selecione **Conta de armazenamento do Azure**.

	![Tipo de armazenamento de dados - Armazenamento do Azure][image-data-factory-tutorial-datastoretype-azurestorage]

4. Na folha **Novo armazenamento de dados**, você verá dois novos campos: **Nome da conta** e **Chave de conta**. Insira o nome da conta e a chave de conta da sua **Conta de Armazenamento do Azure**.

	![Configurações do Armazenamento do Azure][image-data-factory-tutorial-azurestorage-settings]

	Você pode obter o nome da conta e a chave de conta da sua conta de armazenamento do Azure no portal, conforme mostrado abaixo:

	![Chave de Armazenamento][image-data-factory-tutorial-storage-key]
  
5. Depois de clicar em **OK** na folha Novo armazenamento de dados, você verá **StorageLinkedService** na lista de **ARMAZENAMENTOS DE DADOS** na folha **Serviços Vinculados**. Verifique o hub **NOTIFICAÇÕES** (à esquerda) para ver quaisquer eventuais mensagens.

	![Folha Serviços vinculados com armazenamento][image-data-factory-tutorial-linkedservices-blade-storage]
   
6. Repita as **etapas 2-5** para criar outro serviço vinculado chamado: **HDInsightStorageLinkedService**. Esse é o armazenamento usado pelo seu cluster HDInsight.
7. Confirme que você vê **StorageLinkedService** e **HDInsightStorageLinkedService** na lista presente na folha Serviços Vinculados.
8. Na folha **Serviços Vinculados**, clique em **Adicionar (+) Armazenamento de Dados** na barra de comandos.
9. Insira **AzureSqlLinkedService** como o nome.
10. Clique em **TIPO (configurações necessárias)**, selecione **Banco de Dados SQL do Azure**.
11. Agora, você deve ver os seguintes campos adicionais na folha **Novo armazenamento de dados**. Insira o nome do **servidor** de Banco de Dados SQL do Azure SQL, nome do **banco de dados**, **nome de usuário** e **senha**, e clique em **OK**.
	1. Insira **MarketingCampaigns** para o **banco de dados**. Esse é o banco de dados SQL do Azure criado pelos scripts que você executou na Etapa 1. Você deve confirmar se esse banco de dados foi realmente criado pelos scripts (caso tenham ocorrido erros).
		
 		![Configurações do Azure SQL][image-data-factory-tutorial-azuresql-settings]

		Para obter esses valores no Portal de Gerenciamento do Azure: clique em Exibir cadeias de conexão de Banco de Dados SQL para banco de dados MarketingCampaigns

		![Cadeia de conexão de Banco de Dados SQL do Azure][image-data-factory-tutorial-azuresql-database-connection-string]

12. Confirme que você vê todos os três armazenamentos de dados que você criou: **StorageLinkedService**, **HDInsightStorageLinkedService** e **AzureSqlLinkedService**.
13. Você precisa criar outro serviço vinculado, mas esse é um serviço de computação, especificamente o **cluster HDInsight do Azure**. O portal ainda não da suporte à criação de um serviço de computação vinculada. Portanto, você precisa usar o PowerShell do Azure para criar esse serviço vinculado. 
14. Alterne para o **PowerShell do Azure** se você já o tiver aberto (ou) inicie o **PowerShell do Azure**. Se você tiver fechado e reaberto o PowerShell do Azure, você precisa executar os seguintes comandos: 
	- Execute **Add-AzureAccount** e insira o nome de usuário e senha que você usa para entrar no Portal de Visualização do Azure.  
	- Execute **Get-AzureSubscription** para exibir todas as assinaturas dessa conta.
	- Execute **Select-AzureSubscription** para selecionar a assinatura com a qual deseja trabalhar. Esta assinatura deve ser a mesma que você usou no Portal de Visualização do Azure. 
15. Alterne para o modo **AzureResourceManager**, pois os cmdlets da Azure Data Factory estão disponíveis nesse modo.

		Switch-AzureMode AzureResourceManager

16. Navegue até a subpasta **LinkedServices** em **C:\\ADFWalkthrough** ou na pasta do local onde você extraiu os arquivos.
17. Abra **HDInsightLinkedService.json** em seu editor favorito e observe que o tipo é definido como **HDInsightOnDemandLinkedService**.


	O serviço Data Factory do Azure dá suporte à criação de um cluster sob demanda e o usa para processar entrada a fim de gerar dados de saída. Você também pode usar seu próprio cluster para fazer isso. Quando você usa o cluster HDInsight sob demanda, um cluster é criado para cada fatia. Ao passo que, ao utilizar seu próprio cluster de HDInsight, o cluster está pronto para processar a fatia imediatamente. Portanto, quando você usar cluster sob demanda, não verá os dados de saída mais rapidamente que quando usa seu próprio cluster. Para fins de exemplo, vamos usar um cluster sob demanda.
	
	O HDInsightLinkedService vincula um cluster HDInsight sob demanda para a data factory. Para usar seu próprio cluster HDInsight, atualize a seção Propriedades do arquivo HDInsightLinkedService.json, conforme mostrado a seguir (substitua clustername, username e password com valores apropriados):
	
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
19. Use o cmdlet **New-AzureDataFactoryLinkedService** para criar um Serviço Vinculado conforme descrito a seguir. Comece com a conta de armazenamento:

		New-AzureDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightLinkedService.json
 
	Se você estiver usando um nome diferente para o nome de ResourceGroupName, DataFactoryName ou LinkedService, consulte-o no cmdlet anterior. Além disso, forneça o caminho completo do arquivo JSON de Serviço Vinculado se o arquivo não puder ser encontrado.
20. Você deve ver todos os quatro serviços vinculados na folha **Serviços Vinculados**, conforme mostrado abaixo. Se a folha de serviços vinculados não estiver aberta, clique em Serviços Vinculados na página **DATA FACTORY** de **LogProcessingFactory**. Pode levar alguns segundos até a folha de serviços vinculados seja atualizada.

	![Serviços vinculados Todos][image-data-factory-tutorial-linkedservices-all]
 

## <a name="MainStep4"></a> Etapa 4: Criar tabelas 
Nesta etapa, você criará as seguintes tabelas:

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Tutorial de fluxo de ponta a ponta][image-data-factory-tutorial-end-to-end-flow]
 
A figura acima exibe pipelines na linha intermediária e tabelas nas linhas superior e inferior.

O Portal do Azure não dá suporte à criação de conjuntos de dados e tabelas ainda, portanto, será necessário usar o PowerShell do Azure para criar tabelas nesta versão.

### Para criar as tabelas

1.	No PowerShell do Azure, navegue até a pasta **Tabelas** (**C:\\ADFWalkthrough\\Tables**) do local onde você extraiu os exemplos.
2.	Use o cmdlet **New-AzureDataFactoryTable** para criar as tabelas conforme descrito a seguir para **RawGameEventsTable**.json	


		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RawGameEventsTable.json

	Se você estiver usando um nome diferente para o nome de ResourceGroupName e DataFactoryName, consulte-o no cmdlet anterior. Além disso, forneça o caminho completo do arquivo JSON da tabela se o arquivo não puder ser encontrado pelo cmdlet.

3. Repita a etapa anterior para criar as tabelas a seguir:
		
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionedGameEventsTable.json
		
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RefGeoCodeDictionaryTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RefMarketingCampaignTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichedGameEventsTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessSQLTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessBlobTable.json



4. No **Portal de Visualização do Azure**, clique em **Conjuntos de Dados** na folha **DATA FACTORY** para **LogProcessingFactory** e confirme que você vê todos os conjuntos de dados (tabelas são conjuntos de dados retangulares).

	![Conjuntos de dados Todos][image-data-factory-tutorial-datasets-all]

	Você também pode usar o seguinte comando do PowerShell do Azure:
			
		Get-AzureDataFactoryTable –ResourceGroupName ADF –DataFactoryName $df

	


## <a name="MainStep5"></a> Etapa 5: Criar e agendar pipelines
Nesta etapa, você criará os seguintes pipelines: PartitionGameLogsPipeline, EnrichGameLogsPipeline e AnalyzeMarketingCampaignPipeline.

1. No **Windows Explorer**, navegue até a subpasta **Pipelines** na pasta **C:\\ADFWalkthrough** (ou do local onde você extraiu os exemplos).
2.	Abra **PartitionGameLogsPipeline.json** no seu editor favorito, substitua a parte destacada pela sua conta de armazenamento das informações de conta de armazenamento de dados e salve o arquivo.
			
		"RAWINPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/rawgameevents/",
		"PARTITIONEDOUTPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/partitionedgameevents/",

3. Repita a etapa para criar os seguintes pipelines:
	1. **EnrichGameLogsPipeline**.json (3 ocorrências)
	2. **AnalyzeMarketingCampaignPipeline**.json (3 ocorrências)

	**IMPORTANTE:** Confirme que você substituiu todas as <storageaccountname> pelo nome da sua conta de armazenamento.
 
4.  No **PowerShell do Azure**, navegue até a subpasta **Pipelines** na pasta **C:\\ADFWalkthrough** (ou do local onde você extraiu os exemplos).
5.  Use o cmdlet **New-AzureDataFactoryPipeline** para criar os Pipelines conforme descrito a seguir para **PartitionGameLogspeline**.json	 
			
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

Para especificar o período ativo para o pipeline, você pode usar o cmdlet Set-AzureDataFactoryPipelineActivePeriod. Neste passo a passo, os dados de exemplo são de 01/05 a 05/05. Use 2014-05-01 como o StartDateTime. EndDateTime é opcional.
			
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

11. No **Portal de Visualização do Azure**, clique no bloco **Pipelines** (não nos nomes dos pipelines) na folha **DATA FACTORY** para **LogProcessingFactory**, você deverá ver os pipelines criados.

	![Todos os Pipelines][image-data-factory-tutorial-pipelines-all]

12. Na folha **DATA FACTORY** de **LogProcessingFactory**, clique em **Diagrama**.

	![Link do diagrama][image-data-factory-tutorial-diagram-link]

13. Você pode reorganizar o diagrama que você vê, e aqui está um diagrama reorganizado mostrando entradas diretas na parte superior e saídas na parte inferior. Você pode ver que a saída de **PartitionGameLogsPipeline** é passada como entrada para EnrichGameLogsPipeline e a saída de **EnrichGameLogsPipeline** é passada para **AnalyzeMarketingCampaignPipeline**. Clique duas vezes em um título para ver detalhes sobre o artefato que a folha representa.

	![Exibição de diagrama][image-data-factory-tutorial-diagram-view]

	**Parabéns!** Você criou com êxito o Data Factory do Azure, os Serviços Vinculados, Pipelines, Tabelas e iniciou o fluxo de trabalho.


## <a name="MainStep6"></a> Etapa 6: Monitorar pipelines e fatias de dados 

1.	Se você não tiver a folha DATA FACTORY para abrir o LogProcessingFactory, poderá fazer o seguinte:
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
 
7. Agora, na folha **PIPELINE** para **PartiionGameLogsPipeline**, clique em **Produzido**.
8. Você deverá ver a lista de conjuntos de dados que esse pipeline produz: 
9. Clique na tabela **PartitionedGameEvents** na folha **Conjuntos de dados gerados**. 
10.	Confirme se o **status** de todas as fatias está definido como **Pronto**. 
11.	Clique em uma das fatias que está **Pronta** para ver a folha **FATIA DE DADOS** dessa fatia.

	![Folha FATIA DE DADOS RawGameEventsTable][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	Se houver um erro, você verá um status **Falha** aqui. Você também pode ver as duas fatias com status **Pronto** ou com status **PendingValidation**, dependendo da rapidez com que as fatias são processadas.
 
	Consulte a [Referência do Desenvolvedor do Azure Data Factory][developer-reference] para obter uma compreensão de todos os status possíveis da fatia.

12.	Na folha **FATIA DE DADOS**, clique na execução da lista **Execuções de Atividade**. Você deve ver a folha Execução de Atividade dessa fatia. Você deve ver a seguinte folha **DETALHES DE EXECUÇÃO DA ATIVIDADE**.

	![Folha Detalhes da execução de atividade][image-data-factory-monitoring-activity-run-details]

13.	Clique em **Download** para baixar os arquivos. Esta tela é especialmente útil quando você estiver solucionando erros de processamento do HDInsight.
	 
	
Quando todo o pipeline tiver concluído a execução, você poderá examinar **MarketingCampaignEffectivenessTable** no banco de dados SQL do Azure **MarketingCampaigns** para exibir os resultados.

**Parabéns!** Agora você pode monitorar e solucionar problemas de fluxos de trabalho. Você aprendeu a usar o Data Factory do Azure para processar dados e obter a análise.

## Estender o tutorial para usar dados locais
Na última etapa do cenário de processamento de log do passo a passo neste artigo, a saída de eficácia de campanha marketing foi copiada para um banco de dados SQL do Azure. Você também pode mover esses dados para o SQL Server local para análise dentro da sua organização.
 
Para copiar dados de eficácia de campanha de marketing do Blob do Azure para o SQL Server local, você precisa criar Serviço Vinculado local, Tabela e Pipeline adicionais introduzidos no passo a passo deste artigo.

Pratique o [Guia passo a passo: usando a fonte de dados local][tutorial-onpremises-using-powershell] para aprender a criar um pipeline para copiar dados de eficácia de campanha de marketing para um banco de dados do SQL Server local.
 

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
[download-azure-powershell]: ../powershell-install-configure.md

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

<!---HONumber=August15_HO6-->