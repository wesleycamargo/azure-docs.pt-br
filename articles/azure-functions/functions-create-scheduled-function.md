---
title: "Criar uma função executada segundo um agendamento no Azure | Microsoft Docs"
description: "Saiba como criar uma função no Azure que é executada com base em uma agendamento definido por você."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 10461bddeb4d5adf4a6e4f65159ba2653dbef8a4
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
#  <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Criar uma função no Azure que é disparada por um temporizador

Saiba como usar o Azure Functions para criar uma função que é executada com base em uma agendamento definido por você. 

![Criar um aplicativo de funções no portal do Azure](./media/functions-create-scheduled-function/function-app-in-portal-editor.png)

Deve levar menos de cinco minutos para concluir todas as etapas neste tópico.

## <a name="prerequisites"></a>Pré-requisitos 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

Neste tópico, você pode criar uma função disparada por timer em seu aplicativo de funções existente. 

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)] 

## <a name="create-function"></a>Criar uma função disparada por temporizador

1. Expanda seu aplicativo de funções, clique no botão **+** ao lado de **Funções** e clique no modelo **TimerTrigger** para a linguagem de programação desejada. Use então as configurações especificadas na tabela e, em seguida, clique em **Criar**:

    | Configuração      |  Valor sugerido   | Descrição                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nomeie sua função** | TimerTriggerCSharp1 | Define o nome da sua função disparada por temporizador.
    | **[Agendamento](http://en.wikipedia.org/wiki/Cron#CRON_expression)** | 0 */1 * * * * | Uma [expressão CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) de seis campos que agenda sua função para ser executada a cada minuto. |

    Uma nova função na linguagem de programação escolhida por você e que é executada a cada minuto é criada. 

4. Verifique a execução, exibindo informações de rastreamento gravadas nos logs. 

    ![Visualizador de log de função no Portal do Azure.](./media/functions-create-scheduled-function/functions-timer-trigger-view-logs2.png)

Agora, você pode alterar o agendamento da função para que ela seja executada com menos frequência, por exemplo, uma vez por hora. 

## <a name="update-the-timer-schedule"></a>Atualizar o agendamento do temporizador

1. Expanda sua função e clique em **Integrar**. É aqui que você define as associações de entrada e saída de sua função e também define o agendamento. 

2. Insira um novo valor de **Agendamento** de `0 0 */1 * * *` e, em seguida, clique em **Salvar**.  

![As funções atualizam o agendamento do temporizador no Portal do Azure.](./media/functions-create-scheduled-function/functions-timer-trigger-change-schedule.png)

Agora você tem uma função que é executada uma vez a cada hora. 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas
Você criou uma função que é executada segundo um agendamento. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obter mais informações sobre gatilhos de temporizador, consulte [Agendar a execução de código com o Azure Functions](functions-bindings-timer.md). 




