---
title: "Branches paralelos – Aplicativo Lógico do Azure | Microsoft Docs"
description: "Criar ou ingressar um branch paralelo em aplicativos lógicos"
services: logic-apps
keywords: branches, processamento paralelo
documentationcenter: 
author: ecfan
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 9a836b707a576b9a938f43397ef35c00aeb476bf
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2018
---
# <a name="create-or-join-parallel-branches-in-your-logic-app"></a>Criar ou ingressar um branch paralelo no seu aplicativo lógico

Por padrão, as ações em um aplicativo lógico são executadas em sequência. Para executar ações independentes ao mesmo tempo, você pode criar [Branches paralelos](#parallel-branches) e, em seguida, [unir esses branches](#join-branches) posteriormente no seu fluxo. 

> [!TIP] 
> Se você tiver um gatilho que recebe uma matriz e deseja executar um fluxo de trabalho para cada item de matriz, é possível fazer *debatch* dessa matriz com o [**SplitOn** da propriedade de gatilho](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>pré-requisitos

* Uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/). 

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="parallel-branches"></a>

## <a name="add-a-parallel-branch"></a>Adicionar um branch paralelo

Para executar etapas independentes ao mesmo tempo, você pode adicionar branches paralelos próximos a uma etapa existente. 

![Executar as etapas em paralelo](media/logic-apps-control-flow-branches/parallel.png)

Seu aplicativo lógico espera que todos os branches estejam concluídos antes de continuar o fluxo de trabalho.
Os branches em paralelo são executados somente quando seus valores de propriedade `runAfter` corresponderem ao status da etapa pai concluída. Por exemplo, `branchAction1` e `branchAction2` estão definidos para serem executados apenas quando `parentAction` tiver concluído com o status `Succeded`.

> [!NOTE]
> Antes de começar, seu aplicativo lógico já deve ter uma etapa onde você possa adicionar branches paralelos.

1. No <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>, abra o aplicativo lógico no Designer do Aplicativo Lógico.

2. Mova o mouse sobre a seta acima da etapa onde você deseja adicionar ramificações paralelas.

3. Escolha o sinal de **mais** (**+**), escolha **Adicionar um branch paralelo**e selecione o item que você deseja adicionar.

   ![Adicionar branch paralelo](media/logic-apps-control-flow-branches/add-parallel-branch.png)

   O item selecionado agora aparece em um branch paralelo.

4. Para cada branch paralelo, adicione as etapas que você desejar. Para adicionar uma ação sequencial a um branch paralelo, mova seu mouse sob a ação onde você deseja adicionar a ação sequencial. Escolha o sinal de **mais** (**+**) e a etapa que você deseja adicionar.

   ![Adicionar etapa sequencial ao branch paralelo](media/logic-apps-control-flow-branches/add-sequential-action-parallel-branch.png)

5. Para mesclar de volta os branches, [una seus branches paralelos](#join-branches). 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>Definição de branch paralelo (JSON)

Se você estiver trabalhando na exibição de código, será possível definir uma estrutura paralela na definição JSON do aplicativo lógico, por exemplo:

``` json
{
  "triggers": {
    "myTrigger": { }
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

<a name="join-branches"></a>

## <a name="join-parallel-branches"></a>Unir branches paralelos

Para mesclar branches paralelos, adicione uma etapa na parte inferior abaixo de todos os branches. Esta etapa é executada após todos os branches paralelos terem concluído a execução.

![Unir branches paralelos](media/logic-apps-control-flow-branches/join.png)

1. No [Portal do Azure](https://portal.azure.com), encontre e abra seu aplicativo lógico no Designer de Aplicativo Lógico. 

2. Sob os branches paralelos que você desejar unir, adicione a etapa que você deseja executar.

   ![Adicione uma etapa que una os branches paralelos](media/logic-apps-control-flow-branches/join-steps.png)

   Seus branches paralelos são mesclados.

<a name="join-json"></a>

## <a name="join-definition-json"></a>Definição de união (JSON)

Se você estiver trabalhando na exibição de código, será possível definir uma estrutura de união na definição JSON do aplicativo lógico, por exemplo:

``` json
{
  "triggers": {
    "myTrigger": { }
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "joinAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "branchAction1": [
          "Succeeded"
        ],
        "branchAction2": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em recursos e sugestões, visite o [site de comentários do usuário de Aplicativos Lógicos do Azure](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* [Executar etapas baseadas em uma condição (instruções condicionais)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Executar etapas baseadas em valores diferentes (instruções de comutador)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar e repetir etapas (loops)](../logic-apps/logic-apps-control-flow-loops.md)
* [Executar etapas baseadas no status da ação agrupada (escopos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)