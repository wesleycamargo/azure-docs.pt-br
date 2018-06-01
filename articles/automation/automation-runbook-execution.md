---
title: Execução de runbook na Automação do Azure
description: Descreve os detalhes de como um runbook na Automação do Azure é processado.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ff58e22f8b9b837ec272cd2cd6193da80a7b718e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195413"
---
# <a name="runbook-execution-in-azure-automation"></a>Execução de runbook na Automação do Azure

Quando você inicia um runbook na Automação do Azure, um trabalho é criado. Um trabalho é uma instância única de execução de um runbook. Um trabalhador da Automação do Azure é atribuído para executar cada tarefa. Enquanto os trabalhadores são compartilhados por várias contas do Azure, os trabalhos de diferentes contas de automação ficam isolados uns dos outros. Você não tem controle sobre qual trabalhador atende a solicitação do seu trabalho. Um único runbook pode ter vários trabalhos em execução ao mesmo tempo. O ambiente de execução para trabalhos da mesma conta de Automação do Azure pode ser reutilizado. Quando você exibe a lista de runbooks no Portal do Azure, ela lista o status de todos os trabalhos iniciados para cada runbook. Você pode exibir a lista de trabalhos para cada runbook a fim de acompanhar o status de cada um. Para obter uma descrição das diferentes opções de status de trabalho, confira [Status de trabalho](#job-statuses).

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../includes/gdpr-dsr-and-stp-note.md)]

