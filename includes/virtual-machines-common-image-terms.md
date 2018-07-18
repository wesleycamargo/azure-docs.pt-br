---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 02/28/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 47ef014f31c628fed9d7b2b005e67e8138d3e7e9
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2018
ms.locfileid: "29743353"
---
## <a name="terminology"></a>Terminologia

Uma imagem do Marketplace no Azure tem os seguintes atributos:

* **Publicador**: a organização que criou a imagem. Exemplos: Canonical, MicrosoftWindowsServer
* **Oferta**: Nome de um grupo de imagens relacionadas criadas por um publicador. Exemplos: Ubuntu Server, WindowsServer
* **SKU**: uma instância de uma oferta, como uma versão principal de uma distribuição. Exemplos: 16.04-LTS, 2016-Datacenter
* **Versão**: o número de versão de uma imagem de SKU. 

Para identificar uma imagem do Marketplace quando implantar uma VM de forma programática, forneça esses valores individualmente como parâmetros, ou algumas ferramentas aceitam a imagem *URN*. O URN combina esses valores, separados pelo caractere de dois pontos (:): *Publicador*:*Oferta*:*Sku*:*Versão*. Em uma URN, substitua o número de versão por "mais recente", o que seleciona a versão mais recente da imagem. 

Se o publicador de imagem fornece licenças adicionais e termos de compra, você deverá aceitar esses termos e habilitar a implantação programática. Você também precisa fornecer os parâmetros do *plano de compra* ao implantar uma VM por de forma programática. Consulte [Implantar uma imagem com termos do Marketplace](#deploy-an-image-with-marketplace-terms).