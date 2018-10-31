---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6c7a9857f6d6d57dc9e314bcb1ef848a326b7ed2
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2018
ms.locfileid: "49437030"
---
## <a name="terminology"></a>Terminologia

Uma imagem do Marketplace no Azure tem os seguintes atributos:

* **Publicador**: a organização que criou a imagem. Exemplos: Canonical, MicrosoftWindowsServer
* **Oferta**: Nome de um grupo de imagens relacionadas criadas por um publicador. Exemplos: Ubuntu Server, WindowsServer
* **SKU**: uma instância de uma oferta, como uma versão principal de uma distribuição. Exemplos: 16.04-LTS, 2016-Datacenter
* **Versão**: o número de versão de uma imagem de SKU. 

Para identificar uma imagem do Marketplace quando implantar uma VM de forma programática, forneça esses valores individualmente como parâmetros, ou algumas ferramentas aceitam a imagem URN. Algumas ferramentas aceitam uma imagem *URN*, que combina esses valores, separados por dois pontos (:) caractere: *Publicador*: *Oferta*: *Sku* : *versão*. Em uma URN, substitua o número de versão por "mais recente", o que seleciona a versão mais recente da imagem. 

Se o editor de imagens fornecer licenças adicionais e termos de compra, você deverá aceitar esses termos e ativar a implantação programática. Você também precisará fornecer *parâmetros do plano de compra* ao implantar uma VM programaticamente. Consulte [Implantar uma imagem com termos do Marketplace](#deploy-an-image-with-marketplace-terms).