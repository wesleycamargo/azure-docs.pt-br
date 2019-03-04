---
title: Início Rápido – Criar um aplicativo do Unity para Android com as Âncoras Espaciais do Azure | Microsoft Docs
description: Neste Início Rápido, você aprenderá a criar um aplicativo do Android com o Unity usando as Âncoras Espaciais.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: e92c812ffc8b72fe79248c602e48ff01ef9fefcb
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960999"
---
# <a name="quickstart-create-an-android-unity-app-with-azure-spatial-anchors"></a>Início Rápido: Criar um aplicativo do Unity para Android com as Âncoras Espaciais do Azure

Este Início Rápido aborda como criar um aplicativo do Unity para Android usando as [Âncoras Espaciais do Azure](../overview.md). As Âncoras Espaciais do Azure são um serviço de desenvolvedor multiplataforma para você criar experiências de realidade misturada usando objetos que persistem sua localização em todos os dispositivos ao longo do tempo. Quando terminar, você terá um aplicativo do ARCore Android criado com o Unity que pode salvar e fazer recall de uma âncora espacial.

Você aprenderá a:

> [!div class="checklist"]
> * Criar uma conta de Âncoras Espaciais
> * Preparar as configurações de build do Unity
> * Baixar e importar o SDK do ARCore para Unity
> * Configurar o identificador e a chave de conta das Âncoras Espaciais
> * Exportar o projeto do Android Studio
> * Implantar e executar um dispositivo Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que:

- Um computador Windows ou macOS com o <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3 ou posterior</a> e o <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3 ou posterior</a> instalados.
- Um dispositivo Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">habilitado para desenvolvedor</a> e <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">compatível com ARCore</a>.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Abrir o projeto de exemplo no Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Configurar a chave e o identificador da conta

No painel **Projeto**, navegue para `Assets/AzureSpatialAnchorsPlugin/Examples` e abra o arquivo de cena `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Salve a cena selecionando **Arquivo** -> **Salvar**.

## <a name="export-the-android-studio-project"></a>Exportar o projeto do Android Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Selecione **Exportar** para abrir uma caixa de diálogo. Em seguida, selecione uma pasta para exportar o projeto do Android Studio.

Quando a exportação for concluída, uma pasta será exibida contendo o projeto do Android Studio exportado, com uma subpasta chamada **HelloAR U3D**.

## <a name="deploy-the-android-application"></a>Implantar o aplicativo do Android

Abra o Android Studio e selecione **Abrir um projeto existente do Android Studio**. Em seguida, selecione a subpasta **HelloAR U3D** no projeto do Android Studio exportado e clique em **OK**.

Após a abertura, será exibido um prompt perguntando se você deseja usar o wrapper Gradle. Selecione **OK** para usar o wrapper Gradle e abrir o projeto.

Ligue o dispositivo Android, entre nele e conecte-o ao computador usando um cabo USB.

Selecione **Executar** na barra de ferramentas do Android Studio.

![Botão Implantar e Executar do Android Studio](./media/get-started-unity-android/android-studio-deploy-run.png)

Selecione o dispositivo Android na caixa de diálogo **Selecionar o Destino de Implantação** e selecione **OK** para executar o aplicativo no dispositivo Android.

Siga as instruções no aplicativo para colocar uma âncora e fazer recall dela.

> [!NOTE]
> Ao executar o aplicativo, se a câmera não for exibida como a tela de fundo (por exemplo, é exibida uma tela em branco, azul ou outras texturas), você provavelmente precisará reimportar os ativos no Unity. Interrompa o aplicativo. No menu superior do Unity, escolha **Ativos -> Reimportar todos**. Em seguida, execute o aplicativo novamente.

Interrompa o aplicativo, selecionando **Parar** na barra de ferramentas do Android Studio.

![Botão Parar do Android Studio](./media/get-started-unity-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Compartilhar as Âncoras Espaciais nos dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
