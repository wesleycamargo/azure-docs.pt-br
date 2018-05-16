---
title: Opções de arquitetura da solução de Monitoramento remoto – Azure | Microsoft Docs
description: Este artigo descreve as opções técnicas e de arquitetura feitas no Monitoramento Remoto
services: iot-suite
suite: iot-suite
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-suite
ms.date: 04/30/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 192a763c01e60d816ae2046587176627fc9d8736
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="remote-monitoring-architectural-choices"></a>Opções de arquitetura de Monitoramento Remoto

O Monitoramento Remoto (RM) do Azure IoT é um acelerador de solução de software livre, licenciado por MIT, que apresenta cenários comuns de IoT como conectividade do dispositivo, gerenciamento de dispositivos e processamento de fluxo, para que os clientes possam acelerar o processo de desenvolvimento.  O RM segue a arquitetura de referência do Azure IoT recomendada publicada [aqui](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/).  

Este artigo descreve as opções técnicas e de arquitetura feitas em cada um dos subsistemas para RM, e aborda as alternativas consideradas.  É importante observar que as opções técnica feitas no RM não são a única maneira de implementar uma solução de IoT de monitoramento remoto.  A implementação técnica é uma linha de base para a criação de um aplicativo com êxito e deve ser modificada para ajustar as habilidades, experiência e necessidades do aplicativo vertical para uma implementação de solução do cliente.

## <a name="architectural-choices"></a>Opções de arquitetura

### <a name="microservices-serverless-and-cloud-native"></a>Microsserviços, sem servidor e nativo de nuvem

As arquiteturas que recomendamos para aplicativos IoT são: nativo de nuvem, microsserviços e sem servidor.  Os diferentes subsistemas de um aplicativo IoT devem ser compilados como serviços discretos que são implantáveis de forma independente e capazes de dimensionar de modo independente.  Esses atributos permitem maior escala, mais flexibilidade na atualização subsistemas individuais e fornecem a flexibilidade de escolher a tecnologia adequada em uma base por subsistema.  Os microsserviços podem ser implementados com várias tecnologias. Por exemplo, usando a tecnologia de contêiner como o Docker com tecnologia sem servidor, como o Azure Functions, ou hospedagem de microsserviços em serviços de PaaS, como Serviços de Aplicativo do Azure.

## <a name="core-subsystem-technology-choices"></a>Opções de tecnologia de subsistema principal

Esta seção fornece detalhes sobre as opções de tecnologia feitas no RM para cada um dos subsistemas principais.

![Diagrama Principal](media/iot-suite-remote-monitoring-architectural-choices/subsystem.png) 

### <a name="cloud-gateway"></a>Gateway de Nuvem
O Hub IoT do Azure é usado como o Gateway de Nuvem do RM.  O Hub IoT oferece comunicação segura e bidirecional com os dispositivos. Você pode aprender mais sobre o Hub IoT [aqui](https://azure.microsoft.com/services/iot-hub/). Para Conectividade de Dispositivo, o .NET Core e os SDKs do Hub IoT Java são usados.  Os SDKs oferecem wrappers em torno da API REST do Hub IoT e lidam com cenários como a repetição. 

### <a name="stream-processing"></a>Processamento de fluxo
Para o Processamento de Fluxo, o RM usa o Azure Stream Analytics para o processamento de regras complexas.  Para clientes que desejam regras mais simples, temos um microsserviço personalizado com suporte para o processamento de regras simples, embora essa configuração não faça parte da implantação imediata. A arquitetura de referência recomenda o uso do Azure Functions para o processamento de regras simples e o Azure Stream Analytics (ASA) para o processamento de regras complexas.  

### <a name="storage"></a>Armazenamento
Para Armazenamento, o Cosmos DB é usado para todas as necessidades de armazenamento: armazenamento frio, armazenamento passivo, armazenamento de regras e alarmes. Atualmente, estamos movendo para o armazenamento de blobs do Azure, conforme recomendado pela arquitetura de referência.  O Cosmos DB é a solução de armazenamento passivo para fins gerais recomendada para aplicativos IoT, embora soluções como o Azure Time Series Insights e o Azure Data Lake sejam apropriadas para vários casos de uso.

### <a name="business-integration"></a>Integração de negócios
A integração de negócios no RM é limitada à geração de alarmes, que são colocados no armazenamento passivo. Mais integrações de negócios podem ser executadas. Basta integrar a solução com os Aplicativos Lógicos do Azure.

### <a name="user-interface"></a>Interface do usuário
A interface do usuário da Web é criada com JavaScript React.  O React oferece uma estrutura de interface do usuário da Web do setor usada com frequência e é semelhante a outras estruturas populares, como o Angular.  

### <a name="runtime-and-orchestration"></a>Tempo de execução e orquestração
O Application Runtime escolhido para a implementação do subsistema no RM são contêineres do Docker com Kubernetes (K8s) como o orquestrador de escala horizontal.  Essa arquitetura permite a definição de escala individual por subsistema. Porém, ela gera custos de DevOps para manter as VMs e os contêineres atualizados de uma perspectiva de segurança.  As alternativas para Docker & K8s incluem a hospedagem de microsserviços nos serviços de PaaS (por exemplo, Serviço de Aplicativo do Azure) ou usando o Service Fabric, DCOS, Swarm, entre outros, como um orquestrador.

## <a name="next-steps"></a>Próximas etapas
* Implante sua solução de RM [aqui](https://www.azureiotsuite.com/).
* Explore o código do GitHub em [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) e [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* Saiba mais sobre a Arquitetura de Referência do IoT [aqui](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/).
