---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/25/2018
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: 1d3bfb7bc8a5432392dba3b0c5019902b3e59773
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39513861"
---
1. Clique no botão **Serviços de Aplicativos**, selecione seu back-end dos Aplicativos Móveis, selecione **Início Rápido**e selecione a plataforma cliente (iOS, Android, Xamarin, Cordova).

    ![Portal do Azure com Início Rápido de Aplicativos Móveis realçado][quickstart]

1. Se não houver uma conexão de banco de dados configurada, crie uma fazendo o seguinte:

    ![Portal do Azure com Aplicativos Móveis se conecta ao banco de dados][connect]

    a. Crie um novo servidor e Banco de Dados SQL. Talvez você precise deixar o campo do nome da cadeia de conexão com o valor padrão MS_TableConnectionString para concluir a etapa 3 abaixo.

    ![O portal do Azure com Aplicativos Móveis cria novo banco de dados e servidor][server]

    b. Aguarde até que a conexão de dados seja criada.

    ![Notificação no portal do Azure sobre a criação bem-sucedida da conexão de dados][notification]

    c. A conexão de dados deve ser bem-sucedida.

    ![Notificação no portal do Azure, "Você já tem uma conexão de dados"][already-connection]

1. Em **2. Crie uma API de tabela**, selecione Node.js como **Linguagem de back-end**.

1. Aceite a confirmação e selecione **Criar tabela TodoItem**.
    Essa ação cria uma nova tabela de item de tarefas pendentes no banco de dados.

    >[!IMPORTANT]
    > A alternância de um back-end existente para Node.js substitui todo o conteúdo. Em vez disso, para criar um back-end do .NET, confira [Trabalhar com o SDK de servidor de back-end para Aplicativos Móveis][instructions].

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
