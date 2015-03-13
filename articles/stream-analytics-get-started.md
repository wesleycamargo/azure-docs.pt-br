<properties 
	pageTitle="Introdução ao uso da Análise de fluxo do Azure | Azure" 
	description="Comece a usar a análise de fluxo do Azure para processar e transformar eventos no Hub de eventos de barramento de serviço do Azure e armazenar os resultados no banco de dados SQL do Azure." 
	services="stream-analytics" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="2/17/2015" 
	ms.author="jgao" />


# Introdução ao uso da Análise de fluxo do Azure

A Análise de fluxo do Azure é um serviço completamente gerenciado que oferece baixa latência, alta disponibilidade e processamento escalonável de eventos complexos ao longo do fluxo de dados na nuvem. Para obter mais informações, consulte a [Introdução à análise de fluxo do Azure][stream.analytics.introduction] e a [documentação da Análise de fluxo do Azure][stream.analytics.documentation].

Para ajudar você a começar a usar a Análise de fluxo rapidamente, este tutorial mostrará como consumir temperatura de dispositivo lendo dados de um [Hub de eventos do barramento de serviço do Azure][azure.event.hubs.documentation] e processar os dados, exibindo os resultados em um [Banco de Dados SQL do Azure][azure.sql.database.documentation].  O diagrama a seguir mostra o fluxo de eventos de entrada para o processamento de saída:
  
![Azure Stream Analytics get started flow][img.get.started.flowchart]

##Gerar dados de exemplo do Hub de evento
Este tutorial aproveitará o aplicativo de Introdução aos hubs de evento de Barramento de Serviço, um exemplo de código CodeGallery do MSDN, para criar um novo Hub de evento, gerar leituras de temperatura do dispositivo de exemplo e enviar o dispositivo de leitura de dados para o Hub de evento.

###Criar um namespace do Barramento de Serviço
O aplicativo de exemplo criará um Hub de evento em um namespace preexistente do barramento de serviço.  Você pode usar um namespace do Barramento de Serviço já provisionado ou seguir as etapas abaixo para criar um novo:

1.	Entre no [Portal de Gerenciamento do Azure][azure.management.portal].
2.	Clique em **﻿﻿CRIAR** na parte inferior da página de Barramento de Serviço e siga as instruções para criar um namespace. Use **﻿﻿MENSAGENS** como o tipo.
3.	Clique no namespace recém-criado e em **﻿﻿INFORMAÇÕES DE CONEXÃO** na parte inferior da página.
4.	Copie a cadeia de conexão. Você a usará mais tarde no tutorial.

###Criar uma conta de Armazenamento do Azure

Este aplicativo de exemplo requer uma conta de Armazenamento do Azure ou um emulador de armazenamento para manter o estado do aplicativo. Você pode usar uma conta de Armazenamento existente ou seguir as etapas abaixo para criar uma: 

1.	No portal, crie uma nova Conta de armazenamento clicando em **NOVA**, **﻿﻿SERVIÇOS DE DADOS**, **﻿﻿ARMAZENAMENTO**, **﻿﻿CRIAÇÃO RÁPIDA** e siga as instruções.
2.	Selecione a conta de armazenamento criada recentemente e, em seguida, clique em **﻿﻿GERENCIAR CHAVES DE ACESSO** na parte inferior da página.
3.	Copie o nome da conta de armazenamento e uma das chaves de acesso.

###Gerar dados de exemplo do Hub de evento

1.	Baixe e descompacte o [Service Bus evento Hubs obtendo Started.zip](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Event-Hub-286fd097) na sua estação de trabalho.
2.	Abra o arquivo de solução **EventHubSample.sln** no Visual Studio.
3.	Abra o **App. config**.
4.	Especifique o Barramento de Serviço e as cadeias de conexão da conta de Armazenamento. Os nomes de chave são **Microsoft.ServiceBus.ConnectionString** e **AzureStorageConnectionString**. A cadeia de conexão da conta de armazenamento será no seguinte formato: 	

		DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<yourAccountKey>;
5.	Compilar a solução.
6.	Execute o aplicativo da pasta bin.  A utilização é o seguinte: 

		BasicEventHubSample <eventhubname> <NumberOfMessagesToSend> <NumberOfPartitions> 

