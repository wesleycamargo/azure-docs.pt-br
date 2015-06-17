<properties 
	pageTitle="Monitoramento e gerenciamento de trabalhos do Stream Analytics usando o PowerShell | Azure" 
	description="Aprenda a usar os cmdlets do PowerShell do Azure para monitorar e gerenciar trabalhos de Stream Analytics" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="05/07/2015" 
	ms.author="jeffstok"/>


# Monitorar e gerenciar trabalhos de Stream Analytics usando o PowerShell do Azure

Saiba como gerenciar os recursos de Stream Analytics do Azure usando o PowerShell do Azure.

## Pré-requisitos para a execução de cmdlets do PowerShell do Azure para Stream Analytics

1.	Instalar e configurar o PowerShell do Azure.

	Siga as instruções em [Como instalar e configurar o PowerShell do Azure][powershell-install] para instalar o PowerShell do Azure.

	Para se conectar à sua assinatura do Azure usando o método do Active Directory do Azure:

		Add-AzureAccount

	Selecione sua assinatura do Azure com o serviço de Stream Analytics do Azure habilitado:

		Select-AzureSubscription

	>[AZURE.NOTE]A seguinte mensagem de erro indica que a Stream Analytics do Azure não está habilitada na assinatura:
	>
		Error Code: InvalidResourceType.  Error Message: The resource type 'streamingjobs' could not be found in the namespace 'Microsoft.StreamAnalytics'.  
	
	>Para resolver esse problema, habilite a visualização da Stream Analytics na assinatura e, em seguida, execute os seguintes cmdlets para alternar a assinatura:
	>
		Select-AzureSubscription –SubscriptionId xxxxxxxx

2.	Configure o modo do Azure.

	Depois de instalar o PowerShell do Azure, execute o cmdlet [Switch-AzureMode][msdn-switch-azuremode] para definir o modo apropriado do Azure para acessar os cmdlets de Stream Analytics:

		Switch-AzureMode AzureResourceManager

>[AZURE.NOTE]Há uma limitação temporária onde os trabalhos do Stream Analytics criados por meio do PowerShell do Azure não têm monitoramento habilitado. Para solucionar esse problema, navegue até a página **Monitor** do trabalho no Portal do Azure e clique no botão **Habilitar**.

## Cmdlets do PowerShell do Azure para Stream Analytics
Os seguintes cmdlets do PowerShell do Azure podem ser usados para monitorar e gerenciar trabalhos de Stream Analytics do Azure.

### Get-AzureStreamAnalyticsJob
Lista todas os trabalhos de Stream Analytics definidos na assinatura do Azure ou especificados no grupo de recursos ou obtém informações sobre um trabalho específico dentro de um grupo de recursos.

**Exemplo 1**

	Get-AzureStreamAnalyticsJob

Esse comando retorna informações sobre todos os trabalhos do Stream Analytics na assinatura do Azure.

**Exemplo 2**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
Esse comando retorna informações sobre todos os trabalhos de Stream Analytics no grupo de recursos StreamAnalytics-Default-Central-US.

**Exemplo 3**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
Esse comando retorna informações sobre o trabalho StreamingJob de Stream Analytics no grupo de recursos StreamAnalytics-Default-Central-US.

### Get-AzureStreamAnalyticsInput
Lista todas as entradas que são definidas em um trabalho específico de Stream Analytics ou obtém informações sobre uma entrada específica.

**Exemplo 1**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Esse comando retorna informações sobre todas as entradas definidas no trabalho StreamingJob.

**Exemplo 2**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream
Esse comando retorna informações sobre a entrada denominada EntryStream definida no trabalho StreamingJob.

### Get-AzureStreamAnalyticsOutput
Lista todas as saídas que são definidas em um trabalho específico de Stream Analytics ou obtém informações sobre uma saída específica.

**Exemplo 1**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
Esse comando retorna informações sobre as saídas definidas no trabalho StreamingJob.

**Exemplo 2**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output
Esse comando retorna informações sobre a saída denominada Output definida no trabalho StreamingJob.

### Get-AzureStreamAnalyticsQuota
Obtém informações sobre a cota de streaming de unidades em uma região especificada.

**Exemplo 1**

	Get-AzureStreamAnalyticsQuota –Location "Central US" 
Esse comando retorna informações sobre a cota e o uso de unidades de streaming na região Central dos Estados Unidos.

