---
title: Perguntas frequentes sobre o Azure IoT Suite | Microsoft Docs
description: Perguntas frequentes sobre o IoT Suite
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2017
ms.author: dobett
ms.openlocfilehash: 63b059cb91956231fd3bafbe9770a04a0b7e347a
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2017
---
# <a name="frequently-asked-questions-for-iot-suite"></a>Perguntas frequentes sobre o IoT Suite

Consulte também as [Perguntas frequentes](iot-suite-faq-cf.md) específicas sobre a fábrica conectada.

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>Onde encontrar o código-fonte para as soluções pré-configuradas?

O código-fonte é armazenado nos seguintes repositórios GitHub:

* [Solução pré-configurada de monitoramento remoto (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Solução pré-configurada de monitoramento remoto (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Solução pré-configurada de manutenção preditiva](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Solução pré-configurada de fábrica conectada](https://github.com/Azure/azure-iot-connected-factory)

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Quanto custa para provisionar a nova solução de monitoramento remoto?

A nova solução pré-configurada oferece duas opções de implantação:

* Uma opção *básica* projetada para desenvolvedores que buscam menor custo de desenvolvimento ou clientes que querem criar uma demonstração ou prova de conceito.
* Uma opção *padrão* projetada para empresas que desejam implantar uma infraestrutura pronta para produção.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Como garantir que mantenho meus custos durante o desenvolvimento de minha solução?

Além de fornecer duas implantações diferenciadas, a nova solução de monitoramento remoto tem uma configuração para habilitar ou desabilitar todos os dispositivos simulados sob demanda. Desabilitar a simulação reduz os dados ingeridos na solução e, portanto, o custo geral.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-preconfigured-solutions"></a>A nova arquitetura de microsserviços está disponível para todas as três soluções pré-configuradas?

Atualmente, apenas a solução de monitoramento remoto usa a arquitetura de microsserviços, pois ela abrange o cenário mais amplo.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Quais são as vantagens que a nova arquitetura de software livre baseada em microsserviços fornecem na nova atualização?

Nos últimos dois anos, arquitetura de nuvem evoluiu bastante. Os microsserviços foram lançados como um ótimo padrão para obter flexibilidade e escala sem sacrificar a velocidade de desenvolvimento. Esse padrão de arquitetura é usado em vários serviços da Microsoft internamente, com resultados de escalabilidade e confiabilidade excelentes. Estamos colocando esse aprendizado em prática para que os clientes se beneficiem dele.

### <a name="is-the-new-preconfigured-solution-available-in-the-same-geographic-region-as-the-existing-solution"></a>A nova solução pré-configurada está disponível na mesma região geográfica que a solução existente?

Sim, o novo monitoramento remoto está disponível nas mesmas regiões geográficas.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Qual é a diferença entre as opções de implantação padrão e básica? Como decidir entre as duas opções de implantação?

Cada opção de implantação responde a diferentes necessidades. A implantação básica foi desenvolvida para iniciar e desenvolver PoC e pilotos pequenos. Ela fornece uma arquitetura simplificada com o mínimo necessário de recursos e um custo mais baixo. A implantação padrão foi projetada para compilar e personalizar uma solução pronta para produção e fornece uma implantação com os elementos necessários para observar isso. Para confiabilidade e escala, os microsserviços de aplicativo são compilados como contêineres do Docker e implantados usando um orquestrador (Kubernetes por padrão). O orquestrador é responsável pela implantação, colocação em escala e gerenciamento do aplicativo. Você deve escolher uma opção com base em suas necessidades atuais. Você pode usar um, o outro ou uma combinação de ambos, dependendo do estágio do projeto.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-suite"></a>Posso continuar meus investimentos existentes no Azure IoT Suite?

Sim. Qualquer solução que existe atualmente continuará a funcionar na sua assinatura do Azure e o código-fonte permanecerá disponível no GitHub.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Qual é a diferença entre excluir um grupo de recursos no portal do Azure e clicar em excluir em uma solução pré-configurada no site azureiotsuite.com?

* Se você excluir a solução pré-configurada no site [azureiotsuite.com](https://www.azureiotsuite.com/), todos os recursos provisionados na criação da solução pré-configurada serão excluídos. Se você adicionou mais recursos ao grupo de recursos, esses recursos também serão excluídos.
* Se você excluir o grupo de recursos no [Portal do Azure](https://portal.azure.com), somente os recursos nesse grupo de recursos serão excluídos. Você também precisa excluir o aplicativo do Azure Active Directory associado à solução pré-configurada.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias do Hub IoT posso provisionar em uma assinatura?

Por padrão, você pode provisionar [10 Hubs IoT por assinatura](../azure-subscription-service-limits.md#iot-hub-limits). Você pode criar um [tíquete de suporte do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar esse limite. Como resultado, uma vez que cada solução pré-configurada provisiona um novo Hub IoT, você só poderá provisionar até 10 soluções pré-configuradas em uma determinada assinatura.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias do Azure Cosmos DB posso provisionar em uma assinatura?

Cinquenta. Você pode criar um [tíquete de suporte do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar esse limite, mas, por padrão, você poderá apenas provisionar 50 instâncias do Cosmos DB por assinatura.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Quantas APIs do Bing Mapas Gratuitas posso provisionar em uma assinatura?

Duas. Você só pode criar dois Bing Mapas de Transações Internas de Nível 1 para planos Enterprise em uma assinatura do Azure. A solução de monitoramento remoto é provisionada por padrão com o plano Transações Internas de Nível 1. Como resultado, você só poderá provisionar até duas soluções de monitoramento remotas em uma assinatura sem modificações.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Posso criar uma solução pré-configurada se possuo o Microsoft Azure para DreamSpark?

Atualmente, você não pode criar uma solução pré-configurada com uma conta do [Microsoft Azure para DreamSpark](https://www.dreamspark.com/Product/Product.aspx?productid=99). No entanto, você pode criar uma [conta de avaliação gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos que permite a você criar uma solução pré-configurada.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>Poderei criar uma solução pré-configurada se eu tiver uma assinatura do CSP (Provedor de Soluções na Nuvem)?

Atualmente, não é possível criar uma solução pré-configurada com uma assinatura do CSP (Provedor de Soluções na Nuvem). No entanto, você pode criar uma [conta de avaliação gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos que permite a você criar uma solução pré-configurada.

### <a name="how-do-i-delete-an-aad-tenant"></a>Como posso excluir um locatário do AAD?

Veja a postagem no blog de Eric Golpe, [Passo a passo da exclusão de um locatário do Azure AD](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>Próximas etapas

Você também pode explorar alguns dos outros recursos das soluções pré-configuradas do IoT Suite:

* [Visão geral da solução pré-configurada de manutenção preditiva](iot-suite-predictive-overview.md)
* [Visão geral da solução pré-configurada de fábrica conectada](iot-suite-connected-factory-overview.md)
* [Segurança da IoT desde o início](securing-iot-ground-up.md)