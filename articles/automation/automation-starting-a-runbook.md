---
title: "Como iniciar um runbook na Automação do Azure | Microsoft Docs"
description: "Resume os métodos diferentes que podem ser usados para iniciar um runbook na Automação do Azure e fornece detalhes sobre como usar o portal do Azure e o Windows PowerShell."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 6ee756b4-9200-4eb2-9bda-ec156853803b
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2017
ms.author: magoedte;bwren
ms.openlocfilehash: c6a18bedec6eca5ff25d205bccecc23ecd342744
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2018
---
# <a name="starting-a-runbook-in-azure-automation"></a>Como iniciar um Runbook na Automação do Azure
A tabela a seguir o ajuda a determinar o método para inicializar um runbook na Automação do Azure, que seja mais adequado ao seu cenário específico. Este artigo inclui detalhes sobre como iniciar um runbook com o portal do Azure e com o Windows PowerShell. Detalhes sobre outros métodos são fornecidos em outros documentos que você pode acessar através dos links abaixo.

| **MÉTODO** | **CARACTERÍSTICAS** |
| --- | --- |
| [portal do Azure](#starting-a-runbook-with-the-azure-portal) |<li>Método mais simples com interface do usuário interativa.<br> <li>Formulário para fornecer valores de parâmetro simples.<br> <li>Controle facilmente o estado do trabalho.<br> <li>Acesso autenticado com o logon do Azure. |
| [Windows PowerShell](https://msdn.microsoft.com/library/dn690259.aspx) |<li>Chame da linha de comando com os cmdlets do Windows PowerShell.<br> <li>Pode ser incluído em uma solução automatizada com várias etapas.<br> <li>A solicitação é autenticada com certificado ou entidade de usuário/entidade de serviço OAuth.<br> <li>Fornece valores de parâmetro simples e complexos.<br> <li>Acompanhe o estado do trabalho.<br> <li>É necessário um cliente para dar suporte a cmdlets do PowerShell. |
| [API de Automação do Azure](https://msdn.microsoft.com/library/azure/mt662285.aspx) |<li>Método mais flexível, mas também o mais complexo.<br> <li>Chame de qualquer código personalizado que possa fazer solicitações HTTP.<br> <li>A solicitação autenticada com certificado ou entidade de usuário/entidade de serviço OAuth.<br> <li>Fornece valores de parâmetro simples e complexos. *Se você estiver chamando um runbook do Python usando a API, o conteúdo JSON deverá ser serializado.*<br> <li>Acompanhe o estado do trabalho. |
| [Webhooks](automation-webhooks.md) |<li>Inicie o runbook de uma solicitação HTTP única.<br> <li>Autenticado com token de segurança na URL.<br> <li>O cliente não pode substituir valores de parâmetro especificados quando o webhook foi criado. O runbook pode definir um único parâmetro que é preenchido com os detalhes da solicitação HTTP.<br> <li>Sem capacidade de acompanhar o estado do trabalho por meio da URL do webhook. |
| [Responder a um Alerta do Azure](../log-analytics/log-analytics-alerts.md) |<li>Inicie um runbook em resposta a um alerta do Azure.<br> <li>Configure o webhook para o runbook e vincule ao alerta.<br> <li>Autenticado com token de segurança na URL. |
| [Agenda](automation-schedules.md) |<li>Inicie automaticamente o runbook em um cronograma horário, diário, semanal ou mensal.<br> <li>Manipule a agenda pelo portal do Azure, por cmdlets do PowerShell ou pela a API do Azure.<br> <li>Fornece os valores de parâmetro a serem usados com a agenda. |
| [De Outro Runbook](automation-child-runbooks.md) |<li>Use um runbook como uma atividade em outro runbook.<br> <li>É útil para funcionalidades usadas por vários runbooks.<br> <li>Forneça valores de parâmetro para o runbook filho e use a saída no runbook pai. |

A imagem a seguir ilustra o processo passo a passo detalhado no ciclo de vida de um runbook. Isso inclui as diferentes formas como um runbook é iniciado na Automação do Azure, os componentes necessários para que o Hybrid Runbook Worker execute runbooks da Automação do Azure e as interações entre diferentes componentes. Para saber mais sobre a execução de runbooks de Automação em seu datacenter, veja [hybrid runbook workers](automation-hybrid-runbook-worker.md)

![Arquitetura do runbook](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="starting-a-runbook-with-the-azure-portal"></a>Iniciando um runbook com o portal do Azure
1. No Portal do Azure, selecione **Automação** e, em seguida, clique no nome de uma conta de automação.
2. No menu Hub, selecione **Runbooks**.
3. Na página **Runbooks**, selecione um runbook e, em seguida, clique em **Iniciar**.
4. Se o runbook tiver parâmetros, você será solicitado a fornecer valores com uma caixa de texto para cada parâmetro. Confira [Parâmetros de runbook](#Runbook-parameters) abaixo para mais detalhes sobre os parâmetros.
5. Na página **Trabalho**, você exibe o status do trabalho do runbook.

## <a name="starting-a-runbook-with-windows-powershell"></a>Iniciando um runbook com o Windows PowerShell
Você pode usar [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) para iniciar um runbook com o Windows PowerShell. O código de exemplo a seguir inicia um runbook chamado Test-Runbook.

```
Start-AzureRmAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

Start-AzureRmAutomationRunbook retorna um objeto de trabalho que você pode usar para controlar seu status quando o runbook é iniciado. Você pode usar esse objeto de trabalho com [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx) para determinar o status do trabalho e [Get-AzureRmAutomationJobOutput](https://msdn.microsoft.com/library/mt603476.aspx) para obter sua saída. O código de exemplo a seguir inicia um runbook chamado Test-Runbook, aguarda até que ele seja concluído e exibe a sua saída.

```
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzureRmAutomationRunbook –AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzureRmAutomationJob –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzureRmAutomationJobOutput –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup –Stream Output
```

Se o runbook exigir parâmetros, você deve fornecê-los como uma [hashtable](http://technet.microsoft.com/library/hh847780.aspx) , em que a chave da hashtable corresponde ao nome do parâmetro e o valor é o valor do parâmetro. O exemplo a seguir mostra como iniciar um runbook com dois parâmetros de cadeia de caracteres chamados FirstName e LastName, um número inteiro denominado RepeatCount e um parâmetro booliano denominado Show. Para mais informações sobre parâmetros, confira [Parâmetros de runbook](#Runbook-parameters) abaixo.

```
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="runbook-parameters"></a>Parâmetros de runbook
Quando você inicia um runbook por meio do Portal do Azure ou do Windows PowerShell, a instrução é enviada pelo serviço Web da Automação do Azure. Esse serviço não dá suporte a parâmetros com tipos de dados complexos. Se você precisa fornecer um valor para um parâmetro complexo, você deve chamá-lo embutido de outro runbook, como descrito em [Runbooks filho na Automação do Azure](automation-child-runbooks.md).

O serviço Web da Automação do Azure fornece uma funcionalidade especial para os parâmetros usando certos tipos de dados, conforme descrito nas seções a seguir:

### <a name="named-values"></a>Valores nomeados
Se o parâmetro é do tipo de dados [object], você pode usar o seguinte formato JSON para enviar-lhe uma lista de valores nomeados: *{Name1:'Value1', Name2:'Value2', Name3:'Value3'}*. Esses valores devem ser tipos simples. O runbook recebe o parâmetro como um [PSCustomObject](https://msdn.microsoft.com/library/system.management.automation.pscustomobject%28v=vs.85%29.aspx) com propriedades que correspondem a cada valor nomeado.

Considere o runbook de teste a seguir que aceita um parâmetro chamado user.

```
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][object]$user
   )
    $userObject = $user | ConvertFrom-JSON
    if ($userObject.Show) {
        foreach ($i in 1..$userObject.RepeatCount) {
            $userObject.FirstName
            $userObject.LastName
        }
    }
}
```

O texto a seguir pode ser usado para o parâmetro user.

```
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

Isso resulta na seguinte saída:

```
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>Matrizes
Se o parâmetro é uma matriz, como [array] ou [string[]], você pode usar o seguinte formato JSON para enviar-lhe uma lista de valores: *[Value1, Value2, Value3]*. Esses valores devem ser tipos simples.

Considere o runbook de teste a seguir que aceita um parâmetro chamado *user*.

```
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][array]$user
   )
    if ($user[3]) {
        foreach ($i in 1..$user[2]) {
            $ user[0]
            $ user[1]
        }
    }
}
```

O texto a seguir pode ser usado para o parâmetro user.

```
["Joe","Smith",2,true]
```

Isso resulta na seguinte saída:

```
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>Credenciais
Se o parâmetro é do tipo de dados **PSCredential**, você pode fornecer o nome de um [ativo de credenciais](automation-credentials.md)da Automação do Azure . O runbook recupera as credenciais com o nome que você especifica.

Considere o runbook de teste a seguir que aceita um parâmetro chamado credential.

```
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

O texto a seguir pode ser usado para o parâmetro user supondo que havia um ativo de credenciais chamado *Minhas credenciais*.

```
My Credential
```

Supondo que o nome de usuário nas credenciais era *vmonte*, o resultado será o seguinte:

```
jsmith
```

## <a name="next-steps"></a>Próximas etapas
* A arquitetura de runbook no artigo atual oferece uma visão geral de alto nível do gerenciamento de recursos de runbooks no Azure e localmente com o Hybrid Runbook Worker. Para saber mais sobre a execução de runbooks de Automação em seu datacenter, consulte [Hybrid Runbook Workers](automation-hybrid-runbook-worker.md).
* Para saber mais sobre a criação de runbooks modulares a serem usados em outros runbooks para funções específicas ou comuns, consulte [Runbooks filho](automation-child-runbooks.md).

