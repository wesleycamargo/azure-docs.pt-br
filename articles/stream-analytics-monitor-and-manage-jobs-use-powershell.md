<properties title="Azure Stream Analytics monitor and manage jobs using Azure PowerShell" pageTitle="Monitoramento e gerenciamento de trabalhos do Stream Analytics usando o PowerShell | Azure" description="Aprenda como usar os cmdlets do PowerShell do Azure para monitorar e gerenciar trabalhos de análise de fluxo" metaKeywords="" services="stream-analytics" solutions="" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="stream-analytics" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-services" ms.date="12/9/2014" ms.author="jgao" />


#Monitorar e gerenciar trabalhos do Stream Analytics usando o PowerShell do Azure

Saiba como gerenciar os recursos do Stream Analytics do Azure usando o PowerShell do Azure.

##Neste artigo

- [Pré-requisitos](#prerequisites)
- [Cmdlets do PowerShell do Stream Analytics](#cmdlets)
	- [Get-AzureStreamAnalyticsJob](#Get-AzureStreamAnalyticsJob)
	- [Get-AzureStreamAnalyticsInput](#Get-AzureStreamAnalyticsInput)
	- [Get-AzureStreamAnalyticsOutput](#Get-AzureStreamAnalyticsOutput)
	- [Get-AzureStreamAnalyticsQuota](#Get-AzureStreamAnalyticsQuota)
	- [Get-AzureStreamAnalyticsTransformation](#Get-AzureStreamAnalyticsTransformation)
	- [New-AzureStreamAnalyticsInput](#New-AzureStreamAnalyticsInput)
	- [New-AzureStreamAnalyticsJob](#New-AzureStreamAnalyticsJob)
	- [New-AzureStreamAnalyticsOutput](#New-AzureStreamAnalyticsOutput)
	- [New-AzureStreamAnalyticsTransformation](#New-AzureStreamAnalyticsTransformation)
	- [Remove-AzureStreamAnalyticsInput](#Remove-AzureStreamAnalyticsInput)
	- [Remove-AzureStreamAnalyticsJob](#Remove-AzureStreamAnalyticsJob)
	- [Remove-AzureStreamAnalyticsOutput](#Remove-AzureStreamAnalyticsOutput)
	- [Start-AzureStreamAnalyticsJob](#Start-AzureStreamAnalyticsJob)
	- [Stop-AzureStreamAnalyticsJob](#Stop-AzureStreamAnalyticsJob)
	- [Test-AzureStreamAnalyticsInput](#Test-AzureStreamAnalyticsInput)
	- [Test-AzureStreamAnalyticsOutput](#Test-AzureStreamAnalyticsOutput)
- [Confira também](#seealso)

## <a name="prerequisites"></a>Pré-requisitos para executar os cmdlets PowerShell do Stream Analytics

1.	Instalar e configurar o PowerShell do Azure

	Siga as instruções em [como instalar e configurar o PowerShell do Azure][powershell-install] para instalar o PowerShell do Azure.

2.	Configurar o modo do Azure

	Depois de instalar o PowerShell do Azure, execute o cmdlet [Switch-AzureMode][msdn-switch-azuremode] para definir o modo apropriado do Azure para acessar os cmdlets do Stream Analytics:

		Switch-AzureMode AzureResourceManager

>[WACOM.NOTE] Há uma limitação temporária onde os trabalhos do Stream Analytics criados por meio do PowerShell do Azure não têm monitoramento habilitado.  Para solucionar esse problema, navegue até a página Monitor do trabalho no Portal do Azure e clique no botão "Habilitar".    

##<a name="cmdlets"></a>Cmdlets do PowerShell do Stream Analytics
A tabela a seguir lista os cmdlets que você pode usar para monitorar e gerenciar os trabalhos do Stream Analytics do Azure usando o PowerShell do Azure.

###<a name="Get-AzureStreamAnalyticsJob"></a>Get-AzureStreamAnalyticsJob
Lista todas os trabalhos do Stream Analytics definidos na assinatura do Azure ou especificados no grupo de recursos ou obtém informações sobre um trabalho específico dentro de um grupo de recursos.

**Exemplo 1**

	Get-AzureStreamAnalyticsJob

Esse comando retorna informações sobre todos os trabalhos do Stream Analytics na assinatura do Azure.

**Exemplo 2**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US 
Esse comando retorna informações sobre todos os trabalhos do Stream Analytics no grupo de recursos StreamAnalytics-Default-West-US.

**Exemplo 3**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob
Esse comando retorna informações sobre o trabalho StreamingJob do Stream Analytics no grupo de recursos StreamAnalytics-Default-West-US.

###<a name="Get-AzureStreamAnalyticsInput"></a>Get-AzureStreamAnalyticsInput
Lista todas as entradas que são definidas em um trabalho específico do Stream Analytics ou obtém informações sobre uma entrada específica.

**Exemplo 1**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob

Esse comando retorna informações sobre todas as entradas definidas no trabalho StreamingJob.

**Exemplo 2**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name EntryStream
Esse comando retorna informações sobre a entrada denominada EntryStream definida no trabalho StreamingJob.

###<a name="Get-AzureStreamAnalyticsOutput"></a>Get-AzureStreamAnalyticsOutput
Lista todas as saídas que são definidas em um trabalho específico do Stream Analytics ou obtém informações sobre uma saída específica.

**Exemplo 1**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob
Esse comando retorna informações sobre as saídas definidas no trabalho StreamingJob.

**Exemplo 2**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name Output
Esse comando retorna informações sobre a saída denominada Output no trabalho StreamingJob.

###<a name="Get-AzureStreamAnalyticsQuota"></a>Get-AzureStreamAnalyticsQuota
Obtém informações sobre a cota de unidade de transmissão de uma região especificada.

**Exemplo 1**

	Get-AzureStreamAnalyticsQuota -Location "West US" 
Esse comando retorna informações sobre cotas de unidade de transmissão e uso na região Oeste dos EUA.

###<a name="Get-AzureStreamAnalyticsTransformation"></a>Get-AzureStreamAnalyticsTransformation
Obtém informações sobre uma transformação específica definida no trabalho do Stream Analytics.

**Exemplo 1**

	Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name StreamingJob
Esse comando retorna informações sobre a transformação chamada StreamingJob no trabalho StreamingJob.

###<a name="New-AzureStreamAnalyticsInput"></a>New-AzureStreamAnalyticsInput
Cria uma nova entrada dentro do trabalho do Stream Analytics ou atualiza uma entrada existente especificada.
  
O nome da entrada pode ser especificado no arquivo .JSON ou na linha de comando.  Se ambos forem especificados, o nome na linha de comando deve ser o mesmo que o do arquivo.

Se você especificar uma entrada que já existe e não especificar o parâmetro -Force, o cmdlet perguntará se deseja ou não substituir a entrada existente.

Se você especificar o parâmetro -Force e especificar um nome de entrada existente, a entrada será substituída sem confirmação.

**Exemplo 1**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -File "C:\Input.json" 
Este comando cria uma nova entrada do arquivo Input.json.  Se uma entrada existente com o nome especificado no arquivo de definição de entrada já estiver definida, o cmdlet perguntará se deseja ou não substituí-la.

**Exemplo 2**
	
	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
Este comando cria uma nova entrada no trabalho chamado EntryStream.  Se uma entrada existente com esse nome já estiver definida, o cmdlet perguntará se deseja ou não substituí-la.

**Exemplo 3**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
Este comando substitui a definição da fonte de entrada existente chamada EntryStream com a definição do arquivo.

###<a name="New-AzureStreamAnalyticsJob"></a>New-AzureStreamAnalyticsJob
Cria um novo trabalho do Stream Analytics no Microsoft Azure ou atualiza a definição de um trabalho existente especificado.

O nome do trabalho pode ser especificado no arquivo .JSON ou na linha de comando.  Se ambos forem especificados, o nome na linha de comando deve ser o mesmo que o do arquivo.

Se você especificar um nome de trabalho que já existe e não especificar o parâmetro -Force, o cmdlet perguntará se deseja ou não substituir o trabalho existente.

Se você especificar o parâmetro -Force e especificar um nome de trabalho existente, a definição do trabalho será substituída sem confirmação.

**Exemplo 1**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\JobDefinition.json" 
Este comando cria um novo trabalho a partir da definição em JobDefinition.json.  Se um trabalho existente com o nome especificado no arquivo de definição de trabalho já estiver definido, o cmdlet perguntará se deseja ou não substituí-lo.

**Exemplo 2**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
Este comando substitui a definição de trabalho para StreamingJob.

###<a name="New-AzureStreamAnalyticsOutput"></a>New-AzureStreamAnalyticsOutput
Cria uma nova saída dentro de um trabalho do Stream Analytics ou atualiza uma saída existente.    

O nome da saída pode ser especificado no arquivo .JSON ou na linha de comando.  Se ambos forem especificados, o nome na linha de comando deve ser o mesmo que o do arquivo.

Se você especificar uma saída que já existe e não especificar o parâmetro -Force, o cmdlet perguntará se deseja ou não substituir a saída existente.

Se você especificar o parâmetro -Force e especificar um nome de saída existente, a saída será substituída sem confirmação.

**Exemplo 1**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Output.json" -JobName StreamingJob -Name output
Este comando cria uma nova saída chamada "output" no trabalho StreamingJob.  Se uma saída existente com esse nome já estiver definida, o cmdlet perguntará se deseja ou não substituí-la.

**Exemplo 2**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
Este comando substitui a definição para "output" no trabalho StreamingJob.

###<a name="New-AzureStreamAnalyticsTransformation"></a>New-AzureStreamAnalyticsTransformation
Cria uma nova transformação dentro de um trabalho do Stream Analytics ou atualiza uma transformação existente.
  
O nome da transformação pode ser especificado no arquivo .JSON ou na linha de comando.  Se ambos forem especificados, o nome na linha de comando deve ser o mesmo que o do arquivo.

Se você especificar uma transformação que já existe e não especificar o parâmetro -Force, o cmdlet perguntará se deseja ou não substituir a transformação existente.

Se você especificar o parâmetro -Force e especificar um nome de transformação existente, a transformação será substituída sem confirmação.

**Exemplo 1**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
Este comando cria uma nova transformação chamada StreamingJobTransform no trabalho StreamingJob.  Se uma transformação existente com esse nome já estiver definida, o cmdlet perguntará se deseja ou não substituí-la.

**Exemplo 2**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
 Este comando substitui a definição de StreamingJobTransform no trabalho StreamingJob.

###<a name="Remove-AzureStreamAnalyticsInput"></a>Remove-AzureStreamAnalyticsInput
Exclui de maneira assíncrona uma entrada específica de um trabalho do Stream Analytics no Microsoft Azure.    
Se você especificar o parâmetro -Force, a entrada será excluída sem confirmação.

**Exemplo 1**
	
	Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name EventStream
Este comando remove a entrada EventStream do trabalho StreamingJob.    

###<a name="Remove-AzureStreamAnalyticsJob"></a>Remove-AzureStreamAnalyticsJob
Exclui de maneira assíncrona um trabalho específico do Stream Analytics no Microsoft Azure.    
Se você especificar o parâmetro -Force, o trabalho será excluído sem confirmação.

**Exemplo 1**

	Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob 
Esse comando remove o trabalho StreamingJob.    

###<a name="Remove-AzureStreamAnalyticsOutput"></a>Remove-AzureStreamAnalyticsOutput
Exclui de maneira assíncrona uma saída específica de um trabalho do Stream Analytics no Microsoft Azure.    
Se você especificar o parâmetro -Force, a saída será excluída sem confirmação.

**Exemplo 1**

	Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name Output
Este comando remove a saída Output do trabalho StreamingJob.    

###<a name="Start-AzureStreamAnalyticsJob"></a>Start-AzureStreamAnalyticsJob
Implanta de maneira assíncrona e inicia um trabalho do Stream Analytics no Microsoft Azure.

**Exemplo 1**

	Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob 
Esse comando inicia o trabalho StreamingJob.    

###<a name="Stop-AzureStreamAnalyticsJob"></a>Stop-AzureStreamAnalyticsJob
Interrompe de maneira assíncrona um trabalho do Stream Analytics para que não seja executado no Microsoft Azure e desaloca os recursos que estavam sendo usados. A definição de trabalho e os metadados permanecerão disponíveis dentro da sua assinatura por meio de APIs de gerenciamento e do Portal do Azure, de modo que o trabalho pode ser editado e reiniciado. Você não será cobrado por um trabalho no estado Interrompido.

**Exemplo 1**

	Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob 
Esse comando interrompe o trabalho StreamingJob.    

###<a name="Test-AzureStreamAnalyticsInput"></a>Test-AzureStreamAnalyticsInput
Testa a capacidade do Stream Analytics de se conectar a uma entrada especificada.

**Exemplo 1**

	Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name EntryStream
Testa o status de conexão da entrada EntryStream no StreamingJob.    

###<a name="Test-AzureStreamAnalyticsOutput"></a>Test-AzureStreamAnalyticsOutput
Testa a capacidade do Stream Analytics de se conectar a uma saída especificada.

**Exemplo 1**

	Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name Output
Testa o status de conexão da saída Output no StreamingJob.    


##<a name="seealso"></a>Consulte também

- [Introdução ao Stream Analytics do Azure][stream.analytics.introduction]
- [Introdução ao Stream Analytics][stream.analytics.get.started]
- [Limites no pré-lançamento do Stream Analytics][stream.analytics.limitations]
- [Guia do desenvolvedor do Stream Analytics][stream.analytics.developer.guide]
- [Referência de linguagem de consulta para o Stream Analytics][stream.analytics.query.language.reference]
- [Referência de API REST para o Stream Analytics][stream.analytics.rest.api.reference]
 



[msdn-switch-azuremode]: http://msdn.microsoft.com/pt-br/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/pt-br/documentation/articles/install-configure-powershell/


[stream-analytics-introduction]: ../
[stream-analytics-get-started]
[stream-analytics-limitations]
[stream-analytics-developer-guide]
[stream-analytics-query-language-reference]
[stream-analytics-rest-api-reference]
 


[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.scale.jobs]: ../stream-analytics-scale-jobs/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!--HONumber=35.2-->
