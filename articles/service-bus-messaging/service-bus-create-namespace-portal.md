---
title: "Criar um namespace do Barramento de Serviço usando o portal do Azure | Microsoft Docs"
description: "Para começar a usar o Barramento de Serviço, você precisará de um namespace. Aqui está como criar um usando o portal do Azure."
services: service-bus-messaging
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: 
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 08/22/2016
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 9ace119de3676bcda45d524961ebea27ab093415
ms.openlocfilehash: 5fa107c857b3291cf2687cb4097649e23e9f95da


---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Criar um namespace do Barramento de Serviço usando o Portal do Azure
Um namespace é um contêiner comum para todos os componentes de mensagem. Várias filas e tópicos podem residir em um único namespace e os namespaces geralmente servem como contêineres de aplicativos. Atualmente, existem duas maneiras diferentes de criar um namespace do Barramento de Serviço.

1. Portal do Azure (este artigo)
2. [Modelos do Resource Manager][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Criar um namespace no Portal do Azure
[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Parabéns! Agora você criou um namespace Mensagens do Barramento de Serviço.

## <a name="next-steps"></a>Próximas etapas
Confira nossos [exemplos do GitHub][github-samples] que mostram alguns dos recursos mais avançados de Mensagens do Barramento de Serviço do Azure.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples



<!--HONumber=Nov16_HO3-->


