---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 09/12/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ec6cbcbc93fe87634c87caeb0041b75ec916a22f
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48888537"
---
No Azure, você abre uma porta, ou cria um ponto de extremidade, para uma VM (máquina virtual) criando um filtro de rede ou uma sub-rede ou adaptador de rede de VMs. Coloque os filtros, que controlam o tráfego de entrada e de saída, em um grupo de segurança de rede anexado ao recurso que recebe o tráfego.

O exemplo neste artigo demonstra como criar um filtro de rede que usa a porta TCP 80 padrão (supõe-se que você já iniciou os serviços adequados e abriu quaisquer regras de firewall do sistema operacional na VM).

Após criar uma VM configurada para servir solicitações da Web na porta TCP 80 padrão, é possível:

1. Crie um grupo de segurança de rede.

2. Criar uma regra de segurança de entrada que permite o tráfego e atribuir valores às seguintes configurações:

   - **Intervalos de portas de destino**: 80

   - **Intervalos de porta de origem**: * (permite qualquer porta de origem)

   - **Valor de prioridade**: insira um valor menor do que 65.500 e maior em prioridade do que a regra de entrada de negação padrão que captura tudo.

3. Associe o grupo de segurança de rede ao adaptador de rede de VMs ou à sub-rede.

Embora esse exemplo use uma regra simples para permitir o tráfego HTTP, também é possível usar regras e grupos de segurança de rede para criar configurações de rede mais complexas. 




