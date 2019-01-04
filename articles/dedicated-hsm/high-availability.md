---
title: Alta disponibilidade - HSM Dedicado do Azure | Microsoft Docs
description: Exemplo de alta disponibilidade do HSM Dedicado do Azure e considerações básicas
services: dedicated-hsm
author: barclayn
manager: mbaldwin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 991482d3d6be4d09b37014e5bb03708987e0f74e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53078167"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Alta disponibilidade do HSM Dedicado do Azure

O HSM Dedicado do Azure é sustentado pelos datacenters altamente disponíveis da Microsoft. No entanto, qualquer datacenter altamente disponível é vulnerável a falhas localizadas e, em circunstâncias extremas, a falhas no nível regional. A Microsoft implanta dispositivos HSM em diferentes datacenters em uma região para garantir que o provisionamento de vários dispositivos não leve esses dispositivos a compartilhar um único rack. Um nível adicional de alta disponibilidade pode ser obtido ao emparelhar esses HSMs nos datacenters de uma região. Também é possível emparelhar dispositivos entre regiões para resolver o failover regional em uma situação de recuperação de desastre. Com essa configuração de alta disponibilidade em várias camadas, qualquer falha no dispositivo será automaticamente endereçada para manter os aplicativos funcionando. Todos os datacenters também têm dispositivos e componentes sobressalentes no local para que qualquer dispositivo com falha possa ser substituído em tempo hábil.

## <a name="high-availability-example"></a>Exemplo de alta disponibilidade

Informações sobre como configurar dispositivos HSM para alta disponibilidade no nível do software estão no 'Guia de Administração do HSM da Rede Luna da Gemalto'. Este documento está disponível no [Portal de suporte ao cliente da Gemalto](https://supportportal.gemalto.com/csm/).

O diagrama a seguir mostra uma arquitetura altamente disponível. Ele usa vários dispositivos na região e vários dispositivos emparelhados em uma região separada. Essa arquitetura usa um mínimo de quatro dispositivos HSM e componentes de rede virtual.

![Diagrama de alta disponibilidade](media/high-availability/high-availability.png)

## <a name="next-steps"></a>Próximas etapas

Recomenda-se que todos os principais conceitos do serviço, como alta disponibilidade e segurança, sejam bem compreendidos antes do provisionamento do dispositivo e do design ou implantação do aplicativo.
Mais tópicos de nível de conceito:

* [Arquitetura de implantação](deployment-architecture.md)
* [Segurança física](physical-security.md)
* [Rede](networking.md)
* [Capacidade de suporte](supportability.md)
* [Monitoramento](monitoring.md)

Para obter detalhes específicos sobre a configuração de dispositivos HSM para alta disponibilidade, consulte o Portal de suporte ao cliente da Gemalto para os Guias do administrador e consulte a seção 6.
