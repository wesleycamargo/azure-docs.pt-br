---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 02/05/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bc920493b32d500602a5b683c098d23aff855150
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823086"
---
1. No **Android Studio**, selecione **Ferramentas** no menu e selecione **Gerenciador do SDK**. 
2. Selecione a versão de destino do SDK do Android que é usada no projeto e selecione **Mostrar Detalhes do Pacote**. 

    ![Gerenciador do SDK do Android – selecionar versão de destino](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Selecione **APIs do Google**, caso elas ainda não estejam instaladas.

    ![Gerenciador do SDK do Android – APIs do Google selecionadas](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Alterne para a guia **SDK Tools**. Caso ainda não tenha instalado o Google Play Services, selecione **Google Play Services**, conforme mostrado na imagem a seguir. Em seguida, clique em **Aplicar** para instalar. Anote o caminho do SDK, a ser usado em uma etapa posterior.

    ![Gerenciador do SDK do Android – Google Play Services selecionado](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Se a caixa de diálogo **Confirmar Alteração** for exibida, selecione **OK**. O Instalador de Componente instalará os componentes solicitados. Selecione **Concluir** depois que os componentes forem instalados.
4. Selecione **OK** para fechar a caixa de diálogo **Configurações para Novos Projetos**.  
5. Abra o arquivo `build.gradle` no diretório **app** e adicione esta linha em `dependencies`. 

    ```text
    implementation 'com.google.android.gms:play-services-gcm:16.0.0'
    ```
5. Selecione o ícone **Sincronizar Agora** na barra de ferramentas.

    ![Sincronização com o Gradle](./media/notification-hubs-android-studio-add-google-play-services/gradle-sync.png)
1. Abra **Androidmanifest** e adicione esta marcação para a marca do *aplicativo* .

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
