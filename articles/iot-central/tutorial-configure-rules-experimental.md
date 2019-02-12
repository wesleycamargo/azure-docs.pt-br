---
title: Configurar regras e ações no Azure IoT Central | Microsoft Docs
description: Este tutorial mostra a você, como um construtor, como configurar regras e ações baseadas em telemetria em seu aplicativo do Azure IoT Central.
author: ankitscribbles
ms.author: ankitgup
ms.date: 01/28/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: d8a5ca6285624720e23a4986917ab5e715f6ebfa
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55768006"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central-new-ui-design"></a>Tutorial: Configurar regras e ações para o dispositivo no Azure IoT Central (design da nova interface do usuário)

*Este artigo aplica-se a operadores, construtores e administradores.*

Neste tutorial, você cria uma regra que envia um email quando a temperatura em um dispositivo de ar-condicionado conectado excede 90&deg; F.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma regra baseada em telemetria
> * Adicionar uma ação

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deve concluir o tutorial [Definir um novo tipo de dispositivo em seu aplicativo](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) para criar o modelo do dispositivo de **Ar-condicionado conectado** que será usado.

## <a name="create-a-telemetry-based-rule"></a>Criar uma regra baseada em telemetria

1. Para adicionar uma nova regra baseada em telemetria ao aplicativo, no menu de navegação à esquerda, escolha **Modelos de Dispositivo**:

    ![Página Modelos de Dispositivo](media/tutorial-configure-rules-experimental/templatespage1.png)

    Você verá o modelo de dispositivo **Ar-condicionado Conectado (1.0.0)** criado no tutorial anterior.

2. Para personalizar o modelo de dispositivo, clique no modelo **Ar-condicionado Conectado** criado no tutorial anterior.

3. Para adicionar uma regra baseada em telemetria na exibição **Regras**, escolha **Regras**, clique em **+ Nova Regra** e, em seguida, escolha **Telemetria**:

    ![Modo de exibição de regras](media/tutorial-configure-rules-experimental/newrule.png)

5. Para definir a sua regra, use as informações na tabela a seguir:

    | Configuração                                      | Valor                             |
    | -------------------------------------------- | ------------------------------    |
    | NOME                                         | Alerta de temperatura do ar-condicionado |
    | Habilitar regra para todos os dispositivos deste modelo | Por                                |
    | Condição                                    | A temperatura é maior que 90    |
    | Agregação                                  | Nenhum                              |

    ![Condição de regra de temperatura](media/tutorial-configure-rules-experimental/temperaturerule.png)

    Em seguida, clique em **Salvar**.

## <a name="add-an-action"></a>Adicionar uma ação

Quando você definir uma regra, você também define uma ação a ser executada quando as condições da regra são atendidas. Neste tutorial, você criará uma regra com uma ação que envia uma notificação por email.

1. Para adicionar uma **Ação**, primeiro **Salve** a regra e, em seguida, role a tela para baixo no painel **Configurar Regra de Telemetria**. Escolha o **+** ao lado de **Ações** e, em seguida, escolha **Email**:

    ![Ação de regra de temperatura](media/tutorial-configure-rules-experimental/addaction.png)

2. Para definir a sua ação, use as informações na tabela a seguir:

    | Configuração   | Valor                          |
    | --------- | ------------------------------ |
    | Para        | Seu endereço de email             |
    | Observações     | A temperatura do ar-condicionado excedeu o limite. |

    > [!NOTE]
    > Para receber uma notificação por email, o endereço de email deve ser uma [ID de usuário no aplicativo](howto-administer-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json), e esse usuário deve entrar no aplicativo pelo menos uma vez.

    ![Ação de temperatura](media/tutorial-configure-rules-experimental/temperatureaction.png)

3. Clique em **Salvar**. A regra é listada na página **Regras**.

## <a name="test-the-rule"></a>Teste a regra

Logo depois de salvar a regra, ela passa a valer. Quando as condições definidas na regra são atendidas, o aplicativo envia uma mensagem para o endereço de email especificado na ação.

![Ação email](media/tutorial-configure-rules-experimental/email.png)

> [!NOTE]
> Depois que os testes forem concluídos, desligue a regra para interromper o recebimento de alertas na caixa de entrada.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Criar uma regra baseada em telemetria
> * Adicionar uma ação

Agora que você definiu uma regra baseada em limite, a próxima etapa sugerida é [Personalizar as exibições do operador](tutorial-customize-operator-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

Para saber mais sobre os diferentes tipos de regras no Azure IoT Central e como parametrizar a definição de regra, consulte:
* [Criar uma regra de telemetria e configurar as notificações](howto-create-telemetry-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
* [Criar uma regra de evento e configurar as notificações](howto-create-event-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

<!-- Next tutorials in the sequence -->
