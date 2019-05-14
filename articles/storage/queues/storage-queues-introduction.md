---
title: Introdução às Filas do Azure - Armazenamento do Azure
description: Introdução às Filas do Azure
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: overview
ms.date: 02/06/2019
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
ms.openlocfilehash: 544ff9d2c624ef62bf8041afd818153c1c4bfcc8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65142511"
---
# <a name="what-are-azure-queues"></a>O que são as Filas do Azure?

O armazenamento de filas do Azure é um serviço para armazenamento de um grande número de mensagens que podem ser acessadas de qualquer lugar do mundo por meio de chamadas autenticadas usando HTTP ou HTTPS. Uma única mensagem de fila pode ter até 64 KB de tamanho e uma fila pode conter milhões de mensagens, até o limite de capacidade total de uma conta de armazenamento.

## <a name="common-uses"></a>Usos comuns

Usos comuns de Armazenamento de filas incluem:

* Criar uma lista de pendências de trabalho para processar de maneira assíncrona
* Transmitir mensagens de uma função Web do Azure para uma função de Trabalho do Azure

## <a name="queue-service-concepts"></a>Conceitos do serviço Fila

O serviço Fila contém os seguintes componentes:

![Conceitos de fila](./media/storage-queues-introduction/queue1.png)

* **Formato de URL:** As consultas são endereçáveis usando o seguinte formato de URL:   
    https://`<storage account>`.queue.core.windows.net/`<queue>` 
  
    A URL a seguir endereça um fila no diagrama:  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **Conta de armazenamento:** Todo o acesso ao Armazenamento do Azure ocorre por meio de uma conta de armazenamento. Consulte [Escalabilidade e Metas de Desempenho do Armazenamento do Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) para obter detalhes sobre a capacidade da conta de armazenamento.

* **Fila:** Uma fila contém um conjunto de mensagens. Todas as mensagens devem estar em uma fila. Observe que o nome da fila deve estar em letras minúsculas. Para saber mais sobre filas de nomenclatura, confira [Nomenclatura de filas e metadados](https://msdn.microsoft.com/library/azure/dd179349.aspx).

* **Mensagem:** Uma mensagem, em qualquer formato, de até 64 KB. O tempo máximo que uma mensagem pode ficar na fila é de sete dias.

## <a name="next-steps"></a>Próximas etapas

* [Criar uma conta de armazenamento](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Introdução às filas usando .NET](storage-dotnet-how-to-use-queues.md)
