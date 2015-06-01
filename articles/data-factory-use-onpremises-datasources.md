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
	ms.date="2/10/2015" 
	ms.author="spelluru"/>

# Habilitar seus pipelines para trabalhar com dados locais

Para habilitar os pipelines em um data factory do Azure para trabalhar com uma fonte de dados local, você precisa adicionar a fonte de dados local como um serviço vinculado ao data factory usando o Portal de Gerenciamento do Azure ou o PowerShell do Azure.
 
Para poder adicionar uma fonte de dados local como um serviço vinculado a um data factory, você primeiro precisa baixar e instalar o Gateway de Gerenciamento de Dados da Microsoft em um computador local e configurar o serviço vinculado para a fonte de dados local para usar o gateway.
 
> [WACOM.NOTE] Somente o SQL Server é a fonte de dados local com suporte neste momento.

## <a href="DMG"></a> Gateway de gerenciamento de dados

O **Gateway de Gerenciamento de Dados** é um software que conecta fontes de dados locais a serviços de nuvem de maneira segura e gerenciada. Com o Gateway de Gerenciamento de Dados, você pode

- **Conectar-se a dados locais para acesso a dados híbridos** - Você pode conectar dados locais a serviços de nuvem para tirar proveito dos serviços de nuvem enquanto mantém a empresa em funcionamento com dados locais.
- **Definir um proxy de dados seguro** - Você pode definir quais fontes de dados locais são expostas com o Gateway de Gerenciamento de Dados para que esse gateway autentique a solicitação de dados de serviços de nuvem e proteja as fontes de dados locais.
- **Gerenciar seu gateway para controle completo** - Você recebe monitoramento completo e registro em log de todas as atividades dentro o Gateway de Gerenciamento de Dados para gerenciamento e controle.
- **Mover dados com eficiência** - Os dados são transferidos em paralelo, resilientes a problemas de rede intermitentes com lógica de repetição automática.


O Gateway de Gerenciamento de Dados tem uma gama completa de recursos de conexão de dados locais que incluem o seguinte:

- **Firewall não intrusivo a corporativo** - O Gateway de Gerenciamento de Dados funciona após a instalação, sem precisar abrir uma conexão de firewall ou exigir mudanças intrusivas à sua infraestrutura de rede corporativa.
- **Criptografar credenciais com seu certificado** - As credenciais usadas para conectar-se a fontes de dados são criptografadas com um certificado totalmente pertencente a um usuário. Sem o certificado, ninguém pode descriptografar as credenciais em texto sem formatação, incluindo a Microsoft.

### Instalação do gateway - práticas recomendadas

1.	Se o computador host hibernar, o gateway não poderá responder à solicitação de dados. Portanto, configure um **plano de energia** adequado no computador antes de instalar o gateway. 
2.	O Gateway de Gerenciamento de Dados deve ser capaz de se conectar às fontes de dados locais que serão registradas com o serviço Data Factory do Azure. Ele não precisa estar no mesmo computador que a fonte de dados, mas **ficar mais próximo à fonte de dados** reduz o tempo para o gateway se conectar à fonte de dados.

### Considerações para usar o Gateway de Gerenciamento de Dados
1.	Uma única instância do Gateway de Gerenciamento de Dados pode ser usada para várias fontes de dados locais, mas tenha em mente que um **gateway está associado a um data factory do Azure** e não pode ser compartilhado com outro data factory.
2.	Você pode ter **apenas uma instância do Gateway de Gerenciamento de Dados** instalada no computador. Suponha, você tem dois data factories que precisam acessar fontes de dados locais e precisa instalar gateways nos dois computadores locais onde cada gateway é vinculado a um data factory separado.
3.	Se você já tiver um gateway instalado no computador atendendo um cenário do **Power BI**, instale um **gateway separado para o Data Factory do Azure** em outro computador. 
 

## Passo a passo

Neste passo a passo, você pode criar um data factory com um pipeline que move dados de um banco de dados SQL Server local para um blob do Azure. 

### Etapa 1: Criar um data factory do Azure
Nesta etapa, você pode usar o Portal de Gerenciamento do Azure para criar uma instância do Data Factory do Azure denominada **ADFTutorialOnPremDF**. Você também pode criar um data factory usando os cmdlets do Data Factory do Azure. 

