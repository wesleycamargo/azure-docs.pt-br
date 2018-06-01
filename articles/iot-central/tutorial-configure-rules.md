---
title: Configurar regras e ações no Azure IoT Central | Microsoft Docs
description: Este tutorial mostra a você, como um construtor, como configurar regras e ações baseadas em telemetria em seu aplicativo do Azure IoT Central.
services: iot-central
author: ankitgupta
ms.author: ankitgup
ms.date: 04/16/2018
ms.topic: tutorial
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 5ac19e0e25ea3e25ede4d87776c01f8bcaea4655
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202230"
---
# <a name="2---configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>2 - Configurar regras e ações para o seu dispositivo no Azure IoT Central

Este tutorial mostra a você, como um construtor, como configurar regras e ações baseadas em telemetria em seu aplicativo do Microsoft Azure IoT Central.

Neste tutorial, você cria uma regra que envia um email quando a temperatura em um dispositivo de ar-condicionado conectado excede 90&deg; F.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma regra baseada em telemetria
> * Adicionar uma ação

## <a name="prerequisites"></a>pré-requisitos

Antes de começar, você deve concluir o tutorial [Definir um novo tipo de dispositivo em seu aplicativo](tutorial-define-device-type.md) para criar o modelo do dispositivo de **Ar-condicionado conectado** que será usado.

## <a name="create-a-telemetry-based-rule"></a>Criar uma regra baseada em telemetria

1. Para adicionar uma nova regra baseada em telemetria para o seu aplicativo, no menu de navegação à esquerda, escolha **Device Explorer**:

    ![Página do Device Explorer](media/tutorial-configure-rules/explorerpage.png)

    Você verá o modelo do dispositivo do **Ar-condicionado conectado (1.0.0)** e o dispositivo de **Ar-condicionado conectado-1** que você criou no tutorial anterior.

1. Para começar a personalizar o seu dispositivo de ar-condicionado conectado, escolha o dispositivo que você criou no tutorial anterior:

    ![Página de ar-condicionado conectado](media/tutorial-configure-rules/builderdevicelist.png)

1. Para adicionar uma regra no modo de exibição de **Regras**, escolha **Regras**:

    ![Modo de exibição de regras](media/tutorial-configure-rules/builderrulesview.png)

1. Neste tutorial, você adiciona uma regra de telemetria baseada em limites. Para começar a criar uma regra baseada em limite, escolha **Nova regra**, em seguida, **Telemetria**.

1. Para definir a sua regra, use as informações na tabela a seguir:

    | Configuração     | Valor                          |
    | ----------- | ------------------------------ |
    | NOME        | Temperatura do ar-condicionado    |
    | Habilitar regra | Por                             |
    | Condição   | A temperatura é maior que 90 |

    ![Condição de regra de temperatura](media/tutorial-configure-rules/buildertemperaturerule.png)

## <a name="add-an-action"></a>Adicionar uma ação

Quando você definir uma regra, você também define uma ação a ser executada quando as condições da regra são atendidas. Neste tutorial, você deve adiciona uma ação para enviar um email como uma notificação disparada pela regra.

1. Para adicionar uma **Ação**, role para baixo no painel **Configurar regra de telemetria** e escolha o **+** ao lado de **Ações**, em seguida, escolha **Email**:

    ![Ação de regra de temperatura](media/tutorial-configure-rules/builderaddaction.png)

1. Para definir a sua ação, use as informações na tabela a seguir:

    | Configuração   | Valor                          |
    | --------- | ------------------------------ |
    | Para        | Seu endereço de email             |
    | Observações     | A temperatura no ar-condicionado excedeu o limite. |

    > [!NOTE]
    > Para receber uma notificação por email, o endereço de email deve ser uma [ID de usuário no aplicativo](howto-administer.md), e esse usuário deve entrar no aplicativo pelo menos uma vez.

    ![Ação de temperatura do construtor de aplicativo](media/tutorial-configure-rules/buildertemperatureaction.png)

1. Escolha **Salvar**. A regra é listada na página **Regras**:

    ![Regras do Construtor de aplicativo](media/tutorial-configure-rules/builderrules.png)

## <a name="test-the-rule"></a>Teste a regra

Logo depois de salvar a regra, ela passa a valer. Quando as condições definidas na regra são atendidas, o aplicativo envia uma mensagem para o endereço de email especificado na ação.

![Ação email](media/tutorial-configure-rules/email.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Criar uma regra baseada em telemetria
> * Adicionar uma ação

Agora que você definiu uma regra baseada em limite a próxima etapa sugerida é [Personalizar os modos de exibição do operador](tutorial-customize-operator.md).

Para saber mais sobre os diferentes tipos de regras no Azure IoT Central e como parametrizar a definição de regra, consulte:
* [Criar uma regra de telemetria e configurar as notificações](howto-create-telemetry-rules.md).
* [Criar uma regra de evento e configurar as notificações](howto-create-event-rules.md).

<!-- Next tutorials in the sequence -->