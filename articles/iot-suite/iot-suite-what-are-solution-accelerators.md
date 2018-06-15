---
title: Visão geral dos aceleradores de solução do Azure IoT | Microsoft Docs
description: Uma descrição dos aceleradores de solução do Azure IoT e sua arquitetura com links para recursos adicionais.
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 59009f37-9ba0-4e17-a189-7ea354a858a2
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 17/01/2018
ms.author: dobett
ms.openlocfilehash: 2dc286dd228b1990e0307476d3623ffc91489f98
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33779915"
---
# <a name="what-are-the-iot-solution-accelerators"></a>O que são aceleradores de solução de IoT?

Os _aceleradores de solução_ do Azure IoT são um conjunto de soluções que:

* Implanta em minutos
* Ajuda-o a começar rapidamente
* Você pode personalizar para atender às suas necessidades específicas

Os aceleradores de solução são todas projetadas de acordo com os mesmos princípios e metas.

O vídeo a seguir apresenta uma visão geral do acelerador de solução de monitoramento remoto:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Meet-the-new-Remote-Monitoring-accelerator-for-Azure-IoT/Player]

## <a name="solution-accelerators-overview"></a>Visão geral dos aceleradores de solução

Um acelerador de solução é uma implementação de código aberto de padrões de uma solução IoT comum que pode ser implantada no Azure usando sua assinatura. Cada acelerador de solução combina o código personalizado e os serviços do Azure para implementar um ou mais cenários específicos do IoT. Você pode personalizar qualquer um dos cenários para atender às suas necessidades específicas. Esses cenários incluem:

* Visualizar dados em um painel avançado para insights aprofundados e status de solução.
* Configurar regras e alarmes sobre a telemetria de dispositivo IoT ao vivo.
* Agendar trabalhos de gerenciamento de dispositivo, como atualizações de software e de configurações.
* Provisionar seus próprios dispositivos personalizados físicos ou simulados.
* Solucionar e corrigir problemas dentro de seus grupos do dispositivo IoT.

Cada acelerador de solução é uma implementação completa de ponta a ponta que pode usar dispositivos simulados ou físicos para gerar telemetria. Você pode usar os aceleradores de solução como aceleradores de solução para:

* Fornecer um ponto de partida para suas próprias soluções de IoT.
* Saber mais sobre os padrões comuns no design e desenvolvimento da solução de IoT.

Três aceleradores de solução estão disponíveis atualmente:

* [Monitoramento remoto](iot-suite-remote-monitoring-explore.md)
* [Manutenção preditiva](iot-suite-predictive-overview.md)
* [Fábrica Conectada](iot-suite-connected-factory-overview.md)

A tabela a seguir mostra como essas soluções são mapeadas para recursos específicos de IoT:

| Solução | Ingestão de dados | Identidade do dispositivo | Gerenciamento de dispositivos | Processamento de borda | Comando e controle | Regras e ações | Análise preditiva |
| ------------------------------------------------------------ | -- | -- | -- | -- | -- | -- | -- |
| [Monitoramento remoto](iot-suite-remote-monitoring-explore.md)  |sim |sim |sim |-   |sim |sim |-   |
| [Manutenção preditiva](iot-suite-predictive-overview.md)   |sim |sim |-   |-   |sim |sim |sim |
| [Fábrica Conectada](iot-suite-connected-factory-overview.md) |sim |- |- |sim |sim |sim |-   |

* *Ingestão de dados*: entrada de dados em escala para a nuvem.
* *Identidade do dispositivo*: gerenciar identidades exclusivas de dispositivo e controlar o acesso do dispositivo à solução.
* *Gerenciamento de dispositivo*: gerenciar metadados de dispositivo e executar operações como atualizações de firmware e reinicializações do dispositivo.
* *Comando e controle*: para fazer o dispositivo realizar uma ação, envie mensagens a um dispositivo pela nuvem.
* *Regras e ações*: para atuar em dados específicos do dispositivo para a nuvem, o back-end da solução usa regras.
* *Análise preditiva*: o back-end da solução analisa dados do dispositivo para a nuvem para prever quando ações específicas devem ocorrer. Por exemplo, analisar a telemetria de motores de aeronave para determinar quando deve ser realizada a manutenção do motor.