### Get-AzureStreamAnalyticsTransformation
Obtém informações sobre uma transformação específica definida no trabalho de Stream Analytics.

**Exemplo 1**

	Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob
Esse comando retorna informações sobre a transformação chamada StreamingJob no trabalho StreamingJob.

### New-AzureStreamAnalyticsInput
Cria uma nova entrada dentro do trabalho do Stream Analytics ou atualiza uma entrada existente especificada.
  
O nome da entrada pode ser especificado no arquivo .json ou na linha de comando. Se ambos forem especificados, o nome na linha de comando deve ser o mesmo que o do arquivo.

Se você especificar uma entrada que já existe e não especificar o parâmetro –Force, o cmdlet perguntará se deseja ou não substituir a entrada existente.

Se você especificar o parâmetro –Force e especificar um nome de entrada existente, a entrada será substituída sem confirmação.

Para obter informações detalhadas sobre a estrutura do arquivo JSON e o conteúdo, consulte a seção [Criar entrada (Stream Analytics do Azure)][msdn-rest-api-create-stream-analytics-input] da [Biblioteca de referência de API REST de gerenciamento de Stream Analytics][stream.analytics.rest.api.reference].

**Exemplo 1**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 
Este comando cria uma nova entrada do arquivo Input.json. Se uma entrada existente com o nome especificado no arquivo de definição de entrada já estiver definida, o cmdlet perguntará se deseja ou não substituí-la.

**Exemplo 2**
	
	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream
Este comando cria uma nova entrada no trabalho chamado EntryStream. Se uma entrada existente com esse nome já estiver definida, o cmdlet perguntará se deseja ou não substituí-la.

**Exemplo 3**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force
Este comando substitui a definição da fonte de entrada existente chamada EntryStream com a definição do arquivo.

### New-AzureStreamAnalyticsJob
Cria um novo trabalho de Stream Analytics no Microsoft Azure ou atualiza a definição de um trabalho existente especificado.

O nome do trabalho pode ser especificado no arquivo .json ou na linha de comando. Se ambos forem especificados, o nome na linha de comando deve ser o mesmo que o do arquivo.

Se você especificar um nome de trabalho que já existe e não especificar o parâmetro –Force, o cmdlet perguntará se deseja ou não substituir o trabalho existente.

Se você especificar o parâmetro –Force e especificar um nome de trabalho existente, a definição do trabalho será substituída sem confirmação.

Para obter informações detalhadas sobre a estrutura do arquivo JSON e o conteúdo, consulte a seção [Criar trabalho de Stream Analytics][msdn-rest-api-create-stream-analytics-job] da [Biblioteca de referência de API REST de gerenciamento de Stream Analytics][stream.analytics.rest.api.reference].

**Exemplo 1**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 
Este comando cria um novo trabalho a partir da definição em JobDefinition.json. Se um trabalho existente com o nome especificado no arquivo de definição de trabalho já estiver definido, o cmdlet perguntará se deseja ou não substituí-lo.

**Exemplo 2**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force
Este comando substitui a definição de trabalho para StreamingJob.

### New-AzureStreamAnalyticsOutput
Cria uma nova saída dentro de um trabalho de Stream Analytics ou atualiza uma saída existente.

O nome da saída pode ser especificado no arquivo .json ou na linha de comando. Se ambos forem especificados, o nome na linha de comando deve ser o mesmo que o do arquivo.

Se você especificar uma saída que já existe e não especificar o parâmetro –Force, o cmdlet perguntará se deseja ou não substituir a saída existente.

Se você especificar o parâmetro –Force e especificar um nome de saída existente, a saída será substituída sem confirmação.

Para obter informações detalhadas sobre a estrutura do arquivo JSON e o conteúdo, consulte a seção [Criar saída (Stream Analytics do Azure)][msdn-rest-api-create-stream-analytics-output] da [Biblioteca de referência de API REST de gerenciamento de Stream Analytics][stream.analytics.rest.api.reference].

**Exemplo 1**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output
Este comando cria uma nova saída chamada "output" no trabalho StreamingJob. Se uma saída existente com esse nome já estiver definida, o cmdlet perguntará se deseja ou não substituí-la.

**Exemplo 2**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force
Este comando substitui a definição para “output" no trabalho StreamingJob.

