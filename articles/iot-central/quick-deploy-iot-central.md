---
title: Criar um aplicativo Azure IoT Central | Microsoft Docs
description: Crie um novo aplicativo Azure IoT Central para gerenciar dispositivos refrigerados de venda automática. Exiba os dados de telemetria gerados a partir de seus dispositivos simulados.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/15/2018
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: af06766d89804b2f3d0aaf061494fb836f6ec262
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46465598"
---
# <a name="create-an-azure-iot-central-application"></a>Crie um aplicativo Azure IoT Central

Como um _construtor_, use a interface do usuário do Azure IoT Central para definir seu aplicativo Microsoft Azure IoT Central. Este guia de início rápido mostra como:

- Criar um aplicativo Azure IoT Central que contenha um exemplo de _modelo de dispositivo_ e _dispositivos_ simulados.
- Exibir os recursos do modelo de dispositivo da **Máquina de venda automática refrigerada** em seu aplicativo.
- Exibir a telemetria e análise de seus dispositivos simulados de **Refrigerador**.

Neste guia de início rápido, você exibe um dispositivo **Refrigerador** simulado a partir de um modelo de dispositivo. O dispositivo simulado:

* Envia a telemetria, como temperatura e pressão, para seu aplicativo.
* Informa os valores de propriedade do dispositivo, como um alerta de movimento, para seu aplicativo.
* Possui configurações de dispositivo, como velocidade do ventilador, que podem ser definidas no aplicativo.

Quando você cria um dispositivo simulado a partir de um modelo de dispositivo em um aplicativo Azure IoT Central, o dispositivo simulado permite que você teste seu aplicativo antes de você conectar um dispositivo real.

## <a name="create-the-application"></a>Criar o aplicativo

Para concluir este guia de início rápido, você precisa criar um aplicativo Azure IoT Central a partir do modelo de aplicativo de **exemplo Contoso**.

Navegue até a página do [Gerenciador de aplicativos](https://aka.ms/iotcentral) do Azure IoT Central. Em seguida, digite o endereço de email e a senha que você usa para acessar sua assinatura do Azure:

![Insira a conta da sua organização](media/quick-deploy-iot-central/sign-in.png)

Para começar a criar um novo aplicativo Azure IoT Central, escolha **Novo aplicativo**:

![Página do Gerenciador de aplicativos do Azure IoT Central](media/quick-deploy-iot-central/iotcentralhome.png)

Para criar um novo aplicativo Azure IoT Central:

1. Escolha o plano de pagamento **Avaliação gratuita de aplicativo**.
1. Escolha um nome amigável do aplicativo, como **IoT Contoso**. O Azure IoT Central gera um prefixo de URL exclusivo para você. Você pode alterar esse prefixo de URL para algo mais fácil de lembrar.
1. Escolha o modelo de aplicativo de **exemplo Contoso**.
1. Depois, escolha **Criar**.

![Página Criar aplicativo do Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate.png)

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um aplicativo do Azure IoT Central preenchido previamente que contém um modelo de dispositivo de **Máquina de venda automática refrigerada** e dispositivos simulados. Consulte [Definir um novo modelo de dispositivo em seu aplicativo](tutorial-define-device-type.md) para saber mais, como um construtor, sobre como definir seus próprios modelos de dispositivo.
