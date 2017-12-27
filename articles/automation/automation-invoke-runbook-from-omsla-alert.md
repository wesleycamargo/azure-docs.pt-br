---
title: "Chamar um runbook de Automação do Azure a partir de um alerta do Log Analytics | Microsoft Docs"
description: "Este artigo fornece uma visão geral de como chamar um runbook de Automação a partir de um alerta do Log Analytics no Operations Management Suite."
services: automation
documentationcenter: 
author: georgewallace
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
ms.openlocfilehash: 76d5ab23ef1962733ccb3b36640facdba9ab8acb
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2017
---
# <a name="call-an-azure-automation-runbook-from-a-log-analytics-alert"></a>Chamar runbook de Automação do Azure a partir de um alerta do Log Analytics

Você pode configurar um alerta no Azure Log Analytics para criar um registro de alerta quando os resultados correspondem aos seus critérios. Esse alerta pode executar automaticamente um runbook de Automação do Azure em uma tentativa de corrigir o problema. 

Por exemplo, um alerta pode indicar um pico prolongado no uso do processador. Ou pode indicar quando um processo do aplicativo que é essencial para a funcionalidade de um aplicativo de negócios falhará. Um runbook pode, em seguida, escrever um evento correspondente no log de eventos do Windows.  

Há duas opções para chamar um runbook ao configurar o alerta:

* Usar um webhook.
   * Essa será a única opção disponível se seu espaço de trabalho do Operations Management Suite não está vinculado a uma conta de Automação.
   * Se você já tiver uma conta de Automação vinculada a um espaço de trabalho do Operations Management Suite, essa opção ainda estará disponível.  

* Selecione um runbook diretamente.
   * Essa opção está disponível somente se o espaço de trabalho do Operations Management Suite estiver vinculado a uma conta de Automação.

## <a name="calling-a-runbook-by-using-a-webhook"></a>Chamar um runbook com um webhook

Você pode usar um webhook para iniciar um runbook específico na Automação do Azure por meio de uma única solicitação HTTP. Antes de configurar o [alerta do Log Analytics](../log-analytics/log-analytics-alerts.md#alert-rules) para chamar o runbook usando um webhook como uma ação de alerta, você precisará [criar um webhook](automation-webhooks.md#creating-a-webhook) para o runbook que será chamado através desse método. Lembre-se de registrar a URL do webhook para que você possa referenciá-la ao configurar a regra de alerta.   

## <a name="calling-a-runbook-directly"></a>Chamando um runbook diretamente

Você pode instalar e configurar a oferta de Automação e Controle em seu espaço de trabalho do Operations Management Suite. Ao configurar a opção de ações do runbook para o alerta, você poderá exibir todos os runbooks na lista suspensa **Selecionar um runbook** e selecionar o runbook específico que deseja executar em resposta ao alerta. O runbook selecionado pode ser executado em um espaço de trabalho do Azure ou em um Hybrid Runbook Worker. 

Após a criação do alerta usando a opção do runbook, um webhook será criado para o runbook. Você poderá ver o webhook se for para a conta de Automação e abrir o painel do webhook do runbook selecionado. 

Se você excluir o alerta, o webhook não será excluído. Isso não é um problema. O webhook torna-se apenas um item órfão que você, eventualmente, deverá excluir manualmente, para manter uma conta de Automação organizada.  

## <a name="characteristics-of-a-runbook"></a>Características de um runbook

Os dois métodos para chamar o runbook a partir do alerta do Log Analytics possuem características que precisam ser compreendidas antes de configurar suas regras de alerta. 

Os dados de alerta estão em formato json em uma única propriedade chamada **SearchResult**. Esse formato é para ações de runbook e webhook com um payload padrão. Para ações de webhook com payloads personalizados (incluindo **IncludeSearchResults:True** no **RequestBody**) a propriedade é **SearchResults**.

Você deve ter um parâmetro de entrada do runbook denominado **WebhookData** que é do tipo **Objeto**. Pode ser obrigatório ou opcional. O alerta passa os resultados da pesquisa para o runbook usando o parâmetro de entrada.

```powershell
param  
    (  
    [Parameter (Mandatory=$true)]  
    [object] $WebhookData  
    )
```
Você deve ter o código para converter o **WebhookData** em um objeto do PowerShell.

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
```

**$SearchResult** é uma matriz de objetos. Cada objeto contém campos com os valores de um resultado da pesquisa.


## <a name="example-walkthrough"></a>Exemplo de passo a passo

O exemplo a seguir de um runbook gráfico demonstra como esse processo funciona. Ele inicia um serviço do Windows.

![Runbook gráfico para iniciar um serviço do Windows](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice.png)

O runbook possui um parâmetro de entrada do tipo **objeto** que é chamado **WebhookData**. Isso inclui os dados de webhook passados do alerta que contém **SearchResult**.

![Parâmetros de entrada do Runbook](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice-inputparameter.png)

Para este exemplo, criamos dois campos personalizados no Log Analytics: **SvcDisplayName_CF** e **SvcState_CF**. Esses campos extraem o nome de exibição do serviço e o estado do serviço (isto é, em execução ou parado) a partir do evento que é gravado no log de eventos do sistema. Em seguida, criamos uma regra de alerta com a seguinte consulta de pesquisa, para podermos detectar quando o serviço do Spooler de Impressão é interrompido no sistema do Windows:

`Type=Event SvcDisplayName_CF="Print Spooler" SvcState_CF="stopped"` 

Pode ser qualquer serviço de interesse. Para este exemplo, fazemos referência a um dos serviços preexistentes que são incluídos com o sistema operacional do Windows. A ação de alerta está configurada para executar o runbook usado neste exemplo e ser executada no Hybrid Runbook Worker, que está habilitado no sistema de destino.   

A atividade de código do runbook **Obter o nome do serviço de LA** converte a cadeia de caracteres de formato JSON em um tipo de objeto e os filtros em item **SvcDisplayName_CF**. Ela extrai o nome para exibição do serviço do Windows e passa esse valor para a atividade seguinte, que verifica se o serviço foi interrompido antes de tentar reiniciá-lo. **SvcDisplayName_CF** é um [campo personalizado](../log-analytics/log-analytics-custom-fields.md) que criamos no Log Analytics para demonstrar este exemplo.

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
$SearchResult.SvcDisplayName_CF  
```

Quando o serviço para, a regra de alerta no Log Analytics detecta uma correspondência, dispara o runbook e envia o contexto do alerta para o runbook. O runbook tenta verificar se o serviço está parado. Nesse caso, o runbook tenta reiniciar o serviço, verifica se ele foi iniciado corretamente e exibe os resultados.     

Como alternativa, se você não tiver sua conta de Automação vinculada ao seu espaço de trabalho do Operations Management Suite, você pode configurar a regra de alerta com uma ação do webhook. A ação do webhook dispara o runbook. Ele também configura o runbook para converter a cadeia de caracteres de formato JSON e o filtro em **SearchResult** seguindo as diretrizes mencionadas anteriormente.    

>[!NOTE]
> Se o espaço de trabalho foi atualizado para a [nova linguagem de consulta do Log Analytics](../log-analytics/log-analytics-log-search-upgrade.md), o conteúdo do webhook foi alterado. Os detalhes do formato estão na [API REST do Azure Log Analytics](https://aka.ms/loganalyticsapiresponse).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os alertas no Log Analytics e como criar um, consulte [Alertas no Log Analytics](../log-analytics/log-analytics-alerts.md).

* Para entender como disparar runbooks usando um webhook, consulte [Webhooks de Automação do Azure](automation-webhooks.md).
