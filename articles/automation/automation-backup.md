<properties 
   pageTitle="Fazendo backup da Automação do Azure"
   description="Descreve como fazer backup do conteúdo de uma conta de automação para que ele possa ser mantido depois que uma conta de automação é excluída."
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
   ms.date="04/13/2015"
   ms.author="bwren" />

# Fazendo backup da Automação do Azure

Quando você exclui uma conta de automação no Microsoft Azure, todos os objetos na conta são excluídos, incluindo runbooks, módulos, configurações, trabalhos e ativos. Os objetos não podem ser recuperados depois que a conta é excluída. Você pode usar as informações a seguir para fazer backup do conteúdo de sua conta de automação antes de excluí-la.

## Runbooks

Você pode exportar seus runbooks para arquivos de script usando o Portal de Gerenciamento do Azure ou o cmdlet [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx) no Windows PowerShell. Esses arquivos de script podem ser importados para outra conta de automação, conforme discutido em [Criando ou importando um runbook](https://msdn.microsoft.com/library/dn643637.aspx).


## Módulos de integração

Você não pode exportar módulos de integração da Automação do Azure. Você deve garantir que eles estejam disponíveis fora da conta de automação.

## Ativos

Não é possível exportar [ativos](https://msdn.microsoft.com/library/dn939988.aspx) da Automação do Azure. Usando o Portal de Gerenciamento do Azure, você deve observar os detalhes de variáveis, credenciais, certificados, conexões e agendas. Você deve criar manualmente qualquer ativo que seja usado por runbooks importados para outra automação.

Você pode usar [cmdlets do Azure](https://msdn.microsoft.com/library/dn690262.aspx) para recuperar os detalhes de ativos não criptografados e salvá-los para referência futura ou criar ativos equivalentes em outra conta de automação.

Não é possível recuperar o valor de variáveis criptografadas nem o campo de senha de credenciais usando cmdlets. Se não souber esses valores, você poderá recuperá-los de um runbook usando as atividades [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) e [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx).

Você não pode exportar certificados da Automação do Azure. Você deve garantir que todos os certificados estejam disponíveis fora do Azure.

## Artigos relacionados

- [Criando ou importando um Runbook](https://msdn.microsoft.com/library/dn643637.aspx)
- [Ativos de automação](https://msdn.microsoft.com/library/dn939988.aspx)
- [Cmdlets do Azure](https://msdn.microsoft.com/library/dn690262.aspx) 

<!---HONumber=July15_HO4-->