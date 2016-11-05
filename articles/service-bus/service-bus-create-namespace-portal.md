---
title: Criar um namespace do Barramento de Serviço usando o portal do Azure | Microsoft Docs
description: Para começar a usar o Barramento de Serviço, você precisará de um namespace. Aqui está como criar um usando o portal do Azure.
services: service-bus
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: ''

ms.service: service-bus
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 08/22/2016
ms.author: jotaub

---
# Criar um namespace do Barramento de Serviço usando o Portal do Azure
Um namespace é um contêiner comum para todos os componentes de mensagem. Várias filas e tópicos podem residir em um único namespace e os namespaces geralmente servem como contêineres de aplicativos. Atualmente, existem duas maneiras diferentes de criar um namespace do Barramento de Serviço.

1. Portal do Azure (este artigo)
2. [Modelos do Gerenciador de Recursos][create-namespace-using-arm]

## Criar um namespace no Portal do Azure
[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Parabéns! Agora você criou um namespace Mensagens do Barramento de Serviço.

## Próximas etapas
Confira nosso [Repositório GitHub com exemplos](https://github.com/Azure-Samples/azure-servicebus-messaging-samples\]\[github-samples) que mostram alguns dos recursos mais avançados de Mensagens do Barramento de Serviço do Azure.

[create-namespace-using-arm]: ../service-bus-messaging/service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

<!---HONumber=AcomDC_1005_2016-->