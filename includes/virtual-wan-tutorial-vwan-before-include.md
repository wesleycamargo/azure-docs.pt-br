---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 09f642a4c96781276d225cba37d71386d429d0c9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47004114"
---
Verifique se você atende aos seguintes critérios antes de iniciar a configuração:

* Se você já tiver uma rede virtual à qual deseja se conectar, verifique se nenhuma das sub-redes da rede local se sobrepõe às redes virtuais que serão conectadas. Sua rede virtual não requer uma sub-rede de gateway e não pode ter gateways de rede virtual. Se você não tiver uma rede virtual, poderá criar uma usando as etapas neste artigo.
* Obtenha um intervalo de endereços IP para sua região de hub. O hub é uma rede virtual, e o intervalo de endereços especificado para a região de hub não pode se sobrepor a alguma rede virtual à qual você se conecta. Ele também não pode se sobrepor aos intervalos de endereços aos quais você se conecta localmente. Se não estiver familiarizado com os intervalos de endereços IP localizados na configuração de rede local, você precisará trabalhar em conjunto com alguém que possa lhe fornecer os detalhes.
* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.