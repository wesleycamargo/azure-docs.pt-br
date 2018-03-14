---
title: "Instruções condicionais - Executar etapas baseadas em uma condição - Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Execute etapas no aplicativo lógico somente após atender a uma condição. Crie árvores de decisão que executem fluxos de trabalho com base em condições especificadas."
services: logic-apps
keywords: "instruções condicionais, árvores de decisão"
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
ms.openlocfilehash: 486c1053f42ed3becc2c4b60accc993db7f24baa
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2018
---
# <a name="conditional-statements-run-steps-based-on-a-condition-in-logic-apps"></a>Instruções condicionais: executar etapas baseadas em uma condição em aplicativos lógicos

Para executar etapas somente após passar uma condição especificada, utilize uma *instrução condicional*. Essa estrutura compara dados no fluxo de trabalho com valores ou campos específicos. Dessa forma, é possível definir diferentes etapas para executar com base em se os dados atendem a condição ou não. É possível aninhar condições dentro de outras.

Por exemplo, suponha que você tenha um aplicativo lógico que envia muitos emails quando novos itens aparecem no feed RSS de um site. Então, é possível adicionar uma declaração condicional para enviar email somente quando o novo item incluir uma cadeia de caracteres específica. 

> [!TIP]
> Para executar diferentes etapas baseadas em diferentes valores específicos, use uma [*instrução de comutador*](../logic-apps/logic-apps-control-flow-switch-statement.md).

## <a name="prerequisites"></a>pré-requisitos

* Uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Para seguir o exemplo neste artigo, [crie esse aplicativo lógico de exemplo](../logic-apps/quickstart-create-first-logic-app-workflow.md) com uma conta do Outlook.com ou Office 365 Outlook.

## <a name="add-a-condition"></a>Adicione uma condição

1. No <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>, abra o aplicativo lógico no Designer do Aplicativo Lógico.

2. Adicione uma condição no local que deseja. 

   Para adicionar uma condição entre etapas, mova o ponteiro sobre a seta onde você quer adicionar a condição. Escolha o **sinal de adição** (**+**) que aparece e, em seguida, escolha **Adicionar uma condição**. Por exemplo: 

   ![Adicionar condição entre etapas](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

   Quando você quiser adicionar uma condição no final do fluxo de trabalho, na parte inferior do aplicativo lógico, escolha  **+ Nova etapa** > **Adicionar uma condição**.

3. Em **Condição**, cria a condição. 

   1. Na caixa esquerda, especifique os dados ou o campo que deseja comparar.

      A partir da lista **Adicionar conteúdo dinâmico**, você poderá selecionar campos existentes do aplicativo lógico.

   2. Na lista intermediária, selecione a operação a executar. 
   3. Na caixa direita, especifique um valor ou campo como o critério.

   Por exemplo: 

   ![Editar condição no modo básico](./media/logic-apps-control-flow-conditional-statement/edit-condition-basic-mode.png)

   Aqui está a condição completa:

   ![Condição completa](./media/logic-apps-control-flow-conditional-statement/edit-condition-basic-mode-2.png)

   > [!TIP]
   > Para criar uma condição mais avançada ou usar expressões, escolha **Editar em modo avançado**. É possível utilizar expressões definidas pela [Linguagem de Definição de Fluxo de Trabalho](../logic-apps/logic-apps-workflow-definition-language.md).
   > 
   > Por exemplo: 
   >
   > ![Editar condição no código](./media/logic-apps-control-flow-conditional-statement/edit-condition-advanced-mode.png)

5. Em **SE SIM** e **SE NÃO**, adicione as etapas a serem executadas com base em se a condição é atendida. Por exemplo: 

   ![Condição com caminhos SIM e NÃO](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > Você pode arrastar ações existentes para os caminhos **SE SIM** e **SE NÃO**.

6. Salve seu aplicativo lógico.

A partir de agora, esse aplicativo lógico enviará email somente quando os novos itens no feed RSS atenderem à condição.

## <a name="json-definition"></a>Definição JSON

Após ter criado um aplicativo lógico usando uma instrução condicional, vejamos a definição de código de alto nível atrás da instrução condicional.

``` json
"actions": {
  "myConditionName": {
    "type": "If",
    "expression": "@contains(triggerBody()?['summary'], 'Microsoft')",
    "actions": {
      "Send_an_email": {
        "inputs": { },
        "runAfter": {}
      }
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em recursos e sugestões, visite o [site de comentários do usuário de Aplicativos Lógicos do Azure](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* [Executar etapas baseadas em valores diferentes (instrução de comutador)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar e repetir etapas (loops)](../logic-apps/logic-apps-control-flow-loops.md)
* [Executar ou mesclar etapas paralelas (branches)](../logic-apps/logic-apps-control-flow-branches.md)
* [Executar etapas baseadas no status da ação agrupada (escopos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)