1.	Depois de fazer logon no [Portal de Visualização do Azure][
2.	azure-preview-portal], clique em **NOVO** canto inferior esquerdo e clique **Data Factory** na folha **Novo**.

	![New->DataFactory][image-data-factory-new-datafactory-menu] 
	
	Se você não vir **Data Factory** na folha **Novo**, role para baixo.  
6. Na folha **Novo data factory**:
	1. Insira **ADFTutorialOnPremDF** para o **nome**.
	2. Clique em **NOME DO GRUPO DE RECURSOS** e selecione **ADFTutorialResourceGroup** (se você tiver realizado o tutorial de [Introdução ao Data Factory do Azure][adfgetstarted]. Você pode selecionar um grupo de recursos existente ou crie um novo. Para criar um novo grupo de recursos:
		1. Clique em **Criar um novo grupo de recursos**.
		2. Na folha **Criar grupo de recursos**, insira um **nome** para o grupo de recursos e clique em **OK**.

7. Observe que a opção **Adicionar ao Quadro Inicial** está marcada na folha **Novo data factory**.

	![Add to Startboard][image-data-factory-add-to-startboard]

8. Na folha **Novo data factory**, clique em **Criar**.
9. Procure as notificações do processo de criação no hub **NOTIFICAÇÕES** à esquerda.

	![NOTIFICATIONS hub][image-data-factory-notifications-hub]

11. Após a conclusão da criação, você verá a folha Data Factory, conforme mostrado abaixo:

	![Data Factory Home Page][image-data-factory-datafactory-homepage]

12. Você também deve vê-la no **Quadro Inicial** conforme mostrado abaixo. Clique para abrir a folha **Data Factory** se ela ainda não estiver aberta.

	![Startboard][image-data-factory-startboard]

### Etapa 2: Criar serviços vinculados 
Nesta etapa, você criará dois serviços vinculados: **MyBlobStore** e **OnPremSqlLinkedService**. O **OnPremSqlLinkedService** vincula um banco de dados SQL Server local e o serviço vinculado **MyBlobStore** vincula um blob do Azure ao **ADFTutorialDataFactory**. Você criará um pipeline posteriormente neste passo a passo que copia dados do banco de dados SQL Server local para o armazenamento de blob do Azure. 

### Adicionar um serviço vinculado a um banco de dados SQL Server local
1.	Na folha **Data Factory** de **ADFTutorialOnPremDF**, clique em **Serviços Vinculados**. 

	![Data Factory Home Page][image-data-factory-home-age]

2.	Na folha **Serviços Vinculados**, clique em **+ Gateway de dados**.

	![Linked Services - Add a Gateway button][image-data-factory-linkedservices-add-gateway-button]

2. Na folha **Criar**, insira **adftutorialgateway** para o **nome** e clique em **OK**. 	

	![Create Gateway blade][image-data-factory-create-gateway-blade]

3. Na folha **Configurar**, clique em **Instalar diretamente neste computador**. Isso baixará o pacote de instalação para o gateway, instalar, configurar e registrar o gateway no computador.

	![Gateway - Configure blade][image-data-factory-gateway-configure-blade]

	Essa é a maneira mais fácil (um clique) de baixar, instalar, configurar e registrar o gateway em uma única etapa. Você pode ver que o aplicativo **Gerenciador de Configuração de Gateway de gerenciamento de dados da Microsoft** está instalado no computador. Você também pode encontrar o executável **ConfigManager.exe** na pasta: **C:\Program Files\Microsoft Data Management Gateway\1.0\Shared**.

	Você também pode baixar e instalar o gateway manualmente usando os links nessa folha e registrá-lo usando a chave mostrada na caixa de texto **REGISTRAR COM CHAVE**.
	
	Consulte a seção [Gateway de Gerenciamento de Dados](#DMG) para obter detalhes sobre o gateway, incluindo as práticas recomendadas e considerações importantes. 

4. Clique em **OK** na folha **Novo gateway de dados**.
5. Inicie o aplicativo **Gerenciador de Configuração de Gateway de gerenciamento de dados da Microsoft**  no computador.

	![Gateway Configuration Manager][image-data-factory-gateway-configuration-manager]

6. Aguarde até que os valores sejam definidos da seguinte maneira:
	1. Se o **status** do serviço não estiver definido como **Iniciado**, clique em **Iniciar serviço** para iniciar o serviço e aguarde a atualização dos outros campos.
	2. **Nome do gateway** está definido como **adftutorialgateway**.
	3. **Nome da instância** está definido como **adftutorialgateway**.
	4. **Status da chave de gateway** está definido como **Registrado**.
	5. A barra de status inferior exibe **Conectado ao Serviço de Nuvem do Gateway de Gerenciamento de Dados** junto com uma **marca de seleção verde**.  

7. Na folha **Serviços Vinculados**, confirme se o **status** do gateway é **Bom** e clique em **+ Armazenamento de dados**. Talvez seja necessário fechar e reabrir a folha para atualizá-la. 

	![Add Data Store button][image-data-factory-add-datastore-SQL-button]

8. Na folha **Novo armazenamento de dados** insira **OnPremSqlLinkedService** para o **nome**.
9. Clique em **TIPO (Configurações necessárias)** e selecione **SQL Server**. 
10. Na folha **Novo armazenamento de dados**, clique em **GATEWAY DE DADOS (Definir configurações necessárias)**.

	![Data Gateway Configure link][image-data-factory-gateway-configure-link]
  
11. Selecione **adftutorialgateway** criado anteriormente. 
12.	Na folha **Novo armazenamento de dados**, clique em **CREDENCIAIS** para ver a folha **Credenciais**.

	![Data Source Credentials Blade][image-data-factory-credentials-blade]

13.	Na folha **Credenciais**, clique em **Clique aqui para definir credenciais com segurança**. Ele iniciará a seguinte caixa de diálogo pop-up.

	![One Click application install][image-data-factory-oneclick-install]

14. Clique em **Executar** para instalar o aplicativo **Gerenciador de Credenciais** e você verá a caixa de diálogo Definindo Credenciais. 
15.	Na caixa de diálogo **Definindo Credenciais**, insira o **nome de usuário** e a **senha** que o serviço pode usar para acessar o banco de dados SQL Server local e clique em **OK**. Há suporte apenas para **Autenticação SQL** nesta caixa de diálogo. Aguarde até que a caixa de diálogo seja fechada.
16.	Clique em **OK** na folha **Credenciais** e clique em **OK** na folha **Novo armazenamento de dados**. Você deve ver o serviço vinculado **OnPremSqlLinkedService** adicionado à folha Serviços Vinculados.

	![Linked Services Blade with OnPrem Store][image-data-factory-linkedservices-blade-onprem]

	
   


#### Adicionar um serviço vinculado para uma conta de armazenamento do Azure

1.	Na folha **Data Factory**, clique no bloco **Serviços Vinculados** para iniciar a folha **Serviços Vinculados**.
2. Na folha **Serviços Vinculados**, clique em **Adicionar armazenamento de dados**.	
3. Na folha **Novo armazenamento de dados**:
	1. Insira um **nome** para o armazenamento de dados. Para o tutorial, insira **MyBlobStore** para o **nome**.
	2. Insira a **descrição (opcional)** para o armazenamento de dados.
	3. Clique em **Tipo**.
	4. Selecione a **conta de armazenamento do Azure** e clique em **OK**.
		
		![Azure Storage Account][image-data-factory-azure-storage-account]

4. Agora, você está novamente na folha **Novo armazenamento de dados** que tem a seguinte aparência:

	![Azure Storage settings][image-data-factory-azure-storage-settings]

5. Insira o **Nome da Conta** e a **Chave de Conta** para sua Conta de Armazenamento do Azure e clique em **OK**.

  
6. Depois de clicar em **OK** na folha **Novo armazenamento de dados**, você verá **MyBlobStore** na lista de **ARMAZENAMENTOS DE DADOS** na folha **Serviços Vinculados**. Verifique o hub de **NOTIFICAÇÕES** (à esquerda) quanto a quaisquer mensagens.

	![Linked Services blade with MyBlobStore][image-data-factory-linkedservices-with-storage]

 
## Etapa 3: Criar conjuntos de dados de entrada e saída
Nesta etapa, você criará conjuntos de dados de entrada e saída que representam dados de entrada e saída da operação de cópia (banco de dados SQL Server local = > armazenamento de blob do Azure). Ainda não há suporte para a criação de conjuntos de dados e pipelines pelo Portal do Azure, por isso você usará os cmdlets do PowerShell do Azure para criar tabelas e pipelines. Antes de criar conjuntos de dados ou tabelas (conjuntos de dados retangulares), você precisa fazer o seguinte (etapas detalhadas seguem a lista):

- Criar uma tabela chamada **emp** no banco de dados SQL Server adicionado como um serviço vinculado ao data factory e inserir alguns exemplos de entradas na tabela.
- - Se você ainda não verificou o tutorial do artigo [Introdução ao Data Factory do Azure][adfgetstarted], crie um contêiner de blob chamado **adftutorial** na conta de armazenamento de blob do Azure adicionada com um serviço vinculado ao data factory.

### Preparar o SQL Server local para o tutorial

1. No banco de dados especificado para o serviço vinculado do SQL Server local (**OnPremSqlLinkedService**), use o seguinte script SQL para criar a tabela **emp** no banco de dados.


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

1.	Crie um arquivo JSON para uma tabela do Data Factory que representa os dados da tabela **emp** no banco de dados SQL Server. Inicie o **Bloco de Notas**, copie o seguinte script JSON e salve-o como **EmpOnPremSQLTable.json** na pasta C:\ADFGetStarted**OnPrem**. Crie a subpasta **OnPrem** na pasta **C:\ADFGetStarted** se ela não existir. 


        {
    		"name": "EmpOnPremSQLTable",
    		"properties":
    		{
        		"location":
        		{
            		"type": "OnPremisesSqlServerTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "OnPremSqlLinkedService"
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
	
	- o **tipo** de local está definido como **OnPremisesSqlServerTableLocation**.
	- **tableName** é definido como **emp**.
	- **linkedServiceName** está definido como **OnPremSqlLinkedService** (você criou esse serviço vinculado na Etapa 2).
	- Para uma tabela de entrada que não é gerada por outro pipeline no Data Factory do Azure, você deve especificar a seção **waitOnExternal** no JSON. Ela indica que os dados de entrada são produzidos externos ao serviço Data Factory do Azure.   

	Consulte [Referência de Script JSON][json-script-reference] para obter detalhes sobre as propriedades JSON.

2. Os cmdlets do Data Factory do Azure estão disponíveis no modo **AzureResourceManager**. Inicie o **PowerShell do Azure** e execute o seguinte comando para alternar para o modo **AzureResourceManager**.     

        switch-azuremode AzureResourceManager

	Baixe o [PowerShell do Azure][azure-powershell-install] se você não o tiver instalado no computador.
3. Use o cmdlet **New-AzureDataFactoryTable** para criar a tabela de entrada usando o arquivo **EmpOnPremSQLTable.json**.

		New-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialOnPremDF -File C:\ADFGetStarted\OnPrem\EmpOnPremSQLTable.json  
	
	Atualize o comando se você estiver usando outro grupo de recursos.

### Criar tabela de saída

1.	Crie um arquivo JSON para uma tabela de Data Factory a ser usada como uma saída do pipeline que você criará na próxima etapa. Inicie o Bloco de Notas, copie o seguinte script JSON e salve-o como **OutputBlobTable.json** na pasta **C:\ADFGetStarted\OnPrem**.

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
            		"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1
        		}
    		}
		}
  
	Observe o seguinte: 
	
	- location **type** é definido como **AzureBlobLocation**.
	- **linkedServiceName** está definido como **MyBlobStore** (você criou esse serviço vinculado na Etapa 2).
	- **folderPath** está definido como **adftutorial/outfromonpremdf** em que outfromonpremdf é a pasta no contêiner adftutorial. Você precisa apenas criar o contêiner **adftutorial**.
	- A **disponibilidade** é definida como **por hora** (**frequência** definida como **hora** e **intervalo** definido como **1**).  O serviço Data Factory vai gerar uma fatia de dados de saída a cada hora na tabela **emp** no Banco de Dados SQL do Azure. 

	Se você não especificar um **fileName** para uma **tabela de entrada**, todos os arquivos/blobs da pasta de entrada (**folderPath**) serão considerados como entradas. Se você especificar um nome de arquivo em JSON, apenas arquivo/blob especificado será considerado como entrada de asn. Consulte os arquivos de exemplo no [tutorial][adf-tutorial] para ver exemplos.
 
	Se você não especificar um **fileName** para uma **tabela de saída**, os arquivos gerados no **folderPath** serão nomeados no seguinte formato: Data.<Guid>.txt (por exemplo: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	Para definir **folderPath** e **fileName** dinamicamente com base na hora **SliceStart**, use a propriedade partitionedBy. No exemplo a seguir, folderPath usa o ano, mês e dia de SliceStart (hora de início da fatia que está sendo processada) e fileName usa a hora de SliceStart. Por exemplo, se uma fatia é produzida para 2014-10-20T08:00:00, o folderName é definido como wikidatagateway/wikisampledataout/2014/10/20 e o fileName é definido como 08.csv. 

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	Consulte [Referência de Script JSON][json-script-reference] para obter detalhes sobre as propriedades JSON.

2.	No **PowerShell do Azure**, execute o seguinte comando para criar outra tabela de Data Factory para representar a tabela no Banco de Dados SQL do Azure.

		New-AzureDataFactoryTable -DataFactoryName ADFTutorialOnPremDF -File C:\ADFGetStarted\OnPrem\OutputBlobTable.json -ResourceGroupName ADFTutorialResourceGroup  

## Etapa 4: Criar e executar um pipeline
Nesta etapa, você criará um **pipeline** com uma **Atividade de Cópia** que usa **EmpOnPremSQLTable** como entrada e **OutputBlobTable** como saída.

1.	Crie um arquivo JSON para o pipeline. Inicie o Bloco de Notas, copie o seguinte script JSON e salve-o como **ADFTutorialPipelineOnPrem.json** na pasta **C:\ADFGetStarted\OnPrem**.
	 

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
	        	]
			}
		}

	Observe o seguinte:
 
	- Na seção de atividades, há somente a atividade cujo **tipo** é definido como **CopyActivity**.
	- **A entrada** da atividade é definida como **EmpOnPremSQLTable** e a **saída** da atividade é definida como **OutputBlobTable**.
	- Na seção **transformação**, **SqlSource** é especificado como o **tipo de fonte** e **BlobSink** é especificado como o **tipo de coletor**.
	- A consulta SQL **select * from emp** é especificada para a propriedade **sqlReaderQuery** de **SqlSource**.

