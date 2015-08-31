<properties 
   pageTitle="Runbooks Filhos na Automação do Azure | Microsoft Azure"
	description="Descreve os diferentes métodos para iniciar um runbook na Automação do Azure de outro runbook e compartilhar informações entre eles."
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

# Runbooks filhos na Automação do Azure


É uma prática recomendada na Automação do Azure escrever runbooks reutilizáveis e modulares com uma função distinta que pode ser usada por outros runbooks. Um runbook pai geralmente chamará um ou mais runbooks filhos para executar o recurso necessário. Há duas maneiras de chamar um runbook filho e cada uma tem diferenças marcantes que você deve compreender para que possa determinar qual será melhor para seus diferentes cenários.

##  Invocar um runbook filho usando a execução embutida

Para invocar um runbook embutido de outro runbook, use o nome do runbook e forneça valores para os parâmetros exatamente como você faria ao usar uma atividade ou um cmdlet. Todos os runbooks na mesma conta de automação estão disponíveis para todos os outros para serem usados dessa maneira. O runbook pai aguardará o runbook filho concluir antes de passar para a próxima linha e nenhuma saída é retornada diretamente para o pai.

Quando você chama um runbook embutido, ele é executado no mesmo trabalho que o runbook pai. Não haverá nenhuma indicação no histórico de trabalho do runbook filho de que ele foi executado. Todas as exceções e fluxos de saída do runbook filho serão associados ao pai. Isso resulta em menos trabalhos e torna mais fácil rastrear e solucionar problemas, já que as exceções geradas pelo runbook filho e por sua saída de fluxo são associadas ao trabalho pai.

Quando um runbook é publicado, todos os runbooks filhos que ele chamar já devem ter sido publicados. Isso ocorre porque a Automação do Azure cria uma associação com os runbooks filhos quando um runbook é compilado. Se não tiverem sido,o runbook pai aparecerá para publicação corretamente, mas poderá gerar uma exceção quando for iniciado. Se isso acontecer, você poderá republicar o runbook pai para referenciar corretamente os runbooks filhos. Você não precisa republicar o runbook pai se algum dos runbooks filhos forem alterados porque a associação terá sido criada.

Os parâmetros de um runbook filho com chamada embutida podem ser de qualquer tipo de dados, incluindo objetos complexos, e não há nenhuma [serialização JSON](automation-starting-a-runbook.md#runbook-parameters) como quando você inicia o runbook usando o Portal de Gerenciamento do Azure ou o cmdlet Start-AzureAutomationRunbook.

O exemplo a seguir invoca um runbook filho de teste que aceita três parâmetros, um objeto complexo, um número inteiro e um valor booleano. A saída do runbook filho é atribuída a uma variável.

	$vm = Get-AzureVM –ServiceName "MyVM" –Name "MyVM"
	$output = Test-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true

##  Iniciar um runbook filho usando cmdlet

Você pode usar o cmdlet [Start-AzureAutomationRunbook](http://msdn.microsoft.com/library/dn690259.aspx) para iniciar um runbook, conforme descrito em [Para iniciar um runbook com o Windows PowerShell](../automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell). Quando você inicia um runbook filho de um cmdlet, o runbook pai será movido para a próxima linha assim que o trabalho for criado para o runbook filho. Se você precisar recuperar todas as saídas do runbook, deverá acessar o trabalho usando [Get-AzureAutomationJobOutput](http://msdn.microsoft.com/library/dn690268.aspx).

O trabalho de um runbook filho iniciado com um cmdlet executará um trabalho separado do runbook pai. Isso resulta em mais trabalhos do que invocar o script embutido e torna o acompanhamento mais difícil de rastrear. O pai pode iniciar vários runbooks filhos sem ter que esperar a conclusão de cada um. Para a mesma variante de execução paralela que chama runbooks filhos embutidos, o runbook pai precisaria usar a [palavra-chave paralela](automation-powershell-workflow.md#parallel-processing).

Parâmetros de um runbook filho iniciados com um cmdlet são fornecidos como uma tabela de hash, conforme descrito em [Parâmetros de Runbook](automation-starting-a-runbook.md#runbook-parameters). Somente tipos de dados simples podem ser usados. Se o runbook tiver um parâmetro com um tipo de dados complexos, ele deve ser chamado embutido.

O exemplo a seguir inicia um runbook filho com parâmetros e aguarda a sua conclusão. Depois de concluído, a saída é coletada do trabalho pelo runbook pai.

	$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true} 
	$job = Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test- ChildRunbook" –Parameters $params
	
	$doLoop = $true
	While ($doLoop) {
	   $job = Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" -Id $job.Id
	   $status = $job.Status
	   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped") 
	}
	
	Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

[Start-ChildRunbook](http://gallery.technet.microsoft.com/scriptcenter/Start-Azure-Automation-1ac858a9) é um runbook auxiliar disponível na Galeria do TechNet para iniciar um runbook de um cmdlet. Isso fornece a opção de esperar até que o runbook filho seja concluído e recuperar sua saída. Além de usar esse runbook em seu próprio ambiente de Automação do Azure, esse runbook pode ser usado como uma referência para trabalhar com runbooks e trabalhos usando cmdlets. O runbook auxiliar em si deve ser chamado embutido porque ele requer um parâmetro de tabela de hash para aceitar os valores de parâmetro para o runbook filho.


## Comparação de métodos para chamar um runbook filho

A tabela a seguir resume as diferenças entre os dois métodos para chamar um runbook de outro runbook.

| | Embutido| Cmdlet|
|:---|:---|:---|
|Trabalho|Os runbooks filhos são executados no mesmo trabalho que o pai.|Um trabalho separado é criado para o runbook filho.|
|Execução|O runbook pai aguarda a conclusão do runbook filho antes de continuar.|O runbook pai continua imediatamente após o runbook filho ser iniciado.|
|Saída|O runbook pai pode obter saída diretamente do runbook filho.|O runbook pai deve recuperar a saída do trabalho do runbook filho.|
|Parâmetros|Os valores para os parâmetros de runbook filho são especificados separadamente e podem usar qualquer tipo de dados.|Os valores para os parâmetros de runbook filho devem ser combinados em uma única tabela de hash e somente podem tipos de dados simples, de matriz e de objeto que aproveitem a serialização JSON.|
|Conta de Automação|O runbook pai somente pode usar o runbook filho na mesma conta de automação.|O runbook pai pode usar runbook filho de qualquer conta de automação da mesma assinatura do Azure e até mesmo de uma assinatura diferente se você tiver uma conexão com ele.|
|Publicação|O runbook filho deve ser publicado antes da publicação do runbook pai.|O runbook filho deve ser publicado antes da inicialização do runbook pai.|

## Artigos relacionados

- [Como iniciar um Runbook na Automação do Azure](automation-starting-a-runbook.md)
- [Saída de runbook e mensagens na Automação do Azure](automation-runbook-output-and-messages.md)

<!---HONumber=August15_HO8-->