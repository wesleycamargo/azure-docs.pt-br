<properties 
	pageTitle="Habilitar seus pipelines para trabalhar com dados locais | Data Factory do Azure" 
	description="Saiba como registrar uma fonte de dados local com uma fábrica de dados do Azure e copiar dados de/para a fonte de dados." 
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
	ms.date="05/06/2015" 
	ms.author="spelluru"/>

# Habilitar seus pipelines para trabalhar com dados locais

Para habilitar os pipelines em um data factory do Azure para trabalhar com uma fonte de dados local, você precisa adicionar a fonte de dados local como um serviço vinculado ao data factory usando o Portal de Gerenciamento do Azure ou o PowerShell do Azure.
 
Para poder adicionar uma fonte de dados local como um serviço vinculado a um data factory, você primeiro precisa baixar e instalar o Gateway de Gerenciamento de Dados da Microsoft em um computador local e configurar o serviço vinculado para a fonte de dados local para usar o gateway.


## <a href="DMG"></a> Gateway de gerenciamento de dados

**Data Management Gateway** é um software que se conecta a fontes de dados no local para serviços em nuvem de uma maneira segura e gerenciada. Com o Gateway de Gerenciamento de Dados, você pode

- **Aos dados no local para acesso a dados híbrida** – você pode se conectar a dados locais para serviços de nuvem para tirar proveito dos serviços de nuvem enquanto mantém a empresa em funcionamento com dados no local.
- **Definir um proxy de proteger dados** – você pode definir quais fontes de dados locais são expostos com o Gateway de gerenciamento de dados para esse gateway autentica a solicitação de dados de serviços em nuvem e protege as fontes de dados local.
- **Gerencie o seu gateway para governança completa** – é fornecidos com o monitoramento e registro em log de todas as atividades dentro do Gateway de gerenciamento de dados para gerenciamento e controle total.
- **Mover dados com eficiência** – os dados são transferidos em paralelo, problemas de rede flexível a intermitentes com automaticamente a lógica de repetição.


O Gateway de Gerenciamento de Dados tem uma gama completa de recursos de conexão de dados locais que incluem o seguinte:

- **Não-intrusiva para firewall corporativo** – Data Management Gateway funciona apenas após a instalação, sem precisar abrir um firewall de conexão ou exigir intrusiva muda para sua infraestrutura de rede corporativa. 
- **Criptografar credenciais com seu certificado** – as credenciais usadas para conectar-se a fontes de dados são criptografadas com um certificado totalmente pertencente a um usuário. Sem o certificado, ninguém pode descriptografar as credenciais em texto sem formatação, incluindo a Microsoft.

### Considerações para usar o Gateway de Gerenciamento de Dados
1.	Uma única instância do Gateway de gerenciamento de dados pode ser usado para várias fontes de dados no local, mas tenha em mente que um **gateway está vinculada a uma fábrica de dados do Azure** e não pode ser compartilhada com outro alocador de dados.
2.	Você pode ter **apenas uma instância do Gateway de gerenciamento de dados** instalado em seu computador. Suponha, você tem dois data factories que precisam acessar fontes de dados locais e precisa instalar gateways nos dois computadores locais onde cada gateway é vinculado a um data factory separado.
3.	O **gateway não precisa estar no mesmo computador como a fonte de dados**, mas mantendo mais próximo à fonte de dados reduz o tempo para o gateway para se conectar à fonte de dados. Recomendamos que você instale o gateway em um computador diferente daquele que hospeda a fonte de dados no local para que o gateway não disputam os recursos com a fonte de dados.
4.	Você pode ter **vários gateways em diferentes computadores conectados à mesma fonte de dados no local**. Por exemplo, você pode ter dois gateways servindo duas fábricas de dados, mas a mesma fonte de dados local é registrado com as fábricas do dados. 
5.	Se você já tiver um gateway instalado no seu servidor do computador uma **Power BI** cenário, instale um **gateway separado para fábrica de dados do Azure** em outra máquina.

