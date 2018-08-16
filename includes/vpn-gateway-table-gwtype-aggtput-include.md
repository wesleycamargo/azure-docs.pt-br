---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4cbbb64489acf23c1248e35269e1441dd2a6878e
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39513983"
---
|**SKU**   | **S2S/VNet para VNet<br>Túneis** | **Conexões<br>P2S** | **Parâmetro de comparação<br>de taxa de transferência total** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| Máx. 30*                         | Máx. 128\*\*             | 650 Mbps                    |
|**VpnGw2**| Máx. 30*                         | Máx. 128\*\*             | 1 Gbps                      |
|**VpnGw3**| Máx. 30*                         | Máx. 128\*\*             | 1,25 Gbps                   |
|**Básico** | Máx. 10                         | Máx. 128               | 100 Mbps                    | 

* (*) Use [WAN Virtual](../articles/virtual-wan/virtual-wan-about.md) se precisar de mais de 30 túneis de VPN S2S.

* (**) Contate o suporte se conexões adicionais forem necessárias. Isso se aplica somente a IKEv2, o número de conexões para SSTP não pode ser aumentado.

* O parâmetro de comparação da taxa de transferência total se baseia nas medidas de vários túneis agregados por meio de um único gateway. Não é uma taxa de transferência garantida devido às condições de tráfego de Internet e seus comportamentos de aplicativo.

* Encontre informações sobre preços na página [Preços](https://azure.microsoft.com/pricing/details/vpn-gateway) .

* As informações de SLA (contrato de nível de serviço) podem ser encontradas na página [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* VpnGw1, VpnGw2 e VpnGw3 têm suporte somente pelo modelo de implantação do Gerenciador de Recursos.