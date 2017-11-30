---
title: "Criar uma tarefa de observador na conta de Automação do Azure | Microsoft Docs"
description: "Saiba como criar uma tarefa de observador na conta de Automação do Azure para inspecionar novos arquivos criados em uma pasta."
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.assetid: 0dd95270-761f-448e-af48-c8b1e82cd821
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/15/2017
ms.author: eamono
ms.openlocfilehash: 0ddd31f7ce2217c1136eccd391bb30bd4461c3e5
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2017
---
# <a name="azure-automation-watcher-tasks-enable-you-to-respond-to-events-happening-in-your-local-datacenter"></a>As tarefas de observador de Automação do Azure permitem responder a eventos que ocorrem no seu datacenter local

Neste tutorial, saiba como criar uma tarefa de observador para:

> [!div class="checklist"]
> * Criar um runbook de observador que procura novos arquivos em um diretório.
> * Criar uma variável de automação para manter a última vez em que um arquivo foi processado pelo observador.
> * Criar um runbook de ação que é chamado quando o runbook de observador encontra um novo arquivo.
> * Criar uma tarefa de observador que escolhe o runbook de observador e de ação.
> * Disparar um observador adicionando um novo arquivo a um diretório.
> * Inspecionar a saída do runbook de ação que mostra informações sobre o novo arquivo.  

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, os itens a seguir são necessários.
+ Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
+ [Conta de automação](automation-offering-get-started.md) para manter os runbooks de observador e de ação e a tarefa do observador.
+ Um [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) no qual a tarefa do observador é executada.

## <a name="create-a-watcher-runbook-that-looks-for-new-files"></a>Criar um runbook de observador que procura novos arquivos
1.  Abra a conta de automação e clique na página Runbooks.
2.  Clique no botão "Procurar na galeria".
![Lista de runbooks da interface do usuário](media/automation-watchers-tutorial/WatcherTasksRunbookList.png)
3.  Procure "Watch-NewFile" e importe o runbook para a conta de automação.
![Publicar runbook por meio da interface do usuário](media/automation-watchers-tutorial/Watch-NewFileRunbook.png)
4.  Clique em "Editar" para exibir a origem do Runbook e clique no botão "Publicar".

## <a name="create-an-automation-variable-to-keep-the-last-time-a-file-was-processed-by-the-watcher"></a>Criar uma variável de automação para manter a última vez em que um arquivo foi processado pelo observador
1.  Abra a página de variáveis em RECURSOS COMPARTILHADOS e clique em "Adicionar variável" ![Listar variáveis da interface do usuário](media/automation-watchers-tutorial/WatcherVariableList.png)
2.  Digite "Watch-NewFileTimestamp" para o nome
3.  Selecione o tipo como DateTime e, em seguida, clique no botão "Criar".
![Criar variável de marca-d'água por meio da interface do usuário](media/automation-watchers-tutorial/WatcherWatermarkVariable.png)

## <a name="create-an-action-runbook-that-is-called-when-the-watcher-runbook-finds-a-new-file"></a>Criar um runbook de ação que é chamado quando o runbook de observador encontra um novo arquivo
1.  Clique na página Runbooks sob a categoria "AUTOMAÇÃO DE PROCESSO".
2.  Clique no botão "Procurar na galeria".
3.  Procure "Process-NewFile" e importe o runbook para a conta de automação.
4.  Clique em "Editar" para exibir a origem do Runbook e clique no botão "Publicar".
![Observador do processo da interface do usuário](media/automation-watchers-tutorial/Watch-ProcessNewFile.png)


## <a name="create-a-watcher-task-that-selects-the-watcher-runbook-and-action-runbook"></a>Criar uma tarefa de observador que escolhe o runbook de observador e de ação
1.  Abra a página Tarefas do observador e clique no botão "Adicionar uma tarefa de observador".
![Lista do observador da interface do usuário](media/automation-watchers-tutorial/WatchersList.png)
2.  Digite "WatchMyFolder" como nome.
3.  Selecione "Configurar observador" e selecione o runbook "Watch-NewFile".
![Configurar observador por meio da interface do usuário](media/automation-watchers-tutorial/ConfigureWatcher.png)
4.  Digite os seguintes valores para os parâmetros:
    *   FOLDERPATH. Uma pasta no Hybrid Worker em que os novos arquivos são criados
    *   EXTENSION. Deixe em branco para processar todas as extensões de arquivo.
    *   RECURSE. Deixar o padrão.
    *   RUN SETTINGS. Escolha o Hybrid Worker.
5.  Clique em OK e selecione Voltar à página do observador.
6.  Selecione "Configurar ação" e o runbook "Process-NewFile".
![Configurar ação do observador por meio da interface do usuário](media/automation-watchers-tutorial/ConfigureAction.png)
7.  Digite os seguintes valores para os parâmetros:
    *   EVENTDATA. Deixe em branco. Os dados são passados do runbook de observador.
    *   Run Settings. Deixe como Azure, pois este runbook é executado no serviço de automação.
8.  Clique em OK e selecione Voltar à página do observador.
9.  Clique em OK para criar a tarefa do observador.

## <a name="trigger-a-watcher-by-adding-a-new-file-to-a-directory"></a>Disparar um observador adicionando um novo arquivo a um diretório
1.  Deixe remoto para o Hybrid Worker
2.  Adicione um novo arquivo de texto à pasta que está sendo monitorada pela tarefa de observador.

## <a name="inspect-the-output-from-the-action-runbook-that-shows-information-on-the-new-file"></a>Inspecionar a saída do runbook de ação que mostra informações sobre o novo arquivo
1.  Clique na tarefa de inspetor para "WatchMyFolder"
2.  Clique em "Exibir fluxos de dados do observador" para ver que o observador encontrou o novo arquivo e iniciou o runbook de ação.
3.  Clique em "Exibir trabalhos de ação do observador" para ver o trabalho de runbook de ação.
![Trabalhos de ação do observador por meio da interface do usuário](media/automation-watchers-tutorial/WatcherActionJobs.png)


## <a name="next-steps"></a>Próximas etapas:

Para obter mais informações, veja [Meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md).








