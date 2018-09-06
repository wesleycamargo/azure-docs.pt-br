---
title: Como criar um namespace do Barramento de Serviço no portal do Azure | Microsoft Docs
description: Crie um namespace do Barramento de Serviço usando o Portal do Azure.
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/29/2018
ms.author: spelluru
ms.openlocfilehash: 699129e9f75cce76d1682e3e2e2fb83d248ea92e
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696018"
---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Criar um namespace do Barramento de Serviço usando o Portal do Azure

Um namespace é um contêiner de escopo para todos os componentes de mensagem. Várias filas e tópicos podem residir em um único namespace e os namespaces geralmente servem como contêineres de aplicativos. Existem duas maneiras de criar um namespace do Barramento de Serviço:

1. Portal do Azure (este artigo)
2. [Modelos do Gerenciador de Recursos][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Criar um namespace no Portal do Azure

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Parabéns! Agora você criou um namespace Mensagens do Barramento de Serviço.

## <a name="next-steps"></a>Próximas etapas

Confira os [exemplos do GitHub][github-samples] do Barramento de Serviço, que mostram alguns dos recursos mais avançados de Mensagens do Barramento de Serviço.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure/azure-service-bus/tree/master/samples
