---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 01/04/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: f00ca7ddf44a9d5b850cd47520970a0396a0c1b5
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54453079"
---
1. Abra o Gerenciador de SDK do Android clicando no ícone na barra de ferramentas do Android Studio ou clicando em **Ferramentas** > **Android** > **Gerenciador de SDK** no menu. Localize a versão de destino do SDK do Android que é usada em seu projeto, abra-o clicando em **Mostrar Detalhes do Pacote** e escolha **APIs do Google**, se ainda não estiver instalado.
2. Clique na guia **Ferramentas do SDK** . Se você ainda não tiver instalado o Serviço do Google Play, clique em **Serviços do Google Play** , conforme mostrado abaixo. Em seguida, clique em **Aplicar** para instalar. Anote o caminho do SDK, a ser usado em uma etapa posterior.

    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Abra o arquivo `build.gradle` no diretório do aplicativo.

    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)
4. Adicione esta linha embaixo de `dependencies`:

    ```text
    compile 'com.google.android.gms:play-services-gcm:12.0.0'
    ```
5. Clique no botão **Sincronizar projetos com arquivos do Gradle** na barra de ferramentas.
6. Abra **Androidmanifest** e adicione esta marcação para a marca do *aplicativo* .

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
