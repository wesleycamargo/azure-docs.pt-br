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
ms.openlocfilehash: ae36adc78d8c87d85c64fd61cb3a50dfcae60b85
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2019
ms.locfileid: "53995642"
---
Você paga por duas coisas: os custos de computação por hora para o gateway de rede virtual, e transferência de dados de saída do gateway de rede virtual. Encontre informações sobre preços na página [Preços](https://azure.microsoft.com/pricing/details/vpn-gateway) .

**Custos de computação do gateway de rede virtual**<br> Cada gateway de rede virtual tem um custo de computação por hora. O preço tem base no SKU do gateway que você especifica ao criar um gateway de rede virtual. O custo é para o próprio gateway e complementa a transferência de dados que fluem através do gateway. O custo de uma configuração ativa-ativa é a mesma que ativa-passiva.

**Custos de transferência de dados**<br>Os custos de transferência de dados são calculados com base no tráfego de saída do gateway de rede virtual de origem.

* Se você estiver enviando o tráfego para o seu dispositivo VPN local, ele será cobrado com a taxa de transferência de dados de saída da Internet.
* Se você estiver enviando tráfego entre redes virtuais em regiões diferentes, os preços terão base na região.
* Se você estiver enviando o tráfego somente entre redes virtuais que estão na mesma região, não haverá custo de dados. O tráfego entre VNets na mesma região é gratuito.