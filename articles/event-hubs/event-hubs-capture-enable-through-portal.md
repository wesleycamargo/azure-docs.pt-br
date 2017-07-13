---
title: Habilitar a Captura de Hubs de Eventos do Azure por meio do portal | Microsoft Docs
description: Habilite o recurso Captura de Hubs de Eventos usando o portal do Azure.
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/28/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 1f3d373944b909db290f6cf2da7bf12a8a00e1c5
ms.contentlocale: pt-br
ms.lasthandoff: 06/28/2017


---

# Habilitar a Captura de Hubs de Evento usando o portal do Azure
<a id="enable-event-hubs-capture-using-the-azure-portal" class="xliff"></a>

Você pode configurar Captura na hora da criação do hub de eventos usando o [portal do Azure](https://portal.azure.com). Habilite a Captura clicando no botão **Ativado** na folha do portal **Criar Hub de Eventos**. Configure uma conta de armazenamento e o contêiner clicando na seção **Contêiner** da folha. Já que a Captura dos Hubs de Eventos usa a autenticação de serviços com o armazenamento, você não precisa especificar uma cadeia de conexão de armazenamento. O seletor de recurso seleciona automaticamente o URI do recurso para sua conta de armazenamento. Se você usar o Azure Resource Manager, deverá fornecer esse URI explicitamente como uma cadeia de caracteres.

A janela de tempo padrão é de 5 minutos. O valor mínimo é 1, o máximo é 15. A janela **Tamanho** tem um intervalo de 10 a 500 MB.

![][1]

## Adicionando a Captura a um hub de eventos existente
<a id="adding-capture-to-an-existing-event-hub" class="xliff"></a>

A Captura pode ser configurada nos hubs de eventos existentes que estão em namespaces dos Hubs de Eventos. O recurso não está disponível em namespaces de tipo **Misto** ou de **Mensagens**. Para habilitar a Captura em um hub de eventos existente ou alterar as configurações da Captura, clique no namespace para carregar a folha **Essentials** e clique no Hub de Eventos para o qual você deseja habilitar ou alterar a configuração da Captura. Por fim, clique na seção **Propriedades** da folha aberta conforme mostrado na seguinte figura:

![][2]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png

## Próximas etapas
<a id="next-steps" class="xliff"></a>

Você também pode configurar a Captura dos Hubs de Eventos usando modelos do Azure Resource Manager. Para saber mais, confira [Habilitar Captura usando um modelo do Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).

