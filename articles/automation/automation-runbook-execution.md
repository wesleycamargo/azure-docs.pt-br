---
title: "Execução de runbook na Automação do Azure | Microsoft Docs"
description: "Descreve os detalhes de como um runbook na Automação do Azure é processado."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: d10c8ce2-2c0b-4ea7-ba3c-d20e09b2c9ca
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/02/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: dd6b2fa2a1ca003578eaee82ef42741b9b1bf2a4
ms.openlocfilehash: e8b2bf61313ef88c685e30524bb39da6355ef3ee


---
# <a name="runbook-execution-in-azure-automation"></a>Execução de runbook na Automação do Azure
Quando você inicia um runbook na Automação do Azure, um trabalho é criado. Um trabalho é uma instância única de execução de um runbook. Um trabalhador da Automação do Azure é atribuído para executar cada tarefa. Enquanto os trabalhadores são compartilhados por várias contas do Azure, os trabalhos de diferentes contas de automação ficam isolados uns dos outros. Você não tem controle sobre qual trabalhador atenderá a solicitação do seu trabalho.  Um único runbook pode ter vários trabalhos em execução ao mesmo tempo. Quando você exibir a lista de runbooks no portal do Azure, ela listará o status do último trabalho iniciado para cada runbook. Você pode exibir a lista de trabalhos para cada runbook a fim de acompanhar o status de cada um. Para obter uma descrição das opções de status de trabalho diferentes, confira [Status de trabalho](#job-statuses).

O diagrama a seguir mostra o ciclo de vida de um trabalho de runbook para [runbooks gráficos](automation-runbook-types.md#graphical-runbooks) e [runbooks de fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).

![Status de trabalho - Fluxo de trabalho do PowerShell](./media/automation-runbook-execution/job-statuses.png)

O diagrama a seguir mostra o ciclo de vida de um trabalho de runbook para [runbooks do PowerShell](automation-runbook-types.md#powershell-runbooks).

![Status de trabalho - Script do PowerShell](./media/automation-runbook-execution/job-statuses-script.png)

Os trabalhos terão acesso aos recursos do Azure fazendo uma conexão à sua assinatura do Azure. Eles só terão acesso aos recursos no seu data center se estes puderem ser acessados da nuvem pública.

## <a name="job-statuses"></a>Status de trabalho
A tabela a seguir descreve os diferentes status possíveis para um trabalho.

| Status | Descrição |
|:--- |:--- |
| Concluído |Operação concluída com sucesso. |
| Falha |Para [runbooks gráficos e de fluxo de trabalho do PowerShell](automation-runbook-types.md), o runbook falhou ao compilar.  Para [runbooks de Script do PowerShell](automation-runbook-types.md), o runbook falhou ao iniciar ou o trabalho encontrou uma exceção. |
| Erro, aguardando recursos |O trabalho falhou porque atingiu o limite de [fração justa](#fairshare) três vezes e iniciou do mesmo ponto de verificação ou desde o início do runbook em cada uma das vezes. |
| Em fila |O trabalho está aguardando recursos de um trabalho do Automation ficar disponível para que ele possa ser iniciado. |
| Iniciando |O trabalho foi atribuído a um trabalho, e o sistema está iniciando-o. |
| Continuando |O sistema está reiniciando o trabalho depois que ele foi suspenso. |
| Executando |O trabalho está em execução. |
| Executando, aguardando recursos |O trabalho foi descarregado, pois atingiu o limite de [fração justa](#fairshare) . Ele continuará em breve a partir de seu último ponto de verificação. |
| Parada |O trabalho foi interrompido pelo usuário antes de ser concluído. |
| Parando |O sistema está no processo de interromper o trabalho. |
| Suspenso |O trabalho foi suspenso pelo usuário, pelo sistema ou por um comando no runbook. Um trabalho suspenso pode ser iniciado novamente e continuará a partir de seu último ponto de verificação ou desde o início do runbook se não houver nenhum ponto de verificação. O runbook só será suspenso pelo sistema no caso de uma exceção. Por padrão, ErrorActionPreference é definido como **Continuar** o que significa que o trabalho continuará a ser executado com um erro. Se essa variável de preferência é definida como **Parar** em seguida, o trabalho será suspenso com um erro.  Aplica-se a somente [runbooks gráficos e de fluxo de trabalho do PowerShell](automation-runbook-types.md) . |
| Suspensão |O sistema está tentando suspender o trabalho por solicitação do usuário. O runbook precisa atingir seu próximo ponto de verificação antes de poder ser suspenso. Se já passou seu último ponto de verificação, ele será concluído antes de poder ser suspenso.  Aplica-se a somente [runbooks gráficos e de fluxo de trabalho do PowerShell](automation-runbook-types.md) . |

## <a name="viewing-job-status-from-the-azure-portal"></a>Exibindo o status do trabalho no portal do Azure
Você pode exibir um status resumido de todos os trabalhos de runbook ou analisar os detalhes de um trabalho específico de runbook no portal do Azure ou configurando a integração com seu espaço de trabalho de análise do Log Analytics do OMS (Microsoft Operations Management Suite) para encaminhar fluxos e status do trabalho do runbook.  Para saber mais sobre a integração com o Log Analytics do OMS, confira [Encaminhar status e fluxos de trabalho da Automação para o Log Analytics (OMS)](automation-manage-send-joblogs-log-analytics.md).  

### <a name="automation-runbook-jobs-summary"></a>Resumo dos trabalhos de runbook da Automação
Na folha Conta de automação, você pode ver um resumo de todos os trabalhos de runbook para determinada Conta de automação no bloco **Estatísticas de Trabalho**.<br><br> ![Bloco Estatísticas de Trabalho](./media/automation-runbook-execution/automation-account-job-status-summary.png).<br> Esse bloco exibe a contagem e a representação gráfica do status do trabalho para todos os trabalhos executados.  

Um clique no bloco exibirá a folha **Trabalhos**, que inclui uma lista resumida de todos os trabalhos executados, com status, horas de início e de conclusão da execução do trabalho.<br><br> ![Folha Trabalhos da Conta de automação](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)<br><br>  Você pode filtrar a lista de trabalhos selecionando **Filtrar trabalhos** na folha **Trabalhos** e filtrar por um runbook ou status do trabalho específico, ou, na lista suspensa, pelo intervalo de data/hora de pesquisa.<br><br> ![Filtrar por status do trabalho](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Como alternativa, você pode exibir detalhes de resumo do trabalho para um runbook específico selecionando esse runbook na folha **Runbooks** folha da sua Conta de automação e selecionando o bloco **Trabalhos**.  Isso exibirá a folha **Trabalhos** e, dela, você poderá clicar no registro de trabalho para exibir seus detalhes e saída.<br><br> ![Folha Trabalhos da Conta de automação](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)<br> 

### <a name="job-summary"></a>Resumo do trabalho
Você pode exibir uma lista de todos os trabalhos que foram criados para um determinado runbook e o status mais recente. Você pode filtrar essa lista pelo status do trabalho e o intervalo de datas para a última alteração no trabalho. Clique no nome de um trabalho para exibir as informações detalhadas e sua saída. A exibição detalhada do trabalho inclui os valores para os parâmetros de runbook que foram fornecidos para esse trabalho.

Você pode usar as etapas a seguir para exibir os trabalhos de um runbook.

1. No Portal de Gerenciamento do Azure, selecione **Automação** e clique no nome de uma conta de automação.
2. Clique no nome de um runbook.
3. Selecione a guia **Trabalhos** .
4. Clique na coluna **Trabalho criado** para um trabalho para exibir seus detalhes e saída.

## <a name="retrieving-job-status-using-windows-powershell"></a>Recuperando o status do trabalho usando o Windows PowerShell
Você pode usar o [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx) para recuperar os trabalhos criados para um runbook e os detalhes de um trabalho específico. Se você iniciar um runbook com o Windows PowerShell usando [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx), ele retornará o trabalho resultante. Use [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx)Output para obter a saída de um trabalho.

Os comandos de exemplo a seguir recuperam o último trabalho para um exemplo de runbook e exibe seu status, os valores fornecidos para os parâmetros de runbook e a saída do trabalho.

    $job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
    –RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
    $job.Status
    $job.JobParameters
    Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output

## <a name="fair-share"></a>fração justa
Para compartilhar recursos entre todos os runbooks na nuvem, a Automação do Azure vai descarregar temporariamente qualquer trabalho depois de ser executado por 3 horas.  Os runbooks [Graphical](automation-runbook-types.md#graphical-runbooks) e [Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) serão retomados do seu último [ponto de verificação](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints). Durante esse tempo, o trabalho mostrará o status Executando, aguardando recursos. Se o runbook não tiver nenhum ponto de verificação ou o trabalho não atingir o primeiro ponto de verificação antes de ser descarregado, ele será reiniciado desde o início.  [PowerShell](automation-runbook-types.md#powershell-runbooks) são sempre reiniciados desde o início, já que não dão suporte a pontos de verificação.

Se o runbook reiniciar a partir do mesmo ponto de verificação ou desde o início do runbook três vezes consecutivas, ele será encerrado com um status Falha, aguardando recursos. Isso serve para proteger contra runbooks em execução indefinidamente sem conclusão, já que não são capazes alcançar o próximo ponto de verificação sem serem descarregados novamente. Nesse caso, você receberá a exceção com falha abaixo.

*O trabalho não pode continuar a execução porque foi removido repetidamente do mesmo ponto de verificação. Verifique se o Runbook não executa operações demoradas sem persistir o estado.*

Quando você criar um runbook, verifique se o tempo para executar atividades entre dois pontos de verificação não ultrapassa 3 horas. Talvez seja necessário adicionar pontos de verificação ao seu runbook para que não atinja esse limite de 3 horas ou acabar com as operações de execução. Por exemplo, o runbook pode executar uma reindexação em um grande banco de dados SQL. Se essa operação única não for concluída dentro do limite de fração justa, o trabalho será descarregado e reiniciado do princípio. Nesse caso, você deve dividir a operação de reindexação em várias etapas, como a reindexação de uma tabela de cada vez, e inserir um ponto de verificação após cada operação para que o trabalho possa continuar após a conclusão da última operação.

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre os diferentes métodos que podem ser usados para iniciar um runbook na Automação do Azure, confira [Iniciar um Runbook na Automação do Azure](automation-starting-a-runbook.md)




<!--HONumber=Nov16_HO3-->


