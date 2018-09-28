---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: signalr
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 133fa1063a08303bfe4b4a973801b6bc22766ac3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006181"
---
## <a name="run-the-web-application"></a>Executar o aplicativo Web

1. Há um aplicativo Web de página única de exemplo hospedado no GitHub para sua conveniência. Abra seu navegador em [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat/).

    > [!NOTE]
    > A origem do arquivo HTML está localizada em [/docs/demo/chat/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat/index.html).

1. Quando for solicitada a URL base do aplicativo de funções, digite *http://localhost:7071*.

1. Insira um nome de usuário quando solicitado.

1. O aplicativo Web chama a função *GetSignalRInfo* o aplicativo de funções para recuperar as informações de conexão para se conectar ao Serviço Azure SignalR. Quando a conexão for concluída, a caixa de entrada de mensagem de chat será exibida.

1. Digite uma mensagem e pressione Enter. O aplicativo envia a mensagem para a função *SendMessage* no aplicativo Função do Azure, que usa a associação da saída do SignalR para difundir a mensagem para todos os clientes conectados. Se tudo estiver funcionando corretamente, a mensagem deverá aparecer no aplicativo.

    ![Executar o aplicativo](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. Abra outra instância do aplicativo Web em outra janela do navegador. Você verá que todas as mensagens enviadas serão exibidas em todas as instâncias do aplicativo.