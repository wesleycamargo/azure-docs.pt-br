---
title: Carregar imagens para o aplicativo Azure IoT Central | Microsoft Docs
description: Como um construtor, saiba como preparar e carregar imagens para o aplicativo Azure IoT Central.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 7fd9c8ed5559b00bc755e3f04c768dceeb487562
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628014"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Preparar e carregar imagens para o aplicativo Azure IoT Central

Este artigo descreve como você, como um construtor, pode personalizar o aplicativo Microsoft Azure IoT Central carregando imagens personalizadas. Por exemplo, é possível personalizar um painel do dispositivo com uma imagem do dispositivo.

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas neste artigo, você precisa do seguinte:

1. Um aplicativo Azure IoT Central. Para obter mais informações, consulte [Criar o aplicativo Azure IoT Central](howto-create-application.md).
1. Uma ferramenta para dimensionar e redimensionar arquivos de imagens.

## <a name="choose-where-to-use-custom-images"></a>Escolher onde usar imagens personalizadas

É possível adicionar imagens personalizadas aos seguintes locais em um aplicativo Azure IoT Central:

* Página **Gerenciador de Aplicativos**

    ![Imagem na página do gerenciador de aplicativos](media/howto-prepare-images/applicationmanager.png)

* Home page

    ![Imagem na home page](media/howto-prepare-images/homepage.png)

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
| **Gerenciador de Aplicativos** | 268x160 px |
| Modelo de dispositivo | 64x64 px |
| Home page e blocos do painel | O bloco de tamanho menor é 200x200 px, os blocos maiores podem ser múltiplos retangulares ou quadrados de blocos pequenos. Por exemplo 200x400 px, 400x200 px ou 400x400 px |

Para a melhor exibição no aplicativo, é necessário criar imagens que correspondam às dimensões mostradas na tabela anterior.

## <a name="upload-the-images"></a>Carregar as imagens

As seções a seguir descrevem como carregar as imagens para uso nos diferentes locais:

### <a name="application-manager"></a>Gerenciador de Aplicativos

Para carregar uma imagem para utilizar no **Gerenciador de Aplicativos**, navegue até a página **Configurações de Aplicativo** na seção **Administração**. Você deve ser um administrador para concluir essa tarefa:

![Carregar imagem de aplicativo](media/howto-prepare-images/uploadapplicationmanager.png)

Clique na imagem de upload e, em seguida, escolha o arquivo a ser carregado do computador local.

### <a name="home-page"></a>Página inicial

Para carregar uma imagem e utilizar na home page, navegue até a **Home page** do aplicativo e ative o modo de design. Você deve ser um construtor para concluir essa tarefa:

![Carregar imagem da home page](media/howto-prepare-images/uploadhomepage.png)

Clique na imagem de upload e, em seguida, escolha o arquivo a ser carregado do computador local.

Depois que a imagem for carregada, você poderá redimensioná-la enquanto o modo de design estiver ativado.

### <a name="device-template"></a>Modelo de dispositivo

Para carregar uma imagem para utilizar em um modelo de dispositivo, navegue até **Device Explorer**, escolha o modelo de dispositivo e, em seguida, um dispositivo e ative o modo de design. Você deve ser um construtor para concluir essa tarefa:

![Carregar imagem do modelo de dispositivo](media/howto-prepare-images/uploaddevicetemplate.png)

Clique na imagem de upload e, em seguida, escolha o arquivo a ser carregado do computador local.

### <a name="device-dashboard"></a>Painel do dispositivo

Para carregar uma imagem para utilizar em um painel do dispositivo, navegue até **Device Explorer**, escolha o modelo de dispositivo e, em seguida, um dispositivo. Em seguida, escolha a página **Painel** e ative o modo de design. Você deve ser um construtor para concluir essa tarefa:

![Carregar imagem do painel do dispositivo](media/howto-prepare-images/uploaddevicedashboard.png)

Clique na imagem de upload e, em seguida, escolha o arquivo a ser carregado do computador local.

Depois que a imagem for carregada, você poderá redimensioná-la e reposicioná-la enquanto o **Modo de Design** estiver ativado.

### <a name="device-set-dashboard"></a>Painel do conjunto de dispositivos

Para carregar uma imagem para utilizar em um painel de conjunto de dispositivos, navegue até **Conjuntos de Dispositivos**, escolha o conjunto de dispositivos e, em seguida, um dispositivo. Em seguida, escolha a página **Painel** e ative o **Modo de Design Mode**:

![Carregar imagem do painel do conjunto de dispositivos](media/howto-prepare-images/uploaddevicesetdashboard.png)

Clique na imagem de upload e, em seguida, escolha o arquivo a ser carregado do computador local.

Depois que a imagem for carregada, você poderá redimensioná-la e reposicioná-la enquanto o modo de design estiver ativado.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como preparar e carregar imagens para o aplicativo Azure IoT Central, a próxima etapa sugerida é apresentada:

> [!div class="nextstepaction"]
> [Gerenciar dispositivos no aplicativo Azure IoT Central](howto-manage-devices.md)