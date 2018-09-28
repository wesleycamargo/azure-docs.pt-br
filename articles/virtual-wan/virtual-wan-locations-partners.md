---
title: Localizações dos parceiros WAN Virtual do Azure | Microsoft Docs
description: Este artigo contém uma lista de parceiros WAN Virtual do Azure e localizações de hub
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect find a Virtual WAN partner
ms.openlocfilehash: 76542392522ff14642ed7ccc6de8ed543d66513f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984986"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Parceiros WAN Virtual e localizações de hub virtual

Este artigo fornece informações sobre as regiões com suporte da WAN Virtual, bem como parceiros preferenciais, para conectividade no Hub Virtual.

A WAN Virtual do Azure é um serviço de rede que fornece conectividade otimizada e automatizada entre branches por meio do Azure. A WAN Virtual permite que você conecte e configure dispositivos de branch para se comunicar com o Azure. Isso pode ser feito manualmente ou usando dispositivos de provedor preferencial por meio de um parceiro de WAN Virtual preferencial. O uso de dispositivos de parceiro preferencial facilita o uso, simplifica a conectividade e permite o gerenciamento de configurações. A conectividade do dispositivo local é estabelecida em uma forma automatizada para o Hub Virtual. Um hub virtual é uma rede virtual gerenciada pela Microsoft. O hub contém vários pontos de extremidade de serviço para habilitar a conectividade de sua rede local (vpnsite). Você só pode ter um hub por região.

## <a name="regions"></a>Regiões

A lista de regiões disponíveis e com suporte é a seguinte:

|Região geopolítica | Regiões do Azure|
|---|---|
|América do Norte | Leste dos EUA, Oeste dos EUA, Leste dos EUA 2, Oeste dos EUA 2, Centro dos EUA, Centro-Sul dos EUA, Centro-Norte dos EUA, Centro-Oeste dos EUA, Centro do Canadá, Leste do Canadá |
|América do Sul |Sul do Brasil |
| Europa | França Central, Sul da França, Norte da Europa, Europa Ocidental, Oeste do Reino Unido, Sul do Reino Unido |
| Ásia | Ásia Oriental, Sudeste Asiático |
| Japão  | Oeste do Japão, Leste do Japão |
| Austrália | Sudeste da Austrália, Leste da Austrália | 
| Governo da Austrália | Austrália Central, Austrália Central 2 |
| Índia | Oeste da Índia, Índia Central, Sul da Índia |
| Coreia do Sul | Coreia Central, Sul da Coreia | 

## <a name="automation-from-connectivity-partners"></a>Automação de parceiros de conectividade

Esta seção descreve os detalhes de alto nível de automação de provedores de conectividade.

Dispositivos que se conectam à WAN Virtual do Azure têm automação interna para conexão. Normalmente, isso é definido na interface do usuário de gerenciamento do dispositivo (ou equivalente), que configura a conectividade e o gerenciamento de configuração entre o dispositivo de branch VPN para um ponto de extremidade de VPN de Hub Virtual do Azure (gateway de VPN).

A automação de alto nível seguinte é configurada no centro de gerenciamento/console do dispositivo:

* Permissões apropriadas para o dispositivo para acessar o grupo de recursos da WAN Virtual do Azure
* Carregamento de dispositivo de branch na WAN Virtual do Azure
* Download automático de informações de conectividade do Azure 
* Configuração de dispositivo de branch local 

Alguns parceiros de conectividade podem estender a automação para incluir a criação do Gateway de VPN e VNet do Hub Virtual do Azure. Se você quiser saber mais sobre a automação, consulte [Configurar a automação – Parceiros WAN](virtual-wan-configure-automation-providers.md).

## <a name="connectivity-through-preferred-partners"></a>Conectividade por meio de parceiros preferenciais

Se o seu parceiro de dispositivo de branch não estiver listado na seção abaixo, entre em contato com seu provedor de dispositivo de branch e peça que ele entre em contato conosco, enviando um email para azurevirtualwan@microsoft.com.

Você pode verificar os links a seguir para obter mais informações sobre os serviços oferecidos por parceiros preferenciais. 

|Parceiros preferenciais|
|---|
|[Barracuda Networks](https://www.barracuda.com/AzurevWAN)|
| [Check Point](https://www.checkpoint.com/solutions/microsoft-azure-virtual-wan/) |
| [Citrix](https://www.citrix.com/global-partners/microsoft/sd-wan-for-azure-virtual-wan.html)|
|[Netfoundry](https://netfoundry.io/solutions/netfoundry-for-microsoft-azure-virtual-wan/)|
|[Palo Alto Networks](https://researchcenter.paloaltonetworks.com/2018/09/azure-vwan-integration/) |
|[Riverbed Technology](https://www.riverbed.com/go/steelconnect-azurewan.html)|
|[128 Technology](https://www.128technology.com/partners/azure) |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a WAN Virtual, consulte as [Perguntas frequentes sobre a WAN Virtual](virtual-wan-faq.md).

Para obter mais informações sobre como automatizar a conectividade com a WAN Virtual do Azure, consulte [Parceiros da WAN Virtual – Como automatizar](virtual-wan-configure-automation-providers.md).
