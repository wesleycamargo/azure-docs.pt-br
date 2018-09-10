---
title: Criar um hub de eventos do Azure | Microsoft Docs
description: Criar um namespace de Hubs de Eventos do Azure e um hub de eventos usando o Portal do Azure
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: 5b468e1758d752cd3001c85b328d064369429499
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/17/2018
ms.locfileid: "42145054"
---
# <a name="create-an-event-hubs-namespace-and-an-event-hub-using-the-azure-portal"></a>Criar um namespace de Hubs de Eventos e um hub de eventos usando o Portal do Azure

## <a name="create-an-event-hubs-namespace"></a>Criar um namespace de Hubs de Eventos

1. Faça logon no [Portal do Azure][Azure portal] e clique em **Criar um recurso** na parte superior esquerda da tela.
2. Clique em **Internet das Coisas** e, em seguida, clique em **Hubs de Eventos**.
   
    ![](./media/event-hubs-create/create-event-hub9.png)

3. Em **Criar um namespace**, insira um nome de namespace. O sistema imediatamente verifica para ver se o nome está disponível.  

4. Depois de verificar se o nome do namespace está disponível, escolha o tipo de preço (Básico ou Standard). Além disso, escolha uma assinatura do Azure, o grupo de recursos e o local no qual o recurso será criado.
 
5. Clique em **Criar** para criar o namespace. Talvez você precise aguardar alguns minutos para o sistema provisionar totalmente os recursos.

    ![](./media/event-hubs-create/create-event-hub1.png)

6. Na lista de namespaces do portal, clique no namespace recém-criado.

7. Clique em **Políticas de acesso compartilhado** e, em seguida, clique em **RootManageSharedAccessKey**.
    
    ![](./media/event-hubs-create/create-event-hub7.png)

8. Clique no botão de cópia para copiar a cadeia de conexão **RootManageSharedAccessKey** na área de transferência. Salve esta cadeia de conexão em um local temporário, como o Bloco de Notas, para uso futuro.
    
    ![](./media/event-hubs-create/create-event-hub8.png)

## <a name="create-an-event-hub"></a>Criar um Hub de Evento

1. Na lista de namespaces dos Hubs de Eventos, clique no namespace recém-criado.      
   
    ![](./media/event-hubs-create/create-event-hub2.png) 

2. Na folha do namespace, clique em **Hubs de Eventos**.
   
    ![](./media/event-hubs-create/create-event-hub3.png)

3. Na parte superior da folha, clique em **+ Hub de Eventos**.
   
    ![](./media/event-hubs-create/create-event-hub4.png)
4. Digite um nome para seu hub de eventos e clique em **Criar**. 

Seu hub de eventos foi criado, e você tem as cadeias de conexão que precisa para enviar e receber eventos.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os Hubs de Eventos, consulte estes links:

* [Visão geral de Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Visão geral de API de Hubs de Eventos](event-hubs-api-overview.md)

[Azure portal]: https://portal.azure.com/