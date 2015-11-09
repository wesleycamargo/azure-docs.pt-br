<properties 
   pageTitle="Configurações de runbook"
   description="Descreve as definições de configuração de um runbook na Automação do Azure e como alterá-las usando o Portal de Gerenciamento do Azure e o Windows PowerShell."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/22/2015"
   ms.author="bwren" />

# Configurações de runbook

Cada runbook na Automação do Azure tem várias configurações que ajudam a identificá-lo e a alterar seu comportamento de log. Cada uma dessas configurações é descrita abaixo seguida de procedimentos sobre como modificá-las.

## Configurações

### Nome e descrição

Você não pode alterar o nome de um runbook após ele ter sido criado. A descrição é opcional e pode ter até 512 caracteres.

### Marcas

Os rótulos permitem que você atribua diferentes palavras e frases para ajudar a identificar um runbook. Por exemplo, quando você envia um runbook para a [Galeria de Runbook](https://msdn.microsoft.com/library/dn781422.aspx), também define rótulos específicos para identificar em quais categorias o runbook deve estar listado. Você pode especificar vários rótulos para um runbook separando-os com vírgulas.

### Registro em log

Por padrão, os registros Detalhado e Progresso não são gravados no histórico do trabalho. Você pode alterar as configurações de um runbook específico para registrar logs. Para saber mais sobre esses registros, confira [Saída de Runbook e Mensagens](https://msdn.microsoft.com/library/dn879148.aspx).

## Alterando as configurações de runbook

### Alterando as configurações de runbook com o Portal de Gerenciamento do Azure

Você pode alterar as configurações de um runbook no Portal de Gerenciamento do Azure na página **Configurar** para o runbook.

1. No Portal de Gerenciamento do Azure, selecione **Automação** e clique no nome de uma conta de automação.
1. Selecione a guia **Runbooks**.
1. Clique no nome de um runbook.
1. Selecione a guia **Configurar**.

### Alterando as configurações de runbook com o Windows PowerShell

Você pode usar o cmdlet [Set-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690275.aspx) para alterar as configurações de um runbook. Se você quiser especificar várias marcas, pode fornecer uma matriz ou uma única cadeia de caracteres com valores delimitados por vírgula para o parâmetro dos Rótulos. Você pode obter as marcas atuais com o [Get-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690278.aspx).

Os comandos de exemplo a seguir mostram como definir as propriedades de um runbook. Este exemplo adiciona três rótulos aos rótulos existentes e especifica que registros detalhados devem ser registrados em log.

	$automationAccountName = "MyAutomationAccount"
	$runbookName = "Sample-TestRunbook"
	$tags = (Get-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName).Tags
	$tags += "Tag1,Tag2,Tag3"
	Set-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## Artigos relacionados
- [Saída de Runbook e Mensagens](../automation-runbook-output-and-messages) 
- [Criando ou Importando um Runbook](https://msdn.microsoft.com/library/dn643637.aspx) 

<!---HONumber=Nov15_HO1-->