---
title: Enviar eventos usando Python – Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece um passo a passo para criar um aplicativo Python que envia eventos para Hubs de eventos do Azure.
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 11/16/2018
ms.author: shvija
ms.openlocfilehash: 2168fc89134615ffb4e0e718cc0cc27b8c1a7839
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59262764"
---
# <a name="send-events-to-event-hubs-using-python"></a>Enviar eventos para Hubs de eventos usando o Python

Os Hubs de Eventos do Azure são uma plataforma de streaming de Big Data e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos pelos dispositivos e software distribuídos. Os dados enviados para um Hub de Eventos podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Para obter uma visão detalhada dos Hubs de Eventos, confira [Visão geral de Hubs de Eventos](event-hubs-about.md) e [Recursos de Hubs de Eventos](event-hubs-features.md).

Este tutorial descreve como enviar eventos para um hub de eventos a partir de um aplicativo escrito em Python. 

> [!NOTE]
> Você pode baixar do [GitHub](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) este início rápido como um exemplo, substituir as cadeias de caracteres `EventHubConnectionString` e `EventHubName` pelos valores do hub de eventos e executá-lo. Como alternativa, é possível seguir as etapas deste tutorial para criar sua própria solução.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

- Uma assinatura do Azure. Se você não tiver [uma conta gratuita](https://azure.microsoft.com/free/), crie uma antes de começar.
- Python 3.4 ou posterior.


## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Criar um namespace de Hubs de Eventos e um hub de eventos
A primeira etapa é usar o [portal do Azure](https://portal.azure.com) para criar um namespace do tipo Hubs de eventos e obter as credenciais de gerenciamento das quais que seu aplicativo precisa para se comunicar com o hub de eventos. Para criar um namespace e um hub de eventos, siga o procedimento [nesse artigo](event-hubs-create.md).

Obtenha o valor da chave de acesso do hub de eventos seguindo as instruções do artigo: [Obter a cadeia de conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). A chave de acesso será usada no código que você escreverá posteriormente no tutorial. O nome da chave padrão é: **RootManageSharedAccessKey**.

Agora, prossiga para as próximas etapas do tutorial.

## <a name="install-python-package"></a>Instalar o pacote do Python

Para instalar o pacote Python para Hubs de Eventos, abra um prompt de comando que tenha Python em seu caminho e, em seguida, execute este comando: 

```bash
pip install azure-eventhub
```

## <a name="create-a-python-script-to-send-events"></a>Criar um script de Python para enviar eventos

Em seguida, crie um aplicativo Python que envia eventos para um hub de eventos:

1. Abra seu editor de Python favorito, como o [Visual Studio Code][Visual Studio Code].
2. Crie um script chamado **send.py**. Esse script envia 100 eventos ao hub de eventos.
3. Cole o seguinte código em send.py, substituindo os valores ADDRESS, USER e KEY pelos valores obtidos do portal do Azure na seção anterior: 

```python
import sys
import logging
import datetime
import time
import os

from azure.eventhub import EventHubClient, Sender, EventData

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://mynamespace.servicebus.windows.net/myeventhub"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "namespaceSASKey"

try:
    if not ADDRESS:
        raise ValueError("No EventHubs URL supplied.")

    # Create Event Hubs client
    client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
    sender = client.add_sender(partition="0")
    client.run()
    try:
        start_time = time.time()
        for i in range(100):
            print("Sending message: {}".format(i))
            sender.send(EventData(str(i)))
    except:
        raise
    finally:
        end_time = time.time()
        client.stop()
        run_time = end_time - start_time
        logger.info("Runtime: {} seconds".format(run_time))

except KeyboardInterrupt:
    pass
```

## <a name="run-application-to-send-events"></a>Executar o aplicativo para enviar eventos

Para executar o script, abra um prompt de comando que tenha Python no caminho e, em seguida, execute este comando:

```bash
start python send.py
```

Parabéns! Agora você enviou mensagens para um hub de eventos.
 
## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você enviou mensagens para um hub de eventos usando o Python. Para saber como receber eventos de um hub de eventos usando o Python, consulte [Receber eventos de hub de eventos – Python](event-hubs-python-get-started-receive.md).

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[Visual Studio Code]: https://code.visualstudio.com/
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
