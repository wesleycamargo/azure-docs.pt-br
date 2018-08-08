---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 58d81bd4c1ce4b3af91a335039f62df08b340576
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399984"
---
O armazenamento com redundância local (LRS) foi projetado para fornecer pelo menos 99,999999999% (são 11 números 9) de durabilidade dos objetos durante um determinado ano replicando seus dados dentro da unidade de escala de armazenamento. Uma unidade de escala de armazenamento é hospedada em um datacenter na região em que você criou sua conta de armazenamento. Uma solicitação de gravação para uma conta de armazenamento LRS retorna com êxito somente depois que os dados forem gravados para todas as réplicas. Essas réplicas residem em domínios de falha e domínios de atualização separados dentro de uma unidade de escala de armazenamento.

Uma unidade de escala de armazenamento é uma coleção de racks de nós de armazenamento. Um domínio de falha (FD) é um grupo de nós que representam uma unidade física de falha e podem ser considerados nós que pertencem ao mesmo rack físico. Um domínio de atualização (UD) é um grupo de nós atualizados em conjunto durante o processo de atualização de um serviço (distribuição). As réplicas estão difundidas entre UDs e FDs em uma unidade de escala de armazenamento. Essa arquitetura garante que seus dados estejam disponíveis se uma falha de hardware afetar um único rack ou quando os nós forem atualizados durante uma distribuição.

O LRS é a opção de replicação de custo mais baixo e oferece durabilidade menor em comparação com outras opções. Em caso de desastre no nível de datacenter (por exemplo, incêndio, inundação etc.), as réplicas podem ser perdidas ou ficar irrecuperáveis. Para reduzir esse risco, a Microsoft recomenda o uso de armazenamento com redundância de zona (ZRS) ou armazenamento com redundância geográfica (GRS).

* Se seu aplicativo armazenar dados que possam ser facilmente reconstruídos, você pode optar por LRS.
* Alguns aplicativos são restritos à replicação de dados somente em um país devido a requisitos de governança de dados. Em alguns casos, as regiões emparelhadas em que os dados são replicados para contas GRS podem ser em outro país. Para obter mais informações sobre pares de regiões, consulte [Regiões do Azure](https://azure.microsoft.com/regions/).