O exemplo a seguir cria um novo Hub de evento chamado **devicereadings** com **16** partições e, em seguida, envia **200** eventos para o Hub de eventos: 

		BasicEventHubSample devicereadings 200 16

 	![insert image here][img.stream.analytics.event.hub.client.output] 
 	

###Crie uma Política de acesso compartilhado do hub de evento 
Enquanto houver uma Política de acesso compartilhado no namespace de Barramento de Serviço que pode ser usado para conectar-se a tudo dentro do espaço, para práticas recomendadas de segurança criaremos uma política separada somente para o Hub de eventos.

1.	No espaço de trabalho de Barramento de Serviço no portal, clique no nome do namespace de Barramento de Serviço.
2.	Clique em **HUBS DE EVENTO** na parte superior da página.
3.	Clique em **devicereadings**, o Hub de eventos para este tutorial.
4.	Clique em **CONFIGURAR** na parte superior da página.
5.	Em Políticas de acesso compartilhado, crie uma nova política com permissões para **Gerenciar**.

	![][img.stream.analytics.event.hub.shared.access.policy.config]
6.	Clique em **﻿﻿﻿﻿SALVAR** na parte inferior da página.
7.	Se o Hub de eventos estiver em uma assinatura diferente da que o trabalho de Análise de fluxo estará, você precisará copiar e salvar as informações de conexão para mais tarde.  Para fazer isso, clique em **﻿PAINEL**, clique em **﻿﻿INFORMAÇÕES DE CONEXÃO** na parte inferior da página e salve a Cadeia de conexão.


##Preparar um Banco de Dados SQL do Azure para armazenar dados de saída
A Análise de fluxo do Azure pode produzir dados ao Banco de Dados SQL do Azure, armazenamento de ﻿﻿Blobs do Azure e Hub de eventos do Azure. Neste tutorial, você definirá um trabalho que gera um Banco de Dados SQL do Azure. Para obter mais informações, consulte a Introdução ao Banco de Dados SQL do Microsoft Azure.

###Criar um Banco de Dados SQL do Azure
Se já tiver um Banco de Dados SQL do Azure para usar neste tutorial, ignore esta seção.