2. Use o cmdlet **New-AzureDataFactoryPipeline** para criar um pipeline usando o arquivo **ADFTutorialPipeline.json** criado.

		New-AzureDataFactoryPipeline  -DataFactoryName ADFTutorialOnPremDF -File C:\ADFGetStarted\OnPrem\ADFTutorialPipelineOnPrem.json -ResourceGroupName ADFTutorialResourceGroup  

3. Depois que o pipeline é criado, você pode especificar a duração em que o processamento de dados ocorrerá. Especificando o período ativo para um pipeline, você está definindo a duração de tempo em que as fatias de dados serão processadas com base nas propriedades de **Disponibilidade** que foram definidas para cada tabela do Data Factory do Azure.  Execute o seguinte comando do PowerShell para definir o período ativo no pipeline e digite Y para confirmar. 


		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialOnPremDF -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipelineOnPrem  

	> [WACOM.NOTE] Substitua o valor **StartDateTime** pelo dia atual e o valor **EndDateTime** pelo dia seguinte. StartDateTime e EndDateTime são horas UTC (Tempo Universal Coordenado) e devem estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z.EndDateTime é opcional, mas vamos usá-lo neste tutorial.
	> Se você não especificar **EndDateTime**, ele será calculado como "**StartDateTime + 48 horas**". Para executar o pipeline indefinidamente, especifique **9/9/9999** como o **EndDateTime**.

	No exemplo acima, como cada fatia de dados é produzida por hora, haverá 24 fatias de dados.
