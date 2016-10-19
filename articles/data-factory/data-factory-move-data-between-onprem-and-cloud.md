<properties 
	pageTitle="Mover dados - Gateway de Gerenciamento de Dados | Microsoft Azure"
	description="Configure um gateway de dados para mover dados entre o local e a nuvem. Use o Gateway de Gerenciamento de Dados no Azure Data Factory para mover os dados." 
    keywords="gateway de dados, integração de dados, mover dados, credenciais de gateway"
	services="data-factory" 
	documentationCenter="" 
	authors="linda33wj" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/12/2016" 
	ms.author="jingwang"/>

# Mover dados entre fontes locais e a nuvem com o Gateway de Gerenciamento de Dados
Este artigo fornece uma visão geral da integração de dados entre os armazenamentos de dados locais e os armazenamentos de dados na nuvem usando o Data Factory. Ele se baseia no artigo [Atividades de Movimentação de Dados](data-factory-data-movement-activities.md) e em outros artigos de conceitos principais de data factory: [conjuntos de dados](data-factory-create-datasets.md) e [pipelines](data-factory-create-pipelines.md).

## Gateway de gerenciamento de dados
Você deve instalar o Gateway de Gerenciamento de Dados em seu computador local para habilitar a movimentação de dados de/para um armazenamento de dados local. O gateway pode ser instalado no mesmo computador que o armazenamento de dados ou em outro computador, desde que o gateway possa se conectar com o armazenamento de dados. Confira o artigo [Data Management Gateway](data-factory-data-management-gateway.md) (Gateway de Gerenciamento de Dados) para obter todos os detalhes sobre o Gateway de Gerenciamento de Dados.

O passo a passo a seguir mostra como você pode criar uma Data Factory com um pipeline que move dados de um banco de dados local do SQL Server para um blob do Azure. Como parte do passo a passo, você instala e configura o Gateway de Gerenciamento de Dados em seu computador.

## Passo a passo: copiar os dados locais para a nuvem
  
## Criar um data factory
Nesta etapa, você usa o Portal do Azure para criar uma instância do Azure Data Factory chamada **ADFTutorialOnPremDF**. Você também pode criar um data factory usando os cmdlets do Data Factory do Azure.

