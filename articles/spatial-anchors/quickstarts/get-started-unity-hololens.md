---
title: Início rápido - Criar um aplicativo Unity do HoloLens com Âncoras Espaciais do Azure | Microsoft Docs
description: Neste Início Rápido, você aprenderá a criar um aplicativo HoloLens com Unity usando as Âncoras Espaciais.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 2c29d9860f1c8fc4f0f6d9f4d84c06e8ade8dee5
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286968"
---
# <a name="quickstart-create-a-hololens-unity-app-that-uses-azure-spatial-anchors"></a>Início Rápido: Criar um aplicativo Unity do HoloLens que usa Âncoras Espaciais do Azure

Neste início rápido, você criará um aplicativo Unity do HoloLens que usa [Âncoras Espaciais do Azure](../overview.md). Âncoras Espaciais são um serviço de desenvolvedor de plataforma cruzada que permite criar experiências de realidade misturada com objetos que persistem sua localização em todos os dispositivos ao longo do tempo. Quando terminar, você terá um aplicativo HoloLens criado com o Unity que pode salvar e fazer recall de uma âncora espacial.

Você aprenderá a:

- Criar uma conta de Âncoras Espaciais.
- Preparar as configurações de criação do Unity.
- Configurar a chave de conta e o identificador da conta das Âncoras Espaciais.
- Exportar o projeto do Visual Studio do HoloLens.
- Implantar o aplicativo e executá-lo em um dispositivo HoloLens.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:


- Você precisará de um computador Windows com o <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3</a>, ou posterior, e o <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a>, ou posterior, instalados. A instalação do Visual Studio deverá incluir a carga de trabalho de **Desenvolvimento com a Plataforma Universal do Windows**. Além disso, será necessário instalar o <a href="https://git-scm.com/download/win" target="_blank">Git para Windows</a>.
- Você precisará de um dispositivo do HoloLens com o [modo de desenvolvedor](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) habilitado. A [Atualização de outubro de 2018 para o Windows 10](https://docs.microsoft.com/en-us/windows/mixed-reality/release-notes-october-2018) (também conhecida como RS5) deverá ser instalada no dispositivo. Para atualizar para a versão mais recente do HoloLens, abra o aplicativo de **Configurações**, acesse **Atualização e Segurança** e, em seguida, selecione **Verificar atualizações**.
- No aplicativo, é necessário habilitar o recurso **SpatialPerception**. Essa configuração está em **Configurações de Build** > **Configurações do Player** > **	Configurações de Publicação** > **Recursos**.
- No seu aplicativo, é necessário habilitar **Realidade Virtual com Suporte** com **SDK de Realidade Misturada do Windows**. Esta configuração está em **Configurações de Build** > **Configurações do Player** > **Configurações XR**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Abrir o projeto de exemplo no Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

No Unity, abra o projeto na pasta do Unity.

Abra **Configurações de Build** selecionando **Arquivo** > **Configurações de Build**.

Na seção **Plataforma**, selecione **Plataforma Universal do Windows**. Altere o **Dispositivo de Destino** para **HoloLens**.

Selecione **Alternar Plataforma** para alterar a plataforma para a **Plataforma Universal do Windows**. O Unity poderá solicitar que você instale componentes de suporte UWP, se estiverem faltando.

   ![Janela Configurações de Build do Unity](./media/get-started-unity-hololens/unity-build-settings.png)

Feche a janela **Configurações de Build**.

## <a name="configure-the-account-identifier-and-key"></a>Configurar o identificador e a chave da conta

No painel **Projeto**, vá para `Assets/AzureSpatialAnchorsPlugin/Examples` e abra o arquivo de cena `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Salve a cena selecionando **Arquivo** > **Salvar**.

## <a name="export-the-hololens-visual-studio-project"></a>Exportar o projeto do Visual Studio do HoloLens

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Selecione **Compilar**. Na caixa de diálogo, selecione uma pasta para exportar o projeto do Visual Studio do HoloLens.

Quando a exportação estiver concluída, uma pasta contendo o projeto HoloLens exportado será exibida.

## <a name="deploy-the-hololens-application"></a>Implantar o aplicativo HoloLens

Na pasta, clique duas vezes em **HelloAR U3D.sln** para abrir o projeto no Visual Studio.

Altere a **Configuração da Solução** para **Lançamento**, altere a **Plataforma da Solução** para **x86** e selecione **Dispositivo** das opções de destino de implantação.

   ![Configuração do Visual Studio](./media/get-started-unity-hololens/visual-studio-configuration.png)

Ligue o dispositivo do HoloLens, entre e conecte o dispositivo ao computador usando um cabo USB.

Selecione **Depurar** > **Iniciar depuração** para implantar seu aplicativo e iniciar a depuração.

Siga as instruções no aplicativo para colocar uma âncora e fazer recall dela.

No Visual Studio, pare o aplicativo selecionando **Parar Depuração** ou Shift+F5.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Compartilhar âncoras espaciais entre dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
