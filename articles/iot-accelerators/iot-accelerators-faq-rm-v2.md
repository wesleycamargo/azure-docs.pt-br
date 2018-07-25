---
title: Perguntas frequentes sobre o acelerador de solução de monitoramento remoto | Microsoft Docs
description: Perguntas frequentes sobre o acelerador de solução de Monitoramento Remoto
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 21e02a9ae4679c1f9521cc188a6f72878276fb93
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075058"
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>Perguntas frequentes sobre o acelerador de solução de Monitoramento Remoto

Consulte também as [Perguntas frequentes](iot-accelerators-faq.md) gerais.

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Quanto custa para provisionar a nova solução de monitoramento remoto?

O novo acelerador de solução oferece duas opções de implantação:

* Uma opção *básica* projetada para desenvolvedores que buscam menor custo de desenvolvimento ou clientes que querem criar uma demonstração ou prova de conceito.
* Uma opção *padrão* projetada para empresas que desejam implantar uma infraestrutura pronta para produção.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Como garantir que mantenho meus custos durante o desenvolvimento de minha solução?

Além de fornecer duas implantações diferenciadas, a nova solução de monitoramento remoto tem uma configuração para habilitar ou desabilitar todos os dispositivos simulados sob demanda. Desabilitar a simulação reduz os dados ingeridos na solução e, portanto, o custo geral.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Qual é a diferença entre as opções de implantação padrão e básica? Como decidir entre as duas opções de implantação?

Cada opção de implantação responde a diferentes necessidades. A implantação básica foi desenvolvida para iniciar e desenvolver PoC e pilotos pequenos. Ela fornece uma arquitetura simplificada com o mínimo necessário de recursos e um custo mais baixo. A implantação padrão foi projetada para compilar e personalizar uma solução pronta para produção e fornece uma implantação com os elementos necessários para observar isso. Para confiabilidade e escala, os microsserviços de aplicativo são compilados como contêineres do Docker e implantados usando um orquestrador (Kubernetes por padrão). O orquestrador é responsável pela implantação, colocação em escala e gerenciamento do aplicativo. Você deve escolher uma opção com base em suas necessidades atuais. Você pode usar um, o outro ou uma combinação de ambos, dependendo do estágio do projeto.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Como configurar um mapa dinâmico no painel?

Para obter mais informações, consulte [Atualizar chave de mapa para ver dispositivos em um mapa dinâmico](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map).

### <a name="next-steps"></a>Próximas etapas

Você também pode explorar alguns dos outros recursos dos aceleradores de solução de IoT:

* [Explorar os recursos do acelerador da solução de Monitoramento Remoto](iot-accelerators-remote-monitoring-explore.md)
* [Visão geral do acelerador de solução de Manutenção Preditiva](iot-accelerators-predictive-overview.md)
* [Implantar o Acelerador de solução de fábrica Conectada](quickstart-connected-factory-deploy.md)
* [Segurança da IoT desde o início](/azure/iot-fundamentals/iot-security-ground-up)
