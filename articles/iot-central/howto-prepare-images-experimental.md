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
ms.openlocfilehash: b975d7dccc85973a42408d87e3c03a91aaf1c450
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812745"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Preparar e carregar imagens para o aplicativo Azure IoT Central

Este artigo descreve como, um construtor, você pode personalizar o aplicativo Central do  Microsoft IoT Central fazendo o upload de imagens personalizadas. Por exemplo, é possível personalizar um painel do dispositivo com uma imagem do dispositivo.

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas neste artigo, você precisa do seguinte:

1. Um aplicativo Azure IoT Central. Para obter mais informações, consulte o [Criar um início rápido de aplicativo](quick-deploy-iot-central-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
1. Uma ferramenta para dimensionar e redimensionar arquivos de imagens.

## <a name="choose-where-to-use-custom-images"></a>Escolher onde usar imagens personalizadas

É possível adicionar imagens personalizadas aos seguintes locais em um aplicativo Azure IoT Central:

* Página **Gerenciador de Aplicativos**

    ![Imagem na página do gerenciador de aplicativos](media/howto-prepare-images-experimental/applicationmanager.png)

* Home page

    ![Imagem na home page](media/howto-prepare-images-experimental/homepage.png)

* Modelo de dispositivo

    ![Imagem no modelo de dispositivo](media/howto-prepare-images-experimental/devicetemplate.png)

* Bloco em um painel do dispositivo

    ![Imagem no bloco de dispositivo](media/howto-prepare-images-experimental/devicetile.png)

* Um bloco em um painel de conjunto de dispositivos

    ![Imagem no bloco do conjunto de dispositivos](media/howto-prepare-images-experimental/devicesettile.png)

## <a name="prepare-the-images"></a>Preparar as imagens

Nos quatro locais, é possível usar imagens PNG, GIF ou JPEG.

A tabela a seguir resume os tamanhos de imagem que podem ser utilizados:

| Local padrão | Tamanhos |
| -------- | ------ |
| Gerenciador de aplicativos | 268x160 px |
| Modelo de dispositivo | 64x64 px |
| Home page e blocos do painel | O bloco de tamanho menor é 200x200 px, os blocos maiores podem ser múltiplos retangulares ou quadrados de blocos pequenos. Por exemplo 200x400 px, 400x200 px ou 400x400 px |

Para a melhor exibição no aplicativo, é necessário criar imagens que correspondam às dimensões mostradas na tabela anterior.

## <a name="upload-the-images"></a>Carregar as imagens

As seções a seguir descrevem como fazer o upload das imagens nos diferentes locais:

### <a name="application-manager"></a>Gerenciador de Aplicativos

Para fazer upload de uma imagem no **Gerenciador de aplicativos**, navegue até a página **Configurações do aplicativo** na seção **Administração**. Você deve ser um administrador para concluir essa tarefa:

![Carregar imagem de aplicativo](media/howto-prepare-images-experimental/uploadapplicationmanager.png)

Clique na peça de Aplicação de Imagem para enviar sua imagem preparada (268x160 px) da sua máquina local.

### <a name="home-page"></a>Página inicial

Para fazer upload de uma imagem na página inicial, navegue até a **página inicial** do seu aplicativo e clique em **Editar**. Você deve ser um construtor para concluir essa tarefa:

![Carregar imagem da home page](media/howto-prepare-images-experimental/uploadhomepage.png)

Em Configurar Imagem, clique na peça da Imagem para carregar sua imagem preparada de sua máquina local. O bloco de tamanho menor é 200x200 px, os blocos maiores podem ser múltiplos retangulares ou quadrados de blocos pequenos. Por exemplo, 200 x 400 px, x 200 a 400 px ou 400x400 px.

**Salvar** a imagem carregada. Você pode redimensioná-la enquanto estiver no modo de edição. Clique em **Concluído** quando terminar. 

### <a name="device-template"></a>Modelo de dispositivo

Para fazer o upload de uma imagem em um modelo de dispositivo, navegue até **Modelos de dispositivo** e escolha o modelo. Você deve ser um construtor para concluir essa tarefa:

![Carregar imagem do modelo de dispositivo](media/howto-prepare-images-experimental/uploaddevicetemplate.png)

Clique na peça da imagem para enviar sua imagem preparada (64x64 px) da sua máquina local. 

### <a name="device-dashboard"></a>Painel do dispositivo

Para fazer o upload de uma imagem em um dashboard de dispositivo, navegue até **Modelos de dispositivo** e escolha o modelo. Depois escolha a guia **Dashboard**. Você deve ser um construtor para concluir essa tarefa:

![Carregar imagem do painel do dispositivo](media/howto-prepare-images-experimental/uploaddevicedashboard.png)

Em Configurar Imagem, clique na peça Imagem e, em seguida, escolha o arquivo a ser carregado em sua máquina local. O bloco de tamanho menor é 200x200 px, os blocos maiores podem ser múltiplos retangulares ou quadrados de blocos pequenos. Por exemplo, 200 x 400 px, x 200 a 400 px ou 400x400 px.

**Salvar** a imagem carregada. Você pode redimensionar e reposicionar enquanto estiver no modo de edição. Clique em **Concluído** quando terminar.

### <a name="device-set-dashboard"></a>Painel do conjunto de dispositivos

Para fazer upload de uma imagem em um painel de conjunto de dispositivos, navegue até **Conjuntos de dispositivos** e escolha o conjunto de dispositivos e, em seguida, um dispositivo. Em seguida, escolha o **Dashboard** da página e clique em **editar**:

![Carregar imagem do painel do conjunto de dispositivos](media/howto-prepare-images-experimental/uploaddevicesetdashboard.png)

Em Configurar Imagem, clique na peça da Imagem para carregar sua imagem preparada de sua máquina local. O bloco de tamanho menor é 200x200 px, os blocos maiores podem ser múltiplos retangulares ou quadrados de blocos pequenos. Por exemplo, 200 x 400 px, x 200 a 400 px ou 400x400 px.

**Salvar** a imagem carregada. Você pode redimensionar e reposicionar enquanto estiver no modo de edição. Clique em **Concluído** quando terminar.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como preparar e carregar imagens para o aplicativo Azure IoT Central, a próxima etapa sugerida é apresentada:

> [!div class="nextstepaction"]
> [Gerenciar dispositivos no aplicativo Azure IoT Central](howto-manage-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)