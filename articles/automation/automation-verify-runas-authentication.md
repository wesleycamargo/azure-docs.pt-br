---
title: "Validar a conta de automação do Azure | Microsoft Docs"
description: "Este artigo descreve como confirmar se a configuração de sua conta de automação está configurada corretamente."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/14/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 5bed2616e15a2e5f52e79d0c28159b568e7a1de3
ms.lasthandoff: 04/15/2017

---

# <a name="test-azure-automation-run-as-account-authentication"></a>Como testar a autenticação da conta Executar como de Automação do Azure
Depois que uma conta de automação é criada com êxito, você pode executar um teste simples para confirmar que você é capaz de autenticar com êxito no Azure Resource Manager ou na implantação clássica do Azure usando sua conta Executar como de Automação recém-criada ou atualizada.    

## <a name="automation-run-as-authentication"></a>Autenticação da conta Executar como de automação

1. No Portal do Azure, abra a Conta de automação criada anteriormente.  
2. Clique no bloco **Runbooks** para abrir a lista de runbooks.
3. Selecione o runbook **AzureAutomationTutorialScript**, em seguida, clique em **Iniciar** para iniciar o runbook.  Será mostrado um aviso, que verifica se você deseja iniciar o runbook.
4. Um [trabalho de runbook](automation-runbook-execution.md) é criado, a folha Trabalho é exibida e o status do trabalho é mostrado no bloco **Resumo do Trabalho** .  
5. O status do trabalho será iniciado como *Na fila* , indicando que ele está aguardando um runbook worker ficar disponível na nuvem. Mudará para *Iniciando* quando um trabalhador reivindicar o trabalho, em seguida, para *Executando* quando o runbook realmente começar a ser executado.  
6. Quando o trabalho do runbook concluir, deveremos ver um status de **Concluído**.<br> ![Teste de runbook da entidade de segurança](media/automation-verify-runas-authentication/job-summary-automationtutorialscript.png)<br>
7. Para ver os resultados detalhados do runbook, clique no bloco **Saída** .
8. Na folha **Saída** , você deverá ver que ele foi autenticado e que retornou uma lista de todos os recursos disponíveis no grupo de recursos.
9. Feche a folha **Saída** para voltar para a folha **Resumo do Trabalho**.
10. Feche o **Resumo do Trabalho** e a folha do runbook **AzureAutomationTutorialScript** correspondente.

## <a name="classic-run-as-authentication"></a>Autenticação da conta Executar como clássica
Execute as seguintes etapas se você estiver gerenciando recursos do modelo de implantação clássico, para confirmar que você é capaz de autenticar com êxito usando a nova conta Executar como clássica.     

1. No Portal do Azure, abra a Conta de automação criada anteriormente.  
2. Clique no bloco **Runbooks** para abrir a lista de runbooks.
3. Selecione o runbook **AzureClassicAutomationTutorialScript**, em seguida, clique em **Iniciar** para iniciar o runbook.  Será mostrado um aviso, que verifica se você deseja iniciar o runbook.
4. Um [trabalho de runbook](automation-runbook-execution.md) é criado, a folha Trabalho é exibida e o status do trabalho é mostrado no bloco **Resumo do Trabalho** .  
5. O status do trabalho será iniciado como *Na fila* , indicando que ele está aguardando um runbook worker ficar disponível na nuvem. Mudará para *Iniciando* quando um trabalhador reivindicar o trabalho, em seguida, para *Executando* quando o runbook realmente começar a ser executado.  
6. Quando o trabalho do runbook concluir, deveremos ver um status de **Concluído**.<br><br> ![Teste de runbook da entidade de segurança](media/automation-verify-runas-authentication/job-summary-automationclassictutorialscript.png)<br>  
7. Para ver os resultados detalhados do runbook, clique no bloco **Saída** .
8. Na folha **Saída** , você deverá ver que ele foi autenticado e que retornou uma lista de todas as VMs clássicas na assinatura.
9. Feche a folha **Saída** para voltar para a folha **Resumo do Trabalho**.
10. Feche o **Resumo do Trabalho** e a folha do runbook **AzureClassicAutomationTutorialScript** correspondente.

## <a name="next-steps"></a>Próximas etapas
* Para começar a usar os runbooks do PowerShell, confira [Meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md).
* Para saber mais sobre a autorização gráfica, confira [Criação gráfica na automação do Azure](automation-graphical-authoring-intro.md).

