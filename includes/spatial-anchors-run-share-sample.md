---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 5f1e0153b1f919bc9d7e921d2a1b3ae745b2b01f
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753432"
---
## <a name="open-the-sample-project-in-unity"></a>Abrir o projeto de exemplo no Unity

[!INCLUDE [Clone Sample Repo](spatial-anchors-clone-sample-repository.md)]

## <a name="to-set-up-for-an-android-device"></a>Para configurar para um dispositivo Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

## <a name="to-set-up-for-an-ios-device"></a>Para configurar para um dispositivo iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Configurar a chave e o identificador da conta

No painel **Projeto**, navegue para `Assets/AzureSpatialAnchorsPlugin/Examples` e abra o arquivo de cena `AzureSpatialAnchorsLocalSharedDemo.unity`.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

Além disso, no painel **Inspector**, insira o `Sharing Anchors Service url` (de sua implantação do Azure do aplicativo Web ASP.NET) como o valor para `Base Sharing Url`, substituindo `index.html` por `api/anchors`. Dessa forma, o resultado deve ser assim: `https://<app_name>.azurewebsites.net/api/anchors`.

Salve a cena selecionando **Arquivo** -> **Salvar**.

## <a name="to-deploy-to-an-android-device"></a>Para implantar em um dispositivo Android

Ligue o dispositivo Android, entre nele e conecte-o ao computador usando um cabo USB.

Abra **Configurações de Build** selecionando **Arquivo** -> **Configurações de Build**.

Em **Cenas em Build**, coloque uma marca de seleção ao lado da cena `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` e remova as marcas de seleção de todas as outras cenas.

Verifique se a caixa de seleção **Exportar Projeto** não tem uma marca de seleção. Clique em **Compilar e Executar**. Você será solicitado a salvar seu arquivo `.apk`; você pode escolher qualquer nome para ele.

Siga as instruções no aplicativo. Você pode optar por **Criar & Compartilhar Âncora** ou **Localizar Âncora Compartilhada**. A primeira opção permite que você crie uma Âncora que posteriormente pode ser localizada no mesmo dispositivo ou em um diferente. A segunda opção, se anteriormente você tiver executado o aplicativo, no mesmo dispositivo ou um diferente, permitirá localizar âncoras compartilhadas anteriormente.

## <a name="to-deploy-to-an-ios-device"></a>Para implantar em um dispositivo iOS

Abra **Configurações de Build** selecionando **Arquivo** -> **Configurações de Build**.

Em **Cenas em Build**, coloque uma marca de seleção ao lado da cena `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` e remova as marcas de seleção de todas as outras cenas.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Siga as instruções no aplicativo. Você pode optar por **Criar & Compartilhar Âncora** ou **Localizar Âncora Compartilhada**. A primeira opção permite que você crie uma Âncora que posteriormente pode ser localizada no mesmo dispositivo ou em um diferente. A segunda opção, se anteriormente você tiver executado o aplicativo, no mesmo dispositivo ou um diferente, permitirá localizar âncoras compartilhadas anteriormente.

No Xcode, interrompa o aplicativo pressionando **Parar**.
