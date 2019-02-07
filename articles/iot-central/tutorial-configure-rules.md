---
title: Configurar regras e ações no Azure IoT Central | Microsoft Docs
description: Este tutorial mostra a você, como um construtor, como configurar regras e ações baseadas em telemetria em seu aplicativo do Azure IoT Central.
author: ankitscribbles
ms.author: ankitgup
ms.date: 10/12/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: d7269f61579ce1ffd9a686634effd153837a2f25
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55662969"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Tutorial: Configurar regras e ações para o seu dispositivo no Azure IoT Central

*Este artigo aplica-se a operadores, construtores e administradores.*

Neste tutorial, você cria uma regra que envia um email quando a temperatura em um dispositivo de ar-condicionado conectado excede 90&deg; F.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma regra baseada em telemetria
> * Adicionar uma ação

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deve concluir o tutorial [Definir um novo tipo de dispositivo em seu aplicativo](tutorial-define-device-type.md) para criar o modelo do dispositivo de **Ar-condicionado conectado** que será usado.

## <a name="create-a-telemetry-based-rule"></a>Criar uma regra baseada em telemetria

1. Para adicionar uma nova regra baseada em telemetria para o seu aplicativo, no menu de navegação à esquerda, escolha **Device Explorer**:

    ![Página do Device Explorer](media/tutorial-configure-rules/explorerpage1.png)

    Você verá o modelo do dispositivo do **Ar-condicionado conectado (1.0.0)** e o dispositivo de **Ar-condicionado conectado-1** que você criou no tutorial anterior.

2. Para começar a personalizar o seu dispositivo de ar-condicionado conectado, escolha o dispositivo que você criou no tutorial anterior:

    ![Página de ar-condicionado conectado](media/tutorial-configure-rules/builderdevicelist1.png)

3. Para começar a adicionar uma regra no modo de exibição **Regras**, escolha **Regras** e clique em **Editar modelo**:

    ![Modo de exibição de regras](media/tutorial-configure-rules/builderedittemplate.png)

4. Para criar uma regra de telemetria baseada em limite, clique em **Nova Regra** e, em seguida, **Telemetria**.

    ![Editar modelo](media/tutorial-configure-rules/buildernewrule.png)

5. Para definir a sua regra, use as informações na tabela a seguir:

    | Configuração                                      | Valor                             |
    | -------------------------------------------- | ------------------------------    |
    | NOME                                         | Alerta de temperatura do ar-condicionado |
    | Habilitar regra para todos os dispositivos deste modelo | Por                                |
    | Habilitar regra neste dispositivo                   | Por                                |
    | Condição                                    | A temperatura é maior que 90    |
    | Agregação                                  | Nenhum                              |

    ![Condição de regra de temperatura](media/tutorial-configure-rules/buildertemperaturerule1.png)

## <a name="add-an-action"></a>Adicionar uma ação

Quando você definir uma regra, você também define uma ação a ser executada quando as condições da regra são atendidas. Neste tutorial, você deve adiciona uma ação para enviar um email como uma notificação disparada pela regra.

1. Para adicionar uma **Ação**, primeiro **Salve** a regra e, em seguida, role para baixo até o painel **Configurar regra de telemetria** e escolha o **+** ao lado de **Ações** e, em seguida, escolha **Email**:

    ![Ação de regra de temperatura](media/tutorial-configure-rules/builderaddaction1.png)

2. Para definir a sua ação, use as informações na tabela a seguir:

    | Configuração   | Valor                          |
    | --------- | ------------------------------ |
    | Para        | Seu endereço de email             |
    | Observações     | A temperatura do ar-condicionado excedeu o limite. |

    > [!NOTE]
    > Para receber uma notificação por email, o endereço de email deve ser uma [ID de usuário no aplicativo](howto-administer.md), e esse usuário deve entrar no aplicativo pelo menos uma vez.

    ![Ação de temperatura do construtor de aplicativo](media/tutorial-configure-rules/buildertemperatureaction.png)

3. Escolha **Salvar**. A regra é listada na página **Regras**:

    ![Regras do Construtor de aplicativo](media/tutorial-configure-rules/builderrules1.png)

4. Escolha **Concluído** para sair do modo **Editar Modelo**.
 

## <a name="test-the-rule"></a>Teste a regra

Logo depois de salvar a regra, ela passa a valer. Quando as condições definidas na regra são atendidas, o aplicativo envia uma mensagem para o endereço de email especificado na ação.

![Ação email](media/tutorial-configure-rules/email.png)

> [!NOTE]
> Depois que os testes forem concluídos, desative a regra para interromper o recebimento de alertas na Caixa de Entrada. 

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
