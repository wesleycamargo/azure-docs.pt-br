---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 06/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d067f1af3d5479aef28ddf2290cebe3fe45726b0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64733730"
---
| Resource | Limite máximo/padrão |
| --- | --- |
| Circuitos do ExpressRoute por assinatura |10 |
| Circuitos do ExpressRoute por região por assinatura, com o Azure Resource Manager |10 |
| Número máximo de rotas para emparelhamento privado do Azure com o padrão do ExpressRoute |4.000 |
| Número máximo de rotas para emparelhamento privado do Azure com o complemento ExpressRoute Premium |10.000 |
| Número máximo de rotas para emparelhamento privado do Azure do espaço de endereço de rede virtual para uma conexão de ExpressRoute |200 | 
| Número máximo de rotas para emparelhamento do Microsoft Azure com o padrão do ExpressRoute |200 |
| Número máximo de rotas para emparelhamento do Microsoft Azure com o complemento ExpressRoute Premium |200 |
| Número máximo de circuitos do ExpressRoute vinculado à mesma rede virtual no mesmo local de emparelhamento |4 |
| Número máximo de circuitos do ExpressRoute vinculados à mesma rede virtual em diferentes locais de emparelhamento |> 4, depende do tamanho da GatewaySubnet|
| Número de links de rede virtual permitido por circuito do ExpressRoute |Consulte a tabela a seguir. |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a>Número de redes virtuais por circuito do ExpressRoute
| **Tamanho do circuito** | **Número de links de rede virtual para padrão** | **Número de links de rede virtual com o complemento Premium** |
| --- | --- | --- |
| 50 Mbps |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbps |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |

