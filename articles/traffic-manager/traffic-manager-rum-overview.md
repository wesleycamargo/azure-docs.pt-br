---
title: Medidas de Usuário Reais do Gerenciador de Tráfego do Azure
description: Introdução às Medidas Reais de Usuário do Gerenciador de Tráfego
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: fd37ef739522955ae8227db39a41aecf199d65c3
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052812"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Visão geral das Medidas Reais de Usuário do Gerenciador de Tráfego

Quando você configurar um perfil do Gerenciador de Tráfego para usar o método de roteamento de desempenho, o serviço analisará de onde as solicitações de consulta DNS estão chegando e tomará decisões de roteamento para direcionar esses solicitantes para a região do Azure que oferece a latência mais baixa. Isso é feito utilizando a inteligência de latência de rede que o Gerenciador de Tráfego mantém para diferentes redes de usuário final.

O recurso de Medidas Reais de Usuário permite obter as medidas de latência de rede das regiões do Azure, dos aplicativos cliente que seus usuários finais usam e fazer com que o Gerenciador de Tráfego também considere essas informações ao tomar decisões de roteamento. Ao escolher usar as Medidas Reais de Usuário, você pode aumentar a precisão do roteamento para solicitações provenientes dessas redes em que residem os usuários finais. 

## <a name="how-real-user-measurements-work"></a>Como funcionam as Medidas Reais de Usuário

As Medidas de Usuário Reais funcionam fazendo com que os aplicativos clientes meçam a latência das regiões do Azure como ela é percebida nas redes de usuário final em que esses aplicativos são usados. Por exemplo, se houver uma página da Web que seja acessada por usuários em diferentes locais (por exemplo, nas regiões na América do Norte), você poderá usar as Medidas de Usuário Reais com o método de roteamento de desempenho para chegar à melhor região do Azure em que o aplicativo para servidores esteja hospedado.

Ele começa pela inserção de um JavaScript fornecido pelo Azure (contendo uma chave exclusiva) nas páginas da Web. Depois disso, sempre que um usuário visitar a página da Web, o JavaScript consultará o Gerenciador de Tráfego para obter informações sobre as regiões do Azure que ele deve medir. O serviço retorna um conjunto de pontos de extremidade para o script que, em seguida, mede essas regiões consecutivamente ao baixar uma imagem de pixel único hospedada nessas regiões do Azure e observar a latência entre o momento em que a solicitação foi enviada e o momento em que o primeiro byte foi recebido. Em seguida, essas medidas são retornadas ao serviço do Gerenciador de Tráfego.

Ao longo do tempo, isso ocorre muitas vezes e em várias redes levando o Gerenciador de Tráfego a obter informações mais precisas sobre as características de latência das redes em que os usuários finais residem. Essas informações começam a ser incluídas nas decisões de roteamento feitas pelo Gerenciador de Tráfego. Como resultado, isso leva a um aumento de precisão nas decisões baseadas nas Medidas Reais de Usuário enviadas.

Ao usar as Medidas Reais de Usuário, você é cobrado com base no número de medidas enviadas para o Gerenciador de Tráfego. Para obter mais detalhes sobre os preços, visite o [página de preços do Gerenciador de Tráfego](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="next-steps"></a>Próximas etapas
- Saiba como usar as [Medidas Reais de Usuário com páginas da Web](traffic-manager-create-rum-web-pages.md)
- Saiba [como funciona o Gerenciador de Tráfego](traffic-manager-overview.md)
- Saiba mais sobre o [Mobile Center](https://docs.microsoft.com/mobile-center/)
- Saiba mais sobre os [métodos de roteamento do tráfego](traffic-manager-routing-methods.md) com suporte pelo Gerenciador de Tráfego
- Aprenda a [criar um perfil do Gerenciador de Tráfego](traffic-manager-create-profile.md)

