---
title: Início Rápido – Criar um aplicativo para Android com as Âncoras Espaciais do Azure | Microsoft Docs
description: Neste Início Rápido, você aprenderá a criar um aplicativo para Android usando as Âncoras Espaciais.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 64e5e78f93488b2c375c617e8857c84ba2171f00
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56822388"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>Início Rápido: Criar um aplicativo para Android com as Âncoras Espaciais do Azure

Este início rápido aborda como criar um aplicativo Android usando [Âncoras Espaciais do Azure](../overview.md) em Java ou C++/NDK. As Âncoras Espaciais do Azure são um serviço de desenvolvedor multiplataforma para você criar experiências de realidade misturada usando objetos que persistem sua localização em todos os dispositivos ao longo do tempo. Quando tiver terminado, você terá um aplicativo do ARCore para Android que pode salvar e lembrar de uma âncora espacial.

Você aprenderá a:

> [!div class="checklist"]
> * Criar uma conta de Âncoras Espaciais
> * Configurar o identificador e a chave de conta das Âncoras Espaciais
> * Implantar e executar um dispositivo Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que:

- Um computador Windows ou macOS com <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3+</a>.
  - Para compilar o exemplo NDK, você também precisará instalar o NDK e o CMake 3.6 SDK Tools no Android Studio.
- Um dispositivo Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">habilitado para desenvolvedor</a> e <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">compatível com ARCore</a>.
- Seu aplicativo deve ter como destino 1.5 ARCore (suporte para ARCore 1.6+ estará disponível em breve)

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Abrir o projeto de exemplo

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Se você estiver criando a amostra do NDK para Android, precisará baixar `arcore_c_api.h` [daqui](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.5.0/libraries/include/arcore_c_api.h) e colocá-lo em `Android\NDK\libraries\include`.

Abra o Android Studio.

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Selecione **Abrir um projeto existente do Android Studio** e selecione o projeto localizado em `Android/Java/`.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Selecione **Abrir um projeto existente do Android Studio** e selecione o projeto localizado em `Android/NDK/`.

***

## <a name="configure-account-identifier-and-key"></a>Configurar a chave e o identificador da conta

A próxima etapa é usar o identificador de conta e a chave de conta registrados anteriormente ao configurar o recurso Âncoras Espaciais para configurar o aplicativo.

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Abra `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsActivity.java`.

Localize o campo `SpatialAnchorsAccountKey` e substitua `Set me` pela chave de conta.

Localize o campo `SpatialAnchorsAccountId` e substitua `Set me` pelo identificador de conta.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Abra `Android/NDK/app/src/main/cpp/spatial_services_application.cc`.

Localize o campo `SpatialAnchorsAccountKey` e substitua `Set me` pela chave de conta.

Localize o campo `SpatialAnchorsAccountId` e substitua `Set me` pelo identificador de conta.

***

## <a name="deploy-the-app-to-your-android-device"></a>Implantar o aplicativo em seu dispositivo Android

Ligue o dispositivo Android, entre nele e conecte-o ao computador usando um cabo USB.

Selecione **Executar** na barra de ferramentas do Android Studio.

![Botão Implantar e Executar do Android Studio](./media/get-started-android/android-studio-deploy-run.png)

Selecione o dispositivo Android na caixa de diálogo **Selecionar o Destino de Implantação** e selecione **OK** para executar o aplicativo no dispositivo Android.

Siga as instruções no aplicativo para colocar uma âncora e fazer recall dela.

Interrompa o aplicativo, selecionando **Parar** na barra de ferramentas do Android Studio.

![Botão Parar do Android Studio](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Compartilhar as Âncoras Espaciais nos dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
