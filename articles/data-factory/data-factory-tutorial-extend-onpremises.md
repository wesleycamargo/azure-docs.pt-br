<properties 
	pageTitle="Copiar dados de saída para um banco de dados do SQL Server local (Portal do Azure)" 
	description="Este passo a passo estende o tutorial sobre como usar o Data Factory Editor, de modo que o pipeline copie dados de saída para um banco de dados do SQL Server."
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
	ms.date="11/12/2015" 
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

1.	Crie um gateway de dados lógicos. No **Portal de Visualização do Azure**, clique em **Serviços Vinculados** na folha **DATA FACTORY** para o seu Data Factory.
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

1.	No **Portal de Visualização do Azure**, clique no bloco **Criar e Implantar** na folha **DATA FACTORY** de **LogProcessingFactory**.
2.	No **Editor do Data Factory**, clique em **Novo armazenamento de dados** na barra de ferramentas e selecione **Banco de dados SQL Server local**.
3.	No script JSON, faça o seguinte: 
	1.	Substitua **<servername>** pelo nome do servidor que hospeda seu banco de dados SQL Server.
	2.	Substitua **<databasename>** por **MarketingCampaigns**.
	3.	Se você estiver usando **autenticação SQL**
		1.	Especifique **<username>** e **<password>** na **connectionString**.
		2.	Remova as duas últimas linhas (as propriedades **username** e **password** do JSON são necessárias apenas se você estiver usando Autenticação do Windows). 
		3.	Remova **, (vírgula) ** no final da linha **gatewayName**.

		**Se você está usando a Autenticação do Windows:** 1. Defina o valor de **Segurança Integrada** para **True** na **connectionString**. Remova "**User ID=<username>;Password=<password>;**" da connectionString. 2. Especifique o nome do usuário que tem acesso ao banco de dados para a propriedade **nome de usuário**. 3. Especifique **senha** para a conta de usuário.   
	4. Especifique o nome do gateway (**MeuGateway**) para a propriedade gatewayName. 		  	 
3.	Clique em **Implantar** na barra de ferramentas para implantar o serviço vinculado. 

## <a name="OnPremStep3"></a> Etapa 3: criar tabela e pipeline

### Criar a tabela lógica local

1.	No **Editor do Data Factory**, clique em **Novo conjunto de dados** na barra de ferramentas e selecione **SQL local**. 
2. Substitua JSON no painel direito pelo script JSON do arquivo **MarketingCampaignEffectivenessOnPremSQLTable.json** contido na pasta **C:\\ADFWalkthrough\\OnPremises**.
3. Altere o nome de serviço vinculado (propriedade **linkedServiceName**) de **OnPremSqlServerLinkedService** para **SqlServerLinkedService**.
4. Clique em **Implantar** na barra de ferramentas para implantar a tabela. 
	 
#### Criar o pipeline para copiar os dados do Blob do Azure para o SQL Server

1.	1. No **Editor do Data Factory**, clique no botão **Novo pipeline** na barra de ferramentas. Clique em **... (Elipse)** na barra de ferramentas se você não enxergar o botão. Como alternativa, você pode clicar com o botão direito em **Pipelines** no modo de exibição de árvore e clicar em **Novo pipeline**.
2. Substitua JSON no painel direito pelo script JSON do arquivo **EgressDataToOnPremPipeline.json** contido na pasta **C:\\ADFWalkthrough\\OnPremises**.
3. Adicione uma **vírgula (,)** no final da **colchete de fechamento (])** em JSON e, em seguida, adicione as três linhas a seguir após o colchete de fechamento. 

        "start": "2014-05-01T00:00:00Z",
        "end": "2014-05-05T00:00:00Z",
        "isPaused": false

	Observe que os momentos de **início** e **término** são definidos como 01/05/2014 e 05/05/2014, porque os dados de exemplo neste passo a passo são de 01/05/2014 a 05/05/2014.
 
3. Clique em **Implantar** na barra de ferramentas para implantar o pipeline. Confirme que você vê a mensagem **PIPELINE CRIADO COM ÊXITO** na barra de título do Editor.
	
## <a name="OnPremStep4"></a> Etapa 4: monitorar o pipeline e exibir o resultado

Agora você pode usar as mesmas etapas introduzidas na seção **Monitorar pipelines e fatias de dados** do [Tutorial principal][datafactorytutorial] para monitorar o novo pipeline e as frações de dados para a nova tabela ADF local.
 
Quando você ver o status de uma fatia da tabela **MarketingCampaignEffectivenessOnPremSQLTable** mudar para Pronto, isso significa que o pipeline concluiu a execução da fatia. Para exibir os resultados, consulte a tabela **MarketingCampaignEffectiveness** no banco de dados **MarketingCampaigns** no seu SQL Server.
 
Parabéns! Você verificou com êxito o passo a passo para usar sua fonte de dados local.
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[datafactorytutorial]: data-factory-tutorial.md
[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-move-data-between-onprem-and-cloud.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx

[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial-extend-onpremises/DataManagementGatewayConfigurationManager.png

 

<!---HONumber=Nov15_HO4-->