---
title: Perguntas Frequentes de monitoramento remoto o Azure IoT Suite | Microsoft Docs
description: Perguntas frequentes sobre a solução pré-configurada de monitoramento remoto do IoT Suite
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: b0d6dbb567f66537c599d7ac3e4337988b3374c0
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
---
# <a name="frequently-asked-questions-for-iot-suite-remote-monitoring-preconfigured-solution"></a>Perguntas frequentes sobre a solução pré-configurada de monitoramento remoto do IoT Suite

Consulte também as [Perguntas frequentes](iot-suite-faq.md) gerais.

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Quanto custa para provisionar a nova solução de monitoramento remoto?

A nova solução pré-configurada oferece duas opções de implantação:

* Uma opção *básica* projetada para desenvolvedores que buscam menor custo de desenvolvimento ou clientes que querem criar uma demonstração ou prova de conceito.
* Uma opção *padrão* projetada para empresas que desejam implantar uma infraestrutura pronta para produção.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Como garantir que mantenho meus custos durante o desenvolvimento de minha solução?

Além de fornecer duas implantações diferenciadas, a nova solução de monitoramento remoto tem uma configuração para habilitar ou desabilitar todos os dispositivos simulados sob demanda. Desabilitar a simulação reduz os dados ingeridos na solução e, portanto, o custo geral.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Qual é a diferença entre as opções de implantação padrão e básica? Como decidir entre as duas opções de implantação?

Cada opção de implantação responde a diferentes necessidades. A implantação básica foi desenvolvida para iniciar e desenvolver PoC e pilotos pequenos. Ela fornece uma arquitetura simplificada com o mínimo necessário de recursos e um custo mais baixo. A implantação padrão foi projetada para compilar e personalizar uma solução pronta para produção e fornece uma implantação com os elementos necessários para observar isso. Para confiabilidade e escala, os microsserviços de aplicativo são compilados como contêineres do Docker e implantados usando um orquestrador (Kubernetes por padrão). O orquestrador é responsável pela implantação, colocação em escala e gerenciamento do aplicativo. Você deve escolher uma opção com base em suas necessidades atuais. Você pode usar um, o outro ou uma combinação de ambos, dependendo do estágio do projeto.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Como configurar um mapa dinâmico no painel?

Para obter mais informações, consulte [Atualizar chave de mapa para ver dispositivos em um mapa dinâmico](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map).

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Quantas APIs do Bing Mapas Gratuitas posso provisionar em uma assinatura?

Duas. Você só pode criar dois Bing Mapas de Transações Internas de Nível 1 para planos Enterprise em uma assinatura do Azure. A solução de monitoramento remoto é provisionada por padrão com o plano Transações Internas de Nível 1. Como resultado, você só poderá provisionar até duas soluções de monitoramento remotas em uma assinatura sem modificações.

### <a name="next-steps"></a>Próximas etapas

Você também pode explorar alguns dos outros recursos das soluções pré-configuradas do IoT Suite:

* [Explorar os recursos da solução de monitoramento remoto pré-configurada](iot-suite-remote-monitoring-explore.md)
* [Visão geral da solução pré-configurada de manutenção preditiva](iot-suite-predictive-overview.md)
* [Visão geral da solução pré-configurada de fábrica conectada](iot-suite-connected-factory-overview.md)
* [Segurança da IoT desde o início](securing-iot-ground-up.md)