<properties 
	pageTitle="Copiar dados de saída do tutorial para um banco de dados SQL Server local" 
	description="O passo a passo neste tutorial estende o tutorial do data factory para copiar dados de eficácia de campanha de marketing para um banco de dados SQL Server local."
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
	ms.date="07/07/2015" 
	ms.author="spelluru"/>


# Passo a passo: copiar dados de eficácia da campanha para um banco de dados do SQL Server local
Neste passo a passo, você aprenderá a configurar o ambiente para habilitar o pipeline para trabalhar com dados locais.
 
Na última etapa do cenário de processamento de log do primeiro passo a passo com Partição -> Enriquecer -> Analisar fluxo de trabalho, a saída de eficácia de campanha marketing foi copiada para um banco de dados SQL do Azure. Você também pode mover esses dados para o SQL Server local para análise dentro da sua organização.
 
Para copiar dados de eficácia de campanha de marketing do Blob do Azure para o SQL Server local, você precisa criar Serviço Vinculado local, Tabela e Pipeline adicionais usando o mesmo conjunto de cmdlets introduzidos no primeiro passo a passo.

## Pré-requisitos

Você **deve** executar o passo a passo no [Tutorial: Mover e processar arquivos de log usando Data Factory][datafactorytutorial] antes de executar o passo a passo deste artigo.

**(recomendado)** Examine e pratique o passo a passo no artigo [Habilitar seus pipelines para trabalhar com dados locais][useonpremisesdatasources] para ver um passo a passo sobre como criar um pipeline para mover dados do SQL Server local para um armazenamento de blob do Azure.


Neste tutorial, você realizará as seguintes etapas:

