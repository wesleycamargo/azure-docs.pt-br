---
title: Ação de Runbook de Automação do Azure no Log Analytics iniciada pelo usuário | Microsoft Docs
description: Este artigo descreve como executar um runbook de automação de um resultado de pesquisa do Log Analytics sob demanda.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: 8730070e1b235324a53ad81957339f4ef17db6dc
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53193681"
---
# <a name="take-action-with-an-automation-runbook-from-a-log-analytics-log-search-result"></a>Realizar uma ação com um Runbook de Automação de um resultado de pesquisa do Log Analytics

De um resultado de pesquisa do Azure Log Analytics, você pode agora selecionar **Agir** para executar um runbook de Automação.  O runbook pode ser usado para corrigir o problema ou executar alguma outra ação, como coletar informações de solução de problemas, enviar um email ou criar uma solicitação de serviço. 

## <a name="components-and-features-used"></a>Componentes e recursos usados
* [Conta de Automação do Azure](../../automation/automation-quickstart-create-account.md)
* [Workspace do Log Analytics](../../azure-monitor/log-query/log-query-overview.md)

## <a name="to-initiate-runbook-from-log-search"></a>Para iniciar o runbook da pesquisa de logs

Para executar a ação em um evento e iniciar um runbook de seus resultados da pesquisa de logs, você começa criando uma pesquisa de logs e, dos resultados, você pode invocar um runbook sob demanda.  Isso pode ser alcançado a partir do recurso de pesquisa de logs no [Portal do Azure](../../azure-monitor/log-query/log-query-overview.md).  Neste exemplo, podemos realizar uma pesquisa de logs no Portal do Azure com uma demonstração básica desse recurso.

1. No Portal do Azure, clique em **Todos os serviços** e selecione **Log Analytics**.  
2. Selecione seu workspace de Log Analytics.
3. No workspace, selecione **Pesquisa de Logs**.  
4. Na página Pesquisa de Logs, execute uma pesquisa de logs.  
5. Dos resultados da pesquisa de logs, clique na elipse à esquerda de um dos campos e, do popup, selecione **Agir a respeito**.<br><br> ![Selecione a opção Agir do resultado da pesquisa](./media/take-action/log-search-takeaction-menuoption.png) 
6. Selecione **Executar um runbook** e selecione um runbook para ser executado.  Você pode selecionar qualquer runbook na conta de Automação que está vinculada ao workspace do Log Analytics.  Observe o seguinte:

    * Os runbooks são organizados por marcas
    * Valores de parâmetro de entrada de runbook podem ser selecionados diretamente dos campos do resultado de pesquisa.  Aparecerá uma lista suspensa exibindo todos os campos disponíveis do resultado dentre os quais se poderá selecionar.  
    * Você poderá optar por executar o runbook em um [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) que você instalou no computador que tem o problema, desde que você tenha um grupo de Hybrid Runbook Worker correspondente contendo somente esse computador como um membro.  Se o nome do grupo Hybrid Worker corresponde ao nome do computador que está no resultado da pesquisa de logs, o grupo é selecionado automaticamente.    

6. Depois de clicar em **Executar**, a página do trabalho de runbook é aberta para que você possa examinar o status do trabalho.   

Se você selecionar um runbook que foi configurado para ser [chamado de um alerta do Log Analytics](../../automation/automation-create-alert-triggered-runbook.md), ele terá um parâmetro de entrada chamado **WebhookData**, que é do tipo **Objeto**.  Se o parâmetro de entrada é obrigatório, você precisa passar os resultados da pesquisa para o runbook para que ele possa converter a cadeia de caracteres formatada em JSON em um tipo de objeto, permitindo que você filtre itens específicos que você referenciará em atividades de runbook.  Você pode fazer isso selecionando **Resultado da pesquisa (Objeto)** da lista suspensa.<br><br> ![Selecione o objeto de dados de Webhook para o parâmetro do runbook](media/take-action/select-runbook-and-properties.png)   
    
## <a name="next-steps"></a>Próximas etapas

* Examine a [referência de pesquisa de log do Log Analytics](../../azure-monitor/log-query/log-query-overview.md) para exibir todos os campos de pesquisa e as facetas disponíveis no Log Analytics.
* Para saber como invocar um runbook de automação automaticamente, examine [chamar um runbook de Automação do Azure de um alerta do Log Analytics](../../automation/automation-create-alert-triggered-runbook.md).  
