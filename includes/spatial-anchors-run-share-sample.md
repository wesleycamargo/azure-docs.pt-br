---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 397a8a9b07b4d7a88d0345399ac4abcc3e738a82
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631169"
---
## <a name="set-up-your-device"></a>Configurar seu dispositivo

No Unity, abra o projeto na pasta `Unity`.

![Janela do Unity](./media/spatial-anchors-unity/unity-window.png)

### <a name="set-up-an-android-device"></a>Configurar um dispositivo Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Configurar um dispositivo iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Configurar o identificador e a chave da conta

No painel **Projeto**, navegue para `Assets/AzureSpatialAnchorsPlugin/Examples` e abra o arquivo de cena `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

Além disso, no painel **Inspetor**, insira o `Sharing Anchors Service url` (de sua implantação do Azure do aplicativo Web ASP.NET) como o valor para `Base Sharing Url`, substituindo `index.html` por `api/anchors`. O resultado deve ser assim: `https://<app_name>.azurewebsites.net/api/anchors`.

Salve a cena selecionando **Arquivo** > **Salvar**.

## <a name="deploy-to-your-device"></a>Implantar no seu dispositivo

### <a name="deploy-to-android-device"></a>Implantar em um dispositivo Android

Entre em seu dispositivo Android e conecte-o em seu computador usando um cabo USB.

Abra **Configurações de Build** selecionando **Arquivo** > **Configurações de Build**.

Em **Cenas em Build**, coloque uma marca de seleção ao lado da cena `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` e remova as marcas de seleção de todas as outras cenas.

Certifique-se de que **Exportar projeto** não tem uma marca de seleção. Selecione **Compilar e Executar**. Você será solicitado a salvar seu `.apk` arquivo. Você pode escolher qualquer nome para ele.

Siga as instruções no aplicativo. Você pode optar por **Criar e Compartilhar Âncora** ou **Localizar Âncora Compartilhada**. O primeiro cenário permite que você crie uma âncora que posteriormente pode ser localizada no mesmo dispositivo ou em um diferente. O segundo cenário, se você já tiver executado o aplicativo, seja no mesmo dispositivo ou em um dispositivo diferente, permitirá localizar âncoras compartilhadas anteriormente. Depois de escolher seu cenário, o aplicativo o guiará com mais instruções sobre o que fazer. Por exemplo, você será solicitado a mover seu dispositivo para coletar informações sobre o ambiente. No futuro, você vai colocar uma âncora no mundo, aguardará enquanto ela carrega e assim por diante.

### <a name="deploy-to-an-ios-device"></a>Implantar em um dispositivo iOS

Abra **Configurações de Build** selecionando **Arquivo** > **Configurações de Build**.

Em **Cenas em Build**, coloque uma marca de seleção ao lado da cena `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` e remova as marcas de seleção de todas as outras cenas.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Siga as instruções no aplicativo. Você pode optar por **Criar e Compartilhar Âncora** ou **Localizar Âncora Compartilhada**. O primeiro cenário permite que você crie uma âncora que posteriormente pode ser localizada no mesmo dispositivo ou em um diferente. O segundo cenário, se você já tiver executado o aplicativo, seja no mesmo dispositivo ou em um dispositivo diferente, permitirá localizar âncoras compartilhadas anteriormente. Depois de escolher seu cenário, o aplicativo o guiará com mais instruções sobre o que fazer. Por exemplo, você será solicitado a mover seu dispositivo para coletar informações sobre o ambiente. No futuro, você vai colocar uma âncora no mundo, aguardará enquanto ela carrega e assim por diante.

No Xcode, interrompa o aplicativo selecionando **Parar**.
