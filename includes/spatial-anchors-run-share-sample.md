---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 563c2bd561328561d30acee6910b70d53ef64c6b
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305138"
---
## <a name="set-up-your-device"></a>Configurar seu dispositivo

### <a name="set-up-an-android-device"></a>Configurar um dispositivo Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Configurar um dispositivo iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Configurar o identificador e a chave da conta

No painel **Projeto**, navegue para `Assets/AzureSpatialAnchorsPlugin/Examples` e abra o arquivo de cena `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

Além disso, no painel **Inspetor**, insira o `Sharing Anchors Service url` (de sua implantação do Azure do aplicativo Web ASP.NET) como o valor para `Base Sharing Url`, substituindo `index.html` por `api/anchors`. O resultado deve ser assim: `https://<app_name>.azurewebsites.net/api/anchors`.

Salve a cena selecionando **Arquivo** > **Salvar**.

## <a name="to-deploy-the-app-to-an-android-device"></a>Para implantar o aplicativo em um dispositivo Android

Entre em seu dispositivo Android e conecte-o em seu computador usando um cabo USB.

Abra **Configurações de Build** selecionando **Arquivo** > **Configurações de Build**.

Em **Cenas em Build**, coloque uma marca de seleção ao lado da cena `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` e remova as marcas de seleção de todas as outras cenas.

Certifique-se de que **Exportar projeto** não tem uma marca de seleção. Selecione **Compilar e Executar**. Você será solicitado a salvar seu `.apk` arquivo. Você pode escolher qualquer nome para ele.

Siga as instruções no aplicativo. Você pode optar por **Criar e Compartilhar Âncora** ou **Localizar Âncora Compartilhada**. A primeira opção permite que você crie uma âncora que posteriormente pode ser localizada no mesmo dispositivo ou em um diferente. A segunda opção, se anteriormente você tiver executado o aplicativo, no mesmo dispositivo ou um diferente, permitirá localizar âncoras compartilhadas anteriormente.

## <a name="to-deploy-the-app-to-an-ios-device"></a>Implantar o aplicativo em um dispositivo iOS

Abra **Configurações de Build** selecionando **Arquivo** > **Configurações de Build**.

Em **Cenas em Build**, coloque uma marca de seleção ao lado da cena `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` e remova as marcas de seleção de todas as outras cenas.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Siga as instruções no aplicativo. Você pode optar por **Criar e Compartilhar Âncora** ou **Localizar Âncora Compartilhada**. A primeira opção permite que você crie uma âncora que posteriormente pode ser localizada no mesmo dispositivo ou em um diferente. A segunda opção, se anteriormente você tiver executado o aplicativo, no mesmo dispositivo ou um diferente, permitirá localizar âncoras compartilhadas anteriormente.

No Xcode, interrompa o aplicativo selecionando **Parar**.