1. [Etapa 1: criar um gateway de gerenciamento de dados](#OnPremStep1). O Gateway de Gerenciamento de Dados é um agente cliente que fornece acesso a fontes de dados locais de sua organização na nuvem. O gateway permite que a transferência de dados entre um SQL Server local e armazenamentos de dados do Azure.	

	Você deve ter pelo menos um gateway instalado no seu ambiente corporativo, bem como registrá-lo com o Data Factory do Azure antes de adicionar o banco de dados SQL Server local como um serviço vinculado a um data factory do Azure.

2. [Etapa 2: criar um serviço vinculado para o SQL Server local](#OnPremStep2). Nesta etapa, você primeiro cria um banco de dados e uma tabela no computador local do SQL Server e, em seguida, cria o serviço vinculado: **OnPremSqlLinkedService**.
3. [Etapa 3: criar tabela e pipeline](#OnPremStep3). Nesta etapa, você criará uma tabela **MarketingCampaignEffectivenessOnPremSQLTable** e um pipeline **EgressDataToOnPremPipeline**. 

4. [Etapa 4: monitorar o pipeline e exibir o resultado](#OnPremStep4). Nesta etapa, você monitorará as fatias de dados, tabelas e pipelines usando o Portal do Azure.


## <a name="OnPremStep1"></a> Etapa 1: criar um gateway de gerenciamento de dados

O Gateway de Gerenciamento de Dados é um agente cliente que fornece acesso a fontes de dados locais de sua organização na nuvem. O gateway permite que a transferência de dados entre um SQL Server local e armazenamentos de dados do Azure.
  
Você deve ter pelo menos um gateway instalado no seu ambiente corporativo, bem como registrá-lo com o Data Factory do Azure antes de adicionar o banco de dados SQL Server local como um serviço vinculado a um data factory do Azure.

Se você tiver um gateway de dados existente que você possa usar, ignore esta etapa.

1.	Crie um gateway de dados lógicos. No **Portal de Visualização do Azure**, clique em **Serviços vinculados** na folha **DATA FACTORY**.
2.	Clique em **Adicionar (+) Gateway de Dados** na barra de comandos.  
3.	Na folha **Novo gateway de dados**, clique em **CRIAR**.
4.	Na folha **Criar**, insira **MyGateway** para o **nome** do gateway de dados.
5.	Clique em **ESCOLHER UMA REGIÃO** e altere-a se necessário. 
6.	Clique em **OK** na folha **Criar**. 
7.	Você deve ver a folha **Configurar**. 
8.	Na folha **Configurar**, clique em **Instalar diretamente neste computador**. Isso vai baixar, instalar e configurar o gateway em seu computador e registrar o gateway com o serviço. Se você tiver um gateway existente instalado no computador que você deseja vincular a esse gateway lógico no portal, use a chave nessa folha para registrar novamente o gateway usando a ferramenta Gerenciador de Configuração de Gateway de gerenciamento de dados (visualização).

	![Gerenciador de Configuração de Gateway de Gerenciamento de Dados][image-data-factory-datamanagementgateway-configuration-manager]

9. Clique em **OK** para fechar a folha **Configurar** e em **OK** para fechar a folha **Criar**. Aguarde até que o status de **MeuGateway** na folha **Serviços Vinculados** mude para **BOM**. Você também pode iniciar a ferramenta **Gerenciador de Configuração de Gateway de gerenciamento de dados (visualização)** para confirmar se o nome do gateway corresponde ao nome no portal e o **status** é **Registrado**. Você terá que fechar e reabrir a folha Serviços Vinculados para ver o status mais recente. Pode levar alguns minutos antes que a tela seja atualizada com o status mais recente.

## <a name="OnPremStep2"></a> Etapa 2: criar um serviço vinculado para o SQL Server local

Nesta etapa, você primeiro cria o banco de dados e a tabela necessários no computador local do SQL Server e, em seguida, cria o serviço vinculado.

### Preparar tabela e banco de dados local

Para começar, você precisa criar o banco de dados SQL Server, a tabela, os tipos definidos pelo usuário e procedimentos armazenados. Esses serão usados para mover os resultados **MarketingCampaignEffectiveness** do blob do Azure para o banco de dados SQL Server.

1.	No **Windows Explorer**, navegue até a subpasta **OnPremises** na pasta **C:\\ADFWalkthrough** (ou o local no qual você extraiu os exemplos).
2.	Abra **prepareOnPremDatabase&Table.ps1** em seu editor favorito, substitua a parte destacada pelas informações do SQL Server e salve o arquivo (forneça os detalhes de **autenticação do SQL**). Para o tutorial, habilite a autenticação do SQL para o banco de dados. 
			
		$dbServerName = "<servername>"
		$dbUserName = "<username>"
		$dbPassword = "<password>"

3. No **Azure PowerShell**, navegue até a pasta **C:\\ADFWalkthrough\\OnPremises**.
4.	Execute **prepareOnPremDatabase&Table.ps1** **(entre aspas duplas ou conforme mostrado abaixo)**.
			
		& '.\prepareOnPremDatabase&Table.ps1'

5. Depois que o script for executado com êxito, você verá o seguinte:
			
		PS E:\ADF\ADFWalkthrough\OnPremises> & '.\prepareOnPremDatabase&Table.ps1'
		6/10/2014 10:12:33 PM Script to create sample on-premises SQL Server Database and Table
		6/10/2014 10:12:33 PM Creating the database [MarketingCampaigns], table and stored procedure on [.]...
		6/10/2014 10:12:33 PM Connecting as user [sa]
		6/10/2014 10:12:33 PM Summary:
		6/10/2014 10:12:33 PM 1. Database 'MarketingCampaigns' created.
		6/10/2014 10:12:33 PM 2. 'MarketingCampaignEffectiveness' table and stored procedure 


### Criar o serviço vinculado

1.	No **Portal de Visualização do Azure**, clique no bloco **Serviços Vinculados** na folha **DATA FACTORY** de **LogProcessingFactory**.
2.	Na folha **Serviços Vinculados**, clique em **Adicionar (+) armazenamento de dados**.
3.	Na folha **Novo armazenamento de dados**, insira **OnPremSqlLinkedService** como o **Nome**. 
4.	Clique em **Tipo (Configurações necessárias)** e selecione **SQL Server**. Agora você deve ver as configurações de **GATEWAY DE DADOS**, **Servidor**, **Banco de dados** e **CREDENCIAIS** na folha **Novo armazenamento de dados**. 
5.	Clique em **GATEWAY DE DADOS (definir configurações necessárias)** e selecione o **MyGateway** criado anteriormente. 
6.	Insira o **nome** do servidor de banco de dados que hospeda o banco de dados **MarketingCampaigns**. 
7.	Insira **MarketingCampaigns** para o Banco de dados. 
8.	Clique em **CREDENCIAIS**. 
9.	Na folha **Credenciais**, clique em **Clique aqui para definir credenciais com segurança**.
10.	Ela instala um aplicativo em um único clique pela primeira vez e inicia a caixa de diálogo **Definindo Credenciais**.
11.	Na caixa de diálogo **Definindo Credenciais**, insira **Nome de Usuário** e **Senha**, e clique em **OK**. Aguarde até que a caixa de diálogo seja fechada. 
12.	Clique em **OK** na folha **Novo armazenamento de dados**. 
13.	Na folha **Serviços Vinculados**, confirme se **OnPremSqlLinkedService** aparece na lista e o **status** do serviço vinculado é **Bom**.

## <a name="OnPremStep3"></a> Etapa 3: criar tabela e pipeline

### Criar a tabela lógica local

1.	No **PowerShell do Azure**, alterne para a pasta **C:\\ADFWalkthrough\\OnPremises**. 
2.	Use o cmdlet **New-AzureDataFactoryTable** para criar as Tabelas da seguinte maneira para **MarketingCampaignEffectivenessOnPremSQLTable.json**.

			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessOnPremSQLTable.json
	 
#### Criar o pipeline para copiar os dados do Blob do Azure para o SQL Server

1.	Use o cmdlet **New-AzureDataFactoryPipeline** para criar o Pipeline da seguinte maneira para **EgressDataToOnPremPipeline.json**.

			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\EgressDataToOnPremPipeline.json
	 
2. Use o cmdlet **Set-AzureDataFactoryPipelineActivePeriod** para especificar o período ativo de **EgressDataToOnPremPipeline**.

			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name EgressDataToOnPremPipeline

	Pressione **‘Y’** para continuar.
	
## <a name="OnPremStep4"></a> Etapa 4: monitorar o pipeline e exibir o resultado

Agora você pode usar as mesmas etapas introduzidas na [Etapa 6: Monitoramento de tabelas e pipelines](#MainStep6) para monitorar o novo pipeline e as fatias de dados para a nova tabela ADF local.
 
Quando você ver o status de uma fatia da tabela **MarketingCampaignEffectivenessOnPremSQLTable** mudar para Pronto, isso significa que o pipeline concluiu a execução da fatia. Para exibir os resultados, consulte a tabela **MarketingCampaignEffectiveness** no banco de dados **MarketingCampaigns** no seu SQL Server.
 
Parabéns! Você verificou com êxito o passo a passo para usar sua fonte de dados local.
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[datafactorytutorial]: data-factory-tutorial-using-powershell.md
[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-use-onpremises-datasources.md
[usepigandhive]: data-factory-pig-hive-activities.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx

[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial-extend-onpremises-using-powershell/DataManagementGatewayConfigurationManager.png

 

<!---HONumber=August15_HO6-->