4. No **Portal de Visualização do Azure**, clique no bloco **Diagrama** na página inicial do data factory **ADFTutorialOnPremDF**. :

	![Diagram Link][image-data-factory-diagram-link]

5. Você deverá ver o diagrama semelhante ao seguinte:

	![Diagram View][image-data-factory-diagram-view]

	**Parabéns!** Você criou com êxito um data factory do Azure, serviços vinculados, tabelas e um 	pipeline, e iniciou o fluxo de trabalho.

## Etapa 5: Monitorar os conjuntos de dados e os pipelines
Nesta etapa, você utilizará o Portal do Azure para monitorar o que está acontecendo em um data factory do Azure. Você também pode usar os cmdlets do PowerShell para monitorar conjuntos de dados e pipelines. Para obter detalhes sobre como usar os cmdlets para monitoramento, consulte [Monitorar e gerenciar o Data Factory do Azure usando o PowerShell][monitor-manage-powershell].

1. Navegue até o **Portal de Visualização do Azure** (se você o fechou)
2. Se a folha para **ADFTutorialOnPremDF** não estiver aberta, abra-a clicando em **ADFTutorialOnPremDF** no **Quadro Inicial**.
3. Você verá a **contagem** e os **nomes** das tabelas e do pipeline criados nessa folha.

	![Data Factory Home Page][image-data-factory-homepage-2]
