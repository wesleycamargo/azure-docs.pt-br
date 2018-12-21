---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 555a8e3e92dc1d12cb7c6d6e06d2511f15a2c862
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52973112"
---
|**SKU**   | **S2S/VNet para VNet<br>Túneis** | **P2S<br> Conexões SSTP** | **P2S<br> Conexões IKEv2** | **Parâmetro de comparação<br>de taxa de transferência total** | **BGP** |
|---       | ---        | ---       | ---            | ---       | --- |
|**Básico** | Máx. 10    | Máx. 128  | Sem suporte  | 100 Mbps  | Sem suporte|
|**VpnGw1**| Máx. 30*   | Máx. 128  | Máx. 250       | 650 Mbps  | Com suporte |
|**VpnGw2**| Máx. 30*   | Máx. 128  | Máx. 500       | 1 Gbps    | Com suporte |
|**VpnGw3**| Máx. 30*   | Máx. 128  | Máx. 1000      | 1,25 Gbps | Com suporte |


(*) Use [WAN Virtual](../articles/virtual-wan/virtual-wan-about.md) se precisar de mais de 30 túneis de VPN S2S.

* O parâmetro de comparação da taxa de transferência total se baseia nas medidas de vários túneis agregados por meio de um único gateway. O Benchmark Agregado de Taxa de Transferência para um Gateway de VPN é uma combinação de S2S + P2S. **Se você tiver muitas conexões P2S, isso poderá afetar negativamente uma conexão S2S devido a limitações de taxa de transferência.** O Parâmetro de Comparação de Taxa de Transferência Agregada não é uma taxa de transferência garantida devido às condições de tráfego de Internet e seus comportamentos de aplicativo.

* Esses limites de conexão são separados. Por exemplo, você pode ter 128 conexões SSTP e 250 conexões IKEv2 em uma SKU VpnGw1.

* Encontre informações sobre preços na página [Preços](https://azure.microsoft.com/pricing/details/vpn-gateway) .

* As informações de SLA (contrato de nível de serviço) podem ser encontradas na página [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* VpnGw1, VpnGw2 e VpnGw3 têm suporte somente pelo modelo de implantação do Gerenciador de Recursos.

* A SKU Básica é considerada um SKU herdada. A SKU básica tem certas limitações de recurso. Você não pode redimensionar um gateway que usa uma SKU básica para novas SKUs de gateway, em vez disso, você deve alterar para uma nova SKU, que envolve excluir e recriar o gateway VPN. Verifique se o recurso que você precisa é compatível antes de usar a SKU básica.
