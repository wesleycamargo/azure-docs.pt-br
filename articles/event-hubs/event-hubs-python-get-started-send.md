---
title: Enviar eventos para os Hubs de Eventos do Azure usando o Python | Microsoft Docs
description: Comece a enviar eventos para os Hubs de Eventos usando o Python
services: event-hubs
author: sethmanheim
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/26/2018
ms.author: sethm
ms.openlocfilehash: 762e21cfc7d16b614eb637c569f8bfc5b6115db1
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43703229"
---
# <a name="send-events-to-event-hubs-using-python"></a>Enviar eventos para Hubs de eventos usando o Python

Os Hubs de Eventos do Azure são um sistema gerenciamento de inclusão altamente dimensionável que pode receber milhões de eventos por segundo, permitindo que um aplicativo processe e analise grandes quantidades de dados produzidos por dispositivos conectados e outros sistemas. Depois de coletados em um hub de eventos, você pode receber e manipular eventos usando manipuladores em processo ou por encaminhamento para outros sistemas de análise.

Para saber mais sobre os Hubs de Eventos, consulte a [Visão geral dos Hubs de Eventos][Event Hubs overview].

Este tutorial descreve como enviar eventos para um hub de eventos a partir de um aplicativo escrito em Python. Para receber eventos, consulte [o artigo de recebimento correspondente](event-hubs-python-get-started-receive.md).

O código deste tutorial é retirado [destes exemplos do GitHub](https://github.com/Azure/azure-event-hubs-python/tree/master/examples), que você pode examinar para ver todo o aplicativo de trabalho, incluindo instruções de importação e declarações de variável. Outros exemplos estão disponíveis na mesma pasta do GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

- Python 3.4 ou posterior.
- Um namespace dos Hubs de Eventos existente e Hub de Eventos do Azure. Você pode criar essas entidades seguindo as instruções deste [artigo](event-hubs-create.md). 

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]


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

## <a name="send-events"></a>Enviar eventos

Para executar o script, abra um prompt de comando que tenha Python em seu caminho e, em seguida, execute este comando:

```bash
start python send.py
```
 
## <a name="next-steps"></a>Próximas etapas

Agora que você enviou eventos para um hub de eventos usando o Python, para receber eventos ver [o artigo Receive correspondente](event-hubs-python-get-started-receive.md).

Para saber mais sobre os Hubs de Eventos,visite as páginas a seguir:

* [Visão Geral dos Hubs de Eventos][Event Hubs overview]
* [Criar um hub de eventos](event-hubs-create.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[Visual Studio Code]: https://code.visualstudio.com/
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
