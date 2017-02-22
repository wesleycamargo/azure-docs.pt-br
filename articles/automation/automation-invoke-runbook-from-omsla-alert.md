---
title: "Chamando um Runbook de Automação do Azure a partir de um Alerta do Log Analytics | Microsoft Docs"
description: "Este artigo fornece uma visão geral de como chamar um runbook de Automação a partir de um alerta do Log Analytics do OMS da Microsoft."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/31/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 4ce5ad30d79e92a11231313fe13dd42b94fc2aa4
ms.openlocfilehash: 50969591267ca74e5c4d4aa5c1efe5b673498309

---

# <a name="calling-an-azure-automation-runbook-from-an-oms-log-analytics-alert"></a>Chamando um runbook de Automação do Azure a partir de um alerta do Log Analytics do OMS

Quando um alerta for configurado no Log Analytics para criar um registro de alerta, se os resultados corresponderem a um critério específico, como um aumento prolongado na utilização do processador, ou um determinado processo do aplicativo crítico para a funcionalidade de um aplicativo de negócios falhar e gravar um evento correspondente no log de eventos do Windows, esse alerta poderá executar automaticamente um runbook de Automação em uma tentativa de corrigir o problema.  

Há duas opções para chamar um runbook ao configurar o alerta.  Especificamente:

1. Usando um Webhook.
   * Essa será a única opção disponível se seu espaço de trabalho do OMS não está vinculado a uma conta de Automação.
   * Se você já tiver uma conta de Automação vinculada a um espaço de trabalho do OMS, essa opção ainda estará disponível.  

2. Selecione um runbook diretamente.
   * Esta opção está disponível somente quando o espaço de trabalho do OMS está vinculado a uma conta de Automação.  

## <a name="calling-a-runbook-using-a-webhook"></a>Chamando um runbook com um webhook

