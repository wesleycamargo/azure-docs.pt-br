---
title: Disparar o Azure Functions usando webhooks no Azure IoT Central
description: Crie um aplicativo de função que é executado sempre que uma regra é disparada no Azure IoT Central.
author: viv-liu
ms.author: viviali
ms.date: 09/17/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 694c259472bf7e18f0ae3d424acf5b5cfb7ea7ab
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46294247"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Disparar o Azure Functions usando webhooks no Azure IoT Central

*Este tópico aplica-se a construtores e administradores.*

Use o Azure Functions para executar código sem servidor na saída de webhook das regras do IoT Central. Você não precisa provisionar uma máquina virtual ou publicar um aplicativo web para usar o Azure Functions, mas em vez disso, você pode executar esse código sem servidor. Use o Azure Functions para transformar o conteúdo do webhook antes de enviá-lo ao seu destino final, como um banco de dados SQL ou grade de eventos. 

## <a name="prerequisites"></a>Pré-requisitos
+ Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="how-to-connect-azure-functions"></a>Como conectar o Azure Functions

1. [Crie um novo aplicativo de funções no portal do Azure. ](https://ms.portal.azure.com/#create/Microsoft.FunctionApp).

    ![Crie um novo aplicativo de funções no portal do Azure](media/howto-trigger-azure-functions/createfunction.png)

2. Expanda seu aplicativo de funções e clique no **+ botão** próximo a Functions. Se essa função for a primeira em seu aplicativo de funções, selecione **Função personalizada**. Exibe o conjunto completo de modelos de função.
    
    ![Escolha a função personalizada no aplicativo Functions](media/howto-trigger-azure-functions/customfunction.png)

3. No campo de pesquisa, digite **"genérico"** e depois escolha a linguagem desejada para o modelo de gatilho de webhook genérico. Esse tópico usa uma função C#. 

    ![Selecione o gatilho de webhook genérico](media/howto-trigger-azure-functions/genericwebhooktrigger.png)

4. Em sua nova função, clique em **</> Obter URL de função**. Em seguida, copie e salve o valor. Você usará esse valor para configurar o webhook. 

    ![Obtenha a URL para a função](media/howto-trigger-azure-functions/getfunctionurl.png)
4. Na IoT Central, navegue para a regra que você deseja se conectar ao seu aplicativo de funções.

5. Adicionar uma ação de webhook. Insira um **Nome de exibição** e cole a URL de função que você copiou anteriormente na **URL de retorno de chamada**.

    ![Insira a URL da função no campo URL de retorno de chamada](media/howto-trigger-azure-functions/configurewebhook.PNG)

6. Salve a regra. Agora, quando a regra for disparada, o webhook invocará o aplicativo de funções para executar. Em seu aplicativo de funções, você pode clicar em **Monitor** para ver o histórico da função chamada. Você pode usar o App Insights ou o modo de exibição clássico para examinar o histórico.

    ![Monitore o histórico de invocação da função](media/howto-trigger-azure-functions/monitorfunction.PNG)

Para obter mais informações, visite o artigo do Azure Functions sobre [criar uma função disparada pelo webhook genérico](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function). 

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu a configurar e usar webhooks, a próxima etapa sugerida é explorar [Compilar fluxos de trabalho no Microsoft Flow](howto-add-microsoft-flow.md).
