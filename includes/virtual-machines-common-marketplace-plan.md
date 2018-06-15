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
ms.openlocfilehash: a3c5290eb0179fe5842c495c2e08f22580d02bda
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2018
ms.locfileid: "29743354"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Implantar uma imagem com termos do Marketplace

Determinadas imagens de VM no Azure Marketplace têm licenças e termos de compra adicionais que você deve aceitar antes de implantá-las programaticamente.  

Para implantar uma VM por meio de uma dessas imagens, você precisa aceitar os termos da imagem e habilitar a implantação programática. Você só precisa fazer isso uma vez na sua assinatura. Em seguida, cada vez que você implantar uma VM programaticamente por meio da imagem, também precisará especificar os parâmetros do *plano de compra*.

As seções a seguir mostram como:

* Descobrir se uma imagem do Marketplace tem termos de licença adicionais 
* Aceitar os termos de forma programática
* Fornecer parâmetros de plano de compra quando você implantar uma VM de forma programática

