---
title: Criar Hubs de Eventos do Azure habilitados para Apache Kafka | Microsoft Docs
description: Criar um namespace de Hubs de Eventos do Azure e um hub de eventos usando o Portal do Azure
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 08/16/2018
ms.author: bahariri
ms.openlocfilehash: 7ce12f9dcaa15ade95274419f99c13d5915dbaaa
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2018
ms.locfileid: "42142035"
---
# <a name="create-apache-kafka-enabled-event-hubs"></a>Criar hubs de eventos habilitados para Apache Kafka

Os Hubs de eventos do Azure são streaming de Big Data de Plataforma como Serviço (PaaS) que ingere milhões de eventos por segundo e fornece pouca latência e alta taxa de transferência para análise em tempo real e visualização.

Hubs de Eventos do Azure fornecem um ponto de extremidade do Kafka. Esse ponto de extremidade permite que seu namespace de Hubs de eventos entenda nativamente a mensagem de protocolo [Kafka Apache](https://kafka.apache.org/intro) e as APIs. Com essa funcionalidade, você pode se comunicar com seus Hubs de eventos, como você faria com tópicos Kafka sem alterar seus clientes de protocolo ou seus próprio clusters em execução. Hubs de Eventos dão suporte para [Apache Kafka versão 1.0.](https://kafka.apache.org/10/documentation.html) e posterior.

Este artigo descreve como criar um namespace de Hubs de eventos e obter a cadeia de caracteres de conexão necessária para se conectar aplicativos Kafka a Kafka habilitado aos Hubs de Eventos.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Criar um Kafka habilitado com o namespace dos Hubs de Evento

1. Entre no [Portal do Azure][Azure portal] e clique em **Criar um recurso** na parte superior esquerda da tela.

2. Pesquisar os Hubs de eventos e selecionar as opções mostradas aqui:
    
    ![Exportar para Hubs de Eventos no portal](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Forneça um nome exclusivo e habilite o Kafka no namespace. Clique em **Criar**.
    
    ![Criar um namespace](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.png)
 
4. Depois que o namespace foi criado, na guia **Configurações**, clique em **Políticas de acesso compartilhado** para obter a cadeia de conexão.

    ![Clique em Políticas de acesso compartilhado](./media/event-hubs-create/create-event-hub7.png)

5. Você pode escolher o padrão **RootManageSharedAccessKey**, ou adicionar uma nova política. Clique no nome da política e copie a cadeia de conexão. 
    
    ![Selecione uma política](./media/event-hubs-create/create-event-hub8.png)
 
6. Adiciona esta cadeia de conexão à sua configuração de aplicativo Kafka.

Agora você pode transmitir eventos de aplicativos que usam o protocolo Kafka nos Hubs de eventos.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os Hubs de Eventos, consulte estes links:

* [Transmitir para Hubs de Eventos a partir de seus aplicativos Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [Saiba mais sobre os Hubs de Eventos para o Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Saiba sobre os Hubs de Evento](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/