> [!NOTE]
> Para implantar um acelerador de solução e saber mais sobre como personalizá-lo, visite [aceleradores de solução do Microsoft Azure IoT](https://www.azureiotsuite.com/).

## <a name="azure-services"></a>Serviços do Azure

Quando você implanta um acelerador de solução, o processo de provisionamento configura define uma quantidade de serviços do Azure. A tabela a seguir mostra os serviços usados nos aceleradores de solução:

|                      | Monitoramento remoto  | Manutenção preditiva | Fábrica Conectada |
| -------------------- | ------------------ | ---------------------- | ----------------- |
| Hub IoT              | sim                | sim                    | sim               |
| Hubs de Eventos           |                    | sim                    |                   |
| Time Series Insights |                    |                        | sim               |
| Serviços de Contêiner   | sim                |                        |                   |
| Stream Analytics     |                    | sim                    |                   |
| Aplicativos Web             | sim                | sim                    | sim               |
| Cosmos DB            | sim                | sim                    |                    |
| Armazenamento do Azure         |                    | sim                    | sim               |

> [!NOTE]
> Para obter mais informações sobre os recursos implantados no acelerador de solução de monitoramento remoto, consulte este [artigo](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/blob/master/README.md#basic-vs-standard-deployments) no GitHub.

* [Hub IoT do Azure](../iot-hub/index.yml). Esse serviço fornece os recursos de mensagens do dispositivo para a nuvem e da nuvem para o dispositivo e age como o gateway para a nuvem e para outros serviços importantes do acelerador de solução. O serviço permite que você receba mensagens de seus dispositivos em escala e envie comandos para seus dispositivos. O serviço também permite que você [gerencie seus dispositivos](../iot-hub/iot-hub-device-management-overview.md). Por exemplo, você pode configurar, reinicializar ou executar uma redefinição de fábrica em um ou mais dispositivos conectados ao hub.
* [Hubs de Eventos do Azure](../event-hubs/index.md). Esse serviço fornece a ingestão de grandes volumes de evento para a nuvem. Confira a [Comparação do Hub IoT do Azure e Hubs de Eventos do Azure](../iot-hub/iot-hub-compare-event-hubs.md).
* [Azure Time Series Insights](../time-series-insights/index.yml). Os aceleradores de solução usam esse serviço para analisar e exibir os dados de telemetria de seus dispositivos.
* [Serviço de Contêiner do Azure](../container-service/index.yml). Esse serviço hospeda e gerencia os microsserviços nos aceleradores de solução.
* [Azure Cosmos DB](../cosmos-db/index.yml) e [armazenamento do Azure](../storage/index.yml) para armazenamento de dados.
* [Azure Stream Analytics](../stream-analytics/index.yml). A solução pré-configurada da manutenção preditiva usa esse serviço para processar telemetria de entrada, realizar agregação e detectar eventos. Essa solução pré-configurada também usa a análise de stream para processar mensagens informativas que contenham dados como metadados ou respostas de comando de dispositivos.
* [Aplicativos Web do Azure](../app-service/index.yml) para hospedar o código de aplicativo personalizado nas soluções pré-configuradas.

Para obter uma visão geral da arquitetura de uma solução de IoT típica, confira [Microsoft Azure e a IoT (Internet das Coisas)](iot-suite-what-is-azure-iot.md).

## <a name="whats-new-in-solution-accelerators"></a>O que há de novo nos aceleradores de solução?

A Microsoft está atualizando os aceleradores de solução para uma nova arquitetura baseada em microsserviços. A tabela a seguir mostra o status atual dos aceleradores de solução:

| Acelerador de solução | Arquitetura  | Linguagens     |
| ---------------------- | ------------- | ------------- |
| Monitoramento remoto      | Microsserviços | Java e .NET |
| Manutenção preditiva | MVC           | .NET          |
| Fábrica Conectada      | MVC           | .NET          |

As seções a seguir descrevem o que há de novidade nos aceleradores de solução baseadas em microsserviços:

### <a name="microservices"></a>Microsserviços

A nova versão do acelerador de solução de monitoramento remoto usa uma arquitetura de microsserviços. Este acelerador de solução é composto por vários microsserviços, como um *Gerenciador de Hub IoT* e um *Gerenciador de armazenamento*. As versões Java e .NET de cada microsserviço estão disponíveis para download, juntamente com a documentação do desenvolvedor relacionada. Para obter mais informações sobre os microsserviços, confira [Arquitetura de monitoramento remoto](iot-suite-remote-monitoring-sample-walkthrough.md).

Essa arquitetura de microsserviços é um padrão comprovado para soluções de nuvem que:

* É escalonável.
* Permite extensibilidade.
* É fácil de entender.
* Permite que serviços individuais sejam trocados por alternativas.

> [!TIP]
> Para saber mais sobre as arquiteturas de microsserviço, confira [Arquitetura do Aplicativo .NET](https://www.microsoft.com/net/learn/architecture) e [Microsserviços: uma revolução de aplicativo fornecida pela nuvem](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

Ao implantar a nova versão de monitoramento remoto, você deve selecionar uma das seguintes opções de implantação:

* **Básica**: versão de custo reduzido para obter uma demonstração ou testar uma implantação. Todos os microsserviços implantam em uma única máquina virtual do Azure.
* **Standard**: Implantação de infraestrutura expandida para o desenvolvimento de uma implantação de produção. O Serviço de Contêiner do Azure implanta os microsserviços em várias máquinas virtuais do Azure. O Kubernetes orquestra os contêineres do Docker que hospedam os microsserviços individuais.

### <a name="language-choices-java-and-net"></a>Opções de linguagem: Java e .NET

Implementações de cada microsserviço estão disponíveis em Java e .NET. Como o código .NET, o código-fonte Java é código aberto e pode ser personalizado de acordo com suas necessidades específicas:

* [Repositório GitHub de Monitoramento remoto .NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Repositório GitHub de Monitoramento remoto Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

Se você quiser ver outras implementações de linguagem, adicione uma solicitação para [voz do usuário do Azure IoT](https://feedback.azure.com/forums/321918-azure-iot).

### <a name="react-user-interface-framework"></a>Estrutura de interface do usuário React

A interface do usuário (IU) é criada usando a biblioteca javascript [React](https://facebook.github.io/react/). O código-fonte é código aberto e está disponível para baixar e personalizar.

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma visão geral dos aceleradores de solução de IoT, aqui estão as próximas etapas sugeridas para cada um dos aceleradores de solução:

* [Explorar a solução de Monitoramento Remoto](iot-suite-remote-monitoring-explore.md).
* [Visão geral do acelerador de solução de Manutenção Preditiva](iot-suite-predictive-overview.md).
* [Introdução ao acelerador de solução da Fábrica Conectada](iot-suite-connected-factory-overview.md).

Para obter mais informações sobre as arquiteturas de solução de IoT, confira [Serviços de IoT do Microsoft Azure: arquitetura de referência](http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf).
