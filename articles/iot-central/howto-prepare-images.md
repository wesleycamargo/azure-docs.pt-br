---
title: Carregar imagens para o aplicativo Azure IoT Central | Microsoft Docs
description: Como um construtor, saiba como preparar e carregar imagens para o aplicativo Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: a20662c2fc9b416fefce89a6ebe706307ee71bb7
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236474"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Preparar e carregar imagens para o aplicativo Azure IoT Central

Este artigo descreve como, um construtor, você pode personalizar o aplicativo Central do  Microsoft IoT Central fazendo o upload de imagens personalizadas. Por exemplo, é possível personalizar um painel do dispositivo com uma imagem do dispositivo.

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas neste artigo, você precisa do seguinte:

1. Um aplicativo Azure IoT Central. Para obter mais informações, consulte o [Criar um início rápido de aplicativo](quick-deploy-iot-central.md).
1. Uma ferramenta para dimensionar e redimensionar arquivos de imagens.

## <a name="choose-where-to-use-custom-images"></a>Escolher onde usar imagens personalizadas

É possível adicionar imagens personalizadas aos seguintes locais em um aplicativo Azure IoT Central:

* O **meus aplicativos** página

    ![Imagem na página do gerenciador de aplicativos](media/howto-prepare-images/applicationmanager.png)

* O painel do aplicativo

    ![Imagem no painel do aplicativo](media/howto-prepare-images/homepage.png)

* Modelo de dispositivo

    ![Imagem no modelo de dispositivo](media/howto-prepare-images/devicetemplate.png)

* Bloco em um painel do dispositivo

    ![Imagem no bloco de dispositivo](media/howto-prepare-images/devicetile.png)

* Um bloco em um painel de conjunto de dispositivos

    ![Imagem no bloco do conjunto de dispositivos](media/howto-prepare-images/devicesettile.png)

## <a name="prepare-the-images"></a>Preparar as imagens

Nos quatro locais, é possível usar imagens PNG, GIF ou JPEG.

A tabela a seguir resume os tamanhos de imagem que podem ser utilizados:

| Local padrão | Tamanhos |
| -------- | ------ |
| Gerenciador de aplicativos | 268x160 px |
| Modelo de dispositivo | 64x64 px |
| Blocos de painel | O bloco de tamanho menor é 200x200 px, os blocos maiores podem ser múltiplos retangulares ou quadrados de blocos pequenos. Por exemplo 200x400 px, 400x200 px ou 400x400 px |

Para a melhor exibição no aplicativo, é necessário criar imagens que correspondam às dimensões mostradas na tabela anterior.

## <a name="upload-the-images"></a>Carregar as imagens

As seções a seguir descrevem como fazer o upload das imagens nos diferentes locais:

### <a name="application-manager"></a>Gerenciador de Aplicativos

Para carregar uma imagem para usar no **meus aplicativos** página, navegue até a **configurações do aplicativo** página o **administração** seção. Você deve ser um administrador para concluir essa tarefa:

![Carregar imagem de aplicativo](media/howto-prepare-images/uploadapplicationmanager.png)

Selecione o **imagem do aplicativo** lado a lado para carregar uma imagem (160 268 px) em seu computador local.

### <a name="application-dashboard"></a>Painel do aplicativo

Para carregar uma imagem no painel do aplicativo, navegue até a **Dashboard** página do seu aplicativo e selecione **editar**. Você deve ser um construtor para concluir essa tarefa:

![Carregar a imagem do painel](media/howto-prepare-images/uploadhomepage.png)

Sob **configurar a imagem**, selecione o **imagem** lado a lado para carregar uma imagem do computador local. O bloco de tamanho menor é 200x200 px, os blocos maiores podem ser múltiplos retangulares ou quadrados de blocos pequenos. Por exemplo, 200 x 400 px, x 200 a 400 px ou 400x400 px.

**Salvar** a imagem carregada. Você pode redimensioná-la enquanto estiver no modo de edição. Selecione **feito** quando terminar.

### <a name="device-template"></a>Modelo de dispositivo

Para fazer o upload de uma imagem em um modelo de dispositivo, navegue até **Modelos de dispositivo** e escolha o modelo. Você deve ser um construtor para concluir essa tarefa:

![Carregar imagem do modelo de dispositivo](media/howto-prepare-images/uploaddevicetemplate.png)

Selecione o bloco de imagem para carregar uma imagem (64 x 64 px) em seu computador local.

### <a name="device-dashboard"></a>Painel do dispositivo

Para fazer o upload de uma imagem em um dashboard de dispositivo, navegue até **Modelos de dispositivo** e escolha o modelo. Depois escolha a guia **Dashboard**. Você deve ser um construtor para concluir essa tarefa:

![Carregar imagem do painel do dispositivo](media/howto-prepare-images/uploaddevicedashboard.png)

Sob **configurar a imagem**, selecione o **imagem** lado a lado e, em seguida, escolha o arquivo a ser carregado em seu computador local. O bloco de tamanho menor é 200x200 px, os blocos maiores podem ser múltiplos retangulares ou quadrados de blocos pequenos. Por exemplo, 200 x 400 px, x 200 a 400 px ou 400x400 px.

**Salvar** a imagem carregada. Você pode redimensionar e reposicionar enquanto estiver no modo de edição. Selecione **feito** quando terminar.

### <a name="device-set-dashboard"></a>Painel do conjunto de dispositivos

Para fazer upload de uma imagem em um painel de conjunto de dispositivos, navegue até **Conjuntos de dispositivos** e escolha o conjunto de dispositivos e, em seguida, um dispositivo. Em seguida, escolha o **Dashboard** página e selecione **editar**:

![Carregar imagem do painel do conjunto de dispositivos](media/howto-prepare-images/uploaddevicesetdashboard.png)

Sob **configurar a imagem**, selecione o **imagem** lado a lado para carregar uma imagem do computador local. O bloco de tamanho menor é 200x200 px, os blocos maiores podem ser múltiplos retangulares ou quadrados de blocos pequenos. Por exemplo, 200 x 400 px, x 200 a 400 px ou 400x400 px.

**Salvar** a imagem carregada. Você pode redimensionar e reposicionar enquanto estiver no modo de edição. Selecione **feito** quando terminar.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a preparar e carregar imagens no seu aplicativo do Azure IoT Central, aqui estão as próximas etapas sugeridas:

* [Personalizar a interface do usuário do Azure IoT Central](./howto-customize-ui.md)
* [Configurar o painel do aplicativo](./howto-configure-homepage.md)
* [Gerenciar dispositivos no aplicativo Azure IoT Central](howto-manage-devices.md)