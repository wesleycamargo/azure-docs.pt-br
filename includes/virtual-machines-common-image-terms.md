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
ms.openlocfilehash: 19a78b772d2813c263017515f18da06fdb20aa70
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2019
ms.locfileid: "55082258"
---
## <a name="terminology"></a>Terminologia

Uma imagem do Marketplace no Azure tem os seguintes atributos:

* **Editor**: A organização que criou a imagem. Exemplos: Canonical, MicrosoftWindowsServer
* **Oferta**: O nome de um grupo de imagens relacionadas criadas por um editor. Exemplos: Ubuntu Server, WindowsServer
* **SKU**: Uma instância de uma oferta, como uma versão principal de uma distribuição. Exemplos: 18.04-LTS, 2019-Datacenter
* **Versão**: O número da versão de uma SKU da imagem. 

Para identificar uma imagem do Marketplace quando implantar uma VM de forma programática, forneça esses valores individualmente como parâmetros, ou algumas ferramentas aceitam a imagem URN. Algumas ferramentas aceitam uma imagem *URN*, que combina esses valores, separados pelo caractere de dois-pontos (:): *Editor*:*Oferta*:*Sku*:*Versão*. Em uma URN, substitua o número de versão por "mais recente", o que seleciona a versão mais recente da imagem. 

Se o editor de imagens fornecer licenças adicionais e termos de compra, você deverá aceitar esses termos e ativar a implantação programática. Você também precisará fornecer *parâmetros do plano de compra* ao implantar uma VM programaticamente. Consulte [Implantar uma imagem com termos do Marketplace](#deploy-an-image-with-marketplace-terms).