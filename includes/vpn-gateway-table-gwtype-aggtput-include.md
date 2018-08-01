---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fad9b990b6ff1021efdaf8aadeb1e19d8a55871d
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39138005"
---
|**SKU**   | **S2S/VNet para VNet<br>Túneis** | **Conexões<br>P2S** | **Parâmetro de comparação<br>de taxa de transferência total** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| Máx. 30*                         | Máx. 128\*\*             | 650 Mbps                    |
|**VpnGw2**| Máx. 30*                         | Máx. 128\*\*             | 1 Gbps                      |
|**VpnGw3**| Máx. 30*                         | Máx. 128\*\*             | 1,25 Gbps                   |
|**Básico** | Máx. 10                         | Máx. 128               | 100 Mbps                    | 

* (*) Use [WAN Virtual](../articles/virtual-wan/virtual-wan-about.md) se precisar de mais de 30 túneis de VPN S2S.

* (\*\*) Contate o suporte se conexões adicionais forem necessárias

* O parâmetro de comparação da taxa de transferência total se baseia nas medidas de vários túneis agregados por meio de um único gateway. Não é uma taxa de transferência garantida devido às condições de tráfego de Internet e seus comportamentos de aplicativo.

* Encontre informações sobre preços na página [Preços](https://azure.microsoft.com/pricing/details/vpn-gateway) .

* As informações de SLA (contrato de nível de serviço) podem ser encontradas na página [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* VpnGw1, VpnGw2 e VpnGw3 têm suporte somente pelo modelo de implantação do Gerenciador de Recursos.