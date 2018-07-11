---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: service-bus-messaging
author: sethmanheim
ms.service: service-bus-messaging
ms.topic: include
ms.date: 06/29/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: bad7e87cb194fb7d8846e7e675881658a1d33b1e
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37138153"
---
Verifique se você já criou um namespace do Barramento de Serviço, como mostrado [aqui][namespace-how-to].

1. Entre no [Portal do Azure][azure-portal].
2. No painel de navegação à esquerda do portal, clique em **Barramento de Serviço** (se a opção **Barramento de Serviço** não estiver visível, clique em **Todos os serviços**).
3. Clique no namespace no qual você gostaria de criar a fila. Nesse caso, é **sbnstest1**.
   
    ![Criar uma fila][createqueue1]
4. Na janela do namespace, clique em **Filas**, e na janela **Filas**, clique em **+ Fila**.
   
    ![Selecionar Filas][createqueue2]
5. Insira o **Nome** da fila e deixe os outros valores com os padrões.
   
    ![Selecionar Nova][createqueue3]
6. Na parte inferior da janela, clique em **Criar**.

[createqueue1]: ./media/service-bus-create-queue-portal/create-queue1.png
[createqueue2]: ./media/service-bus-create-queue-portal/create-queue2.png
[createqueue3]: ./media/service-bus-create-queue-portal/create-queue3.png

[namespace-how-to]: ../articles/service-bus-messaging/service-bus-create-namespace-portal.md
[azure-portal]: https://portal.azure.com
