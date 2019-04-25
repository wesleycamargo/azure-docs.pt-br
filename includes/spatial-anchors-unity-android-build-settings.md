---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 228f445dda2724985154723a292adb8215a5ad68
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60012108"
---
Abra **Configurações de Build** selecionando **Arquivo** > **Configurações de Build**.

Na seção **Plataforma**, selecione **Android**. Altere o **Sistema de Build** para **Gradle** e selecione **Exportar Projeto**.

Selecione **Mudar Plataforma** para alterar a plataforma para **Android**. O Unity poderá solicitar que você instale componentes de suporte Android, se estiverem faltando.

![Janela Configurações de Build do Unity](./media/spatial-anchors-unity/unity-android-build-settings.png)

Feche a janela **Configurações de Build**.

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Baixar e importar o SDK do ARCore para Unity

Baixe o arquivo `unitypackage` do [SDK do ARCore para versões 1.7 do Unity](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.7.0). De volta no projeto do Unity, selecione **Ativos** > **Importar Pacote** > **Pacote Personalizado...** e, em seguida, selecione o arquivo `unitypackage` baixado anteriormente. Na caixa de diálogo **Importar Pacote do Unity**, verifique se todos os arquivos estão selecionados e, em seguida, selecione **Importar**.