4. Agora, clique no bloco **Conjuntos de Dados**.
5. Na folha **Conjuntos de Dados**, clique em **EmpOnPremSQLTable**.

	![EmpOnPremSQLTable slices][image-data-factory-onprem-sqltable-slices]

6. Observe que as fatias de dados até o momento atual já foram produzidas e estão **Prontas**. Isso porque você inseriu os dados no banco de dados SQL Server e eles estão lá está o tempo todo. Confirme se nenhuma fatia aparecerá na seção **Fatias com problema** na parte inferior.
7. Agora, na folha **Conjuntos de Dados**, clique em **OutputBlobTable**.

	![OputputBlobTable slices][image-data-factory-output-blobtable-slices]
8. Confirme se as fatias até a hora atual estão produzidas e **Prontas**.
9. Clique em qualquer fatia de dados na lista e você verá a folha **FATIA DE DADOS**.

	![Data Slice Blade][image-data-factory-dataslice-blade]
10. Clique na **execução de atividade** na lista na parte inferior para ver **detalhes de execução da atividade**.

	![Activity Run Details blade][image-data-factory-activity-run-details]

11. Clique em **X** para fechar todos as folhas até que você volte à folha de base para **ADFTutorialOnPremDF**.
14. (opcional) Clique em **Pipelines**, clique em **ADFTutorialOnPremDF** e execute uma consulta drill-through das tabelas de entrada (**Consumidas**) ou das tabelas de saída (**Produzidas**).
15. Use ferramentas como o **Explorador de Armazenamento do Azure** para verificar a saída.

	![Azure Storage Explorer][image-data-factory-stroage-explorer]