O diagrama a seguir mostra o ciclo de vida de um trabalho de runbook para [runbooks gráficos](automation-runbook-types.md#graphical-runbooks) e [runbooks de fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).

![Status de trabalho - Fluxo de trabalho do PowerShell](./media/automation-runbook-execution/job-statuses.png)

O diagrama a seguir mostra o ciclo de vida de um trabalho de runbook para [runbooks do PowerShell](automation-runbook-types.md#powershell-runbooks).

![Status de trabalho - Script do PowerShell](./media/automation-runbook-execution/job-statuses-script.png)

Os trabalhos têm acesso aos recursos do Azure fazendo uma conexão à sua assinatura do Azure. Eles só têm acesso aos recursos no seu data center se estes puderem ser acessados da nuvem pública.

## <a name="job-statuses"></a>Status de trabalho

A tabela a seguir descreve os diferentes status possíveis para um trabalho.

| Status | DESCRIÇÃO |
|:--- |:--- |
| Concluído |Operação concluída com sucesso. |
| Com falha |Para [runbooks gráficos e de fluxo de trabalho do PowerShell](automation-runbook-types.md), o runbook falhou ao compilar. Para [runbooks de Script do PowerShell](automation-runbook-types.md), o runbook falhou ao iniciar ou o trabalho encontrou uma exceção. |
| Erro, aguardando recursos |O trabalho falhou porque atingiu o limite de [fração justa](#fair-share) três vezes e iniciou do mesmo ponto de verificação ou desde o início do runbook em cada uma das vezes. |
| Em fila |O trabalho está aguardando recursos de um trabalho do Automation ficar disponível para que ele possa ser iniciado. |
| Iniciando |O trabalho foi atribuído a um trabalho, e o sistema está iniciando-o. |
| Continuando |O sistema está reiniciando o trabalho depois que ele foi suspenso. |
| Executando |O trabalho está em execução. |
| Executando, aguardando recursos |O trabalho foi descarregado, pois atingiu o limite de [fração justa](#fair-share) . Ele será retomado em breve do seu último ponto de verificação. |
| Parado |O trabalho foi interrompido pelo usuário antes de ser concluído. |
| Parando |O sistema está no processo de interromper o trabalho. |
| Suspenso |O trabalho foi suspenso pelo usuário, pelo sistema ou por um comando no runbook. Um trabalho suspenso pode ser iniciado novamente e retomará do seu último ponto de verificação ou desde o início do runbook, se ele não tiver pontos de verificação. O runbook só é suspenso pelo sistema quando ocorre uma exceção. Por padrão, ErrorActionPreference é definido como **Continuar** o que significa que o trabalho continua a ser executado no caso de um erro. Se essa variável de preferência for definida como **Parar**, o trabalho será suspenso no caso de um erro. Aplica-se a somente [runbooks gráficos e de fluxo de trabalho do PowerShell](automation-runbook-types.md). |
| Suspensão |O sistema está tentando suspender o trabalho por solicitação do usuário. O runbook precisa atingir seu próximo ponto de verificação antes de poder ser suspenso. Se já passou de seu último ponto de verificação, ele será concluído antes de ser suspenso. Aplica-se a somente [runbooks gráficos e de fluxo de trabalho do PowerShell](automation-runbook-types.md). |

## <a name="viewing-job-status-from-the-azure-portal"></a>Exibindo o status do trabalho no portal do Azure

É possível exibir um status resumido de todos os trabalhos do runbook, analisar os detalhes de um trabalho do runbook específico no Portal do Azure ou configurar a integração com o espaço de trabalho do Log Analytics para encaminhar fluxos de trabalho e status do trabalho do runbook. Para saber mais sobre a integração com o Log Analytics, consulte [Encaminhar status do trabalho e fluxos de trabalho de Automação para Log Analytics](automation-manage-send-joblogs-log-analytics.md).

### <a name="automation-runbook-jobs-summary"></a>Resumo dos trabalhos de runbook da Automação

À direita da Conta de automação selecionada, você pode ver um resumo de todos os trabalhos de runbook para uma Conta de automação selecionada no bloco **Estatísticas de Trabalho**.

![Bloco Estatísticas de Trabalho](./media/automation-runbook-execution/automation-account-job-status-summary.png).

Esse bloco exibe a contagem e a representação gráfica do status do trabalho para todos os trabalhos executados.

Um clique no bloco exibe a folha **Trabalhos**, que inclui uma lista resumida de todos os trabalhos executados, com status, execução do trabalho e as horas de início e de conclusão.

![Folha Trabalhos da conta de Automação](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Você pode filtrar a lista de trabalhos selecionando **Filtrar trabalhos** e filtrar por um runbook ou status do trabalho específico ou, na lista suspensa, pelo intervalo de data/hora de pesquisa.

![Filtrar por status do Trabalho](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Como alternativa, você pode exibir detalhes de resumo do trabalho para um runbook específico selecionando esse runbook na folha **Runbooks** folha da sua Conta de automação e selecionando o bloco **Trabalhos**. Isso exibe a folha **Trabalhos** e, nela você pode clicar no registro do trabalho para exibir seus detalhes e sua saída.

![Folha Trabalhos da conta de Automação](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>Resumo do trabalho

Você pode exibir uma lista de todos os trabalhos que foram criados para um determinado runbook e o status mais recente. Você pode filtrar essa lista pelo status do trabalho e o intervalo de datas para a última alteração no trabalho. Para exibir as informações detalhadas e sua saída, clique no nome de um trabalho. A exibição detalhada do trabalho inclui os valores para os parâmetros de runbook que foram fornecidos para esse trabalho.

Você pode usar as etapas a seguir para exibir os trabalhos de um runbook.

1. No Portal do Azure, selecione **Automação** e, em seguida, selecione no nome de uma Conta de automação.
2. No hub, selecione **Runbooks** e, em seguida, na folha **Runbooks**, selecione um runbook da lista.
3. Na folha do runbook selecionado, clique no bloco **Trabalhos**.
4. Clique em um dos trabalhos na lista e, na folha de detalhes do trabalho do runbook, você pode exibir seus detalhes e sua saída.

## <a name="retrieving-job-status-using-windows-powershell"></a>Recuperando o status do trabalho usando o Windows PowerShell

Você pode usar o [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx) para recuperar os trabalhos criados para um runbook e os detalhes de um trabalho específico. Se você inicia um runbook com o Windows PowerShell usando [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx), ele retorna o trabalho resultante. Use [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx)Output para obter a saída de um trabalho.

Os comandos de exemplo a seguir recuperam o último trabalho para um exemplo de runbook e exibe seu status, os valores fornecidos para os parâmetros de runbook e a saída do trabalho.

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

O exemplo a seguir recupera a saída de um trabalho específico e retorna cada registro. No caso de que houve uma exceção para um dos registros, a exceção é gravada em vez do valor. Isso é útil como exceções podem fornecer informações adicionais que não podem ser registradas normalmente durante a saída.

```azurepowershell-interactive
$output = Get-AzureRmAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzureRmAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="get-details-from-activity-log"></a>Obter detalhes do log de atividades

Outros detalhes, como o usuário ou conta que iniciou o runbook, podem ser recuperados do log de atividades da conta de automação. O exemplo do PowerShell a seguir fornece o último usuário a executar o runbook em questão:

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$rg = "ResourceGroup01"
$AutomationAccount = "MyAutomationAccount"
$RunbookName = "Test-Runbook"
$JobResourceID = "/subscriptions/$subid/resourcegroups/$rg/providers/Microsoft.Automation/automationAccounts/$AutomationAccount/jobs"

Get-AzureRmLog -ResourceId $JobResourceID -MaxRecord 1 | Select Caller
```

## <a name="fair-share"></a>fração justa

Para compartilhar recursos entre todos os runbooks na nuvem, a Automação do Azure descarregará temporariamente qualquer trabalho depois que ele for executado por três horas. Durante esse tempo, os trabalhos para [runbooks com base no PowerShell](automation-runbook-types.md#powershell-runbooks) são interrompidos e não são reiniciados. O status do trabalho mostrará **Parado**. Esse tipo de runbook é sempre retomado desde o início, já que não dá suporte a pontos de verificação.

[Os runbooks baseados em Fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) são retomados do seu último [ponto de verificação](https://docs.microsoft.com/system-center/sma/overview-powershell-workflows#bk_Checkpoints). Depois de ser executado por três horas, o trabalho do runbook será suspenso pelo serviço e seu status mostrará **Em execução, aguardando recursos**. Quando uma área restrita fica disponível, o runbook é reiniciado automaticamente pelo serviço de Automação e é retomado do último ponto de verificação. Este é o comportamento normal do fluxo de trabalho do PowerShell para suspender/reiniciar. Se o runbook exceder novamente as três horas de tempo de execução, o processo se repetirá mais três vezes. Após a terceira reinicialização, se o runbook ainda não tiver sido concluído nas três horas, o trabalho do runbook será considerado como uma falha e o status do trabalho será exibido como **Falhou, aguardando recursos**. Nesse caso, você receberá a seguinte exceção com a falha.

*O trabalho não pode continuar a execução porque foi removido repetidamente do mesmo ponto de verificação. Verifique se o Runbook não executa operações demoradas sem persistir o estado.*

Isso serve para proteger o serviço contra runbooks em execução indefinidamente sem conclusão, já que não são capazes alcançar o próximo ponto de verificação sem serem descarregados novamente.

Se o runbook não tiver nenhum ponto de verificação ou o trabalho não atingir o primeiro ponto de verificação antes de ser descarregado, ele será reiniciado desde o início.

Quando você cria um runbook, verifique se o tempo para executar atividades entre dois pontos de verificação não ultrapassa as três horas. Talvez seja necessário adicionar pontos de verificação ao seu runbook para que não alcance esse limite de três horas ou ultrapasse as operações de execução. Por exemplo, o runbook pode executar uma reindexação em um grande banco de dados SQL. Se essa operação única não for concluída dentro do limite adequado, o trabalho é descarregado e reiniciado desde o início. Nesse caso, você deve dividir a operação de reindexação em várias etapas, como a reindexação de uma tabela de cada vez, e inserir um ponto de verificação após cada operação para que o trabalho possa continuar após a conclusão da última operação.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os diferentes métodos que podem ser usados para iniciar um runbook na Automação do Azure, confira [Iniciar um Runbook na Automação do Azure](automation-starting-a-runbook.md)
