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
	ms.date="10/29/2015" 
	ms.author="spelluru"/>

# Mover dados entre fontes locais e nuvem com o Gateway de Gerenciamento de Dados
Um dos desafios da integração de dados moderna é mover dados continuamente para e do local para a nuvem. A data factory torna essa integração contínua com o gateway de gerenciamento de dados. O gateway de gerenciamento de data factory é um agente que você pode instalar localmente para habilitar pipelines híbridos.

Este artigo fornece uma visão geral da integração de armazenamentos de dados locais com armazenamentos de dados em nuvem e processamento de nuvem usando a data factory. Este artigo se baseia no artigo [Data Movement Activities (Atividades de movimentação de dados)](data-factory-data-movement-activities.md) e outros artigos de conceitos principais de data factory. A visão geral a seguir pressupõe que você esteja familiarizado com conceitos de data factory como pipelines, atividades, conjuntos de dados e a atividade de cópia.

O gateway de dados fornece as seguintes funcionalidades:

1.	Modelar fontes de dados locais e fontes de dados em nuvem na mesma data factory e mover dados.
2.	Ter um único painel de monitoramento e gerenciamento com visibilidade do status do gateway com o painel de nuvem da data factory.
3.	Gerenciar o acesso a fontes de dados locais com segurança.
	1. Não são necessárias alterações no firewall corporativo. O gateway faz apenas conexões de saída baseadas em HTTP para a Internet aberta.
	2. Criptografar credenciais para seus armazenamentos de dados locais com seu certificado.
4.	Mover dados com eficiência: os dados são transferidos em paralelo, resilientes a problemas de rede intermitente com lógica de repetição automática.

## Considerações para usar o Gateway de Gerenciamento de Dados
1.	Uma única instância do Gateway de Gerenciamento de Dados pode ser usada para várias fontes de dados locais, mas observe que **uma única instância de gateway está associada a apenas uma Azure Data Factory** e não pode ser compartilhada com outra data factory.
2.	Você pode ter **apenas uma instância do Gateway de Gerenciamento de Dados** instalada em um único computador. Suponha, você tem dois data factories que precisam acessar fontes de dados locais e precisa instalar gateways nos dois computadores locais onde cada gateway é vinculado a um data factory separado.
3.	O **gateway não precisa estar no mesmo computador que a fonte de dados**, mas ficar mais próximo à fonte de dados reduz o tempo para o gateway se conectar à fonte de dados. Recomendamos que você instale o gateway em um computador diferente daquele que hospeda a fonte de dados local para que o gateway não dispute os recursos com a fonte de dados.
4.	Você pode ter **vários gateways em diferentes computadores conectados à mesma fonte de dados local**. Por exemplo, você pode ter dois gateways servindo duas data factories, mas a mesma fonte de dados local é registrada com ambas as data factories.
5.	Se você já tiver um gateway instalado no computador atendendo um cenário do **Power BI**, instale um **gateway separado para o Azure Data Factory** em outro computador.
6.	Você deve **usar o gateway, mesmo quando usar a Rota Expressa**. 
7.	Você deve tratar a fonte de dados como uma fonte de dados local (que está atrás de um firewall) mesmo quando usar a **Rota Expressa** e **usar o gateway** para estabelecer a conectividade entre o serviço e a fonte de dados. 

## Instalação do gateway - pré-requisitos
1.	As versões do **sistema operacional** com suporte são Windows 7, Windows 8/8.1, Windows Server 2008 R2, Windows Server 2012.
2.	A **configuração** recomendada para o computador do gateway é no mínimo 2 GHz, 4 núcleos, 8 GB de RAM e 80 GB de disco.
3.	Se o computador host hibernar, o gateway não poderá responder a solicitações de dados. Portanto, configure um **plano de energia** adequado no computador antes de instalar o gateway. A instalação do gateway exibe uma mensagem se o computador estiver configurado para hibernar.

Devido ao fato de as execuções da atividade de cópia ocorrerem em uma frequência específica, o uso de recursos (CPU, memória) no computador também segue o mesmo padrão com tempos ociosos e de pico. A utilização de recursos também depende muito da quantidade de dados sendo movida. Quando vários trabalhos de cópia estiverem em andamento, você observará o uso do recurso aumentar durante horários de pico. Embora o indicado acima seja a configuração mínima, é sempre melhor ter uma configuração com mais recursos do que as configurações mínimas descritas acima dependendo da sua carga específica para movimentação de dados.

