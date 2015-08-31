<properties 
   pageTitle="Saída e mensagens do runbook na Automação do Azure | Microsoft Azure"
	description="Descreve como criar e recuperar a saída e mensagens de erro de runbooks na Automação do Azure."
	services="automation"
	documentationCenter=""
	authors="bwren"
	manager="stevenka"
	editor="tysonn"/>
<tags 
   ms.service="automation"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/17/2015"
	ms.author="bwren"/>

# Saída e mensagens do runbook na Automação do Azure

A maioria dos runbooks da Automação do Azure terá alguma forma de saída, como uma mensagem de erro para o usuário ou um objeto complexo destinado a ser consumido por outro fluxo de trabalho. O Windows PowerShell oferece [vários fluxos](http://blogs.technet.com/heyscriptingguy/archive/2014/03/30/understanding-streams-redirection-and-write-host-in-powershell.aspx) para o envio da saída de um script ou de um de fluxo de trabalho. A Automação do Azure funciona com cada um desses fluxos de forma diferente e você deve seguir as práticas recomendadas de como usar cada um quando estiver criando um runbook.

A tabela a seguir oferece uma breve descrição de cada um dos fluxos e de seu comportamento no Portal de Gerenciamento do Azure ao executar um runbook publicado e ao [testar um runbook](http://msdn.microsoft.com/library/azure/dn879147.aspx). Serão fornecidos mais detalhes sobre cada fluxo nas seções subsequentes.

| Fluxo | Descrição | Publicado | Teste|
|:---|:---|:---|:---|
|Saída|Objetos que se destinam a consumo de outros runbooks.|Gravado no histórico do trabalho.|Exibido no Painel de Saída do Teste.|
|Aviso|Mensagem de aviso para o usuário.|Gravado no histórico do trabalho.|Exibido no Painel de Saída do Teste.|
|Erro|Mensagem de erro para o usuário. Ao contrário de uma exceção, o runbook continua após uma mensagem de erro por padrão.|Gravado no histórico do trabalho.|Exibido no Painel de Saída do Teste.|
|Detalhado|Mensagens que fornecem informações gerais ou de depuração.|Gravado no histórico do trabalho somente se o log detalhado estiver ativado para o runbook.|Exibido no painel Saída de Teste somente se $VerbosePreference for definido como Continuar no runbook.|
|Progresso|Os registros são automaticamente gerados antes e depois de cada atividade no runbook. O runbook não deve tentar criar seus próprios registros de andamento porque são destinados a um usuário interativo.|Gravado no histórico do trabalho somente se o log de andamento estiver ativado para o runbook.|Não exibido no Painel Saída de Teste.|
|Depurar|As mensagens destinadas a um usuário interativo. Não deve ser usado em runbooks.|Não gravado no histórico do trabalho.|Não gravado no Painel Saída de Teste.|

## Fluxo de saída

O fluxo de saída destina-se à saída de objetos criados por um script ou um fluxo de trabalho quando executado corretamente. Na Automação do Azure, esse fluxo é usado principalmente para objetos que se destinam a ser consumidos por [ runbooks pais que chamam o runbook atual](automation-child-runbooks.md). Quando você [chamar um runbook embutido](automation-child-runbooks.md/#InlineExecution) de um runbook pai, ele retornará dados do fluxo de saída para o pai. Você só deverá usar o fluxo de saída para comunicar informações gerais para o usuário se souber que o runbook nunca será chamado por outro runbook. Como prática recomendada, no entanto, você normalmente deverá usar o [Fluxo Detalhado](#Verbose) para comunicar informações gerais para o usuário.

Você pode gravar dados no fluxo de saída usando [Write-Output](http://technet.microsoft.com/library/hh849921.aspx) ou colocando o objeto em sua própria linha no runbook.

	#The following lines both write an object to the output stream.
	Write-Output –InputObject $object
	$object

### Saída de uma função

Quando você grava o fluxo de saída em uma função incluída no seu runbook, a saída é passada de volta ao runbook. Se o runbook atribuir essa saída a uma variável, então ela não será gravada no fluxo de saída. A gravação de qualquer outro fluxo de dentro de uma função gravará o fluxo correspondente no runbook.

Considere os cenários de exemplo a seguir.

	Workflow Test-Runbook
	{
	   Write-Verbose "Verbose outside of function"
	   Write-Output "Output outside of function"
	   $functionOutput = Test-Function
	
	   Function Test-Function
	   {
	      Write-Verbose "Verbose inside of function"
	      Write-Output "Output inside of function"
	   }
	}

O fluxo de saída para o trabalho de runbook deveria ser:

	Output outside of function

O fluxo detalhado para o trabalho de runbook deveria ser:

	Verbose outside of function
	Verbose inside of function

### Declarando o tipo de dados de saída

Um fluxo de trabalho pode especificar o tipo de dados de sua saída usando o [atributo OutputType](http://technet.microsoft.com/library/hh847785.aspx). Esse atributo não tem nenhum efeito em tempo de execução, mas oferece uma indicação para o autor do runbook no tempo de design da saída esperada do runbook. Como o conjunto de ferramentas para runbooks continua a evoluir, a importância de declarar tipos de dados de saída em tempo de design aumentará. Como resultado, é uma prática recomendada incluir essa declaração em todos os runbooks criados.

O exemplo de runbook a seguir gera um objeto de cadeia de caracteres e inclui uma declaração de seu tipo de saída. Se seu runbook gerar uma matriz de um determinado tipo, você ainda deverá especificar o tipo como oposto a uma matriz do tipo.

	Workflow Test-Runbook
	{
	   [OutputType([string])]
	
	   $output = "This is some string output."
	   Write-Output $output
	}

## Fluxos de mensagens

Ao contrário do fluxo de saída, os fluxos de mensagens destinam-se a comunicar informações ao usuário. Existem vários fluxos de mensagens para diferentes tipos de informação, e cada um deles é manipulado de forma diferente pela Automação do Azure.

### Fluxos de aviso e de erro

Os fluxos de Aviso e de Erro pretendem registrar problemas que ocorrem em um runbook. Eles são gravadas no histórico do trabalho quando um runbook é executado e são incluídos no Painel Saída de Teste no Portal de Gerenciamento do Azure quando um runbook é testado. Por padrão, o runbook continuará a ser executado após um aviso ou um erro. Você pode especificar que o runbook seja suspenso em um aviso ou um erro ao definir uma [variável de preferência](#PreferenceVariables) no runbook antes de criar a mensagem. Por exemplo, para fazer com que um runbook seja suspenso em um erro como o faria em uma exceção, defina **$ErrorActionPreference** como Stop.

Crie uma mensagem de aviso ou de erro usando o cmdlet [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) ou [Write-Error](http://technet.microsoft.com/library/hh849962.aspx). As atividades também podem ser gravadas nesses fluxos.

	#The following lines create a warning message and then an error message that will suspend the runbook.
	
	$ErrorActionPreference = "Stop"
	Write-Warning –Message "This is a warning message."
	Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."

### Fluxo detalhado

O fluxo de mensagens Detalhado destina-se a informações gerais sobre a operação do runbook. Como o [Fluxo de Depuração](#Debug) não está disponível em um runbook, as mensagens detalhadas deverão ser usadas para informações de depuração. Por padrão, as mensagens detalhadas de runbooks publicados não serão armazenadas no histórico do trabalho. Para armazenar as mensagens detalhadas, configure runbooks publicados como Registros Detalhados de Log na guia Configurar do runbook no Portal de Gerenciamento do Azure. Na maioria dos casos, você deve manter a configuração padrão de não obter o log de registros detalhados para um runbook por motivos de desempenho. Só ative essa opção para solucionar problemas ou depurar um runbook.

Quando você estiver [testando um runbook](http://msdn.microsoft.com/library/azure/dn879147.aspx), as mensagens detalhadas não serão exibidas, mesmo se o runbook estiver configurado para log de registros detalhados. Para exibir as mensagens detalhadas enquanto [testa um runbook](http://msdn.microsoft.com/library/azure/dn879147.aspx), você deverá definir a variável $VerbosePreference como Continue. Com esse conjunto de variáveis, as mensagens detalhadas serão exibidas no Painel de Saída de Teste do Portal de Gerenciamento do Azure.

Criar uma mensagem detalhada usando o cmdlet [Write-Verbose](http://technet.microsoft.com/library/hh849951.aspx).

	#The following line creates a verbose message.
	
	Write-Verbose –Message "This is a verbose message."

### Fluxo de depuração

O fluxo de Depuração deve ser usado por um usuário interativo e não deve ser usado em runbooks.

## Registros de andamento

Se você configurar um runbook para o log de registros de andamento (na guia Configurar do runbook no Portal de Gerenciamento do Azure), um registro será gravado no histórico do trabalho antes e após a execução de cada atividade. Na maioria dos casos, você deverá manter a configuração padrão de não obter o log de registros detalhados para um runbook para maximizar o desempenho. Só ative essa opção para solucionar problemas ou depurar um runbook. Quando você estiver testando um runbook, as mensagens de andamento não serão exibidas, mesmo se o runbook estiver configurado para log de registros de andamento.

O cmdlet [Write-Progress](http://technet.microsoft.com/library/hh849902.aspx) não é válido em um runbook, já que ele deve ser usado com um usuário interativo.

## Variáveis de preferência

O Windows PowerShell usa [variáveis de preferência](http://technet.microsoft.com/library/hh847796.aspx) para determinar como responder aos dados enviados para fluxos de saída diferentes. Você pode definir essas variáveis em um runbook para controlar como ele responde aos dados enviados em diferentes fluxos.

A tabela a seguir lista as variáveis de preferência que podem ser usadas em runbooks com seus valores válidos e padrão. Observe que essa tabela inclui somente os valores que são válidos em um runbook. Os valores adicionais serão válidos para as variáveis de preferência quando usados no Windows PowerShell fora da Automação do Azure.

| Variável| Valor Padrão| Valores Válidos|
|:---|:---|:---|
|WarningPreference|Continue|Stop<br>Continue<br>SilentlyContinue|
|ErrorActionPreference|Continue|Stop<br>Continue<br>SilentlyContinue|
|VerbosePreference|SilentlyContinue|Stop<br>Continue<br>SilentlyContinue|

A tabela a seguir lista o comportamento para os valores de variáveis de preferência válidos em runbooks.

| Valor| Comportamento|
|:---|:---|
|Continue|Registra em log a mensagem e continua executando o runbook.|
|SilentlyContinue|Continua executando o runbook sem registrar em log a mensagem. Isso tem o efeito de ignorar a mensagem.|
|Parar|Registra em log a mensagem e suspende o runbook.|

## Recuperando saída e mensagens do runbook

### Portal de gerenciamento do Azure

Você pode exibir os detalhes de um trabalho de runbook no Portal de Gerenciamento do Azure na guia Trabalhos de um runbook. O Resumo do trabalho exibirá os parâmetros de entrada e o [Fluxo de Saída](#Output), além de informações gerais sobre o trabalho e todas as exceções, caso tenham ocorrido. O Histórico incluirá mensagens do [Fluxo de Saída](#Output) e dos [Fluxos de Erro e de Aviso](#WarningError), além do [Fluxo Detalhado](#Verbose) e dos [Registros de Andamento](#Progress), caso o runbook esteja configurado para o log de registros detalhados e de andamento.

### Windows PowerShell

No Windows PowerShell, você pode recuperar saída e mensagens de um runbook usando o cmdlet [Get-AzureAutomationJobOutput](http://msdn.microsoft.com/library/dn690268.aspx). Esse cmdlet exige a ID do trabalho e tem um parâmetro chamado Stream, onde você pode especificar qual fluxo será retornado. Você pode especificar Any para retornar todos os fluxos do trabalho.

O exemplo a seguir inicia um runbook de exemplo e aguarda a sua conclusão. Depois de concluído, seu fluxo de saída será coletado do trabalho.

	$job = Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" 
	
	$doLoop = $true
	While ($doLoop) {
	   $job = Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" -Id $job.Id
	   $status = $job.Status
	   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped") 
	}
	
	Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

## Artigos relacionados

- [Controlar um trabalho de runbook](automation-runbook-execution.md)
- [Runbooks filhos](http://msdn.microsoft.com/library/azure/dn857355.aspx)

<!---HONumber=August15_HO8-->