---
title: Perguntas frequentes sobre os aceleradores de solução do Azure IoT | Microsoft Docs
description: Perguntas frequentes sobre os aceleradores de solução do IoT
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
ms.openlocfilehash: 6c8c87dc4f7bb91ce227bbfbaf1cabf0c44d2e0b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Perguntas frequentes sobre os aceleradores de solução do IoT

Consulte também, as [Perguntas frequentes específicas de Fábrica Conectada ](iot-accelerators-faq-cf.md) e as [Perguntas frequentes específicas de monitoramento remoto](iot-accelerators-faq-rm-v2.md) .

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Onde posso encontrar o código-fonte dos aceleradores de solução?

O código-fonte é armazenado nos seguintes repositórios GitHub:

* [Acelerador de solução de Monitoramento Remoto (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Acelerador de solução de Monitoramento Remoto (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Acelerador de solução de Manutenção Preditiva](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Acelerador da solução de Fábrica Conectada](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Quais SDKs posso usar para desenvolver clientes de dispositivos para os aceleradores de solução?

É possível localizar links para as diferentes SDKs do dispositivo IoT de linguagem (C, .NET, Java, Node.js, Python) nos repositórios do GitHub das [SDKs de IoT do Microsoft Azure](https://github.com/Azure/azure-iot-sdks).

Se você estiver usando o dispositivo DevKit, poderá encontrar recursos e exemplos no repositório do GitHub da [SDK de DevKit de IoT](https://github.com/Microsoft/devkit-sdk).

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>A nova arquitetura de microsserviços está disponível para todos os três aceleradores de solução?

Atualmente, apenas a solução de monitoramento remoto usa a arquitetura de microsserviços, pois ela abrange o cenário mais amplo.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Quais são as vantagens que a nova arquitetura de software livre baseada em microsserviços fornecem na nova atualização?

Nos últimos dois anos, arquitetura de nuvem evoluiu bastante. Os microsserviços foram lançados como um ótimo padrão para obter flexibilidade e escala sem sacrificar a velocidade de desenvolvimento. Esse padrão de arquitetura é usado em vários serviços da Microsoft internamente, com resultados de escalabilidade e confiabilidade excelentes. Estamos colocando esse aprendizado em prática para que os clientes se beneficiem dele.

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>O novo acelerador de solução está disponível na mesma região geográfica que a solução existente?

Sim, o novo monitoramento remoto está disponível nas mesmas regiões geográficas.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsuitecom"></a>Qual é a diferença entre excluir um grupo de recursos no portal do Azure e clicar em excluir em um acelerador de solução no site azureiotsuite.com?

* Se você excluir o acelerador de solução no site [azureiotsuite.com](https://www.azureiotsuite.com/), todos os recursos provisionados na criação do acelerador de solução serão excluídos. Se você adicionou mais recursos ao grupo de recursos, esses recursos também serão excluídos.
* Se você excluir o grupo de recursos no [Portal do Azure](https://portal.azure.com), somente os recursos nesse grupo de recursos serão excluídos. Você também precisa excluir o aplicativo do Azure Active Directory associado ao acelerador de solução.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Posso continuar meus investimentos existentes nos aceleradores de solução do Azure IoT?

Sim. Qualquer solução que existe atualmente continuará a funcionar na sua assinatura do Azure e o código-fonte permanecerá disponível no GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias do Hub IoT posso provisionar em uma assinatura?

Por padrão, você pode provisionar [10 Hubs IoT por assinatura](../azure-subscription-service-limits.md#iot-hub-limits). Você pode criar um [tíquete de suporte do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar esse limite. Como resultado, uma vez que cada acelerador de solução provisiona um novo Hub IoT, você só poderá provisionar até 10 aceleradores de solução em uma determinada assinatura.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias do Azure Cosmos DB posso provisionar em uma assinatura?

Cinquenta. Você pode criar um [tíquete de suporte do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar esse limite, mas, por padrão, você poderá apenas provisionar 50 instâncias do Cosmos DB por assinatura.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Quantas APIs do Bing Mapas Gratuitas posso provisionar em uma assinatura?

Duas. Você só pode criar dois Bing Mapas de Transações Internas de Nível 1 para planos Enterprise em uma assinatura do Azure. A solução de monitoramento remoto é provisionada por padrão com o plano Transações Internas de Nível 1. Como resultado, você só poderá provisionar até duas soluções de monitoramento remotas em uma assinatura sem modificações.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Posso criar um acelerador de solução se possuo o Microsoft Azure para DreamSpark?

> [!NOTE]
> O Microsoft Azure para DreamSpark agora é conhecido como Microsoft Imagine para alunos.

Atualmente, você não pode criar um acelerador de solução com uma conta do [Microsoft Azure para DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/). No entanto, você pode criar uma [conta de avaliação gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos que permite a você criar um acelerador de solução.

### <a name="can-i-create-a-solution-accelerator-if-i-have-cloud-solution-provider-csp-subscription"></a>Poderei criar um acelerador de solução se eu tiver uma assinatura do Provedor de Soluções na Nuvem (CSP)?

Atualmente, não é possível criar um acelerador de solução com uma assinatura do Provedor de Soluções na Nuvem (CSP). No entanto, você pode criar uma [conta de avaliação gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos que permite a você criar um acelerador de solução.

### <a name="how-do-i-delete-an-aad-tenant"></a>Como posso excluir um locatário do AAD?

Veja a postagem no blog de Eric Golpe, [Passo a passo da exclusão de um locatário do Azure AD](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>Próximas etapas

Você também pode explorar alguns dos outros recursos dos aceleradores de solução de IoT:

* [Explorar os recursos do acelerador de solução de monitoramento remoto](iot-accelerators-remote-monitoring-explore.md)
* [Visão geral do acelerador de solução de Manutenção Preditiva](../iot-suite/iot-suite-predictive-overview.md)
* [Visão geral do acelerador de solução Connected Factory](iot-accelerators-connected-factory-overview.md)
* [Segurança da IoT desde o início](../iot-suite/securing-iot-ground-up.md)