## Instalação
O Gateway de Gerenciamento de Dados pode ser instalado baixando um pacote de instalação MSI do Centro de Download da Microsoft. O MSI também pode ser usado para atualizar o Gateway de Gerenciamento de Dados existente para a versão mais recente, com todas as configurações preservadas. Você pode encontrar o link para o pacote MSI do Portal do Azure seguindo a explicação passo a passo abaixo.

### Práticas recomendadas de instalação:
1.	Configure o plano de energia no computador host para o gateway para que o computador não hiberne. Se o computador host hibernar, o gateway não poderá responder a solicitações de dados.
2.	Você deve fazer backup do certificado associado ao gateway.

### Solucionando problemas de instalação:
Se sua empresa usa um firewall ou servidor proxy, etapas adicionais podem ser necessárias no caso de o Gateway de Gerenciamento de Dados não poder se conectar aos serviços de nuvem da Microsoft.

#### Examinando logs do Gateway com o Visualizador de Eventos:

O aplicativo do gerenciador de configurações de gateway indica o status do gateway como "Desconectado" ou "Conectando".

Para obter mais informações, você pode examinar os logs de gateway nos logs de eventos do Windows. Você pode encontrá-los usando o **Visualizador de Eventos** do Windows em **Logs de Aplicativos e Serviços** > **Gateway de Gerenciamento de Dados**. Enquanto soluciona problemas relacionados ao gateway, procure por eventos de nível de erro no Visualizador de Eventos.


#### Possíveis sintomas de problemas relacionados ao firewall:

1. Ao tentar registrar o gateway, você recebe o seguinte erro: "Falha ao registrar a chave do gateway. Antes de tentar registrar a chave do gateway novamente, confirme se o Gateway de Gerenciamento de Dados está em um estado conectado e o Serviço de Host do Gateway de Gerenciamento de Dados está iniciado."
2. Ao abrir o Gerenciador de Configurações, você vê o status “Desconectado” ou “Conectando”. Ao exibir os logs de eventos do Windows, em "Visualizador de Eventos" > "Logs de Aplicativos e Serviços" > "Gateway de Gerenciamento de Dados" você vê mensagens de erro como "Impossível conectar-se ao servidor remoto" ou "Um componente do Gateway de Gerenciamento de Dados parou de responder e será reiniciado automaticamente. Nome do componente: Gateway."

Esses são causados pela configuração incorreta do servidor proxy ou firewall, o que impede o Gateway de Gerenciamento de Dados de se conectar aos serviços de nuvem para se autenticar.

Os dois firewalls que possivelmente estão no escopo são: firewall corporativo em execução no roteador central da organização e o firewall do Windows configurado como um daemon no computador local em que o gateway está instalado. Aqui estão algumas considerações:

- Não é necessário alterar a política de entrada do firewall corporativo.
- O firewall corporativo e o firewall do Windows devem habilitar a regra de saída para as portas TCP: 80, 440 e de 9305 a 9354. Elas são usadas pelo Barramento de Serviço do Microsoft Azure para estabelecer a conexão entre os serviços de nuvem e o Gateway de Gerenciamento de Dados.

A instalação do MSI configurará automaticamente regras de firewall do Windows para as portas de entrada do computador do gateway (consulte a seção de considerações sobre portas e segurança acima).

Mas a instalação presume que as portas de saída mencionadas acima são permitidas por padrão no computador local e no firewall corporativo. Você precisa habilitar essas portas de saída, se esse não for o caso. Se você tiver substituído o firewall do Windows por um firewall de terceiros, talvez seja necessário abrir essas portas manualmente.

