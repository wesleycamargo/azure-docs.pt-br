---
title: Plano de controle do Office 365 na WAN Virtual do Azure
description: Saiba mais sobre o plano de controle do Office 365 na WAN Virtual.
author: cherylmc
ms.service: virtual-wan
services: virtual-wan
ms.topic: article
ms.date: 9/24/2018
ms.author: cherylmc
ms.openlocfilehash: cb91c1364a91c101ecf8362acd7aab01440143fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60458570"
---
# <a name="office-365-control-plane-in-virtual-wan"></a>Plano de controle do Office 365 na WAN Virtual

Os clientes de WAN virtual com dispositivos SDWAN selecionados podem configurar políticas de sessão de Internet do O365 para o tráfego confiável no portal do Azure. Isso permite:
- Que o tráfego de O365 entre na rede da Microsoft próxima do usuário oferecendo a experiência ideal ao usuário.
- Evita o retorno do tráfego e o acesso por rede externa, economizando em custos de WAN.
- Entregando os princípios de conectividade do O365.

## <a name="faqs"></a>Perguntas frequentes
### <a name="what-is-the-customer-benefit"></a>O que é o benefício do cliente?
Usando esse recurso na WAN Virtual, os clientes agora podem especificar as categorias de tráfego do Office 365 em que confiam para sessões de Internet direta. Esse tráfego do O365 confiável ignorará os proxies e encaminhará diretamente da localização do usuário para o POP da Microsoft mais próximo. Evita o retorno do tráfego e o acesso por rede externa, oferecendo a experiência ideal ao usuário e economizando em custos de WAN. 

### <a name="what-are-the-office-365-traffic-categories"></a>Quais são as categorias do tráfego do Office 365?
Os pontos de extremidade do Office 365 representam as sub-redes e os endereços de rede. Os pontos de extremidade podem ser URLs, intervalos de IP ou endereços IP. As URLs podem ser um FQDN, como *account.office.net*, ou uma URL curinga, como **.office365.com*. Os pontos de extremidade são separados em três categorias: **Otimizar**, **Permitir** e **Padrão**, com base em sua gravidade. Há mais detalhes sobre as categorias de ponto de extremidade [aqui](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles#BKMK_Categories).

### <a name="which-office-365-traffic-category-is-recommended-by-microsoft-for-direct-internet-breakout"></a>Qual categoria de tráfego do Office 365 é recomendada pela Microsoft para sessões de Internet direta?
A categoria **Otimizar** é os pontos de extremidade de rede mais críticos e é necessária para ignorar a interrupção de SSL e inspecionar outros dispositivos de segurança de rede. Ela deve ter a saída de Internet direta próxima dos usuários. Esses pontos de extremidade representam cenários do Office 365 que são mais sensíveis ao desempenho, à latência e à disponibilidade de rede. Essa categoria inclui um conjunto pequeno (na ordem de cerca de 10) de URLs principais e um conjunto definido de sub-redes IP dedicado às cargas de trabalho principais do Office 365, como o Exchange Online, o SharePoint Online, o Skype for Business Online e o Microsoft Teams. 

A categoria **Permitir** também é recomendada para a saída de Internet direta. No entanto, a permissão do tráfego de rede pode tolerar um pouco de latência de rede. Os pontos de extremidade nas categorias Otimizar e Permitir estão todos hospedados em datacenters da Microsoft e são gerenciados como parte do Office 365. A categoria padrão pode ser direcionada para uma localização de saída padrão da Internet e não exige nenhuma saída de Internet direta ou bypass de interrupção de SSL e inspeção de dispositivos.

### <a name="how-do-i-set-my-o365-policies-via-virtual-wan"></a>Como definir minhas políticas do O365 por meio da WAN Virtual?
Você pode habilitar as políticas por meio da guia **WAN Virtual** -> **Definições** -> **Configuração**. Aqui você pode especificar suas categorias preferidas do tráfego do O365 para sessões de Internet direta.

![Configurar o plano de controle do Office 365 na WAN Virtual](media/virtual-wan-office365-overview/configure-office365-control-plane.png)

### <a name="how-does-this-work"></a>Como isso funciona?

1.  O tráfego de O365 entra na rede da Microsoft próxima do usuário oferecendo a experiência ideal ao usuário.
2.  As políticas de rota são consumidas pelo SDWAN. Em seguida, ele ignora os proxies de segurança das categorias confiáveis e executa sessões diretas locais para essas categorias.
3.  O retorno do tráfego e o acesso por rede externa são evitados, economizando em custos de WAN.

### <a name="which-partner-devices-support-this-via-virtual-wan"></a>Quais dispositivos de parceiro dão suporte a isso por meio da WAN Virtual?
Atualmente, o Citrix dá suporte a essas políticas por meio da WAN Virtual.

### <a name="what-happens-to-the-remaining-categories-of-untrusted-o365-traffic"></a>O que acontece com as categorias restantes de tráfego (não confiável) do O365?
O tráfego de O365 restante seguirá o caminho de tráfego de Internet padrão dos clientes.

### <a name="what-if-i-have-already-specified-my-o365-policies-via-my-sdwan-provider"></a>E se eu já tiver especificado minhas políticas do O365 por meio do meu provedor SDWAN?
Se você especificar as políticas por meio do SDWAN UX e também da WAN Virtual do Azure, as políticas definidas na WAN Virtual terão precedência.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre a [WAN Virtual](virtual-wan-about.md).