### New-AzureStreamAnalyticsTransformation
Cria uma nova transformação dentro de um trabalho de Stream Analytics ou atualiza uma transformação existente.
  
O nome da transformação pode ser especificado no arquivo .json ou na linha de comando. Se ambos forem especificados, o nome na linha de comando deve ser o mesmo que o do arquivo.

Se você especificar uma transformação que já existe e não especificar o parâmetro –Force, o cmdlet perguntará se deseja ou não substituir a transformação existente.

Se você especificar o parâmetro –Force e especificar um nome de transformação existente, a transformação será substituída sem confirmação.

Para obter informações detalhadas sobre a estrutura do arquivo JSON e o conteúdo, consulte a seção [Criar transformação (Stream Analytics do Azure)][msdn-rest-api-create-stream-analytics-transformation] da [Biblioteca de referência de API REST de gerenciamento de Stream Analytics][stream.analytics.rest.api.reference].

**Exemplo 1**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform
Este comando cria uma nova transformação chamada StreamingJobTransform no trabalho StreamingJob. Se uma transformação existente com esse nome já estiver definida, o cmdlet perguntará se deseja ou não substituí-la.

**Exemplo 2**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force
 Este comando substitui a definição de StreamingJobTransform no trabalho StreamingJob.

### Remove-AzureStreamAnalyticsInput
Exclui de maneira assíncrona uma entrada específica de um trabalho do Stream Analytics no Microsoft Azure. Se você especificar o parâmetro –Force, a entrada será excluída sem confirmação.

**Exemplo 1**
	
	Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream
Este comando remove a entrada EventStream do trabalho StreamingJob.

### Remove-AzureStreamAnalyticsJob
Exclui de maneira assíncrona um trabalho específico do Stream Analytics no Microsoft Azure. Se você especificar o parâmetro –Force, o trabalho será excluído sem confirmação.

**Exemplo 1**

	Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
Esse comando remove o trabalho StreamingJob.

### Remove-AzureStreamAnalyticsOutput
Exclui de maneira assíncrona uma saída específica de um trabalho do Stream Analytics no Microsoft Azure. Se você especificar o parâmetro –Force, a saída será excluída sem confirmação.

**Exemplo 1**

	Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
Este comando remove a saída Output do trabalho StreamingJob.

### Start-AzureStreamAnalyticsJob
Implanta de maneira assíncrona e inicia um trabalho do Stream Analytics no Microsoft Azure.

**Exemplo 1**

	Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Esse comando inicia o trabalho StreamingJob com uma hora personalizada de início de saída definida como 12 de dezembro de 2012, 12:12:12 UTC.


### Stop-AzureStreamAnalyticsJob
Interrompe de maneira assíncrona um trabalho do Stream Analytics para que não seja executado no Microsoft Azure e desaloca os recursos que estavam sendo usados. A definição de trabalho e os metadados permanecerão disponíveis dentro da sua assinatura por meio de APIs de gerenciamento e do Portal do Azure, de modo que o trabalho possa ser editado e reiniciado. Você não será cobrado por um trabalho no estado Interrompido.

**Exemplo 1**

	Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
Esse comando interrompe o trabalho StreamingJob.

### Test-AzureStreamAnalyticsInput
Testa a capacidade do Stream Analytics de se conectar a uma entrada especificada.

**Exemplo 1**

	Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream
Esse comando testa o status de conexão da entrada EntryStream no StreamingJob.

###Test-AzureStreamAnalyticsOutput
Testa a capacidade do Stream Analytics de se conectar a uma saída especificada.

**Exemplo 1**

	Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
Este comando testa o status da conexão da saída Output no StreamingJob.

> [AZURE.NOTE]Os trabalhos de Stream Analytics criados programaticamente não têm monitoramento habilitado por padrão. Você pode habilitar manualmente o monitoramento no Portal do Azure, navegando até a página de monitoramento do trabalho e clicando no botão Ativar ou você pode fazer isso programaticamente, seguindo as etapas em [Stream Analytics do Azure - Monitorar programaticamente os trabalhos de Stream Analytics](stream-analytics-monitor-jobs.md)

## Obtenha suporte
Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)


## Próximas etapas

- [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
- [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
- [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
- [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 



[msdn-switch-azuremode]: http://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.limitations]: ../stream-analytics-limitations.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!--HONumber=54--> 