1.	No Portal de Gerenciamento, clique em **﻿﻿NOVO**, ﻿﻿**SERVIÇOS DE DADOS**, **﻿﻿﻿BANCO DE DADOS SQL**, **﻿﻿CRIAÇÃO RÁPIDA**.  Especifique um nome de banco de dados existente ou um novo servidor de banco de dados SQL.
2.	Selecione o banco de dados recém-criado
3.	Clique em **﻿PAINEL**, clique em **Mostrar cadeias de conexão** no painel à direita da página e copie a cadeia de conexão **ADO.NET**. Você a usará mais tarde no tutorial.  
4.	Verifique se que as configurações de firewall de nível de servidor permitem que você se conecte ao banco de dados.  Você pode fazer isso adicionando uma nova regra IP no guia de configuração do servidor. Para obter mais detalhes, incluindo como lidar com IP dinâmico, consulte [http://msdn.microsoft.com/library/azure/ee621782.aspx](http://msdn.microsoft.com/library/azure/ee621782.aspx).

###Criar tabelas de saída
1.	Abra o Visual Studio ou o SQL Server Management Studio.
2.	Conecte-se ao Banco de Dados SQL do Azure.
3.	Use as seguintes instruções T-SQL para criar duas tabelas no banco de dados:

		CREATE TABLE [dbo].[PassthroughReadings] (
		    [DeviceId]      BIGINT NULL,
			[Temperature] BIGINT    NULL
		);

		GO
		CREATE CLUSTERED INDEX [PassthroughReadings]
		    ON [dbo].[PassthroughReadings]([DeviceId] ASC);
		GO

		CREATE TABLE [dbo].[AvgReadings] (
		    [WinStartTime]   DATETIME2 (6) NULL,
		    [WinEndTime]     DATETIME2 (6) NULL,
		    [DeviceId]      BIGINT NULL,
			[AvgTemperature] FLOAT (53)    NULL,
			[EventCount] BIGINT null
		);
		
		GO
		CREATE CLUSTERED INDEX [AvgReadings]
		    ON [dbo].[AvgReadings]([DeviceId] ASC);

>[WACOM.NOTE] Índices clusterizados são necessárias em todas as tabelas de banco de dados SQL para inserir dados.
	   
##Criar um trabalho de análise de fluxo

Depois de ter criado o Hub de evento do Barramento de Serviço do Azure, o banco de dados SQL do Azure e as tabelas de saída, você está pronto para criar um trabalho de Análise de fluxo.

###Provisionar um trabalho de análise de fluxo
1.	No Portal de Gerenciamento, clique em **﻿﻿NOVO**, ﻿﻿**SERVIÇOS DE DADOS**, **﻿﻿﻿ANÁLISE DE FLUXO**, **﻿﻿CRIAÇÃO RÁPIDA**. 
2.	Especifique os seguintes valores e, em seguida, clique em **﻿﻿CRIAR TRABALHO DE ANÁLISE DE FLUXO**:

	- **NOME DO TRABALHO**: Digite um nome de trabalho. Por exemplo, **DeviceTemperatures**.
	- **REGIÃO**: Selecione a região onde você deseja executar o trabalho. Análise de fluxo do Azure está disponível atualmente apenas em 2 regiões durante a visualização. Para obter mais informações, consulte [Limitações e problemas conhecidos da Análise de fluxo do Azure][stream.analytics.limitations]. Considere colocar o trabalho e o Hub de eventos na mesma região para garantir um desempenho melhor e que você não estará pagando para transferir dados entre regiões.
	- **CONTA DE ARMAZENAMENTO**: Escolha a conta de armazenamento que você deseja usar para armazenar dados de monitoramento de todos os trabalhos de análise de fluxo em execução nessa região. Você tem a opção de escolher uma conta de armazenamento existente ou criar uma nova.
	
3.	Clique em **﻿﻿ANÁLISE DE FLUXO** no painel à esquerda para listar os trabalhos de Análise de fluxo.

	![][img.stream.analytics.portal.button]
 
O novo trabalho será listado com um status de **﻿﻿NÃO INICIADO**.  Observe que o botão **﻿INICIAR**, na parte inferior da página, está desabilitado. Você deve configurar o trabalho de entrada, saída, consulta e assim por diante antes de iniciar o trabalho. 

###Especificar entrada de trabalho

1.	Clique no nome do trabalho.
2.	Clique em **ENTRADAS** da parte superior da página e, em seguida, clique em **﻿ADICIONAR ENTRADA**. A caixa de diálogo que se abre vai orientá-lo por meio de uma série de etapas para configurar sua Entrada.
3.	Selecione o **﻿﻿FLUXO DE DADOS** e, em seguida, clique no botão à direita.
4.	Selecione o **HUB DE EVENTO** e, em seguida, clique no botão à direita
5.	Digite ou selecione os seguintes valores na terceira página: 

	- **ALIAS DE ENTRADA**: Digite um nome amigável para este trabalho de entrada. Observe que você usará esse nome na consulta posteriormente.
	- **HUB DE EVENTOS**: Se o Hub de eventos que você criou na mesma assinatura que o trabalho de análise de fluxo, selecione o namespace que é o Hub de eventos no.

		Se o seu Hub de evento for em uma assinatura diferente, selecione **Usar o hub de eventos de outra assinatura** e insira manualmente o **NAMESPACE ﻿﻿DO BARRAMENTO DE SERVIÇO**, **﻿﻿﻿NOME DO HUB DE EVENTO**, **﻿﻿﻿NOME DE POLÍTICA DO HUB DE EVENTO**, **﻿﻿﻿CHAVE DE POLÍTICA DE HUB DE EVENTO** e **﻿﻿﻿A CONTAGEM DE PARTIÇÃO DO HUB DE EVENTOS**.  

		>[WACOM.NOTE] Este exemplo usa o número padrão de partições, que é 16.
		
	- **NOME DO HUB DE EVENTOS**: Selecione o nome do Hub de eventos do Azure que você criou. Para este tutorial use **devicereadings**.
	- **NOME DA POLÍTICA DO HUB DE EVENTOS**: Selecione a política de Hub de eventos criada anteriormente neste tutorial.
 
	![][img.stream.analytics.config.input]

6.	Clique no botão direito.
7.	Especifique os seguintes valores:

	- **FORMATO DE SERIALIZADOR DE EVENTO**: JSON
	- **CODIFICAÇÃO**: UTF8

8.	Clique no botão de seleção para adicionar essa fonte e verificar se a Análise de fluxo pode se conectar com êxito ao Hub de eventos.

###Especifique a saída do trabalho
1.	Clique em **﻿﻿SAÍDA** da parte superior da página, clique em **﻿﻿﻿﻿ADICIONAR SAÍDA**.
2.	Selecione o **BANCO DE DADOS SQL** e, em seguida, clique no botão à direita.
3.	Digite ou selecione os valores a seguir.  Use a cadeia de conexão do banco de dados ADO.NET para preencher os seguintes campos:

	- **﻿﻿﻿BANCO DE DADOS SQL**: Escolha o Banco de Dados SQL criado anteriormente no tutorial.  Se ele estiver em - a mesma assinatura, selecione o banco de dados no menu suspenso.   Caso contrário, insira manualmente os campos Nome do servidor e Banco de dados. 
	- **﻿NOME DE USUÁRIO**: Insira o nome de logon do Banco de dados SQL.
	- **﻿SENHA**: Insira a senha de logon do Banco de dados SQL.
	- **﻿TABELA**: Especifique a tabela que você deseja enviar a saída.  Por enquanto, use **PassthroughReadings**.

	![][img.stream.analytics.config.output]

4.	Clique no botão de seleção para criar a saída e verificar se a análise de fluxo com êxito pode se conectar ao banco de dados SQL conforme especificado.

###Especifique a consulta de trabalho
A Análise de fluxo dá suporte a um modelo de consulta simples e declarativo para descrever as transformações.  Para saber mais sobre a linguagem, consulte a Referência de linguagem de consulta da análise de fluxo do Azure.  

Este tutorial começa com uma consulta de passagem simples que gera leituras de temperatura do dispositivo em uma tabela do Banco de Dados SQL.

1.	Clique em **Consulta** na parte superior da página 
2.	Adicione o seguinte ao editor de código:

		﻿SELECIONE DeviceId, Temperatura DA entrada
Certifique-se de que o nome da fonte de entrada corresponde ao nome da entrada que você especificou anteriormente.
3.	Clique em **﻿SALVAR** na parte inferior da página e em **﻿SIM** para confirmar.

##Iniciar o trabalho
Como padrão, os trabalhos de Análise de fluxo começam a ler eventos de entrada no momento em que o trabalho é iniciado.  Como o Hub de evento contém dados existentes para processar, precisamos configurar o trabalho para consumir esses dados históricos.  

1.	Clique em **﻿CONFIGURAR** na parte superior da página.
2.	Altere o valor de **﻿﻿SAÍDA DE INÍCIO** para **﻿﻿TEMPO PERSONALIZADO** e especifique uma hora de início.  Certifique-se de que a hora de início é um dia antes da hora em que você executou BasicEventHubSample.  
3.	Clique em **﻿SALVAR** na parte inferior da página e em **﻿SIM** para confirmar.
3.	Clique em **﻿PAINEL** na parte superior da página, clique em **﻿INICIAR** na parte inferior da página e em **﻿SIM** para confirmar.  No painel **de visão rápida**, o **STATUS** será alterado para **Iniciando** e pode levar alguns minutos para concluir o processo inicial e mover para o estado de **Execução**.   


##Exibir saída de trabalho

1.	No Visual Studio ou o SQL Server Management Studio, conecte-se ao SEU banco de dados SQL e execute a seguinte consulta: 

		SELECT * FROM PassthroughReadings

2.	Você verá os registros correspondentes aos eventos de leitura do Hub de eventos.   

	![][img.stream.analytics.job.output1]

	Você pode executar novamente o aplicativo BasicEventHubSample para gerar novos eventos e vê-los a se propagar para a saída em tempo real executando novamente a seleção * consulta.
	
	Se você tiver problemas com a saída ausente ou inesperado, exiba os Logs de operação para o trabalho, vinculado no painel à direita da página do Painel.

##Parar, atualizar e reiniciar o Trabalho
Agora, vamos fazer uma consulta mais interessante sobre os dados.
1.	Na página do **﻿﻿PAINEL** ou do **MONITOR**, clique em **﻿PARAR**.
2.	Na página de **﻿﻿CONSULTA**, substitua a consulta existente pela seguinte e, em seguida, clique em **﻿SALVAR**:

		SELECT DateAdd(second,-5,System.TimeStamp) as WinStartTime, system.TimeStamp as WinEndTime, DeviceId, Avg(Temperature) as AvgTemperature, Count(*) as EventCount 
		FROM input
		GROUP BY TumblingWindow(second, 5), DeviceId

	Essa nova consulta usará a hora em que o evento foi enviado para o Hub de eventos como o carimbo de data/hora, localizando e projetar a temperatura média 5 segundos e o número de eventos que se enquadram nessa janela segundo 5 de leitura.
3.	Na página de **﻿﻿SAÍDA**, clique em **﻿EDITAR**.  Alterar a tabela de saída de PassthroughReadings para AvgReadings e, em seguida, clique no ícone de verificação.

4.	Na página do **﻿PAINEL**, clique em **﻿INICIAR**.

##Exibir saída de trabalho

1.	No Visual Studio ou o SQL Server Management Studio, conecte-se ao banco de dados SQL e execute a seguinte consulta:

		SELECT * from AvgReadings

2.	Você verá registros de intervalos de 5 segundos mostrando a temperatura média e o número de eventos de cada dispositivo: 

	![][img.stream.analytics.job.output2]
 
3.	 Para continuar a receber eventos processados pelo trabalho em execução, execute novamente o aplicativo BasicEventHubSample.







##<a name="nextsteps"></a>Próximas etapas
Neste tutorial, você aprendeu a usar a Análise de fluxo para processar os dados meteorológicos. Para saber mais, consulte os seguintes artigos:


- [Introdução à análise de fluxo do Azure][stream.analytics.introduction]
- [Guia de desenvolvedor da Análise de fluxo do Azure][stream.analytics.developer.guide]
- [Escalonar trabalhos de Análise de fluxo do Azure][stream.analytics.scale]
- [Limitações e problemas conhecidos da Análise de fluxo do Azure][stream.analytics.limitations]
- [Referência da linguagem de consulta da Análise de fluxo do Azure][stream.analytics.query.language.reference]
- [Referência da API REST do gerenciamento da Análise de fluxo do Azure ][stream.analytics.rest.api.reference]




[img.stream.analytics.event.hub.client.output]: .\media\stream-analytics-get-started\AzureStreamAnalyticsEHClientOuput.png
[img.stream.analytics.event.hub.shared.access.policy.config]: .\media\stream-analytics-get-started\AzureStreamAnalyticsEHSharedAccessPolicyConfig.png
[img.stream.analytics.job.output2]: .\media\stream-analytics-get-started\AzureStreamAnalyticsSQLOutput2.png
[img.stream.analytics.job.output1]: .\media\stream-analytics-get-started\AzureStreamAnalyticsSQLOutput1.png
[img.stream.analytics.config.output]: .\media\stream-analytics-get-started\AzureStreamAnalyticsConfigureOutput.png
[img.stream.analytics.config.input]: .\media\stream-analytics-get-started\AzureStreamAnalyticsConfigureInput.png



[img.get.started.flowchart]: ./media/stream-analytics-get-started/StreamAnalytics.get.started.flowchart.png
[img.job.quick.create]: ./media/stream-analytics-get-started/StreamAnalytics.quick.create.png
[img.stream.analytics.portal.button]: ./media/stream-analytics-get-started/StreamAnalyticsPortalButton.png
[img.event.hub.policy.configure]: ./media/stream-analytics-get-started/StreamAnalytics.Event.Hub.policy.png
[img.create.table]: ./media/stream-analytics-get-started/StreamAnalytics.create.table.png
[img.stream.analytics.job.output]: ./media/stream-analytics-get-started/StreamAnalytics.job.output.png
[img.stream.analytics.operation.logs]: ./media/stream-analytics-get-started/StreamAnalytics.operation.log.png
[img.stream.analytics.operation.log.details]: ./media/stream-analytics-get-started/StreamAnalytics.operation.log.details.png


[azure.sql.database.firewall]: http://msdn.microsoft.com/library/azure/ee621782.aspx
[azure.event.hubs.documentation]: http://azure.microsoft.com/services/event-hubs/
[azure.sql.database.documentation]: http://azure.microsoft.com/services/sql-database/

[sql.database.introduction]: http://azure.microsoft.com/services/sql-database/
[event.hubs.introduction]: http://azure.microsoft.com/services/event-hubs/
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.sdk.net]: ../dotnet-sdk/

[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.scale]: ../stream-analytics-scale-jobs/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.documentation]: http://go.microsoft.com/fwlink/?LinkId=512093




[azure.management.portal]: https://manage.windowsazure.com


<!--HONumber=46--> 
