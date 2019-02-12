---
title: Monitorar seus dispositivos no Azure IoT Central | Microsoft Docs
description: Como um operador, use o seu aplicativo Azure IoT Central para monitorar seus dispositivos.
author: dominicbetts
ms.author: dobett
ms.date: 02/01/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 9480565643887b5a9a4d644ba3173b365eaea29c
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55767766"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices"></a>Tutorial: Use o Azure IoT Central para monitorar seus dispositivos

Este tutorial mostra a você, como um operador, como usar o seu aplicatio Azure IoT Central para monitorar seus dispositivos e alterar as configurações.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Receber uma notificação
> * Investigar um problema
> * Resolver um problema

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, o construtor deve concluir os três tutorial para construtores para criar o aplicativo Azure IoT Central:

* [Definir um novo tipo de dispositivo](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Configurar regras e ações para o seu dispositivo](tutorial-configure-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Personalizar os modos de exibição do operador](tutorial-customize-operator-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)

## <a name="receive-a-notification"></a>Receber uma notificação

O Azure IoT Central envia notificações sobre dispositivos como mensagens de email. O construtor adicionou uma regra para enviar uma notificação quando a temperatura em um ar-condicionado conectado exceder um limite. Verifique os emails enviados para a conta escolhida pelo construtor para receber notificações.

Abra a mensagem de email recebida no final do tutorial [Configurar regras e ações para o seu dispositivo](tutorial-configure-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json). No email, escolha **Clique aqui para abrir o seu dispositivo**:

![Email de notificação de alerta](media/tutorial-monitor-devices-experimental/email.png)

A página **Dispositivo** para o dispositivo simulado **Ar-condicionado conectado 1** que você criou nos tutoriais anteriores abre no navegador:

![Dispositivo que disparou a mensagem de email com a notificação](media/tutorial-monitor-devices-experimental/sourcedevice.png)

## <a name="investigate-an-issue"></a>Investigar um problema

Como um operador, você pode ver informações sobre as páginas de **Medidas**, **Configurações**, **Propriedades**, **Regras** e **Painel**. O construtor personalizou o **Painel** para exibir informações importantes sobre um dispositivo de ar-condicionado conectado.

Escolha o modo de exibição de **Painel** para ver informações sobre o dispositivo.

![Painel do dispositivo](media/tutorial-monitor-devices-experimental/initial_screen.png)

O gráfico no painel mostra um gráfico de temperatura do dispositivo. Você também pode ver a temperatura de destino atual para o dispositivo no bloco **Definir temperatura de destino**. Você decide se a temperatura de destino está muito alta.

## <a name="remediate-an-issue"></a>Resolver um problema

Para alterar a temperatura de destino do dispositivo, use a página **Configurações**:

1. Escolha **Configurações**. Altere a **Configuração de temperatura** para 75. Escolha **Atualizar** para enviar a nova temperatura de destino par ao dispositivo. Quando o dispositivo confirma as alterações de configuração, o status da configuração muda para **sincronizado**:

    ![Atualizar configurações](media/tutorial-monitor-devices-experimental/change_settings.png)

2. Escolha **Painel** e verifique o novo valor configurado:

    ![Painel do dispositivo atualizado](media/tutorial-monitor-devices-experimental/new_settings.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="nextstepaction"]
> * Receber uma notificação
> * Investigar um problema
> * Resolver um problema

Agora que você sabe como monitorar o seu dispositivo, a próxima etapa sugerida é [Adicionar um dispositivo](tutorial-add-device-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).