## Criando e registrando um gateway usando cmdlets do PowerShell do Azure
Esta seção descreve como criar e registrar um gateway usando cmdlets do PowerShell do Azure. 

1. Inicie o **PowerShell do Azure** no modo de administrador. 
2. Os cmdlets do Data Factory do Azure estão disponíveis no modo **AzureResourceManager**. Execute o seguinte comando para alternar para o modo **AzureResourceManager**.     

        switch-azuremode AzureResourceManager


2. Use o cmdlet **New-AzureDataFactoryGateway** para criar um gateway lógico da seguinte maneira:

		New-AzureDataFactoryGateway -Name <gatewayName> -DataFactoryName $df -Location "West US" -ResourceGroupName ADF -Description <desc>

	**Exemplo de comando e saída**:


		PS C:> New-AzureDataFactoryGateway -Name MyGateway -DataFactoryName $df -Location "West US" -ResourceGroupName ADF -Description "gateway for walkthrough"

		Name            : MyGateway
		Location        : West US
		Description     : gateway for walkthrough
		Version         :
		Status          : NeedRegistration
		VersionStatus   : None
		CreateTime      : 9/28/2014 10:58:22
		RegisterTime    :
		LastConnectTime :
		ExpiryTime      :


3. Use o cmdlet **New-AzureDataFactoryGatewayKey** para gerar uma chave de registro para o gateway recém-criado e armazenar a chave em uma variável local **$Key**:

		New-AzureDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName $df 

	
	**Exemplo de saída de comando:**


		PS C:> $Key = New-AzureDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. No PowerShell do Azure, alterne para a pasta: **C:\Program Files\Microsoft Data Management Gateway\1.0\PowerShellScript** e execute o script **RegisterGateway.ps1** associado à variável local **$Key** conforme mostrado no seguinte comando para registrar o agente cliente instalado no computador com o gateway lógico criado anteriormente.

		PS C:> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. Você pode usar o cmdlet **Get-AzureDataFactoryGateway** para obter a lista de gateways no data factory. Quando o **Status** mostra **online**, isso significa que seu gateway está pronto para uso.

		Get-AzureDataFactoryGateway -DataFactoryName $df -ResourceGroupName ADF

