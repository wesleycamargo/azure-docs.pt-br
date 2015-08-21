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
	ms.date="07/10/2015" 
	ms.author="spelluru"/>

# Habilitar seus pipelines para trabalhar com dados locais

Para habilitar os pipelines em um data factory do Azure para trabalhar com uma fonte de dados local, você precisa adicionar a fonte de dados local como um serviço vinculado ao data factory usando o Portal de Gerenciamento do Azure ou o PowerShell do Azure.
 
Para poder adicionar uma fonte de dados local como um serviço vinculado a um data factory, você primeiro precisa baixar e instalar o Gateway de Gerenciamento de Dados da Microsoft em um computador local e configurar o serviço vinculado para a fonte de dados local para usar o gateway.


## <a href="DMG"></a> Gateway de gerenciamento de dados

O **Gateway de Gerenciamento de Dados** é um software que conecta fontes de dados locais a serviços de nuvem de maneira segura e gerenciada. Com o Gateway de Gerenciamento de Dados, você pode

- **Conectar-se a dados locais para acesso a dados híbridos** – Você pode conectar dados locais a serviços de nuvem para tirar proveito dos serviços de nuvem enquanto mantém a empresa em funcionamento com dados locais.
- **Definir um proxy de dados seguro** – Você pode definir quais fontes de dados locais são expostas com o Gateway de Gerenciamento de Dados para que esse gateway autentique a solicitação de dados de serviços de nuvem e proteja as fontes de dados locais.
- **Gerenciar seu gateway para controle completo** – Você recebe monitoramento completo e registro em log de todas as atividades dentro o Gateway de Gerenciamento de Dados para gerenciamento e controle.
- **Mover dados com eficiência** – Os dados são transferidos em paralelo, resilientes a problemas de rede intermitentes com lógica de repetição automática.


O Gateway de Gerenciamento de Dados tem uma gama completa de recursos de conexão de dados locais que incluem o seguinte:

- **Firewall não intrusivo a corporativo** – O Gateway de Gerenciamento de Dados funciona após a instalação, sem precisar abrir uma conexão de firewall ou exigir mudanças intrusivas à sua infraestrutura de rede corporativa. 
- **Criptografar credenciais com seu certificado** – As credenciais usadas para conectar-se a fontes de dados são criptografadas com um certificado totalmente pertencente a um usuário. Sem o certificado, ninguém pode descriptografar as credenciais em texto sem formatação, incluindo a Microsoft.

### Considerações para usar o Gateway de Gerenciamento de Dados
1.	Uma única instância do Gateway de Gerenciamento de Dados pode ser usada para várias fontes de dados locais, mas tenha em mente que um **gateway está associado a uma data factory do Azure** e não pode ser compartilhado com outra data factory.
2.	Você pode ter **apenas uma instância do Gateway de Gerenciamento de Dados** instalada no computador. Suponha, você tem dois data factories que precisam acessar fontes de dados locais e precisa instalar gateways nos dois computadores locais onde cada gateway é vinculado a um data factory separado.
3.	O **gateway não precisa estar no mesmo computador que a fonte de dados**, mas ficar mais próximo à fonte de dados reduz o tempo para o gateway se conectar à fonte de dados. Recomendamos que você instale o gateway em um computador diferente daquele que hospeda a fonte de dados local para que o gateway não dispute os recursos com a fonte de dados.
4.	Você pode ter **vários gateways em diferentes computadores conectados à mesma fonte de dados local**. Por exemplo, você pode ter dois gateways servindo duas data factories, mas a mesma fonte de dados local é registrada com ambas as data factories. 
5.	Se você já tiver um gateway instalado no computador atendendo um cenário do **Power BI**, instale um **gateway separado para o Azure Data Factory** em outro computador.