Um webhook permite que você inicie um runbook específico na Automação do Azure por meio de uma única solicitação HTTP.  Antes de configurar o [alerta do Log Analytics](../log-analytics/log-analytics-alerts.md#creating-an-alert-rule) para chamar o runbook usando um webhook como uma ação de alerta, primeiro você precisará criar um webhook para o runbook que será chamado com esse método.  Examine e siga as etapas no artigo [Criar um webhook](automation-webhooks.md#creating-a-webhook) e lembre de registrar a URL do webhook para que você possa referenciá-la ao configurar a regra de alerta.   

## <a name="calling-a-runbook-directly"></a>Chamando um runbook diretamente

Se você tiver a oferta Automação e Controle instalada e configurada em seu espaço de trabalho do OMS, ao configurar a opção de ações do Runbook para o alerta, poderá exibir todos os runbooks na lista suspensa **Selecionar um runbook** e selecionar o runbook específico que deseja executar em resposta ao alerta.  O runbook selecionado pode ser executado em um espaço de trabalho na nuvem do Azure ou em um Hybrid Runbook Worker.  Quando o alerta for criado usando a opção do runbook, um webhook será criado para o runbook.  Você poderá ver o webhook se for para a conta de Automação e navegar até a folha do webhook do runbook selecionado.  Se você excluir o alerta, o webhook não será excluído, mas o usuário poderá excluir o webhook manualmente.  Não será um problema se o webhook não for excluído, ele é apenas um item órfão que precisará ser excluído para manter uma conta de Automação organizada.  

## <a name="characteristics-of-a-runbook-for-both-options"></a>Características de um runbook (para ambas as opções)

Os dois métodos para chamar o runbook a partir do alerta do Log Analytics têm características de comportamento diferentes que precisam ser compreendidas antes de configurar suas regras de alerta.  

* Você deve ter um parâmetro de entrada do runbook denominado **WebhookData** que é do tipo **Objeto**.  Pode ser obrigatório ou opcional.  O alerta passa os resultados da pesquisa para o runbook usando o parâmetro de entrada.

        param  
         (  
          [Parameter (Mandatory=$true)]  
          [object] $WebhookData  
         )
  
*  Você deve ter o código para converter o WebhookData em um objeto do PowerShell.

    `$SearchResults = (ConvertFrom-Json $WebhookData.RequestBody).SearchResults.value`

    *$SearchResults* será uma matriz de objetos; cada objeto contém campos com os valores de um resultado da pesquisa

### <a name="webhookdata-inconsistencies-between-the-webhook-option-and-runbook-option"></a>Inconsistências do WebhookData entre a opção do webhook e a opção do runbook 

* Ao configurar um alerta para chamar um Webhook, insira a URL do webhook criada para um runbook e clique no botão **Testar Webhook**.  O WebhookData resultante enviado para o runbook não contém *.SearchResult* nem *.SearchResults*.

*  Se você salvar esse alerta, quando o alerta disparar e chamar o webhook, o WebhookData enviado para o runbook conterá *.SearchResult*.
* Se você criar um alerta e configurá-lo para chamar um runbook (que também cria um webhook), quando o alerta disparar, enviará o WebhookData para o runbook que contém *.SearchResults*.

Portanto, no exemplo de código acima, você precisará obter *.SearchResult* se o alerta chamar um webhook e será necessário obter *.SearchResults* se o alerta chamar um runbook diretamente.

## <a name="example-walkthrough"></a>Exemplo de passo a passo 

Demonstraremos como isso funciona usando o seguinte runbook gráfico de exemplo, que inicia um serviço do Windows.<br><br> ![Iniciar Runbook Gráfico do Serviço do Windows](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice.png)<br>

O runbook tem um parâmetro de entrada do tipo **Objeto** que é denominado **WebhookData** e inclui os dados do webhook passados a partir do alerta que contém *.SearchResults*.<br><br> ![Parâmetros de entrada do runbook](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice-inputparameter.png)<br>

Para este exemplo, no Log Analytics criamos dois campos personalizados, *SvcDisplayName_CF* e *SvcState_CF*, para extrair o nome de exibição do serviço e o estado do serviço (ou seja, em execução ou parado) do evento gravado no Log de eventos do sistema.  Em seguida, criamos uma regra de alerta com a seguinte consulta de pesquisa: `Type=Event SvcDisplayName_CF="Print Spooler" SvcState_CF="stopped"` para podermos detectar quando o serviço do Spooler de Impressão é interrompido no sistema do Windows.  Pode ser qualquer serviço de interesse, mas para este exemplo, fazemos referência a um dos serviços preexistentes que são incluídos com o SO do Windows.  A ação de alerta está configurada para executar nosso runbook usado neste exemplo e ser executada no Hybrid Runbook Worker, que estão habilitados nos sistemas de destino.   

A atividade de código do runbook **Obter o Nome do Serviço de LA** converterá a cadeia de caracteres formatada do JSON em um tipo de objeto, filtrará o item *SvcDisplayName_CF* para extrair o nome de exibição do serviço do Windows e passará isso para a próxima atividade que verificará se o serviço está parado antes de tentar reiniciá-lo.  *SvcDisplayName_CF* é um [campo personalizado](../log-analytics/log-analytics-custom-fields.md) criado no Log Analytics para demonstrar este exemplo.

    $SearchResults = (ConvertFrom-Json $WebhookData.RequestBody).SearchResults.value
    $SearchResults.SvcDisplayName_CF  

Quando o serviço parar, a regra de alerta no Log Analytics irá detectar uma correspondência, disparar o runbook e enviar o contexto do alerta para o runbook. O runbook tomará uma ação para verificar se o serviço está parado e se estiver, tentará reiniciar o serviço, irá verificar se ele iniciou corretamente e enviará os resultados.     

Como alternativa, se você não tivesse sua conta de Automação vinculada ao espaço de trabalho do OMS, configuraria a regra de alerta com uma ação do webhook para disparar o runbook, configuraria o runbook para converter a cadeia de caracteres formatada do JSON e filtraria *.SearchResult* seguindo as orientações mencionadas anteriormente.    

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os alertas no Log Analytics e como criar um, consulte [Alertas no Log Analytics](../log-analytics/log-analytics-alerts.md).

* Para entender como disparar runbooks usando um webhook, consulte [Webhooks de Automação do Azure](automation-webhooks.md).



<!--HONumber=Feb17_HO2-->


