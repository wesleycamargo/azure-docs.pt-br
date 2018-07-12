---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/17/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d9c8a0e6a3bd6d79a11ee0d0dab0500a209e5571
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38944134"
---
No Azure, você abre uma porta, ou cria um ponto de extremidade, para uma VM (máquina virtual) criando um filtro de rede ou uma sub-rede ou interface de rede de VM. Coloque os filtros, que controlam o tráfego de entrada e saída, em um Grupo de Segurança de Rede anexado ao recurso que recebe o tráfego.

Vamos usar um exemplo comum de tráfego da Web na porta 80. Quando você tiver uma VM configurada para atender a solicitações da Web na porta TCP 80 padrão (lembre-se de iniciar os serviços apropriados e abrir quaisquer regras de firewall de SO na VM), você:

1. Criará um Grupo de Segurança de Rede.
2. Criará uma regra de entrada permitindo o tráfego com:
   * o intervalo de porta de destino de "80"
   * intervalo de porta de origem " * " (permitindo qualquer porta de origem)
   * um valor de prioridade inferior a 65.500 (para que a prioridade seja maior do que a regra de negação de entrada padrão catch-all)
3. Associe o Grupo de Segurança de Rede à interface de rede da VM ou sub-rede.

Você pode criar configurações de rede complexas para proteger seu ambiente usando regras e Grupos de Segurança de Rede. Nosso exemplo usa apenas uma ou duas regras que permitem o tráfego HTTP ou gerenciamento remoto. Para saber mais, confira a seção [’Mais informações’](#more-information-on-network-security-groups) abaixo ou [O que é um Grupo de Segurança de Rede?](../articles/virtual-network/security-overview.md)

