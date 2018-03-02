---
title: Perguntas frequentes sobre o Azure IoT Suite | Microsoft Docs
description: Perguntas frequentes sobre o IoT Suite
services: iot-suite
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
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: c79c90c4f6c28153d4d299015a06a6bc37145081
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="frequently-asked-questions-for-iot-suite"></a>Perguntas frequentes sobre o IoT Suite

Consulte também, as [Perguntas frequentes específicas de alocador conectado ](iot-suite-faq-cf.md) e as [Perguntas frequentes específicas de monitoramento remoto](iot-suite-faq-rm-v2.md) .

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>Onde encontrar o código-fonte para as soluções pré-configuradas?

O código-fonte é armazenado nos seguintes repositórios GitHub:

* [Solução pré-configurada de monitoramento remoto (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Solução pré-configurada de monitoramento remoto (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Solução pré-configurada de manutenção preditiva](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Solução pré-configurada de fábrica conectada](https://github.com/Azure/azure-iot-connected-factory)

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-preconfigured-solutions"></a>A nova arquitetura de microsserviços está disponível para todas as três soluções pré-configuradas?

Atualmente, apenas a solução de monitoramento remoto usa a arquitetura de microsserviços, pois ela abrange o cenário mais amplo.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Quais são as vantagens que a nova arquitetura de software livre baseada em microsserviços fornecem na nova atualização?

Nos últimos dois anos, arquitetura de nuvem evoluiu bastante. Os microsserviços foram lançados como um ótimo padrão para obter flexibilidade e escala sem sacrificar a velocidade de desenvolvimento. Esse padrão de arquitetura é usado em vários serviços da Microsoft internamente, com resultados de escalabilidade e confiabilidade excelentes. Estamos colocando esse aprendizado em prática para que os clientes se beneficiem dele.

### <a name="is-the-new-preconfigured-solution-available-in-the-same-geographic-region-as-the-existing-solution"></a>A nova solução pré-configurada está disponível na mesma região geográfica que a solução existente?

Sim, o novo monitoramento remoto está disponível nas mesmas regiões geográficas.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Qual é a diferença entre excluir um grupo de recursos no portal do Azure e clicar em excluir em uma solução pré-configurada no site azureiotsuite.com?

* Se você excluir a solução pré-configurada no site [azureiotsuite.com](https://www.azureiotsuite.com/), todos os recursos provisionados na criação da solução pré-configurada serão excluídos. Se você adicionou mais recursos ao grupo de recursos, esses recursos também serão excluídos.
* Se você excluir o grupo de recursos no [Portal do Azure](https://portal.azure.com), somente os recursos nesse grupo de recursos serão excluídos. Você também precisa excluir o aplicativo do Azure Active Directory associado à solução pré-configurada.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-suite"></a>Posso continuar meus investimentos existentes no Azure IoT Suite?

Sim. Qualquer solução que existe atualmente continuará a funcionar na sua assinatura do Azure e o código-fonte permanecerá disponível no GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias do Hub IoT posso provisionar em uma assinatura?

Por padrão, você pode provisionar [10 Hubs IoT por assinatura](../azure-subscription-service-limits.md#iot-hub-limits). Você pode criar um [tíquete de suporte do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar esse limite. Como resultado, uma vez que cada solução pré-configurada provisiona um novo Hub IoT, você só poderá provisionar até 10 soluções pré-configuradas em uma determinada assinatura.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias do Azure Cosmos DB posso provisionar em uma assinatura?

Cinquenta. Você pode criar um [tíquete de suporte do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar esse limite, mas, por padrão, você poderá apenas provisionar 50 instâncias do Cosmos DB por assinatura.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Quantas APIs do Bing Mapas Gratuitas posso provisionar em uma assinatura?

Duas. Você só pode criar dois Bing Mapas de Transações Internas de Nível 1 para planos Enterprise em uma assinatura do Azure. A solução de monitoramento remoto é provisionada por padrão com o plano Transações Internas de Nível 1. Como resultado, você só poderá provisionar até duas soluções de monitoramento remotas em uma assinatura sem modificações.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Posso criar uma solução pré-configurada se possuo o Microsoft Azure para DreamSpark?

> [!NOTE]
> O Microsoft Azure para DreamSpark agora é conhecido como Microsoft Imagine para alunos.

Atualmente, você não pode criar uma solução pré-configurada com uma conta do [Microsoft Azure para DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/). No entanto, você pode criar uma [conta de avaliação gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos que permite a você criar uma solução pré-configurada.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>Poderei criar uma solução pré-configurada se eu tiver uma assinatura do CSP (Provedor de Soluções na Nuvem)?

Atualmente, não é possível criar uma solução pré-configurada com uma assinatura do CSP (Provedor de Soluções na Nuvem). No entanto, você pode criar uma [conta de avaliação gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos que permite a você criar uma solução pré-configurada.

### <a name="how-do-i-delete-an-aad-tenant"></a>Como posso excluir um locatário do AAD?

Veja a postagem no blog de Eric Golpe, [Passo a passo da exclusão de um locatário do Azure AD](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>Próximas etapas

Você também pode explorar alguns dos outros recursos das soluções pré-configuradas do IoT Suite:

* [Explorar os recursos da solução de monitoramento remoto pré-configurada](iot-suite-remote-monitoring-explore.md)
* [Visão geral da solução pré-configurada de manutenção preditiva](iot-suite-predictive-overview.md)
* [Visão geral da solução pré-configurada de fábrica conectada](iot-suite-connected-factory-overview.md)
* [Segurança da IoT desde o início](securing-iot-ground-up.md)