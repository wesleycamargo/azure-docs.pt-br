<properties 
	pageTitle="Mover dados entre o local e a nuvem usando a Azure Data Factory" 
	description="Saiba mais sobre como mover dados entre locais e a nuvem usando o Gateway de Gerenciamento de Dados e a Azure Data Factory." 
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
	ms.date="05/18/2016" 
	ms.author="spelluru"/>

# Mover dados entre fontes locais e nuvem com o Gateway de Gerenciamento de Dados
O Azure Data Factory facilita mover os dados entre [repositórios compatíveis de dados locais e na nuvem](data-factory-data-movement-activities.md#supported-data-stores) usando o **Gateway de Gerenciamento de Dados**. O Gateway de Gerenciamento de Dados é um agente cliente que você instala no computador local para habilitar a cópia de dados entre repositórios de dados locais e na nuvem. O gateway pode ser instalado no mesmo computador que a fonte de dados ou em outro computador, desde que o gateway possa acessar a fonte de dados.

## Fluxo de dados com o Gateway de Gerenciamento de Dados
Quando você usa uma [Atividade de Cópia](data-factory-data-movement-activities.md) em um pipeline de dados a fim de incluir dados locais na nuvem para processamento adicional ou exportar dados de resultados na nuvem de volta para um repositório de dados local, a atividade de cópia usa internamente um gateway para transferir os dados da fonte de dados local para a nuvem, e vice-versa. Veja a seguir um fluxo de dados de alto nível e o resumo das etapas para a cópia com o gateway de dados: ![Fluxo de dados usando o gateway](./media/data-factory-move-data-between-onprem-and-cloud/data-flow-using-gateway.png)

1.	O desenvolvedor de dados cria um novo **gateway** para uma Azure Data Factory usando o [Portal do Azure](https://portal.azure.com) ou [Cmdlet do PowerShell](https://msdn.microsoft.com/library/dn820234.aspx). Essa etapa inclui a criação de um gateway lógico no back-end, a instalação de um gateway físico no computador local e a vinculação deles com uma chave.  
2.	O desenvolvedor de dados define um novo **serviço vinculado** para um repositório de dados local e especifica o nome do gateway para usar com o repositório de dados. Como parte da configuração de dados do serviço vinculado, o desenvolvedor especifica credenciais para acessar o repositório de dados local.  
3.	O gateway **criptografará as credenciais** com o certificado associado ao gateway (fornecido pelo desenvolvedor de dados) antes de salvar as credenciais na nuvem.
4.	O serviço Data Factory se comunica com o gateway para o agendamento e o gerenciamento de trabalhos por meio de um canal de controle que usa uma fila do Barramento de Serviço do Azure compartilhado. Quando o trabalho de atividade de cópia precisa ser inicializado, o Data Factory enfileira a solicitação com as informações de credencial. O gateway inicia o trabalho depois de sondar a fila.
5.	O gateway **descriptografa as credenciais** com o mesmo certificado e usa as credenciais para se conectar ao repositório de dados local.
6.	O gateway **copia dados** do repositório local para um armazenamento em nuvem, ou de um armazenamento em nuvem para um repositório de dados local, dependendo de como a Atividade de Cópia está configurada no pipeline de dados. Observação: para esta etapa, o gateway se comunica diretamente com o serviço de armazenamento baseado em nuvem (por exemplo, Blob do Azure, SQL do Azure etc.) por um canal seguro (HTTPS). 

Este artigo fornece informações detalhadas sobre como usar o Gateway de Gerenciamento de Dados. Primeiramente, vamos examinar um passo a passo simples de como usar o gateway para mover dados de um banco de dados SQL Server local para um blob do Azure.

## Passo a passo: uso do Gateway de Gerenciamento de Dados 
Neste passo a passo, você pode criar um data factory com um pipeline que move dados de um banco de dados SQL Server local para um blob do Azure.

### Etapa 1: criar uma data factory do Azure
Nesta etapa, você usa o Portal do Azure para criar uma instância do Azure Data Factory chamada **ADFTutorialOnPremDF**. Você também pode criar um data factory usando os cmdlets do Data Factory do Azure.

1.	Depois de fazer logon no [Portal do Azure](https://portal.azure.com), clique em **NOVO** no canto inferior esquerdo, selecione **Análises de dados** na folha **Criar** e clique em **Data Factory** na folha **Análises de dados**.

	![Novo -> DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)
  
6. Na folha **Nova data factory**:
	1. Insira **ADFTutorialOnPremDF** como o **nome**.
	2. Clique em **NOME DE GRUPO DE RECURSOS** e selecione **ADFTutorialResourceGroup**. Você pode selecionar um grupo de recursos existente ou crie um novo. Para criar um novo grupo de recursos:
		1. Clique em **Criar um novo grupo de recursos**.
		2. Na **folha Criar grupo de recursos**, insira um **nome** para o grupo de recursos e clique em **OK**.

7. Observe que a opção **Adicionar ao Quadro Inicial** está marcada na folha **Nova data factory**.

	![Adicionar ao quadro inicial](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

8. Na folha **Nova data factory**, clique em **Criar**.

	O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro: **O nome da data factory “ADFTutorialOnPremDF” não está disponível**, altere o nome da data factory (por exemplo, yournameADFTutorialOnPremDF) e tente criá-la novamente. Use esse nome em vez de ADFTutorialOnPremDF ao executar as etapas restantes neste tutorial.

9. Procure as notificações do processo de criação clicando no botão **Notificações** na barra de título, conforme mostrado na imagem a seguir. Clique novamente para fechar a janela de notificações.

	![Hub de NOTIFICAÇÕES](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNotificationsHub.png)

11. Após a conclusão da criação, você verá a folha **Data Factory**, conforme mostrado abaixo:

	![Página inicial do Data Factory](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

### Etapa 2: criar um gateway de gerenciamento de dados
5. Na folha **DATA FACTORY**, clique no bloco **Criar e implantar** para iniciar o **Editor** da data factory.

	![Bloco Criar e implantar](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png) 
6.	Em Editor do Data Factory, clique em **...(reticências)** na barra de ferramentas, e em seguida, clique em **Novo gateway de dados**. 

	![Novo gateway de dados na barra de ferramentas](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
2. Na folha **Criar**, insira **adftutorialgateway** como **nome** e clique em **OK**. 	

	![Folha Criar Gateway](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

3. Na folha **Configurar**, clique em **Instalar diretamente neste computador**. Isso baixará o pacote de instalação para o gateway, instalar, configurar e registrar o gateway no computador.

	> [AZURE.NOTE] 
	Use o Internet Explorer ou um navegador da Web compatível com Microsoft ClickOnce.
	> 
	> Se você estiver usando o Chrome, vá para a [Chrome web store](https://chrome.google.com/webstore/), pesquise a palavra-chave "ClickOnce", escolha uma das extensões do ClickOnce e instale-a.
	>  
	> Você precisa fazer o mesmo para o Firefox (instalar suplemento). Clique no botão **Abrir menu** na barra de ferramentas (**três linhas horizontais** no canto superior direito), clique em **Complementos**, pesquise a palavra-chave "ClickOnce", escolha uma das extensões do ClickOnce e instale-a.

	![Folha Gateway - Configurar](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

	Essa é a maneira mais fácil (um clique) de baixar, instalar, configurar e registrar o gateway em uma única etapa. Você pode ver que o aplicativo **Gerenciador de Configuração de Gateway de gerenciamento de dados da Microsoft** está instalado no computador. Você também pode encontrar o executável **ConfigManager.exe** na pasta: **C:\\Arquivos de Programas\\Microsoft Data Management Gateway\\1.0\\Shared**.

	Você também pode baixar e instalar o gateway manualmente usando os links nessa folha e registrá-lo usando a chave mostrada na caixa de texto **REGISTRAR COM CHAVE**.
	
	Consulte as seções no início deste artigo para obter detalhes sobre o gateway, incluindo as práticas recomendadas e considerações importantes.

	>[AZURE.NOTE] Você deve ser um administrador no computador local para instalar e configurar com êxito o Gateway de gerenciamento de dados. Você pode acrescentar usuários adicionais para o grupo local de usuários do Gateway de gerenciamento de dados do Windows. Os membros desse grupo poderão usar a ferramenta Gerenciador de configuração de Gateway de gerenciamento de dados para configurar o gateway.

5. Aguarde alguns minutos e inicie o aplicativo **Gerenciador de Configuração do Gateway de Gerenciamento de Dados** no computador. Na janela de **Pesquisa**, digite **Gateway de Gerenciamento de Dados** para acessar esse utilitário. Você também pode encontrar o executável **ConfigManager.exe** na pasta: **C:\\Arquivos de Programas\\Gateway de Gerenciamento de Dados\\1.0\\Shared**.

	![Gerenciador de configuração de gateway](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)

6. Aguarde até que os valores sejam definidos da seguinte maneira:
	1. O **Status** é definido como **Iniciado**.
	2. **Nome do gateway** está definido como **adftutorialgateway**.
	3. **Nome da instância** está definido como **adftutorialgateway**.
	4. **Registro** é definido como **Registrado**.
	5. A barra de status inferior exibe **Conectado ao Serviço de Nuvem do Gateway de Gerenciamento de Dados** junto com uma **marca de seleção verde**.

8. Alterne para a guia **Certificados**. O certificado especificado nessa guia é usado para criptografar/descriptografar credenciais para o armazenamento de dados local que você especifica no portal. Clique em **Alterar** para usar seu próprio certificado. Por padrão, o gateway usa o certificado que é gerado automaticamente pelo serviço de Data Factory.

	![Configuração do certificado do gateway](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)
9. (opcional) Alterne para a guia **Diagnóstico** e marque a opção **Habilitar log detalhado para solução de problemas** se você desejar habilitar o log detalhado que pode usar para solucionar problemas com o gateway. As informações de log podem ser encontradas no **Visualizador de Eventos** em **Logs de Aplicativos e Serviços** -> nó **Gateway de Gerenciamento de Dados**. 

	![Guia Diagnósticos](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

	Você também pode usar essa página para **testar a conexão** para uma fonte de dados local usando o gateway.
10. No Portal do Azure, clique em **OK** na folha **Configurar** e na folha **Novo gateway de dados**.
6. Você deve consultar **adftutorialgateway** em **Gateways de Dados** no modo de exibição de árvore à esquerda. Se você clicar nisso, você verá o JSON associado. 
	

### Etapa 3: Criar serviços vinculados 
Nesta etapa, você criará dois serviços vinculados: **AzureStorageLinkedService** e **SqlServerLinkedService**. O **SqlServerLinkedService** vincula um banco de dados SQL Server local e o serviço vinculado **AzureStorageLinkedService** vincula um repositório de blob do Azure ao Data Factory. Você criará um pipeline posteriormente neste passo a passo que copia dados do banco de dados SQL Server local para o armazenamento de blob do Azure.

#### Adicionar um serviço vinculado a um banco de dados SQL Server local
1.	No **Editor do Data Factory**, clique em **Novo armazenamento de dados** na barra de ferramentas e selecione **SQL Server**. 

	![Serviço vinculado do SQL Server](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png) 
3.	No **Editor JSON**, faça o seguinte: 
	1. Em **gatewayName**, especifique **adftutorialgateway**.	
	2. Se você está usando a Autenticação do Windows:
		1. Em **connectionString**: 
			1. Defina **Integrated Security** como **true**.
			2. Especifique o banco de dados **nome do servidor** e **nome do banco de dados**. 
			2. Remova a **ID de usuário** e **Senha**. 
		3. Especifique o nome de usuário e senha para as propriedades **userName** e **password**.  
		
				"typeProperties": {
            		"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
            		"gatewayName": "adftutorialgateway",
            		"userName": "<Specify user name if you are using Windows Authentication. Example: <domain>\<user>",
            		"password": "<Specify password for the user account>"
        		}
                
            > [AZURE.NOTE] Se você está usando a autenticação do Windows (IntegratedSecurity=true), é opcional especificar o nome de usuário e a senha. Se você não especificar essas propriedades, o Gateway de Gerenciamento de Dados usará as credenciais do usuário conectado no computador do gateway para acessar o banco de dados. Se desejar que o gateway use credenciais diferentes para acessar o banco de dados, especifique o nome de usuário e a senha explicitamente.

	4. Se você estiver usando a autenticação SQL:
		1. Especifique o **nome do servidor**, o **nome do banco de dados**, a **ID de usuário** e a **Senha** do banco de dados na **connectionString**.       
		2. Remova as duas últimas propriedades JSON - **userName** e **password** - do JSON.
		3. Remova a **, (vírgula)** à direita no final da linha que especifica o valor da propriedade **gatewayName**. 

				"typeProperties": {
            		"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
	           		"gatewayName": "<Name of the gateway that the Data Factory service should use to connect to the on-premises SQL Server database>"
    		    }
	
		As credenciais serão **criptografadas** usando um certificado que o serviço do Data Factory tem. Em vez disso, se você quiser usar o certificado associado ao Gateway de Gerenciamento de Dados, confira [Definir credenciais com segurança](#set-credentials-and-security).
    
2.	Clique em **Implantar** na barra de comandos para implantar o serviço vinculado do SQL Server.

#### Adicionar um serviço vinculado para uma conta de armazenamento do Azure
 
1. No **Editor do Data Factory**, clique em **Novo armazenamento de dados** na barra de comandos e clique em **Armazenamento do Azure**.
2. Insira o nome da sua conta de armazenamento do Azure em **Nome da conta**.
3. Insira a chave da sua conta de armazenamento do Azure em **Chave de conta**.
4. Clique em **Implantar** para implantar o **AzureStorageLinkedService**.
   
 
### Etapa 4: Criar conjuntos de dados de entrada e saída
Nesta etapa, você criará conjuntos de dados de entrada e saída que representam dados de entrada e saída da operação de cópia (banco de dados SQL Server local = > armazenamento de blob do Azure). Antes de criar conjuntos de dados ou tabelas (conjuntos de dados retangulares), você precisa fazer o seguinte (etapas detalhadas seguem a lista):

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

	Observe o seguinte:
	
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
  
	Observe o seguinte:
	
	- **tipo** é definido como **AzureBlob**.
	- **linkedServiceName** é definido como **AzureStorageLinkedService** (você criou esse serviço vinculado na Etapa 2).
	- **folderPath** é definido como **adftutorial/outfromonpremdf**, em que outfromonpremdf é a pasta no contêiner adftutorial. Você precisa apenas criar o contêiner **adftutorial**.
	- A **disponibilidade** é definida como **por hora** (**frequência** definida como **hora** e **intervalo** definido como **1**). O serviço Data Factory gerará uma fatia de dados de saída a cada hora na tabela **emp** no banco de dados SQL do Azure. 

	Se você não especificar um **fileName** para uma **tabela de entradas**, todos os arquivos/blobs da pasta de entrada (**folderPath**) serão considerados como entradas. Se você especificar um nome de arquivo em JSON, apenas arquivo/blob especificado será considerado como entrada de asn. Consulte os arquivos de exemplo no [tutorial][adf-tutorial] para ver exemplos.
 
	Se você não especificar um **fileName** para uma **tabela de saída**, os arquivos gerados no **folderPath** serão nomeados no seguinte formato: Data.<Guid>.txt (por exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt).

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
  

### Etapa 5: Criar e executar um pipeline
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
		    "start": "2015-02-13T00:00:00Z",
		    "end": "2015-02-14T00:00:00Z",
		    "isPaused": false
		  }
		}

	Observe o seguinte:
 
	- Na seção de atividades, há somente uma atividade cujo **type** é definido como **Copy**.
	- A **entrada** da atividade é definida como **EmpOnPremSQLTable** e a **saída** da atividade é definida como **OutputBlobTable**.
	- Na seção **transformation**, **SqlSource** é especificado como o **source type** e **BlobSink **é especificado como o **sink type**.
	- A consulta SQL **select * from emp** é especificada para a propriedade **sqlReaderQuery** de **SqlSource**.

	Substitua o valor da propriedade **início** pelo dia atual e o valor de **término** pelo dia seguinte. Ambos os valores de data/hora de início e de término devem estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. A hora de **end** é opcional, mas nós o usaremos neste tutorial.
	
	Se você não especificar o valor para a propriedade **término**, ele será calculado como "**início + 48 horas**". Para executar o pipeline indefinidamente, especifique **9/9/9999** como o valor da propriedade **end**.
	
	Especificando o período ativo de um pipeline, você está definindo a duração de tempo em que as fatias de dados serão processadas com base nas propriedades de **Disponibilidade** que foram definidas para cada tabela da Azure Data Factory.
	
	No exemplo acima, como cada fatia de dados é produzida por hora, haverá 24 fatias de dados.
	
2. Clique em **Implantar** na barra de comandos para implantar o conjunto de dados (a tabela é um conjunto de dados retangular). Confirme que você vê uma mensagem na barra de título que diz **PIPELINE IMPLANTADO COM ÊXITO**.
5. Agora, feche a folha **Editor** clicando em **X**. Clique em **X** novamente para fechar a folha ADFTutorialDataFactory com o modo de exibição de árvore e a barra de ferramentas. Se você vir **suas edições não salvas serão descartadas**, clique em **OK**.
6. Você deve ser retornado à folha **DATA FACTORY** para o **ADFTutorialOnPremDF**.

**Parabéns!** Você criou um data factory do Azure, serviços vinculados, tabelas e uma pipeline e a pipeline agendada com êxito.

#### Exibir a data factory em um Modo de Exibição de Diagrama 
1. No **Portal do Azure**, clique no bloco **Diagrama** na página inicial da data factory **ADFTutorialOnPremDF**.

	![Link do diagrama](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)

2. Você deverá ver o diagrama semelhante ao seguinte:

	![Exibição de diagrama](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

	É possível ampliar, reduzir, aplicar zoom de 100%, ajustar nível de zoom, posicionar pipelines e tabelas automaticamente, bem como mostrar informações de linhagem (realça itens upstream e downstream dos itens selecionados). Você pode clicar duas vezes em um objeto (pipeline ou tabela de entrada/saída) para ver as propriedades dele.

### Etapa 6: Monitorar os conjuntos de dados e os pipelines
Nesta etapa, você utilizará o Portal do Azure para monitorar o que está acontecendo em um data factory do Azure. Você também pode usar os cmdlets do PowerShell para monitorar conjuntos de dados e pipelines. Para obter detalhes sobre monitoramento, consulte [Monitorar e gerenciar pipelines](data-factory-monitor-manage-pipelines.md).

1. Navegue até o **Portal do Azure** (se você o fechou)
2. Se a folha para **ADFTutorialOnPremDF** não estiver aberta, abra-a clicando em **ADFTutorialOnPremDF** no **Quadro Inicial**.
3. Você verá a **contagem** e os **nomes** das tabelas e do pipeline criados nessa folha.

	![Página inicial do Data Factory](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)
4. Agora, clique no bloco **Conjuntos de dados**.
5. Na folha **Conjuntos de Dados**, clique em **EmpOnPremSQLTable**.

	![Fatias de EmpOnPremSQLTable](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)

6. Observe que as fatias de dados até o momento atual já foram produzidas e estão **Prontas**. Isso porque você inseriu os dados no banco de dados SQL Server e eles estão lá está o tempo todo. Confirme se nenhuma fatia aparecerá na seção **Fatias com problema** na parte inferior.


	Ambas as listas **Fatias atualizadas recentemente** e **Fatias com falha recente** são classificadas segundo o **HORÁRIO DA ÚLTIMA ATUALIZAÇÃO**. A hora de atualização de uma fatia é alterada nas situações a seguir.
    

	-  Você atualiza o status da fatia manualmente, por exemplo, usando o **Set-AzureRmDataFactorySliceStatus** (ou) clicando em **EXECUTAR** na folha **FATIA** da fatia.
	-  A fatia é alterada devido a uma execução (por exemplo, uma execução iniciada, uma execução finalizada e com falha, uma execução finalizada e bem-sucedida, etc).
 
	Clique no título das listas ou em **... (reticências)** para ver a lista maior de fatias. Clique em **Filtrar** na barra de ferramentas para filtrar as fatias.
	
	Para exibir, em vez disso, as fatias de dados classificadas pelas horas de início/término da fatia, clique no bloco **Fatias de dados (por horário da fatia)**.

7. Agora, na folha **Conjuntos de Dados**, clique em **OutputBlobTable**.

	![Fatias OputputBlobTable][image-data-factory-output-blobtable-slices]
8. Confirme se as fatias até a hora atual estão produzidas e **Prontas**. Aguarde até que o status das fatias até a hora atual estar definido como **Prontas**.
9. Clique em qualquer fatia de dados na lista e você deverá ver a folha **FATIA DE DADOS**.

	![Folha Fatia de dados](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

	Se a fatia não está no estado **Pronto**, você pode ver as fatias upstream que não estão Prontas e estão impedindo a execução da fatia atual na lista **Fatias upstream que não estão prontas**.

10. Clique na **execução de atividade** na lista na parte inferior para ver **detalhes de execução da atividade**.

	![Folha de detalhes de execução da atividade][image-data-factory-activity-run-details]

11. Clique no **X** para fechar todas as folhas até
12. voltar para a folha inicial do **ADFTutorialOnPremDF**.
14. (opcional) Clique em **Pipelines**, clique em **ADFTutorialOnPremDF** e execute uma consulta drill-through nas tabelas de entrada (**Consumed**) ou nas tabelas de saída (**Produced**).
15. Use ferramentas como o **Explorador de Armazenamento do Azure** para verificar a saída.

	![Gerenciador de Armazenamento do Azure](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

As seções a seguir fornecem informações detalhadas sobre o Gateway de Gerenciamento de Dados.

## Considerações para usar o Gateway de Gerenciamento de Dados
1.	Uma única instância do Gateway de Gerenciamento de Dados pode ser usada para várias fontes de dados locais, mas observe que **uma única instância de gateway está associada a apenas uma Azure Data Factory** e não pode ser compartilhada com outra data factory.
2.	Você pode ter **apenas uma instância do Gateway de Gerenciamento de Dados** instalada em um único computador. Suponha, você tem dois data factories que precisam acessar fontes de dados locais e precisa instalar gateways nos dois computadores locais onde cada gateway é vinculado a um data factory separado.
3.	O **gateway não precisa estar no mesmo computador que a fonte de dados**, mas ficar mais próximo à fonte de dados reduz o tempo para o gateway se conectar à fonte de dados. Recomendamos que você instale o gateway em um computador diferente daquele que hospeda a fonte de dados local para que o gateway não dispute os recursos com a fonte de dados.
4.	Você pode ter **vários gateways em diferentes computadores conectados à mesma fonte de dados local**. Por exemplo, você pode ter dois gateways servindo duas data factories, mas a mesma fonte de dados local é registrada com ambas as data factories.
5.	Se você já tiver um gateway instalado no computador atendendo um cenário do **Power BI**, instale um **gateway separado para o Azure Data Factory** em outro computador.
6.	Você deve **usar o gateway, mesmo quando usar a Rota Expressa**. 
7.	Você deve tratar a fonte de dados como uma fonte de dados local (que está atrás de um firewall) mesmo quando usar a **Rota Expressa** e **usar o gateway** para estabelecer a conectividade entre o serviço e a fonte de dados. 

## Instalar o Gateway de Gerenciamento de Dados

### Instalação do gateway - pré-requisitos
1.	As versões de **Sistema Operacional** com suporte são Windows 7, Windows 8/8.1, Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2. Instalação do Gateway de Gerenciamento de Dados em um controlador de domínio não tem suporte atualmente.
2.	A **configuração** recomendada para o computador do gateway é no mínimo 2 GHz, 4 núcleos, 8 GB de RAM e 80 GB de disco.
3.	Se o computador host hibernar, o gateway não poderá responder a solicitações de dados. Portanto, configure um **plano de energia** adequado no computador antes de instalar o gateway. A instalação do gateway exibe uma mensagem se o computador estiver configurado para hibernar.

Devido ao fato de as execuções da atividade de cópia ocorrerem em uma frequência específica, o uso de recursos (CPU, memória) no computador também segue o mesmo padrão com tempos ociosos e de pico. A utilização de recursos também depende muito da quantidade de dados sendo movida. Quando vários trabalhos de cópia estiverem em andamento, você observará o uso do recurso aumentar durante horários de pico. Embora o indicado acima seja a configuração mínima, é sempre melhor ter uma configuração com mais recursos do que as configurações mínimas descritas acima dependendo da sua carga específica para movimentação de dados.

### Instalação
No passo a passo acima, você viu como baixar e executar o arquivo MSI para Gerenciamento de Dados.

Também é possível instalar o Gateway de Gerenciamento de Dados baixando um pacote de instalação MSI do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).

O MSI também pode ser usado para atualizar o Gateway de Gerenciamento de Dados existente para a versão mais recente, com todas as configurações preservadas.

### Práticas recomendadas de instalação:
1.	Configure o plano de energia no computador host para o gateway para que o computador não hiberne. Se o computador host hibernar, o gateway não poderá responder a solicitações de dados.
2.	Você deve fazer backup do certificado associado ao gateway.

## Atualizar o Gateway de Gerenciamento de Dados
Por padrão, o Gateway de Gerenciamento de Dados é atualizado automaticamente quando uma versão mais recente do gateway está disponível. O gateway não é atualizado até que todas as tarefas agendadas sejam concluídas. Nenhuma tarefa adicional é processada pelo gateway até que a operação de atualização seja concluída. Se a atualização falhar, o gateway será revertido para a versão antiga.

Você verá o horário de atualização agendado no portal na folha de propriedades do gateway, na home page do Gerenciador de Configurações do Gateway de Gerenciamento de Dados, e na mensagem de notificação da bandeja do sistema. Você tem a opção de instalar a atualização imediatamente ou aguardar até que o gateway seja atualizado automaticamente no horário agendado. Por exemplo, a captura de tela a seguir mostra a mensagem de notificação do Gerenciador de Configurações do Gateway de Gerenciamento de Dados, juntamente com o botão Atualizar no qual você clica para instalá-lo imediatamente.

![Atualizar no Gerenciador de Configurações DMG](./media/data-factory-move-data-between-onprem-and-cloud/gateway-auto-update-config-manager.png)

A mensagem de notificação na bandeja do sistema seria semelhante ao seguinte:

![Mensagem da bandeja do sistema](./media/data-factory-move-data-between-onprem-and-cloud/gateway-auto-update-tray-message.png)

Você verá o status da operação de atualização (manual ou automática) na bandeja do sistema. Ao abrir o Gerenciador de Configurações do Gateway de Gerenciamento de Dados posteriormente, você verá uma mensagem na barra de notificação indicando que o gateway foi atualizado, com um link para o [tópico de novidades](data-factory-gateway-release-notes.md).

A guia Atualizar do Gerenciador de Configurações do Gateway de Gerenciamento de Dados exibe a agenda de atualização, bem como a última vez em que o gateway foi instalado/atualizado. Se a atualização automática estiver desabilitada, será mostrada uma mensagem sobre isso, mas você não poderá habilitar o recurso na guia. Você terá que usar o cmdlet para habilitar o recurso.
  

## Ícones/notificações da bandeja do sistema
A imagem a seguir mostra alguns dos ícones da bandeja que você verá.

![ícones da bandeja do sistema](./media/data-factory-move-data-between-onprem-and-cloud/gateway-tray-icons.png)

Se mover o cursor sobre o ícone de bandeja do sistema/mensagem de notificação, você verá detalhes sobre o estado do gateway/da operação de atualização em uma janela pop-up.

## Para habilitar/desabilitar o recurso de atualização automática
Você pode habilitar/desabilitar o recurso de atualização automática fazendo o seguinte:

1. Inicie o Windows **PowerShell** no computador do gateway como um administrador (**Executar como administrador**). 
2. Alterne para a pasta C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\PowerShellScript.
3. Execute o seguinte comando para DESATIVAR (desabilitar) o recurso de atualização automática.   

		.\GatewayAutoUpdateToggle.ps1  -off

4. Para ativá-la novamente:
	
		.\GatewayAutoUpdateToggle.ps1  -on  

## Considerações sobre a porta e segurança
Há dois firewalls que você precisa levar em consideração: o **firewall corporativo** em execução no roteador central da organização e o **Firewall do Windows** configurado como um daemon no computador local em que o gateway está instalado.

![firewalls](./media/data-factory-move-data-between-onprem-and-cloud/firewalls.png)

### Gateway de conexão com serviços de nuvem
Para manter a conectividade do gateway com o Azure Data Factory e outros serviços de nuvem, você precisa verificar se a regra de saída para as portas **TCP** **80** e **443** está configurada. E, opcionalmente, habilitar as portas **9350** a **9354** e a **5671**, que são usadas pelo Barramento de Serviço do Microsoft Azure para estabelecer a conexão entre o Azure Data Factory e o Gateway de Gerenciamento de Dados e podem melhorar o desempenho da comunicação entre eles.

No nível do firewall corporativo, você precisa configurar os seguintes domínios e portas de saída:

| Nomes de domínio | Portas | Descrição |
| ------ | --------- | ------------ |
| *.servicebus.windows.net | 443, 80 | Ouvintes de Retransmissão do Barramento de Serviço sobre TCP (requer 443 para aquisição de token de Controle de Acesso) | 
| *.servicebus.windows.net | 9350-9354, 5671 | Retransmissão do Barramento de Serviço opcional sobre TCP | 
| *.core.windows.net | 443 | HTTPS | 
| *.clouddatahub.net | 443 | HTTPS | 
| graph.windows.net | 443 | HTTPS | 
| login.windows.net | 443 | HTTPS | 

No nível do firewall do windows, essas portas de saída normalmente são habilitadas. Se não forem, você poderá configurar as portas e os domínios adequadamente no computador do gateway.

### Definir credenciais
A porta de entrada **8050** será usada pelo aplicativo **Definindo Credenciais** para retransmitir as credenciais ao gateway quando você configurar um serviço vinculado local no Portal do Azure (detalhes mais adiante neste artigo). Durante a instalação do gateway, por padrão, a instalação do Gateway de Gerenciamento de Dados o abre no computador do gateway.
 
Caso seja usado um firewall de terceiros, você poderá abrir manualmente a porta 8050. Se tiver problemas de firewall durante a instalação do gateway, você poderá tentar usar o comando a seguir para instalar o gateway sem configurar o firewall.

	msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Se optar por não abrir a porta 8050 no computador do gateway, para configurar um serviço vinculado local, você precisará usar mecanismos diferentes do aplicativo **Definindo Credenciais** para configurar as credenciais do repositório de dados. Por exemplo, você pode usar o cmdlet do PowerShell [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx). Confira a seção [Definindo Credenciais e Segurança](#set-credentials-and-securityy) para saber como as credenciais do repositório de dados podem ser definidas.

**Para copiar dados de um armazenamento de dados de origem para um armazenamento de dados coletor:**

Você precisa certificar-se de que as regras de firewall estejam habilitadas corretamente no firewall corporativo, no Firewall do Windows no computador do gateway e no armazenamento de dados em si. Isso permite que o gateway se conecte à origem e ao coletor com êxito. Você precisa habilitar regras para cada repositório de dados que está envolvido na operação de cópia.

Por exemplo, para copiar de **um repositório de dados local para um coletor do Banco de Dados SQL do Azure ou um coletor do SQL Data Warehouse do Azure**, você precisa permitir a comunicação **TCP** de saída na porta **1433** para o Firewall do Windows e o firewall corporativo, bem como precisa definir as configurações de firewall do SQL Server do Azure para adicionar o endereço IP do computador do gateway à lista de endereços IP permitidos.

### Considerações do servidor proxy
Por padrão, o Gateway de Gerenciamento de Dados utilizará as configurações de proxy do Internet Explorer e usará as credenciais padrão para acessá-lo. Se isso não se adequar ao seu caso, você poderá definir as **configurações do servidor proxy** conforme mostrado abaixo para garantir que o gateway seja capaz de se conectar ao Azure Data Factory:

1.	Depois de instalar o Gateway de Gerenciamento de Dados, no Explorador de Arquivos, faça uma cópia de segurança de “C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\diahost.exe.config” para fazer backup do arquivo original.
2.	Inicie o Notepad.exe executando como administrador e abra o arquivo de texto “C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\diahost.exe.config”. Você encontrará a marca padrão para system.net da seguinte forma:

			<system.net>
				<defaultProxy useDefaultCredentials="true" />
			</system.net>	

	Você pode adicionar os detalhes do servidor proxy, como o endereço proxy dentro dessa marca pai, por exemplo:

			<system.net>
			      <defaultProxy enabled="true">
			            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
			      </defaultProxy>
			</system.net>

	Propriedades adicionais são permitidas dentro da marca de proxy para especificar as configurações necessárias como scriptLocation. Confira [proxy Element (Network Settings)](https://msdn.microsoft.com/library/sa91de1e.aspx) (Elemento proxy (Configurações de Rede)) na sintaxe.

			<proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>

3. Salve o arquivo de configuração no local original e reinicie o serviço de Gateway de Gerenciamento de Dados para acompanhar as alterações. Você pode fazer isso em **Iniciar** > **Services.msc** ou, no **Gerenciador de Configurações do Gateway de Gerenciamento de Dados**, clique no botão **Parar Serviço** e em **Iniciar Serviço**. Se o serviço não iniciar, é provável que uma sintaxe de marca XML incorreta tenha sido adicionada ao arquivo de configuração de aplicativo que foi editado.

Além dos pontos acima, você também precisa certificar-se de que o Microsoft Azure esteja na lista branca da sua empresa. A lista de endereços IP válidos do Microsoft Azure pode ser baixada no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=41653).

### Possíveis sintomas de problemas relacionados ao firewall e ao servidor proxy
Se você encontrar erros como os descritos a seguir, eles provavelmente se devem à configuração incorreta do servidor proxy ou firewall, que impede o Gateway de Gerenciamento de Dados de se conectar ao Azure Data Factory para se autenticar. Consulte a seção acima para garantir que seu firewall e servidor proxy estejam configurados corretamente.

1.	Ao tentar registrar o gateway, você recebe o seguinte erro: "Falha ao registrar a chave do gateway. Antes de tentar registrar a chave do gateway novamente, confirme se o Gateway de Gerenciamento de Dados está em um estado conectado e o Serviço de Host do Gateway de Gerenciamento de Dados está iniciado."
2.	Ao abrir o Gerenciador de Configurações, você vê o status “Desconectado” ou “Conectando”. Ao exibir os logs de eventos do Windows, em "Visualizador de Eventos" > "Logs de Aplicativos e Serviços" > "Gateway de Gerenciamento de Dados" você vê mensagens de erro como "Impossível conectar-se ao servidor remoto" ou "Um componente do Gateway de Gerenciamento de Dados parou de responder e será reiniciado automaticamente. Nome do componente: Gateway."

## Solução de problemas de gateway


- Você pode encontrar informações detalhadas nos logs de gateway nos logs de eventos do Windows. Você pode encontrá-los usando o **Visualizador de Eventos** do Windows em **Logs de Aplicativos e Serviços** > **Gateway de Gerenciamento de Dados**. Enquanto soluciona problemas relacionados ao gateway, procure por eventos de nível de erro no Visualizador de Eventos.
- Se o gateway parar de funcionar depois que você **alterar o certificado**, reinicie (pare e inicie) o **Serviço de Gateway de Gerenciamento de Dados** usando a ferramenta Gerenciador de Configurações do Gateway de Gerenciamento de Dados Microsoft ou o miniaplicativo do painel de controle Serviços. Se você vir um erro, você precisará conceder permissões explícitas para o usuário do serviço de Gateway de Gerenciamento de Dados acessar o certificado no Gerenciador de Certificados (certmgr.msc). A conta de usuário padrão para o serviço é: **NT Service\\DIAHostService**. 
- Se você encontrar erros relacionados ao driver ou à conexão com o repositório de dados, inicie o **Gerenciador de Configurações do Gateway de Gerenciamento de Dados** no computador do gateway, alterne para a guia **Diagnóstico**, escolha/insira valores adequados para os campos no grupo **Testar a conectividade com a fonte de dados local usando este gateway** e clique em **Testar conectividade** para ver se é possível se conectar à fonte de dados local do computador do gateway usando as informações e credenciais de conexão. Se a conexão de teste continuar falhando depois que você instalar um driver, reinicie o gateway para que ele assimile a alteração mais recente.  

	![Testar conectividade](./media/data-factory-move-data-between-onprem-and-cloud/TestConnection.png)
		

## Mover o gateway de um computador para outro
Esta seção fornece etapas para movimentação do cliente do gateway de um computador para outro.

2. No portal, navegue até a **home page do Data Factory** e clique no bloco **Serviços Vinculados**. 

	![Link de Gateways de Dados](./media/data-factory-move-data-between-onprem-and-cloud/DataGatewaysLink.png) 
3. Selecione seu gateway na seção **GATEWAYS DE DADOS** da folha **Serviços Vinculados**.
	
	![Folha Serviços Vinculados com gateway selecionado](./media/data-factory-move-data-between-onprem-and-cloud/LinkedServiceBladeWithGateway.png)
4. Na folha **Gateway de dados**, clique em **Baixar e instalar o gateway de dados**.
	
	![Baixar o link do gateway](./media/data-factory-move-data-between-onprem-and-cloud/DownloadGatewayLink.png) 
5. Na folha **Configurar**, clique em **Baixar e instalar o gateway de dados** e siga as instruções para instalar o gateway de dados no computador. 

	![Configurar folha](./media/data-factory-move-data-between-onprem-and-cloud/ConfigureBlade.png)
6. Mantenha o **Gerenciador de Configuração de Gateway do Gerenciamento de Dados da Microsoft** 
 
	![Gerenciador de Configurações](./media/data-factory-move-data-between-onprem-and-cloud/ConfigurationManager.png)	
7. Na folha **Configurar** no portal, clique em **Recriar chave** na barra de comandos e clique em **Sim** para a mensagem de aviso. Clique no **botão copiar** ao lado do texto da chave para copiar a chave para a área de transferência. Observe que o gateway do computador antigo deixará de funcionar assim que você recriar a chave.  
	
	![Recriar chave](./media/data-factory-move-data-between-onprem-and-cloud/RecreateKey.png)
	 
8. Cole a **chave** na caixa de texto na página **Registrar Gateway** do **Gerenciador de Configuração de Gateway do Gerenciamento de Dados** em seu computador. (Opcional) Clique na caixa de seleção **Mostrar chave do gateway** para ver o texto da chave.
 
	![Copiar chave e Registrar-se](./media/data-factory-move-data-between-onprem-and-cloud/CopyKeyAndRegister.png)
9. Clique em **Registrar** para registrar o gateway no serviço de nuvem.
10. Na página **Especificar certificado**, clique em **Procurar** para selecionar o mesmo certificado que foi usado com o gateway antigo, insira a **senha** e clique em **Concluir**. 
 
	![Especificar Certificado](./media/data-factory-move-data-between-onprem-and-cloud/SpecifyCertificate.png)

	Você pode exportar um certificado do gateway antigo da seguinte maneira: inicie o Gerenciador de Configuração de Gateway do Gerenciamento de Dados na máquina antiga, alterne para a guia **Certificado**, clique em **Exportar** e siga as instruções. 
10. Após a conclusão do registro do gateway, você deverá ver o **Registro** definido como **Registrado**, e o **Status** definido como **Iniciado** na Home page do Gerenciador de Configuração de Gateway. 

## Configurar credenciais e segurança
Para criptografar credenciais no Editor do Data Factory, faça o seguinte:

1. Clique em um **serviço vinculado** existente no modo de exibição de árvore para ver sua definição JSON ou criar um novo serviço vinculado que exija um Gateway de Gerenciamento de Dados (por exemplo: SQL Server ou Oracle). 
2. No editor de JSON, para a propriedade **gatewayName**, insira o nome do gateway. 
3. Insira o nome do servidor para a propriedade **Data Source** em **connectionString**.
4. Insira o nome do banco de dados para a propriedade **Initial Catalog** em **connectionString**.    
5. Clique no botão **Criptografar** na barra de comandos. Você verá a caixa de diálogo **Definindo Credenciais**. ![Caixa de diálogo de Configurando Credenciais](./media/data-factory-move-data-between-onprem-and-cloud/setting-credentials-dialog.png)
6. Na caixa de diálogo **Definindo Credenciais**, faça o seguinte:  
	1.	Selecione a **autenticação** que você deseja que o serviço de Data Factory use para se conectar ao banco de dados. 
	2.	Insira o nome do usuário que tem acesso ao banco de dados para a configuração **NOME DE USUÁRIO**. 
	3.	Insira a senha do usuário para a configuração **SENHA**.  
	4.	Clique em **OK** para criptografar credenciais e fechar a caixa de diálogo. 
5.	Agora, você verá uma propriedade **encryptedCredential** em **connectionString**.		
		
			{
	    		"name": "SqlServerLinkedService",
		    	"properties": {
		        	"type": "OnPremisesSqlServer",
			        "description": "",
		    	    "typeProperties": {
		    	        "connectionString": "data source=myserver;initial catalog=mydatabase;Integrated Security=False;EncryptedCredential=eyJDb25uZWN0aW9uU3R",
		            	"gatewayName": "adftutorialgateway"
		        	}
		    	}
			}

Se você acessar o portal de um computador diferente do computador do gateway, você deve garantir que o aplicativo Gerenciador de credenciais possa se conectar ao computador do gateway. Se o aplicativo não puder acessar o computador do gateway, ele não permitirá que você defina credenciais da fonte de dados teste a conexão à fonte de dados.

Quando você usa o aplicativo **Definindo Credenciais** iniciado pelo Portal do Azure para definir credenciais para uma fonte de dados local, o portal criptografa as credenciais com o certificado especificado na guia **Certificado** do **Gerenciador de Configuração de Gateway de Gerenciamento de Dados** no computador do gateway.

Se você estiver procurando uma abordagem baseada em API para criptografar as credenciais, poderá usar o cmdlet [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) do PowerShell para criptografar as credenciais. O cmdlet usa o certificado que esse gateway está configurado para usar para criptografar as credenciais. Você pode obter as credenciais criptografadas retornadas por esse cmdlet e adicioná-las ao elemento **EncryptedCredential** de **connectionString** do arquivo JSON que será usado com o cmdlet [New-AzureRmDataFactoryLinkedService](https://msdn.microsoft.com/library/mt603647.aspx) ou no trecho de código JSON no Editor do Data Factory no portal.

	"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",

Há mais uma abordagem para definir as credenciais usando o Editor Data Factory. Se você criar um serviço vinculado SQL Server usando o editor e inserir credenciais em texto sem formatação, as credenciais são criptografadas usando um certificado que é de propriedade do serviço Data Factory, NÃO o certificado que o gateway está configurado para usar. Embora essa abordagem possa ser um pouco mais rápida em alguns casos, ela é menos segura. Portanto, é recomendável que você siga essa abordagem somente para fins de desenvolvimento/teste.


## Criar e registrar o Gateway de Gerenciamento de Dados usando o Azure PowerShell 
Esta seção descreve como criar e registrar um gateway usando cmdlets do PowerShell do Azure.

1. Inicie o **PowerShell do Azure** no modo de administrador. 
2. Faça logon na sua conta do Azure executando o seguinte comando e digitando suas credenciais do Azure. 

	Login-AzureRmAccount
2. Use o cmdlet **New-AzureRmDataFactoryGateway** para criar um gateway lógico da seguinte maneira:

		$MyDMG = New-AzureRmDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**Exemplo de comando e saída**:


		PS C:\> $MyDMG = New-AzureRmDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

		Name              : MyGateway
		Description       : gateway for walkthrough
		Version           :
		Status            : NeedRegistration
		VersionStatus     : None
		CreateTime        : 9/28/2014 10:58:22
		RegisterTime      :
		LastConnectTime   :
		ExpiryTime        :
		ProvisioningState : Succeeded
		Key               : ADF#00000000-0000-4fb8-a867-947877aef6cb@fda06d87-f446-43b1-9485-78af26b8bab0@4707262b-dc25-4fe5-881c-c8a7c3c569fe@wu#nfU4aBlq/heRyYFZ2Xt/CD+7i73PEO521Sj2AFOCmiI

	
4. No Azure PowerShell, alterne para a pasta **C:\\Arquivos de Programas\\Microsoft Data Management Gateway\\1.0\\PowerShellScript** e execute o script **RegisterGateway.ps1** associado à variável local **$Key** conforme mostrado no seguinte comando para registrar o agente cliente instalado no computador com o gateway lógico criado anteriormente.

		PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
		
		Agent registration is successful!

	Você pode registrar o gateway em um computador remoto usando o parâmetro IsRegisterOnRemoteMachine. Exemplo:
		
		.\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true

5. Você pode usar o cmdlet **Get-AzureRmDataFactoryGateway** para obter a lista de gateways no data factory. Quando o **Status** mostra **online**, isso significa que seu gateway está pronto para uso.

		Get-AzureRmDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

Você pode remover um gateway usando o cmdlet **Remove-AzureRmDataFactoryGateway** e atualizar a descrição de um gateway com os cmdlets **Set-AzureRmDataFactoryGateway**. Para sintaxe e outros detalhes sobre esses cmdlets, consulte Referência de Cmdlet de Data Factory.

## Listar gateways usando o PowerShell

	Get-AzureRmDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup

## Remover gateways usando o PowerShell
	
	Remove-AzureRmDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force 

<!---HONumber=AcomDC_0601_2016-->