### Considerações de segurança e sobre portas 
- O programa de instalação do Gateway de gerenciamento de dados abre as portas **8050** e **8051** no computador do gateway. Essas portas são usadas pelo **Gerenciador de credenciais** (aplicativo ClickOnce), que permite que você defina credenciais para um serviço vinculado local e teste a conexão à fonte de dados. Essas portas não podem ser acessadas da Internet e você não precisa mantê-las abertas no firewall corporativo.
- Ao copiar dados de/para um banco de dados do SQL Server local de um banco de dados SQL do Azure, certifique-se do seguinte:
 
	- Que o firewall no computador do gateway permite a comunicação TCP de saída na porta **TCP** **1433**
	- Defina as [Configurações de firewall do Azure SQL](https://msdn.microsoft.com/library/azure/jj553530.aspx) para adicionar o **endereço IP do computador do gateway** aos **endereços IP permitidos**.

- Ao copiar dados para/de um SQL Server local para qualquer destino quando os computadores do gateway e do SQL Server forem diferentes, faça o seguinte: [configure o Firewall do Windows](https://msdn.microsoft.com/library/ms175043.aspx) no computador do SQL Server para que o gateway possa acessar o banco de dados por meio de portas que a instância do SQL Server escuta. Para a instância padrão, é a porta 1433.

- Você deve, para ser capaz de definir credenciais da fonte de dados e para testar a conexão com a fonte de dados, iniciar o aplicativo **Gerenciador de credenciais** em um computador que possa se conectar ao Gateway de gerenciamento de dados.

### Instalação do gateway - pré-requisitos 

1.	As versões do **sistema operacional** com suporte são Windows 7, Windows 8/8.1, Windows Server 2008 R2, Windows Server 2012.
2.	A **configuração** recomendada para o computador do gateway é no mínimo 2 GHz, 4 núcleos, 8 GB de RAM e 80 GB de disco.
3.	Se o computador host hibernar, o gateway não poderá responder a solicitações de dados. Portanto, configure um **plano de energia** adequado no computador antes de instalar o gateway. A instalação do gateway exibe uma mensagem se o computador estiver configurado para hibernar.  

O Gateway de Gerenciamento de Dados serializa e desserializa dados de origem e do coletor no computador em que está hospedado. Ele também executa conversões de tipo nos dados enquanto estão sendo copiados. Durante a operação de cópia, o gateway lê dados da origem para um buffer de memória e ao mesmo tempo outro thread grava o buffer no coletor. Uma vez que vários trabalhos de atividade de cópia podem estar em execução ao mesmo tempo no computador host do gateway durante horários de pico, o consumo de memória e os recursos de CPU são muito maiores do que no tempo ocioso. Portanto, o computador host que executa um gateway de gerenciamento de dados pode precisar de mais recursos do que os descritos na configuração mínima de computador sugerida acima ou menos recursos durante o tempo ocioso.


## Passo a passo

Neste passo a passo, você pode criar um data factory com um pipeline que move dados de um banco de dados SQL Server local para um blob do Azure.

## Etapa 1: criar uma data factory do Azure
Nesta etapa, você pode usar o Portal de Gerenciamento do Azure para criar uma instância do Azure Data Factory denominada **ADFTutorialOnPremDF**. Você também pode criar um data factory usando os cmdlets do Data Factory do Azure.

1.	Após fazer logon no [Portal de Visualização do Azure][azure-preview-portal], clique em **NOVO** no canto inferior esquerdo, selecione **Análises de dados** na folha **Criar** e clique em **Data Factory** na folha **Análises de dados**.

	![Novo -> DataFactory][image-data-factory-new-datafactory-menu]
  
6. Na folha **Nova data factory**:
	1. Insira **ADFTutorialOnPremDF** como o **nome**.
	2. Clique em **NOME DO GRUPO DE RECURSOS** e selecione **ADFTutorialResourceGroup** (se você tiver realizado o tutorial de [Introdução ao Azure Data Factory][adfgetstarted]. Você pode selecionar um grupo de recursos existente ou crie um novo. Para criar um novo grupo de recursos:
		1. Clique em **Criar um novo grupo de recursos**.
		2. Na **folha Criar grupo de recursos**, insira um **nome** para o grupo de recursos e clique em **OK**.

7. Observe que a opção **Adicionar ao Quadro Inicial** está marcada na folha **Nova data factory**.

	![Adicionar ao quadro inicial][image-data-factory-add-to-startboard]

8. Na folha **Nova data factory**, clique em **Criar**.

	O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro: **O nome da data factory “ADFTutorialOnPremDF” não está disponível**, altere o nome da data factory (por exemplo, yournameADFTutorialOnPremDF) e tente criá-la novamente. Use esse nome em vez de ADFTutorialOnPremDF ao executar as etapas restantes neste tutorial.

9. Procure as notificações do processo de criação no hub **NOTIFICAÇÕES** à esquerda. Clique em **X** para fechar a folha **NOTIFICAÇÕES** se ela estiver aberta.

	![Hub de NOTIFICAÇÕES][image-data-factory-notifications-hub]

11. Após a conclusão da criação, você verá a folha **Data Factory**, conforme mostrado abaixo:

	![Página inicial do Data Factory][image-data-factory-datafactory-homepage]

## Etapa 2: Criar um gateway de gerenciamento de dados
5.	Na folha **Data Factory** de **ADFTutorialOnPremDF**, clique em **Serviços Vinculados**. 

	![Página inicial do Data Factory][image-data-factory-home-age]

2.	Na folha **Serviços Vinculados**, clique em **+ Gateway de dados**.

	![Serviços vinculados - botão Adicionar um Gateway][image-data-factory-linkedservices-add-gateway-button]

2. Na folha **Criar**, insira **adftutorialgateway** como **nome** e clique em **OK**.

	![Folha Criar Gateway][image-data-factory-create-gateway-blade]

3. Na folha **Configurar**, clique em **Instalar diretamente neste computador**. Isso baixará o pacote de instalação para o gateway, instalar, configurar e registrar o gateway no computador.

	> [AZURE.NOTE] Use o Internet Explorer ou um navegador da Web compatível com Microsoft ClickOnce.

	![Folha Gateway - Configurar][image-data-factory-gateway-configure-blade]

	Essa é a maneira mais fácil (um clique) de baixar, instalar, configurar e registrar o gateway em uma única etapa. Você pode ver que o aplicativo **Gerenciador de Configuração de Gateway de gerenciamento de dados da Microsoft** está instalado no computador. Você também pode encontrar o executável **ConfigManager.exe** na pasta: **C:\\Arquivos de Programas\\Microsoft Data Management Gateway\\1.0\\Shared**.

	Você também pode baixar e instalar o gateway manualmente usando os links nessa folha e registrá-lo usando a chave mostrada na caixa de texto **REGISTRAR COM CHAVE**.
	
	Consulte a seção [Gateway de Gerenciamento de Dados](#DMG) para obter detalhes sobre o gateway, incluindo as práticas recomendadas e considerações importantes.

	>[AZURE.NOTE] Você deve ser um administrador no computador local para instalar e configurar com êxito o Gateway de gerenciamento de dados. Você pode acrescentar usuários adicionais para o grupo local de usuários do Gateway de gerenciamento de dados do Windows. Os membros desse grupo poderão usar a ferramenta Gerenciador de configuração de Gateway de gerenciamento de dados para configurar o gateway.

4. Clique no hub **NOTIFICAÇÕES** à esquerda. Aguarde até que você veja a mensagem **Instalação expressa de “adftutorialgateway” bem-sucedida** na folha **Notificações**.

	![Instalação expressa bem-sucedida][express-setup-succeeded]
5. Clique em **OK** na folha **Criar** e, em seguida, na folha **Novo gateway de dados**.
6. Feche a folha **Serviços vinculados** (pressionando o botão **X** no canto superior direito) e reabra a folha **Serviços vinculados** para ver o status mais recente do gateway. 
7. Confirme se o **status** do gateway é **Online**. 

	![Status do gateway][gateway-status]
5. Inicie o aplicativo **Gerenciador de Configuração de Gateway de gerenciamento de dados da Microsoft** no computador.

	![Gerenciador de configuração de gateway][image-data-factory-gateway-configuration-manager]

6. Aguarde até que os valores sejam definidos da seguinte maneira:
	1. Se o **status** do serviço não estiver definido como **Iniciado**, clique em **Iniciar serviço** para iniciar o serviço e aguarde a atualização dos outros campos.
	2. **Nome do gateway** está definido como **adftutorialgateway**.
	3. **Nome da instância** está definido como **adftutorialgateway**.
	4. **Status da chave de gateway** está definido como **Registrado**.
	5. A barra de status inferior exibe **Conectado ao Serviço de Nuvem do Gateway de Gerenciamento de Dados** junto com uma **marca de seleção verde**.
	
7. Na folha **Serviços Vinculados**, confirme se o **status** do gateway é **Bom**.
8. Feche todas as folhas até chegar à página inicial do **Data Factory**. 

## Etapa 2: Criar serviços vinculados 
Nesta etapa, você criará dois serviços vinculados: **StorageLinkedService** e **SqlServerLinkedService**. O **SqlServerLinkedService** vincula um banco de dados SQL Server local e o serviço vinculado **StorageLinkedService** vincula um blob do Azure ao **ADFTutorialDataFactory**. Você criará um pipeline posteriormente neste passo a passo que copia dados do banco de dados SQL Server local para o armazenamento de blob do Azure.

### Adicionar um serviço vinculado a um banco de dados SQL Server local
1.	Na folha **DATA FACTORY**, clique no bloco **Criar e implantar** para iniciar o **Editor** para a data factory.

	![Bloco Criar e implantar][image-author-deploy-tile]

	Consulte o tópico [Editor Data Factory][data-factory-editor] para obter a visão geral detalhada do Editor Data Factory.

2.	No **Editor**, clique no botão **Novo armazenamento de dados** na barra de ferramentas e selecione **Banco de dados do SQL Server local** no menu suspenso.

	![Botão Novo repositório de dados do editor][image-editor-newdatastore-onpremsql-button]
    
3.	Você deve ver o modelo JSON para criar um serviço local do SQL Server vinculado no painel direito. ![Serviço vinculado SQL local - configurações][image-editor-newdatastore-onpremsql-settings]

4.	No painel JSON, faça o seguinte:
	1.	Para a propriedade **gatewayName**, digite **adftutorialgateway** para substituir todo o texto dentro das aspas duplas.  
	2.	Se você estiver usando **autenticação SQL**: 
		1.	Para a propriedade **connectionString**, substitua **<servername>**, **<databasename>**, **<username>**, e **<password>** por nomes de seu SQL Server, banco de dados, conta de usuário e senha locais. Para especificar um nome de instância, use o caractere de escape:. Por exemplo:**server\\instancename**. 	
		2.	Remova as duas últimas propriedades (**username** e **password**) do arquivo JSON e remova o caractere de **vírgula (,)** ao final da última linha do script JSON restante.
		
				{
				  "name": "SqlServerLinkedService",
				  "properties": {
				    "type": "OnPremisesSqlServer",
				    "typeProperties": {
				      "ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
				      "gatewayName": "adftutorialgateway"
				    }
				  }
				}
	3.	Se você está usando a **Autenticação do Windows**:
		1. Para a propriedade **connectionString**, substitua **<servername>** e **<databasename>** por nomes de seu SQL Server e banco de dados locais. Defina **Integrated Security** como **True**. Remova **ID** e **Password** da cadeia de conexão.
			
				{
				  "name": "SqlServerLinkedService",
				  "properties": {
				    "type": "OnPremisesSqlServer",
				    "typeProperties": {
				      "ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
				      "gatewayName": "adftutorialgateway",
				      "username": "<Specify user name if you are using Windows Authentication>",
				      "password": "<Specify password for the user account>"
				    }
				  }
				}		
		
6. Clique em **Implantar** na barra de ferramentas para implantar o SqlServerLinkedService. Confirme que você vê a mensagem **SERVIÇO VINCULADO CRIADO COM ÊXITO** na barra de título. Você também verá o **SqlServerLinkedService** no modo de exibição de árvore à esquerda.
		   
	![Implantação de SqlServerLinkedService bem-sucedida][image-editor-sql-linked-service-successful]
	
  
Você também pode criar um serviço vinculado do SQL Server clicando no botão **Novo armazenamento de dados** da barra de ferramentas, na folha **Serviços vinculados**. Se você seguir esse caminho, defina as credenciais para a fonte de dados usando o aplicativo ClickOnce Gerenciador de credenciais que é executado no computador que está acessando o portal. Se você acessar o portal de um computador diferente do computador do gateway, você deve garantir que o aplicativo Gerenciador de credenciais possa se conectar ao computador do gateway. Se o aplicativo não puder acessar o computador do gateway, ele não permitirá que você defina credenciais da fonte de dados teste a conexão à fonte de dados.

#### Adicionar um serviço vinculado para uma conta de armazenamento do Azure
 
1. No **Editor**, clique no botão **Novo armazenamento de dados** na barra de ferramentas e selecione **Armazenamento do Azure** no menu suspenso. Você deve ver o modelo JSON para criar um serviço de armazenamento vinculado do Azure no painel à direita. 

	![Botão Novo repositório de dados do editor][image-editor-newdatastore-button]
    
6. Substitua **<accountname>** e **<accountkey>** pelos valores de nome e de chave da sua conta de armazenamento do Azure.

	![JSON do armazenamento de Blob do editor][image-editor-blob-storage-json]
	
	Consulte a [Referência de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971) para obter detalhes sobre as propriedades JSON.

6. Clique em **Implantar** na barra de ferramentas para implantar o StorageLinkedService. Confirme que você vê a mensagem **SERVIÇO VINCULADO CRIADO COM ÊXITO** na barra de título.

	![Implantar armazenamento de Blob do editor][image-editor-blob-storage-deploy]

 
## Etapa 3: Criar conjuntos de dados de entrada e saída
Nesta etapa, você criará conjuntos de dados de entrada e saída que representam dados de entrada e saída da operação de cópia (banco de dados SQL Server local = > armazenamento de blob do Azure). Antes de criar conjuntos de dados ou tabelas (conjuntos de dados retangulares), você precisa fazer o seguinte (etapas detalhadas seguem a lista):

- Criar uma tabela chamada **emp** no banco de dados SQL Server adicionado como um serviço vinculado à data factory e inserir alguns exemplos de entradas na tabela.
- - - Se você ainda não verificou o tutorial do artigo [Introdução ao Azure Data Factory][adfgetstarted], crie um contêiner de blob chamado **adftutorial** na conta de armazenamento de blob do Azure adicionada à data factory como um serviço vinculado.

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

1.	No **Editor Data Factory**, clique em **Novo conjunto de dados** na barra de comandos e clique em **SQL local**. 
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
	- Para uma tabela de entrada que não é gerada por outro pipeline no Azure Data Factory, você deve especificar a propriedade **external** como **true**. Você pode, opcionalmente, especificar políticas na seção **externalData**.   

	Consulte a [Referência de script JSON][json-script-reference] para obter detalhes sobre as propriedades JSON.

2. Clique em **Implantar** na barra de comandos para implantar o conjunto de dados (a tabela é um conjunto de dados retangular). Confirme que você vê uma mensagem na barra de título que diz **TABELA IMPLANTADA COM ÊXITO**.


### Criar tabela de saída

1.	No **Editor Data Factory**, clique em **Novo conjunto de dados** na barra de comandos e clique em **Armazenamento de Blob do Azure**.
2.	Substitua o JSON no painel direito pelo texto a seguir: 

		{
		  "name": "OutputBlobTable",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
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
	
	- **type** é definido como **AzureBlob**.
	- **linkedServiceName** é definido como **StorageLinkedService** (você criou esse serviço vinculado na Etapa 2).
	- **folderPath** é definido como **adftutorial/outfromonpremdf**, em que outfromonpremdf é a pasta no contêiner adftutorial. Você precisa apenas criar o contêiner **adftutorial**.
	- A **availability** é definida como **hourly** (**frequency** definida como **hour** e **interval** definido como **1**). O serviço Data Factory gerará uma fatia de dados de saída a cada hora na tabela **emp** no banco de dados SQL do Azure. 

	Se você não especificar um **fileName** para uma **tabela de entrada**, todos os arquivos/blobs da pasta de entrada (**folderPath**) serão considerados como entradas. Se você especificar um nome de arquivo em JSON, apenas arquivo/blob especificado será considerado como entrada de asn. Consulte os arquivos do [tutorial][adf-tutorial] para obter exemplos.
 
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

 

	Consulte a [Referência de script JSON][json-script-reference] para obter detalhes sobre as propriedades JSON.

2.	Clique em **Implantar** na barra de comandos para implantar o conjunto de dados (a tabela é um conjunto de dados retangular). Confirme que você vê uma mensagem na barra de título que diz **TABELA IMPLANTADA COM ÊXITO**.
  

## Etapa 4: Criar e executar um pipeline
Nesta etapa, você criará um **pipeline** com uma **Atividade de Cópia** que usa **EmpOnPremSQLTable** como entrada e **OutputBlobTable** como saída.

1.	Clique em **Novo pipeline** na barra de comandos. Se você não puder ver o botão, clique em **... (reticências)** para expandir a barra de comandos.
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
 
	- Na seção atividades, há somente a atividade cujo **type** é definido como **Copy**.
	- A **entrada** da atividade é definida como **EmpOnPremSQLTable** e a **saída** da atividade é definida como **OutputBlobTable**.
	- Na seção **transformação**, **SqlSource** é especificado como o **tipo de fonte** e **BlobSink **é especificado como o **tipo de coletor**.
	- A consulta SQL **select * from emp** é especificada para a propriedade **sqlReaderQuery** de **SqlSource**.

	Substitua o valor da propriedade **início** pelo dia atual e o valor de **término** pelo dia seguinte. Ambos os valores de data/hora de início e de término devem estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. A hora de **end** é opcional, mas nós o usaremos neste tutorial.
	
	Se você não especificar o valor para a propriedade **end**, ele será calculado como "**início + 48 horas**". Para executar o pipeline indefinidamente, especifique **9/9/9999** como o valor da propriedade **end**.
	
	Especificando o período ativo de um pipeline, você está definindo a duração de tempo em que as fatias de dados serão processadas com base nas propriedades de **Disponibilidade** que foram definidas para cada tabelo da Azure Data Factory.
	
	No exemplo acima, como cada fatia de dados é produzida por hora, haverá 24 fatias de dados.
	
2. Clique em **Implantar** na barra de comandos para implantar o conjunto de dados (a tabela é um conjunto de dados retangular). Confirme que você vê uma mensagem na barra de título que diz **PIPELINE IMPLANTADO COM ÊXITO**.
5. Agora, feche a folha **Editor** clicando em **X**. Clique em **X** novamente para fechar a folha ADFTutorialDataFactory com o modo de exibição de árvore e a barra de ferramentas. Se você vir **suas edições não salvas serão descartadas**, clique em **OK**.
6. Você deve ser retornado à folha **DATA FACTORY** para o **ADFTutorialOnPremDF**.

**Parabéns!** Você criou um data factory do Azure, serviços vinculados, tabelas e uma pipeline e a pipeline agendada com êxito.

### Visualizar a data factory em um modo de exibição de diagrama 
1. No **Portal de Visualização do Azure**, clique no bloco **Diagrama** na página inicial da data factory **ADFTutorialOnPremDF**.

	![Link do diagrama][image-data-factory-diagram-link]

2. Você deverá ver o diagrama semelhante ao seguinte:

	![Exibição de diagrama][image-data-factory-diagram-view]

	É possível ampliar, reduzir, aplicar zoom de 100%, ajustar nível de zoom, posicionar pipelines e tabelas automaticamente, bem como mostrar informações de linhagem (realça itens upstream e downstream dos itens selecionados). Você pode clicar duas vezes em um objeto (pipeline ou tabela de entrada/saída) para ver as propriedades para ele.

## Etapa 5: Monitorar os conjuntos de dados e pipelines
Nesta etapa, você utilizará o Portal do Azure para monitorar o que está acontecendo em um data factory do Azure. Você também pode usar os cmdlets do PowerShell para monitorar conjuntos de dados e pipelines. Para obter detalhes sobre como utilizar os cmdlets para monitoramento, consulte [Monitorar e gerenciar a Azure Data Factory utilizando o PowerShell][monitor-manage-powershell].

1. Navegue até o **Portal de Visualização do Azure** (se você o fechou)
2. Se a folha para **ADFTutorialOnPremDF** não estiver aberta, abra-a clicando em **ADFTutorialOnPremDF** no **Quadro Inicial**.
3. Você verá a **contagem** e os **nomes** das tabelas e do pipeline criados nessa folha.

	![Página inicial do Data Factory][image-data-factory-homepage-2]
4. Agora, clique no bloco **Conjuntos de dados**.
5. Na folha **Conjuntos de Dados**, clique em **EmpOnPremSQLTable**.

	![Fatias de EmpOnPremSQLTable][image-data-factory-onprem-sqltable-slices]

6. Observe que as fatias de dados até o momento atual já foram produzidas e estão **Prontas**. Isso porque você inseriu os dados no banco de dados SQL Server e eles estão lá está o tempo todo. Confirme se nenhuma fatia aparecerá na seção **Fatias com problema** na parte inferior.


	Ambas as listas **Fatias atualizadas recentemente** e **Fatias com falha recente** são classificadas segundo o **HORÁRIO DA ÚLTIMA ATUALIZAÇÃO**. A hora de atualização de uma fatia é alterada nas situações a seguir.
    

	-  Você atualiza o status da fatia manualmente, por exemplo, usando o **Set-AzureDataFactorySliceStatus** (ou) clicando em **EXECUTAR** na folha **FATIA** da fatia.
	-  A fatia é alterada devido a uma execução (por exemplo, uma execução iniciada, uma execução finalizada e com falha, uma execução finalizada e bem-sucedida, etc).
 
	Clique no título das listas ou em **... (reticências)** para ver a lista maior de fatias. Clique em **Filtrar** na barra de ferramentas para filtrar as fatias.
	
	Para exibir, em vez disso, as fatias de dados classificadas pelas horas de início/término da fatia, clique no bloco **Fatias de dados (por horário da fatia)**.

7. Agora, na folha **Conjuntos de Dados**, clique em **OutputBlobTable**.

	![Fatias de OputputBlobTable][image-data-factory-output-blobtable-slices]
8. Confirme se as fatias até a hora atual estão produzidas e **Prontas**. Aguarde até que o status das fatias até a hora atual estar definido como **Prontas**.
9. Clique em qualquer fatia de dados na lista e você deverá ver a folha **FATIA DE DADOS**.

	![Folha Fatia de dados][image-data-factory-dataslice-blade]

	Se a fatia não está no estado **Pronto**, você pode ver as fatias upstream que não estão Prontas e estão impedindo a execução da fatia atual na lista **Fatias upstream que não estão prontas**.

10. Clique na **execução de atividade** na lista na parte inferior para ver **detalhes de execução da atividade**.

	![Folha Detalhes da execução de atividade][image-data-factory-activity-run-details]

11. Clique em **X** para fechar todos as folhas até que você volte à folha de base para **ADFTutorialOnPremDF**.
14. (opcional) Clique em **Pipelines**, clique em **ADFTutorialOnPremDF** e execute uma consulta drill-through das tabelas de entrada (**Consumidas**) ou das tabelas de saída (**Produzidas**).
15. Use ferramentas como o **Explorador de Armazenamento do Azure** para verificar a saída.

	![Gerenciador de Armazenamento do Azure][image-data-factory-stroage-explorer]


## Criando e registrando um gateway usando o PowerShell do Azure 
Esta seção descreve como criar e registrar um gateway usando cmdlets do PowerShell do Azure.

1. Inicie o **PowerShell do Azure** no modo de administrador. 
2. Os cmdlets do Azure Data Factory estão disponíveis no modo **AzureResourceManager**. Execute o seguinte comando para alternar para o modo **AzureResourceManager**.     

        switch-azuremode AzureResourceManager


2. Use o cmdlet **New-AzureDataFactoryGateway** para criar um gateway lógico da seguinte maneira:

		New-AzureDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**Exemplo de comando e saída**:


		PS C:\> New-AzureDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

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


3. Use o cmdlet **New-AzureDataFactoryGatewayKey** para gerar uma chave de registro para o gateway recém-criado e armazenar a chave em uma variável local **$Key**:

		New-AzureDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName <dataFactoryName>

	
	**Exemplo de saída de comando:**


		PS C:\> $Key = New-AzureDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. No Azure PowerShell, alterne para a pasta **C:\\Arquivos de Programas\\Microsoft Data Management Gateway\\1.0\\PowerShellScript\\** e execute o script **RegisterGateway.ps1** associado à variável local **$Key** conforme mostrado no seguinte comando para registrar o agente cliente instalado no computador com o gateway lógico criado anteriormente.

		PS C:\> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. Você pode usar o cmdlet **Get-AzureDataFactoryGateway** para obter a lista de gateways na data factory. Quando o **Status** mostra **online**, isso significa que seu gateway está pronto para uso.

		Get-AzureDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

Você pode remover um gateway usando o cmdlet **Remove-AzureDataFactoryGateway** e atualizar a descrição de um gateway usando os cmdlets **Set-AzureDataFactoryGateway**. Para sintaxe e outros detalhes sobre esses cmdlets, consulte Referência de Cmdlet de Data Factory.



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

<!---HONumber=August15_HO6-->