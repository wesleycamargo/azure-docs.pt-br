---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227456"
---
Após criar o ouvinte do grupo de disponibilidade, é possível que seja necessário ajustar os parâmetros do cluster RegisterAllProvidersIP e HostRecordTTL para o recurso do ouvinte. Esses parâmetros podem reduzir o tempo de reconexão após um failover, o que pode impedir as interrupções de conexão. Para obter mais informações sobre esses parâmetros, bem como um código de exemplo, consulte [Criar ou configurar um ouvinte de grupo de disponibilidade](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover).

