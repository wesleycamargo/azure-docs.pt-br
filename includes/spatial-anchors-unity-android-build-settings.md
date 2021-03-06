---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 33c932c36cd6de730d3768d596a214c442d74ae1
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58633017"
---
Abra **Configurações de Build** selecionando **Arquivo** > **Configurações de Build**.

Na seção **Plataforma**, selecione **Android**. Altere o **Sistema de Build** para **Gradle** e selecione **Exportar Projeto**.

Selecione **Mudar Plataforma** para alterar a plataforma para **Android**. O Unity poderá solicitar que você instale componentes de suporte Android, se estiverem faltando.

![Janela Configurações de Build do Unity](./media/spatial-anchors-unity/unity-android-build-settings.png)

Feche a janela **Configurações de Build**.

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Baixar e importar o SDK do ARCore para Unity

Baixe o arquivo `unitypackage` do [SDK do ARCore para versões 1.5 do Unity](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.5.0). De volta no projeto do Unity, selecione **Ativos** > **Importar Pacote** > **Pacote Personalizado...** e, em seguida, selecione o arquivo `unitypackage` baixado anteriormente. Na caixa de diálogo **Importar Pacote do Unity**, verifique se todos os arquivos estão selecionados e, em seguida, selecione **Importar**.