Se sua empresa usa um servidor proxy, você precisa adicionar o Microsoft Azure à lista branca. Você pode baixar uma lista de endereços IP do Microsoft Azure válidos do [Centro de Download da Microsoft](http://msdn.microsoft.com/library/windowsazure/dn175718.aspx).

## Usando o Gateway de dados: tutorial passo a passo
Neste passo a passo, você pode criar um data factory com um pipeline que move dados de um banco de dados SQL Server local para um blob do Azure.

### Etapa 1: criar uma data factory do Azure
Nesta etapa, você pode usar o Portal de Gerenciamento do Azure para criar uma instância do Azure Data Factory denominada **ADFTutorialOnPremDF**. Você também pode criar um data factory usando os cmdlets do Data Factory do Azure.

1.	Após fazer logon no [Portal de Visualização do Azure](https://portal.azure.com), clique em **NOVO** no canto inferior esquerdo, selecione **Análises de dados** na folha **Criar** e clique em **Data Factory** na folha **Análises de dados**.

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

### Etapa 2: Criar um gateway de gerenciamento de dados
5. Na folha **DATA FACTORY**, clique no bloco **Criar e implantar** para iniciar o **Editor** da data factory.

	![Bloco Criar e implantar](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png) 
6.	Em Editor do Data Factory, clique em **...(reticências)** na barra de ferramentas, e em seguida, clique em **Novo gateway de dados**. 

	![Novo gateway de dados na barra de ferramentas](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
2. Na folha **Criar**, insira **adftutorialgateway** como **nome** e clique em **OK**. 	

	![Folha Criar Gateway](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

3. Na folha **Configurar**, clique em **Instalar diretamente neste computador**. Isso baixará o pacote de instalação para o gateway, instalar, configurar e registrar o gateway no computador.

	> [AZURE.NOTE]Use o Internet Explorer ou um navegador da Web compatível com Microsoft ClickOnce.

	![Folha Gateway - Configurar](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

	Essa é a maneira mais fácil (um clique) de baixar, instalar, configurar e registrar o gateway em uma única etapa. Você pode ver que o aplicativo **Gerenciador de Configuração de Gateway de gerenciamento de dados da Microsoft** está instalado no computador. Você também pode encontrar o executável **ConfigManager.exe** na pasta: **C:\\Arquivos de Programas\\Microsoft Data Management Gateway\\1.0\\Shared**.

	Você também pode baixar e instalar o gateway manualmente usando os links nessa folha e registrá-lo usando a chave mostrada na caixa de texto **REGISTRAR COM CHAVE**.
	
	Consulte as seções no início deste artigo para obter detalhes sobre o gateway, incluindo as práticas recomendadas e considerações importantes.

	>[AZURE.NOTE]Você deve ser um administrador no computador local para instalar e configurar com êxito o Gateway de gerenciamento de dados. Você pode acrescentar usuários adicionais para o grupo local de usuários do Gateway de gerenciamento de dados do Windows. Os membros desse grupo poderão usar a ferramenta Gerenciador de configuração de Gateway de gerenciamento de dados para configurar o gateway.

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
Nesta etapa, você criará dois serviços vinculados: **StorageLinkedService** e **SqlServerLinkedService**. O **SqlServerLinkedService** vincula um banco de dados SQL Server local e o serviço vinculado **StorageLinkedService** vincula um blob do Azure à data factory. Você criará um pipeline posteriormente neste passo a passo que copia dados do banco de dados SQL Server local para o armazenamento de blob do Azure.

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
            		"userName": "<Specify user name if you are using Windows Authentication>",
            		"password": "<Specify password for the user account>"
        		}

	4. Se você estiver usando a autenticação SQL:
		1. Especifique o **nome do servidor**, o **nome do banco de dados**, a **ID de usuário** e a **Senha** do banco de dados na **connectionString**.       
		2. Remova as duas últimas propriedades JSON - **userName** e **password** - do JSON.
		3. Remova a **, (vírgula)** à direita no final da linha que especifica o valor da propriedade **gatewayName**. 

				"typeProperties": {
            		"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
	           		"gatewayName": "<Name of the gateway that the Data Factory service should use to connect to the on-premises SQL Server database>"
    		    }
	   
2.	Clique em **Implantar** na barra de comandos para implantar o serviço vinculado do SQL Server.

#### Adicionar um serviço vinculado para uma conta de armazenamento do Azure
 
1. No **Editor do Data Factory**, clique em **Novo armazenamento de dados** na barra de comandos e clique em **Armazenamento do Azure**.
2. Insira o nome da sua conta de armazenamento do Azure em **Nome da conta**.
3. Insira a chave da sua conta de armazenamento do Azure em **Chave de conta**.
4. Clique em **Implantar** para implantar o **StorageLinkedService**.
   
 
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
	
	Se você não especificar o valor para a propriedade **end**, ele será calculado como "**início + 48 horas**". Para executar o pipeline indefinidamente, especifique **9/9/9999** como o valor da propriedade **end**.
	
	Especificando o período ativo de um pipeline, você está definindo a duração de tempo em que as fatias de dados serão processadas com base nas propriedades de **Disponibilidade** que foram definidas para cada tabelo da Azure Data Factory.
	
	No exemplo acima, como cada fatia de dados é produzida por hora, haverá 24 fatias de dados.
	
2. Clique em **Implantar** na barra de comandos para implantar o conjunto de dados (a tabela é um conjunto de dados retangular). Confirme que você vê uma mensagem na barra de título que diz **PIPELINE IMPLANTADO COM ÊXITO**.
5. Agora, feche a folha **Editor** clicando em **X**. Clique em **X** novamente para fechar a folha ADFTutorialDataFactory com o modo de exibição de árvore e a barra de ferramentas. Se você vir **suas edições não salvas serão descartadas**, clique em **OK**.
6. Você deve ser retornado à folha **DATA FACTORY** para o **ADFTutorialOnPremDF**.

**Parabéns!** Você criou um data factory do Azure, serviços vinculados, tabelas e uma pipeline e a pipeline agendada com êxito.

#### Visualizar a data factory em um modo de exibição de diagrama 
1. No **Portal de Visualização do Azure**, clique no bloco **Diagrama** na página inicial da data factory **ADFTutorialOnPremDF**.

	![Link do diagrama](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)

2. Você deverá ver o diagrama semelhante ao seguinte:

	![Exibição de diagrama](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

	É possível ampliar, reduzir, aplicar zoom de 100%, ajustar nível de zoom, posicionar pipelines e tabelas automaticamente, bem como mostrar informações de linhagem (realça itens upstream e downstream dos itens selecionados). Você pode clicar duas vezes em um objeto (pipeline ou tabela de entrada/saída) para ver as propriedades dele.

### Etapa 6: Monitorar os conjuntos de dados e os pipelines
Nesta etapa, você utilizará o Portal do Azure para monitorar o que está acontecendo em um data factory do Azure. Você também pode usar os cmdlets do PowerShell para monitorar conjuntos de dados e pipelines. Para obter detalhes sobre monitoramento, consulte [Monitorar e gerenciar pipelines](monitor-manage-pipelines.md).

1. Navegue até o **Portal de Visualização do Azure** (se você o fechou)
2. Se a folha para **ADFTutorialOnPremDF** não estiver aberta, abra-a clicando em **ADFTutorialOnPremDF** no **Quadro Inicial**.
3. Você verá a **contagem** e os **nomes** das tabelas e do pipeline criados nessa folha.

	![Página inicial do Data Factory](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)
4. Agora, clique no bloco **Conjuntos de dados**.
5. Na folha **Conjuntos de Dados**, clique em **EmpOnPremSQLTable**.

	![Fatias de EmpOnPremSQLTable](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)

6. Observe que as fatias de dados até o momento atual já foram produzidas e estão **Prontas**. Isso porque você inseriu os dados no banco de dados SQL Server e eles estão lá está o tempo todo. Confirme se nenhuma fatia aparecerá na seção **Fatias com problema** na parte inferior.


	Ambas as listas **Fatias atualizadas recentemente** e **Fatias com falha recente** são classificadas segundo o **HORÁRIO DA ÚLTIMA ATUALIZAÇÃO**. A hora de atualização de uma fatia é alterada nas situações a seguir.
    

	-  Você atualiza o status da fatia manualmente, por exemplo, usando o **Set-AzureDataFactorySliceStatus** (ou) clicando em **EXECUTAR** na folha **FATIA** da fatia.
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

## Movimentando o gateway de um computador para outro
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

## Configurando credenciais e segurança

Você também pode criar um serviço do SQL Server vinculado usando a folha de serviços vinculados em vez de usar o Editor do Data Factory.
 
3.	Na página inicial do Data Factory, clique no bloco **Serviços Vinculados**. 
4.	Na folha **Serviços Vinculados**, clique em **Novo armazenamento de dados** na barra de comandos. 
4.	Insira **SqlServerLinkedService** como o **nome**. 
2.	Clique na seta ao lado de **Tipo** e selecione **SQL Server**.

	![Criar novo armazenamento de dados](./media/data-factory-move-data-between-onprem-and-cloud/new-data-store.png)
3.	Você deverá ver mais configurações abaixo da configuração **Tipo**.
4.	Para a configuração **Gateway de dados**, selecione o gateway que você acabou de criar. 

	![Configurações do SQL Server](./media/data-factory-move-data-between-onprem-and-cloud/sql-server-settings.png)
4.	Insira o nome do seu servidor de banco de dados para a configuração **Servidor**.
5.	Insira o nome do banco de dados para a configuração **Banco de dados**.
6.	Clique na seta ao lado de **Credenciais**.

	![Folha Credenciais](./media/data-factory-move-data-between-onprem-and-cloud/credentials-dialog.png)
7.	Na folha **Credenciais**, clique em **Clique aqui para definir credenciais**.
8.	Na caixa de diálogo **Definindo Credenciais**, faça o seguinte:

	![Caixa de diálogo de Configurando Credenciais](./media/data-factory-move-data-between-onprem-and-cloud/setting-credentials-dialog.png) 1. Selecione a **autenticação** que você deseja que o serviço de Data Factory use para se conectar ao banco de dados. 2. Insira o nome do usuário que tem acesso ao banco de dados para a configuração **NOME DE USUÁRIO**. 3. Insira a senha do usuário para a configuração **SENHA**. 4. Clique em **OK** para fechar a caixa de diálogo. 
4. Clique em **OK** para fechar a folha **Credenciais**. 
5. Clique em **OK** na folha **Novo armazenamento de dados**. 	
6. Confirme se o status de **SqlServerLinkedService** está definido como Online na folha Serviços Vinculados.![Status do serviço vinculado do SQL Server](./media/data-factory-move-data-between-onprem-and-cloud/sql-server-linked-service-status.png)

Se você acessar o portal de um computador diferente do computador do gateway, você deve garantir que o aplicativo Gerenciador de credenciais possa se conectar ao computador do gateway. Se o aplicativo não puder acessar o computador do gateway, ele não permitirá que você defina credenciais da fonte de dados teste a conexão à fonte de dados.

Quando você usa o aplicativo "Configurando Credenciais" iniciado pelo do Portal do Azure para configurar credenciais para uma fonte de dados local, o portal criptografa as credenciais com o certificado especificado na guia Certificado do Gerenciador de Configuração de Gateway de Gerenciamento de Dados no computador do gateway.

Se você estiver procurando uma abordagem baseada em API para criptografar as credenciais, poderá usar o cmdlet [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/azure/dn834940.aspx) do PowerShell para criptografar as credenciais. O cmdlet usa o certificado que esse gateway está configurado para usar para criptografar as credenciais. Você pode obter as credenciais criptografadas retornadas por esse cmdlet e adicioná-las ao elemento EncryptedCredential de connectionString do arquivo JSON que será usado com o cmdlet [New-AzureDataFactoryLinkedService](https://msdn.microsoft.com/library/azure/dn820246.aspx) ou no trecho de código JSON no editor de Data Factory no portal.

	"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",

**Observação:** se você usar o aplicativo "Definindo Credenciais", ele definirá automaticamente as credenciais criptografadas no serviço vinculado como mostrado acima.

Há mais uma abordagem para definir as credenciais usando o Editor Data Factory. Se você criar um serviço vinculado SQL Server usando o editor e inserir credenciais em texto sem formatação, as credenciais são criptografadas usando um certificado que é de propriedade do serviço Data Factory, NÃO o certificado que o gateway está configurado para usar. Embora essa abordagem possa ser um pouco mais rápida em alguns casos, ela é menos segura. Portanto, é recomendável que você siga essa abordagem somente para fins de desenvolvimento/teste.


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

	
4. No Azure PowerShell, alterne para a pasta **C:\\Arquivos de Programas\\Microsoft Data Management Gateway\\1.0\\PowerShellScript** e execute o script **RegisterGateway.ps1** associado à variável local **$Key** conforme mostrado no seguinte comando para registrar o agente cliente instalado no computador com o gateway lógico criado anteriormente.

		PS C:\> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. Você pode usar o cmdlet **Get-AzureDataFactoryGateway** para obter a lista de gateways na data factory. Quando o **Status** mostra **online**, isso significa que seu gateway está pronto para uso.

		Get-AzureDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

Você pode remover um gateway usando o cmdlet **Remove-AzureDataFactoryGateway** e atualizar a descrição de um gateway usando os cmdlets **Set-AzureDataFactoryGateway**. Para sintaxe e outros detalhes sobre esses cmdlets, consulte Referência de Cmdlet de Data Factory.


## Fluxo de dados de cópia usando o Gateway de Gerenciamento de Dados
Quando você usa uma atividade de cópia em um pipeline de dados para incluir dados locais na nuvem para processamento adicional, ou exportar dados de resultado na nuvem de volta para um armazenamento de dados local, a atividade de cópia usa internamente um gateway para transferir os dados da fonte de dados local para a nuvem e vice-versa.

Aqui está o fluxo de dados de alto nível para e o resumo das etapas para a cópia com o gateway de dados: ![Fluxo de dados usando o gateway](./media/data-factory-move-data-between-onprem-and-cloud/data-flow-using-gateway.png)

1.	O desenvolvedor de dados cria um novo gateway para uma Azure Data Factory usando o [Portal do Azure](http://portal.azure.com) ou [Cmdlet do PowerShell](https://msdn.microsoft.com/library/dn820234.aspx). 
2.	O desenvolvedor de dados usa o painel "Serviços vinculados" para definir um novo serviço vinculado para um armazenamento de dados local com o gateway. Como parte da configuração de dados do serviço vinculado, o desenvolvedor usa o aplicativo Configurando Credenciais como mostrado no tutorial passo a passo para especificar as credenciais e tipos de autenticação. A caixa de diálogo do aplicativo Configurando Credenciais se comunicará com o armazenamento de dados para testar a conexão e o gateway para salvar as credenciais.
3.	O gateway criptografará as credenciais com o certificado associado ao gateway (fornecido pelo desenvolvedor de dados) antes de salvar as credenciais na nuvem.
4.	O serviço de movimentação da data factory se comunica com o gateway para o agendamento e o gerenciamento de trabalhos por meio de um canal de controle que usa uma fila do barramento de serviço do Azure compartilhado. Quando o trabalho de atividade de cópia precisa ser inicializado, a data factory enfileira a solicitação junto com as informações de credencial. O gateway inicia o trabalho depois de sondar a fila.
5.	O gateway descriptografa as credenciais com o mesmo certificado e se conecta ao armazenamento de dados local com o tipo de autenticação adequado.
6.	O gateway copia dados do armazenamento local para um armazenamento em nuvem ou de um armazenamento em nuvem para um armazenamento de dados local dependendo de como a atividade de cópia é configurada no pipeline de dados. Observação: para esta etapa, o gateway se comunica diretamente com o serviço de armazenamento baseado em nuvem (por exemplo, Blob do Azure, SQL do Azure etc.) por um canal seguro (HTTPS).

### Considerações de segurança e sobre portas

1. Conforme mencionado anteriormente no tutorial passo a passo, há várias maneiras de configurar credenciais para armazenamentos de dados locais com a data factory. As considerações de porta variam para essas opções.	

	- Usando o aplicativo **Definindo Credenciais**: o programa de instalação do Gateway de Gerenciamento de Dados por padrão abre as portas **8050** e **8051** no firewall do Windows local para o computador do gateway. Essas portas são usadas pelo aplicativo Configurando Credenciais para retransmitir as credenciais para o gateway. Essas portas estão abertas somente para o computador no firewall do Windows local. Elas não podem ser acessadas da Internet e você não precisa mantê-las abertas no firewall corporativo.
	2.	Usando o cmdlet do PowerShell [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx): a. Se você estiver usando o comando do PowerShell para criptografar as credenciais e como resultado não deseja que a instalação do gateway abra as portas de entrada no computador do gateway no firewall do Windows, pode fazer isso usando o comando a seguir durante a instalação:
	
			msiexec /q /i DataManagementGateway.msi NOFIREWALL=1
3.	Se estiver usando o aplicativo **Definindo Credenciais**, você deve iniciá-lo em um computador que pode se conectar ao Gateway de Gerenciamento de Dados para poder configurar credenciais para a fonte de dados e para testar a conexão com a fonte de dados.
4.	Ao copiar dados de/para um banco de dados do SQL Server local de um banco de dados SQL do Azure, certifique-se do seguinte:	
	- 	Que o firewall no computador do gateway permite a comunicação TCP de saída na porta **TCP** **1433**.
	- 	Defina as [Configurações de firewall do Azure SQL](https://msdn.microsoft.com/library/azure/jj553530.aspx) para adicionar o **endereço IP do computador do gateway** aos **endereços IP permitidos**.
5.	Ao copiar dados para/de um SQL Server local para qualquer destino quando os computadores do gateway e do SQL Server forem diferentes, faça o seguinte: [configure o Firewall do Windows](https://msdn.microsoft.com/library/ms175043.aspx) no computador do SQL Server para que o gateway possa acessar o banco de dados por meio de portas que a instância do SQL Server escuta. Para a instância padrão, é a porta 1433.

## Enviar comentários
Apreciamos muito seus comentários sobre este artigo. Reserve alguns minutos para enviar seus comentários por meio de [email](mailto:adfdocfeedback@microsoft.com?subject=data-factory-move-data-between-onprem-and-cloud.md).

<!---HONumber=Nov15_HO2-->