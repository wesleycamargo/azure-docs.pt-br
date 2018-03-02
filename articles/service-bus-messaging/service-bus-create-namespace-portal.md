---
title: "Como criar um namespace do Barramento de Serviço no portal do Azure | Microsoft Docs"
description: "Crie um namespace do Barramento de Serviço usando o Portal do Azure."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 02/22/2018
ms.author: sethm
ms.openlocfilehash: a24fa21848005d9768d26ae865680a4851e1dd81
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/24/2018
---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Criar um namespace do Barramento de Serviço usando o Portal do Azure

Um namespace é um contêiner de escopo para todos os componentes de mensagem. Várias filas e tópicos podem residir em um único namespace e os namespaces geralmente servem como contêineres de aplicativos. Existem duas maneiras de criar um namespace do Barramento de Serviço:

1. Portal do Azure (este artigo)
2. [Modelos do Gerenciador de Recursos][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Criar um namespace no Portal do Azure

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Parabéns! Agora você criou um namespace Mensagens do Barramento de Serviço.

## <a name="next-steps"></a>Próximas etapas

Confira nossos [exemplos do GitHub][github-samples], que mostram alguns dos recursos mais avançados de Mensagens do Barramento de Serviço.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure/azure-service-bus/tree/master/samples
