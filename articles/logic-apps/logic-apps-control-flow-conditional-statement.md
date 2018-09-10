---
title: Adicionar instruções condicionais a fluxos de trabalho – Aplicativo Lógico do Azure | Microsoft Docs
description: Como criar condições que controlam ações em fluxos de trabalho no Aplicativo Lógico do Azure
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.date: 03/05/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: d4e69d33e07f484b4ccc5343786865230368c7ca
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096369"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>Crie instruções condicionais que controlam ações de fluxo de trabalho no Aplicativo Lógico do Azure

Para executar ações específicas em seu aplicativo lógico somente depois de passar uma condição especificada, adicione uma *instrução condicional*. Essa estrutura compara dados no fluxo de trabalho com valores ou campos específicos. Dessa forma, é possível definir diferentes ações para executar com base em se os dados atendem a condição ou não. É possível aninhar condições dentro de outras.

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

3. Em **Condição**, compile a condição. 

   1. Na caixa esquerda, especifique os dados ou o campo que deseja comparar.

      Quando você clica dentro da caixa à esquerda, a lista de conteúdo dinâmico é exibida para que você possa selecionar saídas de etapas anteriores em seu aplicativo lógico. 
      Para este exemplo, selecione o resumo do feed RSS.

      ![Compilar a condição](./media/logic-apps-control-flow-conditional-statement/edit-condition.png)

   2. Na lista intermediária, selecione a operação a executar. 
   Neste exemplo, selecione “**contém**”. 

   3. Na caixa direita, especifique um valor ou campo como o critério. 
   Neste exemplo, especifique esta cadeia de caracteres: **Microsoft**

   Aqui está a condição completa:

   ![Condição completa](./media/logic-apps-control-flow-conditional-statement/edit-condition-2.png)

5. Em **Se verdadeiro** e **Se falso**, adicione as etapas a serem executadas com base em se a condição é atendida. Por exemplo: 

   ![Condição com caminhos "Se verdadeiro" e "Se falso"](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > Você pode arrastar ações existentes para os caminhos **Se verdadeiro** e **Se falso**.

6. Salve seu aplicativo lógico.

A partir de agora, esse aplicativo lógico enviará email somente quando os novos itens no feed RSS atenderem à condição.

## <a name="json-definition"></a>Definição JSON

Após ter criado um aplicativo lógico usando uma instrução condicional, vejamos a definição de código de alto nível atrás da instrução condicional.

``` json
"actions": {
  "Condition": {
    "type": "If",
    "actions": {
      "Send_an_email": {
        "inputs": {},
        "runAfter": {}
    },
    "expression": {
      "and": [ 
        { 
          "contains": [ 
            "@triggerBody()?['summary']", "Microsoft"
          ]
        } 
      ]
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em recursos e sugestões, visite o [site de comentários do usuário de Aplicativos Lógicos do Azure](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* [Executar etapas baseadas em valores diferentes (instruções de comutador)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar e repetir (loops)](../logic-apps/logic-apps-control-flow-loops.md)
* [Executar ou mesclar etapas paralelas (branches)](../logic-apps/logic-apps-control-flow-branches.md)
* [Executar etapas baseadas no status da ação agrupada (escopos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