Você pode remover um gateway usando o cmdlet **Remove-AzureDataFactoryGateway** e atualizar a descrição de um gateway usando os cmdlets **Set-AzureDataFactoryGateway**. Para sintaxe e outros detalhes sobre esses cmdlets, consulte Referência de Cmdlet de Data Factory.  




## Consulte também

Artigo | Descrição
------ | ---------------
[Introdução ao Data Factory do Azure][adf-getstarted] | Este artigo fornece um tutorial de ponta a ponta que mostra como criar um data factory do Azure de exemplo que copia dados de um blob do Azure para um banco de dados SQL do Azure.
[Usar o Pig e Hive com o Data Factory][use-pig-and-hive-with-data-factory] | Este artigo possui um passo a passo que mostra como usar a atividade de HDInsight para executar um script do hive/pig para processar dados de entrada para gerar dados de saída. 
[Tutorial: Mover e arquivos de log do processo usando o Data Factory][adf-tutorial] | Este artigo fornece um passo a passo que mostra como implementar um cenário próximo do real usando o Data Factory do Azure para transformar dados de arquivos de log em informações.
[Usar atividades personalizadas em um Data Factory][use-custom-activities] | Este artigo fornece um passo a passo com instruções para criar uma atividade personalizada e usá-la em um pipeline. 
[Solucionar problemas da fábrica dados][troubleshoot] | Este artigo descreve como solucionar problemas do Data Factory do Azure.  
[Referência do Desenvolvedor do Data Factory do Azure][developer-reference] | A Referência do Desenvolvedor tem o conteúdo de referência abrangente para cmdlets, script JSON, funções etc... 



[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[adf-getstarted]: ../data-factory-get-started
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[64bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517623
[32bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517624

[azure-preview-portal]: http://portal.azure.com
[adfgetstarted]: ../data-factory-get-started
[monitor-manage-powershell]: ../data-factory-monitor-manage-using-powershell





[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456



[azure-powershell-install]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/


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

[image-data-factory-azure-storage-account]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageAccount.png

[image-data-factory-azure-storage-settings]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageSettings.png

[image-data-factory-get-storage-key]: ./media/data-factory-use-onpremises-datasources/OnPremGetStorageKey.png

[image-data-factory-linkedservices-with-storage]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesWithMyBlobStore.png

[image-data-factory-linkedservices-add-gateway-button]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesAddGaewayButton.png

[image-data-factory-create-gateway-blade]: ./media/data-factory-use-onpremises-datasources/OnPremCreateGatewayBlade.png

[image-data-factory-gateway-configure-blade]: ./media/data-factory-use-onpremises-datasources/OnPremGatewayConfigureBlade.png

[image-data-factory-gateway-configuration-manager]: ./media/data-factory-use-onpremises-datasources/OnPremDMGConfigurationManager.png

[image-data-factory-add-datastore-SQL-button]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesAddSQLDataStoreButton.png

[image-data-factory-gateway-configure-link]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataStoreDataGatewayConfigureLink.png

[image-data-factory-credentials-blade]: ./media/data-factory-use-onpremises-datasources/OnPremCredentionlsBlade.png

[image-data-factory-oneclick-install]: ./media/data-factory-use-onpremises-datasources/OnPremOneClickInstall.png

[image-data-factory-diagram-link]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramLink.png

[image-data-factory-diagram-view]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramView.png

[image-data-factory-homepage-2]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage2.png

[image-data-factory-stroage-explorer]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageExplorer.png

[image-data-factory-home-age]: ./media/data-factory-use-onpremises-datasources/DataFactoryHomePage.png

[image-data-factory-linkedservices-blade-onprem]: ./media/data-factory-use-onpremises-datasources/LinkedServiceBladeWithOnPremSql.png

[image-data-factory-onprem-sqltable-slices]: ./media/data-factory-use-onpremises-datasources/OnPremSQLTableSlicesBlade.png

[image-data-factory-output-blobtable-slices]: ./media/data-factory-use-onpremises-datasources/OutputBlobTableSlicesBlade.png

[image-data-factory-dataslice-blade]: ./media/data-factory-use-onpremises-datasources/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-use-onpremises-datasources/ActivityRunDetailsBlade.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-use-onpremises-datasources/NewDataFactoryMenu.png

[image-data-factory-preview-portal-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

<!--HONumber=46--> 