1.	Depois de fazer logon no [portal do Azure](https://portal.azure.com), clique em **NOVO** no canto inferior esquerdo, selecione **Análises de dados** na folha **Criar** e clique em **Data Factory** na folha **Análises de dados**.

	![Novo -> DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)
  
6. Na folha **Nova data factory**:
	1. Insira **ADFTutorialOnPremDF** como o **nome**.
	2. Clique em **NOME DE GRUPO DE RECURSOS** e selecione **ADFTutorialResourceGroup**. Você pode selecionar um grupo de recursos existente ou criar um. Para criar um grupo de recursos:
		1. Clique em **Criar um novo grupo de recursos**.
		2. Na **folha Criar grupo de recursos**, insira um **nome** para o grupo de recursos e clique em **OK**.

7. Observe que a opção **Adicionar ao Quadro Inicial** está marcada na folha **Nova data factory**.

	![Adicionar ao quadro inicial](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

8. Na folha **Nova data factory**, clique em **Criar**.

	O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro: **O nome da data factory “ADFTutorialOnPremDF” não está disponível**, altere o nome da data factory (por exemplo, yournameADFTutorialOnPremDF) e tente criá-la novamente. Use esse nome em vez de ADFTutorialOnPremDF ao executar as etapas restantes neste tutorial.

9. Procure as notificações do processo de criação clicando no botão **Notificações** na barra de título, conforme mostrado na imagem a seguir. Clique novamente para fechar a janela de notificações.

	![Hub de NOTIFICAÇÕES](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNotificationsHub.png)

11. Depois que a criação estiver concluída, você verá a folha **Data Factory** como mostrado na imagem abaixo:

	![Página inicial do Data Factory](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## Criar gateway
5. Na folha **DATA FACTORY**, clique no bloco **Criar e implantar** para iniciar o **Editor** da data factory.

	![Bloco Criar e implantar](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
6.	Em Editor do Data Factory, clique em **...(reticências)** na barra de ferramentas, e em seguida, clique em **Novo gateway de dados**.

	![Novo gateway de dados na barra de ferramentas](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
2. Na folha **Criar**, insira **adftutorialgateway** como **nome** e clique em **OK**.

	![Folha Criar Gateway](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

3. Na folha **Configurar**, clique em **Instalar diretamente neste computador**. Essa ação baixará o pacote de instalação para o gateway, instalará, configurará e registrará o gateway no computador.

	> [AZURE.NOTE] 
	Use o Internet Explorer ou um navegador da Web compatível com o Microsoft ClickOnce.
	> 
	> Se você estiver usando o Chrome, vá para a [loja na Web do Chrome](https://chrome.google.com/webstore/), pesquise a palavra-chave "ClickOnce", escolha uma das extensões do ClickOnce e instale-a.
	>  
	> Faça o mesmo para o Firefox (instalar o suplemento). Clique no botão **Abrir Menu** na barra de ferramentas (**três linhas horizontais** no canto superior direito), clique em **Complementos**, pesquise a palavra-chave "ClickOnce", escolha uma das extensões do ClickOnce e instale-a.

	![Folha Gateway - Configurar](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

	Essa é a maneira mais fácil (um clique) de baixar, instalar, configurar e registrar o gateway em uma única etapa. Você pode ver que o aplicativo **Gerenciador de Configuração de Gateway de gerenciamento de dados da Microsoft** está instalado no computador. Você também pode encontrar o executável **ConfigManager.exe** na pasta: **C:\\Arquivos de Programas\\Microsoft Data Management Gateway\\2.0\\Shared**.

	Você também pode baixar e instalar o gateway manualmente usando os links nesta folha e registrá-lo usando a chave mostrada na caixa de texto **NOVA CHAVE**.
	
	Confira o artigo [Data Management Gateway](data-factory-data-management-gateway.md) (Gateway de Gerenciamento de Dados) para obter todos os detalhes sobre o gateway.

	>[AZURE.NOTE] Você deve ser um administrador no computador local para instalar e configurar com êxito o Gateway de gerenciamento de dados. Você pode acrescentar usuários adicionais para o grupo local de usuários do Gateway de gerenciamento de dados do Windows. Os membros desse grupo podem usar a ferramenta Gerenciador de Configuração de Gateway de Gerenciamento de Dados para configurar o gateway.

5. Aguarde alguns minutos e inicie o aplicativo **Gerenciador de Configuração do Gateway de Gerenciamento de Dados** no computador. Na janela de **Pesquisa**, digite **Gateway de Gerenciamento de Dados** para acessar esse utilitário. Você também pode encontrar o executável **ConfigManager.exe** na pasta: **C:\\Arquivos de Programas\\Microsoft Data Management Gateway\\2.0\\Shared**

	![Gerenciador de configuração de gateway](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)

6. Aguarde:
	2. **Nome do gateway** está definido como **adftutorialgateway**.
	4. A barra de status inferior exibe a mensagem **Conectado ao serviço de nuvem** junto com uma **marca de seleção verde**.

	Na guia **Início**, você também pode fazer as seguintes operações: - **Registrar** um gateway com uma chave do Portal do Azure usando o botão Registrar. - **Parar** o Serviço de Host do Gateway de Gerenciamento de Dados em execução no computador do gateway. - **Agendar atualizações** para serem instaladas em uma hora específica do dia. - Exibir quando o gateway foi **atualizado pela última vez**.

8. Alterne para a guia **Configurações**. O certificado especificado na seção **Certificado** é usado para criptografar/descriptografar as credenciais do armazenamento de dados local que você especifica no portal. (opcional) Como alternativa, clique em **Alterar** para usar seu próprio certificado. Por padrão, o gateway usa o certificado que é gerado automaticamente pelo serviço de Data Factory.

	![Configuração do certificado do gateway](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

	Você também pode fazer as seguintes operações na guia Configurações: - Exibir ou exportar o certificado que está sendo usado pelo gateway. - Alterar o ponto de extremidade HTTPS usado pelo gateway -
9. (opcional) Mude para a guia **Diagnóstico** e marque a opção **Habilitar log detalhado** se você quiser habilitar o log detalhado que pode ser usado para solucionar problemas com o gateway. As informações de log podem ser encontradas no **Visualizador de Eventos** em **Logs de Aplicativos e Serviços** -> nó **Gateway de Gerenciamento de Dados**.

	![Guia Diagnósticos](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

	Você também pode executar as seguintes ações na guia **Diagnóstico**:
	
	- Use a seção **Testar Conexão** para uma fonte de dados local usando o gateway.
	- Clique em **Exibir Logs** para ver o log de Gateway de Gerenciamento de Dados em uma janela do Visualizador de Eventos.
	- Clique em **Enviar Logs** para carregar um arquivo zip com logs dos últimos sete dias para a Microsoft a fim de facilitar a solução de problemas.
10. No portal do Azure, clique em **OK** na folha **Configurar** e na folha **Novo gateway de dados**.
6. Você deve consultar **adftutorialgateway** em **Gateways de Dados** no modo de exibição de árvore à esquerda. Se você clicar nisso, verá o JSON associado.
	

## Criar serviços vinculados 
Nesta etapa, você cria dois serviços vinculados: **AzureStorageLinkedService** e **SqlServerLinkedService**. O **SqlServerLinkedService** vincula um banco de dados local SQL Server e o serviço vinculado **AzureStorageLinkedService** vincula um repositório de blob do Azure ao Data Factory. Você criará um pipeline posteriormente neste passo a passo que copia dados do banco de dados SQL Server local para o repositório de blob do Azure.

#### Adicionar um serviço vinculado a um banco de dados SQL Server local
1.	No **Editor do Data Factory**, clique em **Novo armazenamento de dados** na barra de ferramentas e selecione **SQL Server**.

	![Serviço vinculado do SQL Server](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
3.	No **Editor JSON**, siga estas etapas:
	1. Em **gatewayName**, especifique **adftutorialgateway**.
	2. Se você está usando a Autenticação do Windows:
		1. Em **connectionString**, defina a **Segurança Integrada** como **true**, especifique o banco de dados **nome do servidor** e **nome do banco de dados** e remova **ID de usuário** e **Senha**.
		3. Especifique o nome de usuário e senha para as propriedades **userName** e **password**.
		
				"typeProperties": {
            		"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
            		"gatewayName": "adftutorialgateway",
            		"userName": "<Specify user name if you are using Windows Authentication. Example: <domain>\<user>",
            		"password": "<Specify password for the user account>"
        		}

	4. Se você estiver usando a autenticação SQL:
		1. Especifique o **nome do servidor**, o **nome do banco de dados**, a **ID de usuário** e a **Senha** do banco de dados na **connectionString**.
		2. Remova as duas últimas propriedades JSON - **userName** e **password** - do JSON.
		3. Remova a `,` (vírgula) à direita** no final da linha que especifica o valor da propriedade gatewayName****.

				"typeProperties": {
            		"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
	           		"gatewayName": "<Name of the gateway that the Data Factory service should use to connect to the on-premises SQL Server database>"
    		    }
	
		As credenciais são **criptografadas** usando um certificado que o serviço Data Factory detém. Como alternativa, se você quiser usar o certificado associado ao Gateway de Gerenciamento de Dados, confira [Set credentials securely (Definir credenciais com segurança)](#set-credentials-and-security).
    
2.	Clique em **Implantar** na barra de comandos para implantar o serviço vinculado do SQL Server.

#### Adicionar um serviço vinculado para uma conta de armazenamento do Azure
 
1. No **Editor do Data Factory**, clique em **Novo armazenamento de dados** na barra de comandos e clique em **Armazenamento do Azure**.
2. Insira o nome da sua conta de armazenamento do Azure em **Nome da conta**.
3. Insira a chave da sua conta de armazenamento do Azure em **Chave de conta**.
4. Clique em **Implantar** para implantar o **AzureStorageLinkedService**.
   
 
## Criar conjuntos de dados
Nesta etapa, você cria conjuntos de dados de entrada e saída que representam dados de entrada e saída da operação de cópia (banco de dados SQL Server local = > armazenamento de blobs do Azure). Antes de criar conjuntos de dados ou tabelas (conjuntos de dados retangulares), você precisa fazer seguir estas etapas (etapas detalhadas seguem a lista):

- Criar uma tabela chamada **emp** no banco de dados SQL Server adicionado como um serviço vinculado à data factory e inserir alguns exemplos de entradas na tabela.
- Crie um contêiner de blob chamado **adftutorial** na conta de armazenamento de blob do Azure que você adicionou como um serviço vinculado à data factory.

### Preparar o SQL Server local para o tutorial

1. No banco de dados especificado para o serviço vinculado do SQL Server local (**SqlServerLinkedService**), use o seguinte script SQL para criar a tabela **emp** no banco de dados.


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL, 
			FirstName varchar(50),
			LastName varchar(50),
    		CONSTRAINT PK_emp PRIMARY KEY (ID)
		)
		GO
 

2. Insira algum exemplo na tabela:


        INSERT INTO emp VALUES ('John', 'Doe')
		INSERT INTO emp VALUES ('Jane', 'Doe')



### Criar tabela de entrada

1. No **Editor de Data Factory**, clique em **Novo conjunto de dados** na barra de comandos e clique na tabela **SQL Server**.
2.	Substitua o JSON no painel direito pelo texto a seguir:

		{
		  "name": "EmpOnPremSQLTable",
		  "properties": {
		    "type": "SqlServerTable",
		    "linkedServiceName": "SqlServerLinkedService",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    },
		    "policy": {
		      "externalData": {
		        "retryInterval": "00:01:00",
		        "retryTimeout": "00:10:00",
		        "maximumRetry": 3
		      }
		    }
		  }
		}

	Observe os seguintes pontos:
	
	- **type** é definido como **SqlServerTable**.
	- **tableName** está definido como **emp**.
	- **linkedServiceName** está definido como **SqlServerLinkedService** (você criou esse serviço vinculado na Etapa 2).
	- Para uma tabela de entrada que não é gerada por outro pipeline na Azure Data Factory, você deve configurar **external** como **true**. Ela indica que os dados de entrada são produzidos externos ao serviço Data Factory do Azure. Opcionalmente, você pode especificar quaisquer políticas de dados externas usando o elemento **externalData** na seção **Policy**.

	Consulte [Referência de script JSON][json-script-reference] para obter detalhes sobre as propriedades JSON.

2. Clique em **Implantar** na barra de comandos para implantar o conjunto de dados (a tabela é um conjunto de dados retangular). Confirme que você vê uma mensagem na barra de título que diz **TABELA IMPLANTADA COM ÊXITO**.


### Criar tabela de saída

1.	No **Editor Data Factory**, clique em **Novo conjunto de dados** na barra de comandos e clique em **Armazenamento de Blob do Azure**.
2.	Substitua o JSON no painel direito pelo texto a seguir:

		{
		  "name": "OutputBlobTable",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/outfromonpremdf",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}
  
	Observe os seguintes pontos:
	
	- **tipo** é definido como **AzureBlob**.
	- O **linkedServiceName** é definido como **AzureStorageLinkedService** (você criou esse serviço vinculado na Etapa 2).
	- **folderPath** é definido como **adftutorial/outfromonpremdf**, em que outfromonpremdf é a pasta no contêiner adftutorial. Crie o contêiner **adftutorial** se ele ainda não existir.
	- A **availability** é definida como **hourly** (**frequency** definida como **hour** e **interval** definido como **1**). O serviço Data Factory gera uma fatia de dados de saída a cada hora na tabela **emp** no banco de dados SQL do Azure.

	Se você não especificar um **fileName** para uma **tabela de entradas**, todos os arquivos/blobs da pasta de entrada (**folderPath**) serão considerados como entradas. Se você especificar um nome de arquivo em JSON, apenas arquivo/blob especificado será considerado como entrada de asn. Consulte os arquivos de exemplo no [tutorial][adf-tutorial] para ver exemplos.
 
	Se você não especificar um **fileName** em uma **tabela de saída**, os arquivos gerados no **folderPath** serão nomeados no seguinte formato: Data.<Guid>.txt (por exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	Para definir **folderPath** e **fileName** dinamicamente com base no horário **SliceStart**, use a propriedade partitionedBy. No exemplo a seguir, folderPath usa o ano, mês e dia de SliceStart (hora de início da fatia que está sendo processada) e fileName usa a hora de SliceStart. Por exemplo, se uma fatia é produzida para 2014-10-20T08:00:00, o folderName é definido como wikidatagateway/wikisampledataout/2014/10/20 e o fileName é definido como 08.csv.

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	Consulte [Referência de script JSON][json-script-reference] para obter detalhes sobre as propriedades JSON.

2.	Clique em **Implantar** na barra de comandos para implantar o conjunto de dados (a tabela é um conjunto de dados retangular). Confirme que você vê uma mensagem na barra de título que diz **TABELA IMPLANTADA COM ÊXITO**.
  

## Criar um pipeline
Nesta etapa, você criará um **pipeline** com uma **Atividade de Cópia** que usa **EmpOnPremSQLTable** como entrada e **OutputBlobTable** como saída.

1.	Na folha **DATA FACTORY**, clique no bloco **Criar e implantar** para iniciar o **Editor** da data factory.

	![Bloco Criar e implantar](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2.	Clique em **Novo pipeline** na barra de comandos. Se você não puder ver o botão, clique em **... (reticências)** para expandir a barra de comandos.
2.	Substitua o JSON no painel direito pelo texto a seguir:
	
			{
				"name": "ADFTutorialPipelineOnPrem",
				"properties": {
		    	"description": "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
		    	"activities": [
		      	{
		        	"name": "CopyFromSQLtoBlob",
			        "description": "Copy data from on-prem SQL server to blob",
		        	"type": "Copy",
		        	"inputs": [
		          	{
		            	"name": "EmpOnPremSQLTable"
		          	}
		        	],
		        	"outputs": [
		          	{
			            "name": "OutputBlobTable"
			          }
			        ],
			        "typeProperties": {
			          "source": {
			            "type": "SqlSource",
			            "sqlReaderQuery": "select * from emp"
			          },
			          "sink": {
			            "type": "BlobSink"
			          }
			        },
			        "Policy": {
			          "concurrency": 1,
			          "executionPriorityOrder": "NewestFirst",
			          "style": "StartOfInterval",
			          "retry": 0,
			          "timeout": "01:00:00"
			        }
			      }
			    ],
			    "start": "2016-07-05T00:00:00Z",
			    "end": "2016-07-06T00:00:00Z",
			    "isPaused": false
			  }
			}

	Observe os seguintes pontos:
 
	- Na seção de atividades, há somente uma atividade cujo **type** é definido como **Copy**.
	- A **entrada** da atividade é definida como **EmpOnPremSQLTable** e a **saída** da atividade é definida como **OutputBlobTable**.
	- Na seção **transformation**, o **SqlSource** é especificado como o **tipo de origem** e o **BlobSink** é especificado como o **tipo de coletor**.
	- A consulta SQL `select * from emp` é especificada para a propriedade **sqlReaderQuery** de **SqlSource**.

	Substitua o valor da propriedade **start** pelo dia atual e o valor de **end** pelo dia seguinte. Ambos os valores de data/hora de início e de término devem estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. A hora **final** é opcional, mas nós a usaremos neste tutorial.
	
	Se você não especificar o valor para a propriedade **end**, ele será calculado como "**início + 48 horas**". Para executar o pipeline indefinidamente, especifique **9/9/9999** como o valor da propriedade **end**.
	
	Especificando o período ativo de um pipeline, você está definindo a duração de tempo em que as fatias de dados serão processadas com base nas propriedades de **Disponibilidade** que foram definidas para cada tabela da Azure Data Factory.
	
	No exemplo, como cada fatia de dados é produzida por hora, existem 24 fatias de dados.

	
2. Clique em **Implantar** na barra de comandos para implantar o conjunto de dados (a tabela é um conjunto de dados retangular). Confirme que você vê uma mensagem na barra de título que diz **PIPELINE IMPLANTADO COM ÊXITO**.
5. Agora, feche a folha **Editor** clicando em **X**. Clique em **X** novamente para fechar a folha ADFTutorialDataFactory com o modo de exibição de árvore e a barra de ferramentas. Se você vir **suas edições não salvas serão descartadas**, clique em **OK**.
6. Você deve ser retornado à folha **DATA FACTORY** para o **ADFTutorialOnPremDF**.

**Parabéns!** Você criou um data factory do Azure, serviços vinculados, tabelas e uma pipeline e a pipeline agendada com êxito.

#### Exibir a data factory em um Modo de Exibição de Diagrama 
1. No **portal do Azure**, clique no bloco **Diagrama** na página inicial da data factory **ADFTutorialOnPremDF**:

	![Link do diagrama](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)

2. Você deverá ver o diagrama semelhante à imagem abaixo:

	![Exibição de diagrama](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

	É possível ampliar, reduzir, aplicar zoom de 100%, ajustar nível de zoom, posicionar pipelines e tabelas automaticamente, bem como mostrar informações de linhagem (realça itens upstream e downstream dos itens selecionados). Você pode clicar duas vezes em um objeto (pipeline ou tabela de entrada/saída) para ver as propriedades dele.

## Monitorar o pipeline
Nesta etapa, você utiliza o portal do Azure para monitorar o que está acontecendo em um data factory do Azure. Você também pode usar os cmdlets do PowerShell para monitorar conjuntos de dados e pipelines. Para obter detalhes sobre monitoramento, consulte [Monitorar e gerenciar pipelines](data-factory-monitor-manage-pipelines.md).

1. Navegue até o **portal do Azure** (se você o tiver fechado)
2. Se a folha para **ADFTutorialOnPremDF** não estiver aberta, abra-a clicando em **ADFTutorialOnPremDF** no **Quadro Inicial**.
3. Você verá a **contagem** e os **nomes** das tabelas e do pipeline criados nessa folha.

	![Página inicial do Data Factory](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)
4. Agora, clique no bloco **Conjuntos de dados**.
5. Na folha **Conjuntos de Dados**, clique em **EmpOnPremSQLTable**.

	![Fatias de EmpOnPremSQLTable](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)

6. Observe que as fatias de dados até o momento atual já foram produzidas e estão **Prontas**. Isso porque você inseriu os dados no banco de dados SQL Server e eles estão lá está o tempo todo. Confirme se nenhuma fatia aparecerá na seção **Fatias com problema** na parte inferior.
	
	Ambas as listas **Fatias atualizadas recentemente** e **Fatias com falha recente** são classificadas segundo o **HORÁRIO DA ÚLTIMA ATUALIZAÇÃO**. A hora de atualização de uma fatia é alterada nas situações a seguir.

	Você atualiza o status da fatia manualmente, por exemplo, usando o **Set-AzureRmDataFactorySliceStatus** (ou) clicando em **EXECUTAR** na folha **FATIA** da fatia.
	
	A fatia é alterada devido a uma execução (por exemplo, uma execução iniciada, uma execução finalizada e com falha, uma execução finalizada e bem-sucedida etc).

	Clique no título das listas ou em **... (reticências)** para ver a lista maior de fatias. Clique em **Filtrar** na barra de ferramentas para filtrar as fatias.

	Para exibir, em vez disso, as fatias de dados classificadas pelas horas de início/término da fatia, clique no bloco **Fatias de dados (por horário da fatia)**.
7. Agora, na folha **Conjuntos de Dados**, clique em **OutputBlobTable**.

	![Fatias de OputputBlobTable](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
8. Confirme se você vê as fatias até a hora atual estão produzidas e **Prontas**. Aguarde até que o status das fatias até a hora atual estar definido como **Prontas**.
9. Clique em qualquer fatia de dados na lista e você deverá ver a folha **FATIA DE DADOS**.

	![Folha Fatia de dados](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

	Quando a fatia não está no estado **Pronto**, você pode ver as fatias upstream que não estão Prontas e estão impedindo a execução da fatia atual na lista **Fatias upstream que não estão prontas**.

10. Clique na **execução de atividade** na lista na parte inferior para ver **detalhes de execução da atividade**.

	![Folha Detalhes da execução de atividade](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

11. Clique no **X** para fechar todas as folhas até
12. voltar para a folha inicial do **ADFTutorialOnPremDF**.
14. (opcional) Clique em **Pipelines**, clique em **ADFTutorialOnPremDF** e execute uma consulta drill-through nas tabelas de entrada (**Consumed**) ou nas tabelas de saída (**Produced**).
15. Use ferramentas como o **Explorador de Armazenamento do Azure** para verificar a saída.

	![Gerenciador de Armazenamento do Azure](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## Próximas etapas

- Confira o artigo [Data Management Gateway](data-factory-data-management-gateway.md) (Gateway de Gerenciamento de Dados) para obter todos os detalhes sobre o Gateway de Gerenciamento de Dados.
- Confira [Copiar dados do Blob do Azure para o SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para saber mais sobre como usar a Atividade de Cópia para mover dados de um repositório de dados de origem para um repositório de dados de coletor.

<!---HONumber=AcomDC_0928_2016-->