### Portas e considerações de segurança 
- O programa de instalação do Gateway de gerenciamento de dados abre **8050** e **8051** portas na máquina do gateway. Essas portas são usadas pelo **Gerenciador de credenciais** (aplicativo ClickOnce), que permite que você definir credenciais para um serviço local vinculado e para testar a conexão com a fonte de dados. Essas portas não podem ser acessadas da internet e você não precisa tiver esses aberta no firewall corporativo.
- Ao copiar dados de/para um banco de dados do SQL Server no local de um banco de dados do SQL Azure, verifique o seguinte:
 
	- Firewall no computador gateway permite comunicação TCP de saída em **TCP** porta **1433**
	- Configurar [configurações do firewall do SQL Azure](https://msdn.microsoft.com/library/azure/jj553530.aspx) para adicionar o **endereço IP do computador gateway** para o **endereços IP permitidos**.

- Ao copiar dados para/de local do SQL Server para um destino e o gateway e máquinas do SQL Server forem diferentes, faça o seguinte: [Configurar o Firewall do Windows](https://msdn.microsoft.com/library/ms175043.aspx) no SQL Server do computador para que o gateway possa acessar o banco de dados por meio de portas que a instância do SQL Server escuta. Para a instância padrão, é a porta 1433.

- Você deve iniciar o **Gerenciador de credenciais** aplicativo em um computador que pode conectar-se para o Gateway de gerenciamento de dados para poder definir credenciais da fonte de dados e para testar a conexão com a fonte de dados.

### Instalação do gateway - pré-requisitos 

1.	Com o suporte **sistema operacional** versões são Windows 7, Windows 8/8.1, Windows Server 2008 R2, Windows Server 2012.
2.	Recomendado **configuração** para o gateway de máquina é pelo menos 2 GHz, 4 núcleos, 8 GB de RAM e 80 GB de disco.
3.	Se a máquina host hiberna, o gateway não será capaz de responder às solicitações de dados. Portanto, configure um apropriado **plano de energia** no computador antes de instalar o gateway. A instalação do gateway solicita uma mensagem se o computador estiver configurado para hibernar.  


 

## Passo a passo

Neste passo a passo, você pode criar um data factory com um pipeline que move dados de um banco de dados SQL Server local para um blob do Azure.

## Etapa 1: Criar uma fábrica de dados do Azure
Nesta etapa, você deve usar o Portal de gerenciamento para criar uma instância de fábrica de dados do Azure denominada **ADFTutorialOnPremDF**. Você também pode criar um data factory usando os cmdlets do Data Factory do Azure.

1.	Após fazer logon na [Azure Preview Portal][azure-preview-portal], clique em **novo** no canto inferior esquerdo, selecione **análises de dados** no **criar** blade e clique em **Data Factory** no **análises de dados** lâmina.

	![Novo -> DataFactory][image-data-factory-new-datafactory-menu]
  
6. No **nova fábrica de dados** blade:
	1. Digite **ADFTutorialOnPremDF** para o **nome**.
	2. Clique em **nome do grupo de recursos** e selecione **ADFTutorialResourceGroup** (se você tivesse feito do tutorial em [Introdução ao Azure Data Factory][adfgetstarted]. Você pode selecionar um grupo de recursos existente ou crie um novo. Para criar um novo grupo de recursos:
		1. Clique em **criar um novo grupo de recursos**.
		2. No **lâmina do grupo de recursos criar**, insira um **nome** para o grupo de recursos e clique em **OK**.

7. Observe que **Adicionar ao quadro inicial** é verificada a **nova fábrica de dados** lâmina.

	![Adicionar ao quadro inicial][image-data-factory-add-to-startboard]

8. No **nova fábrica de dados** lâmina, clique em **criar**.

	> [AZURE.NOTE]**Nome da fábrica de dados "ADFTutorialOnPremDF" não está disponível**  
9. Procure as notificações do processo de criação no **notificações** hub à esquerda. Clique em **X** para fechar o **notificações** lâmina se ele estiver aberto.

	![Hub de notificações][image-data-factory-notifications-hub]

11. Após a conclusão da criação, você verá o **Data Factory** lâmina conforme mostrado abaixo:

	![Página inicial da fábrica de dados][image-data-factory-datafactory-homepage]

## Etapa 2: Criar um gateway de gerenciamento de dados
5.	Sobre o **fábrica de dados** lâmina para **ADFTutorialOnPremDF**, clique em **Serviços vinculados**. 

	![Página inicial da fábrica de dados][image-data-factory-home-age]

2.	Sobre o **Serviços vinculados** lâmina, clique em **+ gateway dados**.

	![Serviços vinculados - adicionar um botão de Gateway][image-data-factory-linkedservices-add-gateway-button]

2. No **criar** lâmina, digite **adftutorialgateway** para o **nome**, e clique em **OK**.

	![Criar a lâmina de Gateway][image-data-factory-create-gateway-blade]

3. Na folha **Configurar**, clique em **Instalar diretamente neste computador**. Isso baixará o pacote de instalação para o gateway, instalar, configurar e registrar o gateway no computador.

	> [AzURE.NOTE]Use o Internet Explorer ou um navegador da web compatível com Microsoft ClickOnce.

	![Gateway - Configurar pá][image-data-factory-gateway-configure-blade]

	Essa é a maneira mais fácil (um clique) de baixar, instalar, configurar e registrar o gateway em uma única etapa. Você pode ver o **Microsoft Data Management Gateway Configuration Manager** aplicativo está instalado no seu computador. Você também pode encontrar o executável **ConfigManager.exe** na pasta: **C:\Program Files\Microsoft Data Management Gateway\1.0\Shared**.

	Também pode baixar e instalar o gateway manualmente usando os links nessa lâmina e registrá-lo usando a chave que mostra o **com chave de registo** caixa de texto.
	
	Consulte [Data Management Gateway](#DMG) seção para obter detalhes sobre o gateway, incluindo as práticas recomendadas e considerações importantes.

	>[AZURE.NOTE]Você deve ser um administrador no computador local para instalar e configurar o Gateway de gerenciamento de dados com êxito. Você pode adicionar outros usuários para o grupo local usuários de Gateway de gerenciamento de dados do Windows. Os membros desse grupo poderão usar a ferramenta Gerenciador de configuração de Gateway de gerenciamento de dados para configurar o gateway.

4. Clique o **notificações** hub à esquerda. Aguarde até que você veja **Express para 'adftutorialgateway' com êxito a instalação** mensagem no **notificações** lâmina.

	![Instalação expressa bem-sucedida][express-setup-succeeded]
5. Clique em **OK** no **criar** blade e, em seguida, o **novo gateway de dados** blade.
6. Feche o **Serviços vinculados** blade (pressionando **X** botão no canto superior direito) e reabra o **Serviços vinculados** lâmina para ver o status mais recente do gateway. 
7. Confirme se o **status** do gateway é **Online**. 

	![Status do gateway][gateway-status]
5. Iniciar **Microsoft Data Management Gateway Configuration Manager** aplicativo em seu computador.

	![Gerenciador de configuração de gateway][image-data-factory-gateway-configuration-manager]

6. Aguarde até que os valores sejam definidos da seguinte maneira:
	1. Se o serviço **status** não está definido como **iniciado**, clique em **Iniciar serviço** para iniciar o serviço e aguarde um minuto para os outros campos atualizar.
	2. **Nome do gateway** é definido como **adftutorialgateway**.
	3. **Nome da instância** é definido como **adftutorialgateway**.
	4. **Status da chave do gateway** é definido como **registrados**.
	5. O status exibe a parte inferior da barra **conectado ao serviço de nuvem do Gateway de gerenciamento de dados** juntamente com um **marca de seleção verde**.
	
7. Sobre o **Serviços vinculados** lâmina, confirme se o **status** do gateway é **boa**.
8. Feche todos os blades até chegar ao **Data Factory** página inicial. 

## Etapa 2: Criar serviços vinculados 
Nesta etapa, você criará dois serviços vinculados: **StorageLinkedService** e **SqlServerLinkedService**. O **SqlServerLinkedService** vincula um banco de dados do SQL Server local e o **StorageLinkedService** service vinculado vincula um armazenamento de BLOBs do Azure para o **ADFTutorialDataFactory**. Você criará um pipeline posteriormente neste passo a passo que copia dados do banco de dados SQL Server local para o armazenamento de blob do Azure.

### Adicionar um serviço vinculado a um banco de dados SQL Server local
1.	No **DATA FACTORY** lâmina, clique em **autor e implantar** lado a lado para iniciar o **Editor** da fábrica de dados.

	![Bloco Criar e implantar][image-author-deploy-tile]

	> [AZURE.NOTE][Editor de fábrica de dados][data-factory-editor]
2.	No **Editor**, clique em **novo armazenamento de dados** botão na barra de ferramentas e selecione **banco de dados local SQL server** no menu suspenso. 

	![Botão de repositório de dados novo editor][image-editor-newdatastore-onpremsql-button]
    
3.	Você deve ver o modelo JSON para criar um serviço do SQL Server vinculado no local no painel direito. ![Local SQL vinculado Service - configurações][image-editor-newdatastore-onpremsql-settings]

4.	Faça o seguinte no painel de JSON:
	1.	Para o **gatewayName** propriedade, digite **adftutorialgateway** para substituir todo o texto dentro de aspas duplas.  
	2.	Se você estiver usando **autenticação SQL**: 
		1.	Para o **connectionString** propriedade, substitua **< nome_do_servidor >**, **< nomedobancodedados >**, **< username >**, e **< senha >** com nomes de seu SQL server no local, o banco de dados, a conta de usuário e a senha.	
		2.	Remover as duas últimas propriedades (* * * * de nome de usuário e **senha**) o JSON de arquivo e remover o **vírgula (,)** caracteres ao final da última linha do script JSON restante.
		
				{
	    			"name": "SqlServerLinkedService",
	    			"properties": {
		        		"type": "OnPremisesSqlLinkedService",
		        		"ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
		        		"gatewayName": "adftutorialgateway"
	    			}
				}
	3.	Se você estiver usando **autenticação do Windows**:
		1. Para o **connectionString** propriedade, substitua **< servername >** e **< nomedobancodedados >** com nomes de seu banco de dados e o SQL server no local. Definir **segurança integrada** para **True**. Remover **ID** e **senha** da cadeia de conexão.
			
				{
    				"name": "SqlServerLinkedService",
    				"properties": {
        				"type": "OnPremisesSqlLinkedService",
        				"ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
		   				"gatewayName": "adftutorialgateway",
				        "username": "<Specify user name if you are using Windows Authentication>",
				        "password": "<Specify password for the user account>"
    				}
				}		
		
6. Clique em **Deploy** na barra de ferramentas para implantar o SqlServerLinkedService. Confirme que você vê a mensagem **VINCULADO serviço criado com êxito** na barra de título. Você também verá o **SqlServerLinkedService** na exibição de árvore à esquerda.
		   
	![SqlServerLinkedService implantação bem-sucedida][image-editor-sql-linked-service-successful]
	
  
> [AZURE.NOTE]Você também pode criar um serviço do SQL Server vinculado clicando **novo armazenamento de dados** botão o **Serviços vinculados** lâmina. Se você seguir esse caminho, defina as credenciais da fonte de dados usando o aplicativo ClickOnce de Gerenciador de credenciais que é executado no computador que está acessando o portal. Se você acessar o portal de um computador diferente do computador do gateway, você deve garantir que o aplicativo Gerenciador de credenciais pode se conectar à máquina do gateway. Se o aplicativo não pode acessar a máquina de gateway, ele não permitirá que você definir credenciais da fonte de dados e para testar a conexão à fonte de dados.

#### Adicionar um serviço vinculado para uma conta de armazenamento do Azure
 
1. No **Editor**, clique em **novo armazenamento de dados** botão na barra de ferramentas e selecione **armazenamento do Azure** no menu suspenso. Você deve ver o modelo JSON para criar um serviço de armazenamento do Azure vinculado no painel à direita. 

	![Botão de repositório de dados novo editor][image-editor-newdatastore-button]
    
6. Substitua **< nome_da_conta >** e **< accountkey >** com o nome de conta e valores de chave de conta para sua conta de armazenamento do Azure.

	![Armazenamento de Blob do Editor de JSON][image-editor-blob-storage-json]
	
	> [AZURE.NOTE]Consulte [referência de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971) para obter detalhes sobre as propriedades JSON.

6. Clique em **Deploy** na barra de ferramentas para implantar o StorageLinkedService. Confirme que você vê a mensagem **VINCULADO serviço criado com êxito** na barra de título.

	![Implantar o armazenamento de Blob do Editor][image-editor-blob-storage-deploy]

 
## Etapa 3: Criar a entrada e saída de conjuntos de dados
Nesta etapa, você criará conjuntos de dados de entrada e saída que representam dados de entrada e saída da operação de cópia (banco de dados SQL Server local = > armazenamento de blob do Azure). Antes de criar conjuntos de dados ou tabelas (conjuntos de dados retangulares), você precisa fazer o seguinte (etapas detalhadas seguem a lista):

- Criar uma tabela chamada **emp** no banco de dados SQL Server é adicionado como um serviço vinculado aos dados fábrica e inserir alguns exemplos de entradas na tabela.
- - Se você não passou por meio do tutorial em [Introdução ao Azure Data Factory][adfgetstarted] artigo, crie um contêiner de blob denominado **adftutorial** adicionado como um serviço vinculado à fábrica de dados de conta de armazenamento de blob no Azure.

### Preparar o SQL Server local para o tutorial

1. No banco de dados especificado para o local do SQL Server vinculada serviço (* * SqlServerLinkedService * *), use o seguinte script SQL para criar a **emp** tabela no banco de dados.


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

1.	No **dados fábrica de Editor**, clique em **novo conjunto de dados** na barra de comandos e clique **SQL local**. 
2.	Substitua o JSON no painel à direita com o seguinte texto:    

        {
    		"name": "EmpOnPremSQLTable",
    		"properties":
    		{
        		"location":
        		{
            		"type": "OnPremisesSqlServerTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "SqlServerLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1,       
	    			"waitOnExternal":
	    			{
        				"retryInterval": "00:01:00",
	        			"retryTimeout": "00:10:00",
	        			"maximumRetry": 3
	    			}
		  
        		}
    		}
		}

	Observe o seguinte:
	
	- local **tipo** é definido como **OnPremisesSqlServerTableLocation**.
	- **tableName** é definido como **emp**.
	- **linkedServiceName** é definido como **SqlServerLinkedService** (você tivesse criado esse serviço vinculado na etapa 2).
	- Para uma tabela de entrada não é gerada por outra tubulação na fábrica de dados do Azure, você deve especificar **waitOnExternal** seção em JSON. Ela indica que os dados de entrada são produzidos externos ao serviço Data Factory do Azure.   

	Consulte [referência de script JSON][json-script-reference] para obter detalhes sobre as propriedades JSON.

2. Clique em **Deploy** na barra de comando para implantar o conjunto de dados (tabela é um conjunto de dados retangular). Confirme que você vê uma mensagem na barra de título que diz **tabela IMPLANTADO com êxito**.


### Criar tabela de saída

1.	No **dados fábrica de Editor**, clique em **novo conjunto de dados** na barra de comandos e clique **armazenamento de BLOBs do Azure**.
2.	Substitua o JSON no painel à direita com o seguinte texto: 

		{
    		"name": "OutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/outfromonpremdf",
            		"format":
            		{
                		"type": "TextFormat",
                		"columnDelimiter": ","
            		},
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1
        		}
    		}
		}
  
	Observe o seguinte:
	
	- local **tipo** é definido como **AzureBlobLocation**.
	- **linkedServiceName** é definido como **StorageLinkedService** (você tivesse criado esse serviço vinculado na etapa 2).
	- **folderPath** é definido como **adftutorial/outfromonpremdf** onde outfromonpremdf é a pasta no contêiner adftutorial. Você precisa apenas criar o **adftutorial** contêiner.
	- O **disponibilidade** é definido como **por hora** (* * frequência * * definido como **hora** e **intervalo** definida como **1**). O serviço de dados Factory irá gerar uma fatia de dados de saída cada hora o **emp** tabela no banco de dados do SQL Azure. 

	Se você não especificar um **fileName** para um **tabela de entrada**, todos os arquivos/blobs da pasta entrada (* * folderPath * *) são considerados como entradas. Se você especificar um nome de arquivo em JSON, apenas arquivo/blob especificado será considerado como entrada de asn. Consulte os arquivos de exemplo de [tutorial][adf-tutorial] para obter exemplos.
 
	Se você não especificar um **fileName** para uma **tabela de saída**, os arquivos gerados no **folderPath** serão nomeados no seguinte formato: Data.<Guid>.txt (por exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt).

	Para definir **folderPath** e **fileName** dinamicamente com base no **SliceStart** tempo, use a propriedade partitionedBy. No exemplo a seguir, folderPath usa o ano, mês e dia de SliceStart (hora de início da fatia que está sendo processada) e fileName usa a hora de SliceStart. Por exemplo, se uma fatia é produzida para 2014-10-20T08:00:00, o folderName é definido como wikidatagateway/wikisampledataout/2014/10/20 e o fileName é definido como 08.csv.

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	Consulte [referência de script JSON][json-script-reference] para obter detalhes sobre as propriedades JSON.

2.	Clique em **Deploy** na barra de comando para implantar o conjunto de dados (tabela é um conjunto de dados retangular). Confirme que você vê uma mensagem na barra de título que diz **tabela IMPLANTADO com êxito**.
  

## Etapa 4: Criar e executar um pipeline
Nesta etapa, você cria um **pipeline** com um **cópia atividade** que usa **EmpOnPremSQLTable** como entrada e **OutputBlobTable** como saída.

1.	Clique em **novo pipeline** na barra de comandos. Se você não vir o botão, clique em **... (reticências)** para expandir a barra de comandos.
2.	Substitua o JSON no painel à direita com o seguinte texto:   


        {
			"name": "ADFTutorialPipelineOnPrem",
    		"properties":
    		{
        		"description" : "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
	       		 "activities":
	        	[
			    	{
						"name": "CopyFromSQLtoBlob",
						"description": "Copy data from on-prem SQL server to blob",		
						"type": "CopyActivity",
						"inputs": [ {"name": "EmpOnPremSQLTable"} ],
						"outputs": [ {"name": "OutputBlobTable"} ],
						"transformation":
						{
							"source":
							{                               
								"type": "SqlSource",
								"sqlReaderQuery": "select * from emp"
							},
							"sink":
							{
								"type": "BlobSink"
							}	
						},
						"Policy":
						{
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
 
	- Na seção de atividades, há somente atividade cuja **tipo** é definido como **CopyActivity**.
	- **Entrada** a atividade é definida como **EmpOnPremSQLTable** e **saída** a atividade é definida como **OutputBlobTable**.
	- No **transformação** seção, **SqlSource** é especificado como o **tipo de fonte de** e **BlobSink **é especificado como o **tipo de PIA**. - Consulta SQL **Selecionar * EMP** é especificado para o **sqlReaderQuery** propriedade **SqlSource**.

	> [AZURE.NOTE]Substitua o valor da **Iniciar** propriedade com o dia atual e **final** valor com o dia seguinte. Ambos começam e datetimes final deve estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. O **final** tempo é opcional, mas usaremos neste tutorial. Se você não especificar o valor para o **final** propriedade, ele é calculado como "* * início + 48 horas * *". Para executar o pipeline indefinidamente, especifique **9/9/9999** como o valor para o **final** propriedade. Você está definindo a duração de tempo em que as fatias de dados serão processadas com base no **disponibilidade** propriedades que foram definidas para cada tabela de fábrica de dados do Azure. No exemplo acima, como cada fatia de dados é produzida por hora, haverá 24 fatias de dados.
	
2. Clique em **Deploy** na barra de comando para implantar o conjunto de dados (tabela é um conjunto de dados retangular). Confirme que você vê uma mensagem na barra de título que diz **PIPELINE IMPLANTADO com êxito**.
5. Agora, feche o **Editor** lâmina clicando **X**. Clique em **X** novamente para fechar a lâmina de ADFTutorialDataFactory com o modo de exibição de árvore e barra de ferramentas. Se você vir **suas edições não salvas serão descartadas** clique em **OK**.
6. Seja para o **DATA FACTORY** lâmina para o **ADFTutorialOnPremDF**.

**Parabéns!** Você criou um data factory do Azure, serviços vinculados, tabelas e uma pipeline e a pipeline agendada com êxito.

### Exibir a fábrica de dados em um modo de exibição de diagrama 
1. No **Azure Preview Portal**, clique em **diagrama** lado a lado na página inicial para o **ADFTutorialOnPremDF** fábrica dados.:

	![Link do diagrama][image-data-factory-diagram-link]

2. Você deverá ver o diagrama semelhante ao seguinte:

	![Exibição de diagrama][image-data-factory-diagram-view]

	Você pode aumentar o zoom, zoom, aplicar zoom de 100%, zoom ajustar automaticamente posicionar pipelines e tabelas e mostrar informações de linhagem (realça itens upstream e downstream dos itens selecionados). Você pode blick de duplo em um objeto (tabela de entrada/saída ou pipeline) para ver as propriedades para ele.

## Etapa 5: Monitorar os conjuntos de dados e os pipelines
Nesta etapa, você utilizará o Portal do Azure para monitorar o que está acontecendo em um data factory do Azure. Você também pode usar os cmdlets do PowerShell para monitorar conjuntos de dados e pipelines. Para obter detalhes sobre como usar os cmdlets para monitoramento, consulte [monitorar e gerenciar um Azure Data Factory usando o PowerShell][monitor-manage-powershell].

1. Navegue até **Azure Preview Portal** (se você tiver fechado)
2. Se a lâmina de **ADFTutorialOnPremDF** não estiver aberto, abra-o clicando em **ADFTutorialOnPremDF** sobre o **quadro inicial**.
3. Você deve ver o **contagem** e **nomes** de tabelas e pipeline criado nessa lâmina.

	![Página inicial da fábrica de dados][image-data-factory-homepage-2]
4. Agora, clique em **conjuntos de dados** lado a lado.
5. Sobre o **conjuntos de dados** lâmina, clique o **EmpOnPremSQLTable**.

	![Fatias de EmpOnPremSQLTable][image-data-factory-onprem-sqltable-slices]

6. Observe que as fatias de dados até a hora atual já foram produzidas e são **pronto**. Isso porque você inseriu os dados no banco de dados SQL Server e eles estão lá está o tempo todo. Confirme que nenhuma fatia aparecerão no **fatias problema** seção na parte inferior.


	Ambos **atualizado recentemente fatias** e **recentemente falha fatias** listas são classificadas pelo **hora da última atualização**. O tempo de atualização de uma fatia é alterado nas seguintes situações.
    

	-  Você atualiza o status da fatia manualmente, por exemplo, usando o **conjunto AzureDataFactorySliceStatus** (ou) clicando em **executar** no **FATIA** blade da fatia.
	-  A fatia é alterado devido a uma execução (por exemplo, uma execução de Introdução, uma execução finalizada e falha, uma execução terminou e foi bem-sucedida, etc).
 
	Clique no título da lista ou **... (reticências)** Para ver a lista de intervalos de maior. Clique em **filtro** na barra de ferramentas para filtrar as fatias.
	
	Para exibir as fatias de dados classificadas pelas horas de início/término fatia em vez disso, clique em **fatias de dados (por hora fatia)** lado a lado.

7. Agora, no **conjuntos de dados** lâmina, clique em **OutputBlobTable**.

	![Fatias de OputputBlobTable][image-data-factory-output-blobtable-slices]
8. Confirme que são produzidas fatias até a hora atual e **pronto**. Aguarde até que o status das fatias até a hora atual são definidos como **pronto**.
9. Clique em qualquer fatia de dados na lista e você deverá ver o **FATIA de dados** lâmina.

	![Blade de fatias de dados][image-data-factory-dataslice-blade]

	Se a fatia não está no **pronto** estado, você pode ver as fatias upstream que não estão prontos e estão bloqueando a fatia atual da execução no **fatias Upstream que não estão prontas** lista.

10. Clique no **atividade execute** da lista na parte inferior para ver **detalhes da execução de atividade**.

	![Blade de detalhes da execução de atividade][image-data-factory-activity-run-details]

11. Clique em **X** para fechar todos os blades até voltar na lâmina inicial para o **ADFTutorialOnPremDF**.
14. (opcional) Clique em **Pipelines**, clique em **ADFTutorialOnPremDF**, e Detalhar tabelas de entrada (* * consumido * *) ou tabelas de saída (* * Produced * *).
15. Use ferramentas como **Azure Storage Explorer** para verificar a saída.

	![Gerenciador de Armazenamento do Azure][image-data-factory-stroage-explorer]


## Criando e registrando um gateway usando o PowerShell do Azure 
Esta seção descreve como criar e registrar um gateway usando cmdlets do PowerShell do Azure.

1. Iniciar **PowerShell do Azure** no modo de administrador. 
2. Os cmdlets do Azure Data Factory estão disponíveis na **AzureResourceManager** modo. Execute o seguinte comando para alternar para o **AzureResourceManager** modo.     

        switch-azuremode AzureResourceManager


2. Use o **AzureDataFactoryGateway novo** cmdlet para criar um gateway a lógico da seguinte maneira:

		New-AzureDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**Comando de exemplo e saída**:


		PS C:> New-AzureDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

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


3. Use o **AzureDataFactoryGatewayKey novo** cmdlet para gerar uma chave de registro para o gateway recém-criado e armazenar a chave em uma variável local **$Key**:

		New-AzureDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName <dataFactoryName>

	
	**Exemplo de saída de comando:**


		PS C:> $Key = New-AzureDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. No Azure PowerShell, alterne para a pasta: **C:\Program Files\Microsoft Data Management Gateway\1.0\PowerShellScript** e executar **RegisterGateway.ps1** script associado à variável local **$Key** conforme o seguinte comando para registrar o agente cliente instalado em seu computador com o gateway lógico criar anteriormente.

		PS C:> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. Você pode usar o **Get-AzureDataFactoryGateway** para obter a lista de Gateways em sua fábrica de dados. Quando o **Status** mostra **on-line**, isso significa que o gateway está pronto para uso.

		Get-AzureDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

Você pode remover um gateway usando o **AzureDataFactoryGateway remover** descrição do cmdlet e a atualização para um gateway usando o **AzureDataFactoryGateway conjunto** cmdlets. Para sintaxe e outros detalhes sobre esses cmdlets, consulte Referência de Cmdlet de Data Factory.



[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-getstarted]: data-factory-get-started.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-editor]: data-factory-editor.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[64bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517623
[32bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517624

[azure-preview-portal]: http://portal.azure.com
[adfgetstarted]: data-factory-get-started.md
[monitor-manage-powershell]: data-factory-monitor-manage-using-powershell.md





[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456



[azure-powershell-install]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[image-author-deploy-tile]: ./media/data-factory-use-onpremises-datasources/author-deploy-tile.png
[image-editor-newdatastore-button]: ./media/data-factory-use-onpremises-datasources/editor-newdatastore-button.png
[image-editor-blob-storage-json]: ./media/data-factory-use-onpremises-datasources/editor-blob-storage-settings.png
[image-editor-blob-storage-deploy]: ./media/data-factory-use-onpremises-datasources/editor-deploy-blob-linked-service.png
[image-editor-newdatastore-onpremsql-button]: ./media/data-factory-use-onpremises-datasources/editor-newdatastore-onpremsql-button.png
[image-editor-newdatastore-onpremsql-settings]: ./media/data-factory-use-onpremises-datasources/editor-onprem-sql-settings.png
[image-editor-sql-linked-service-successful]: ./media/data-factory-use-onpremises-datasources/editor-sql-linked-service-successful.png

[gateway-status]: ./media/data-factory-use-onpremises-datasources/gateway-status.png
[express-setup-succeeded]: ./media/data-factory-use-onpremises-datasources/express-setup-succeeded.png
[image-data-factory-onprem-new-everything]: ./media/data-factory-use-onpremises-datasources/OnPremNewEverything.png

[image-data-factory-onprem-datastorage-cache-backup]: ./media/data-factory-use-onpremises-datasources/OnPremDataStorageCacheBackup.png

[image-data-factory-onprem-datastorage-see-all]: ./media/data-factory-use-onpremises-datasources/OnPremDataStorageSeeAll.png

[image-data-factory-onprem-dataservices-blade]: ./media/data-factory-use-onpremises-datasources/OnPremDataServicesBlade.png

[image-data-factory-onprem-datafactory-preview-blade]: ./media/data-factory-use-onpremises-datasources/DataFactoryPreviewBlade.png

[image-data-factory-onprem-create-resource-group]: ./media/data-factory-use-onpremises-datasources/OnPremCreateResourceGroup.png

[image-data-factory-add-to-startboard]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataFactoryAddToStartboard.png

[image-data-factory-notifications-hub]: ./media/data-factory-use-onpremises-datasources/OnPremNotificationsHub.png

[image-data-factory-datafactory-homepage]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage.png

[image-data-factory-startboard]: ./media/data-factory-use-onpremises-datasources/OnPremStartboard.png

[image-data-factory-linkedservices-blade]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesBlade.png

[image-data-factory-linkedservices-add-datastore-button]: ./media/data-factory-use-onpremises-datasources/ONPremLinkedServicesAddDataStoreButton.png

[image-data-factory-new-datastore-blade]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataStoreBlade.png

[image-data-factory-get-storage-key]: ./media/data-factory-use-onpremises-datasources/OnPremGetStorageKey.png

[image-data-factory-linkedservices-add-gateway-button]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesAddGaewayButton.png

[image-data-factory-create-gateway-blade]: ./media/data-factory-use-onpremises-datasources/OnPremCreateGatewayBlade.png

[image-data-factory-gateway-configure-blade]: ./media/data-factory-use-onpremises-datasources/OnPremGatewayConfigureBlade.png

[image-data-factory-gateway-configuration-manager]: ./media/data-factory-use-onpremises-datasources/OnPremDMGConfigurationManager.png

[image-data-factory-oneclick-install]: ./media/data-factory-use-onpremises-datasources/OnPremOneClickInstall.png

[image-data-factory-diagram-link]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramLink.png

[image-data-factory-diagram-view]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramView.png

[image-data-factory-homepage-2]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage2.png

[image-data-factory-stroage-explorer]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageExplorer.png

[image-data-factory-home-age]: ./media/data-factory-use-onpremises-datasources/DataFactoryHomePage.png

[image-data-factory-onprem-sqltable-slices]: ./media/data-factory-use-onpremises-datasources/OnPremSQLTableSlicesBlade.png

[image-data-factory-output-blobtable-slices]: ./media/data-factory-use-onpremises-datasources/OutputBlobTableSlicesBlade.png

[image-data-factory-dataslice-blade]: ./media/data-factory-use-onpremises-datasources/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-use-onpremises-datasources/ActivityRunDetailsBlade.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-use-onpremises-datasources/NewDataFactoryMenu.png

[image-data-factory-preview-portal-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

<!---HONumber=GIT-SubDir-->