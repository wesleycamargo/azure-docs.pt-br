---
title: "Arquitetura da solução de monitoramento remota – Azure | Microsoft Docs"
description: "Um passo a passo sobre a arquitetura da solução pré-configurada de monitoramento remoto."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: a4b28e8a1269374a24e169f9363401109bacc471
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2017
---
# <a name="remote-monitoring-preconfigured-solution-architecture"></a>Arquitetura da solução pré-configurada de monitoramento remoto

A [solução pré-configurada](iot-suite-what-are-preconfigured-solutions.md) de monitoramento remoto do IoT Suite implementa uma solução de monitoramento de ponta a ponta para vários computadores em locais remotos. A solução combina os principais serviços do Azure para fornecer uma implementação genérica de cenário de negócios. Você pode usar a solução como um ponto de partida para sua própria implementação e [personalizá-la](iot-suite-remote-monitoring-customize.md) para atender às suas próprias necessidades de negócios específicas.

Este artigo explica alguns dos principais elementos da solução de monitoramento remoto para que você possa entender como ela funciona. Esse conhecimento ajuda a:

* Solucionar problemas na solução.
* Planejar como personalizar a solução para atender a seus próprios requisitos específicos.
* Criar sua própria solução IoT que usa os serviços do Azure.

## <a name="logical-architecture"></a>Arquitetura lógica

O diagrama a seguir descreve os componentes lógicos da solução pré-configurada de monitoramento remoto sobreposta na [arquitetura da IoT](iot-suite-what-is-azure-iot.md):

![Arquitetura lógica](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Por que os microsserviços?

A arquitetura de nuvem evoluiu desde que a Microsoft lançou as primeira soluções pré-configuradas. Os [microsserviços](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) foram lançados como uma prática comprovada para obter flexibilidade e escala sem sacrificar a velocidade de desenvolvimento. Vários serviços da Microsoft usam esse padrão de arquitetura internamente, com resultados de escalabilidade e confiabilidade excelentes. As soluções pré-configuradas atualizadas colocam estas lições em prática para que você também possa se beneficiar delas.

> [!TIP]
> Para saber mais sobre as arquiteturas de microsserviço, consulte [Arquitetura do Aplicativo .NET](https://www.microsoft.com/net/learn/architecture) e [Microsserviços: uma revolução de aplicativo fornecida pela nuvem](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="device-connectivity"></a>Conectividade do dispositivo

A solução inclui os seguintes componentes na parte de conectividade do dispositivo da arquitetura lógica:

### <a name="simulated-devices"></a>Dispositivos simulados

A solução inclui um microsserviço que permite que você gerencie um pool de dispositivos simulados para testar o fluxo de ponta a ponta na solução. Os dispositivos simulados:

* Geram telemetria do dispositivo para a nuvem.
* Respondem a chamadas de método da nuvem para o dispositivo do Hub IoT.

O microsserviço fornece um ponto de extremidade RESTful para criar, iniciar e interromper as simulações. Cada simulação consiste em um conjunto de dispositivos virtuais de tipos diferentes, que enviam a telemetria e respondem a chamadas de método.

Você pode provisionar os dispositivos simulados do painel no portal de solução.

### <a name="physical-devices"></a>Dispositivos físicos

Você pode conectar os dispositivos físicos à solução. Você pode implementar o comportamento de seus dispositivos simulados usando as SDKs do dispositivo IoT do Azure.

Você pode provisionar os dispositivos físicos do painel no portal de solução.

### <a name="iot-hub-and-the-iot-manager-microservice"></a>Hub IoT e microsserviço do gerente de IoT

O [Hub IoT](../iot-hub/index.md) consome os dados enviados dos dispositivos para a nuvem e os disponibiliza para o microsserviço `telemetry-agent`.

O hub IoT na solução também:

* Mantém um registro de identidade que armazena as IDs e as chaves de autenticação de todos os dispositivos permitidos para se conectar ao portal. Você pode habilitar e desabilitar dispositivos por meio do registro de identidade.
* Chama métodos em seus dispositivos em nome do portal de solução.
* Mantém dispositivos gêmeos para todos os dispositivos registrados. Um dispositivo gêmeo armazena os valores de propriedade relatados por um dispositivo. Um dispositivo gêmeo também armazena propriedades desejadas, definidas no portal de solução para o dispositivo recuperar ao lado se conectar.
* Agenda trabalhos para definir propriedades para vários dispositivos ou chama métodos em vários dispositivos.

A solução inclui o microsserviço `iot-manager` para lidar com as interações com o Hub IoT, como:

* Criação e gerenciamento de dispositivos de IoT.
* Gerenciamento de dispositivos gêmeos.
* Invocação de métodos em dispositivos.
* Gerenciamento de credenciais de IoT.

Este serviço também executa consultas do Hub IoT para recuperar os dispositivos que pertencem a grupos definidos pelo usuário.

O microsserviço fornece um ponto de extremidade RESTful para gerenciar os dispositivos e os dispositivos gêmeos, invocar métodos e executar consultas do Hub IoT.

## <a name="data-processing-and-analytics"></a>Processamento de dados e análise

A solução inclui os seguintes componentes na parte de análise da arquitetura lógica e processamento de dados:

### <a name="device-telemetry"></a>Telemetria de dispositivo

A solução inclui dois microsserviços para lidar com a telemetria do dispositivo.

O microsserviço [agente de telemetria](https://github.com/Azure/telemetry-agent-dotnet):

* Armazena a telemetria no Azure Cosmos DB.
* Analisa o fluxo de telemetria dos dispositivos.
* Gera alarmes de acordo com as regras definidas.

Os alarmes são armazenados no Cosmos DB.

O microsserviço `telemetry-agent` permite que o portal de soluções leia a telemetria enviada dos dispositivos. O portal de soluções também usa esse serviço para:

* Definir as regras de monitoramento, como os limites que disparam alarmes
* Recuperar a lista dos alarmes anteriores.

Use o ponto de extremidade RESTful fornecido por esse microsserviço para gerenciar a telemetria, as regras e os alarmes.

### <a name="storage"></a>Armazenamento

O microsserviço [storage-adapter](https://github.com/Azure/pcs-storage-adapter-dotnet) é um adaptador na frente do serviço de armazenamento principal usado para a solução pré-configurada. Ele fornece a coleção simples e o armazenamento de chave-valor.

A implantação padrão da solução pré-configurada usa o Azure Cosmos DB como seu serviço de armazenamento principal.

O banco de dados Azure Cosmos DB armazena dados na solução pré-configurada. O microsserviço **storage-adapter** atua como um adaptador de outros microsserviços na solução para acessar os serviços de armazenamento.

## <a name="presentation"></a>Apresentação

A solução inclui os seguintes componentes na parte de apresentação da arquitetura lógica:

A [interface do usuário da web é um aplicativo do React Javascript](https://github.com/Azure/pcs-remote-monitoring-webui). O aplicativo:

* Usa somente o Javascript React e é executado totalmente no navegador.
* É projetado com CSS.
* Interage com microsserviços voltados ao público por meio de chamadas AJAX.

A interface do usuário apresenta todas as funcionalidades da solução pré-configurada e interage com outros serviços, como:

* O microsserviço [authentication](https://github.com/Azure/pcs-auth-dotnet) para proteger os dados de usuário.
* O microsserviço [iothub-manager](https://github.com/Azure/iothub-manager-dotnet) para listar e gerenciar os dispositivos de IoT.

O microsserviço [ui-config](https://github.com/Azure/pcs-config-dotnet) habilita a interface do usuário para armazenar e recuperar as definições de configuração.

## <a name="next-steps"></a>Próximas etapas

Se você quiser explorar a documentação do desenvolvedor e do código-fonte, inicie com um os dois repositórios principais GitHub:

* [Solução pré-configurada de monitoramento remoto com o Azure IoT (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/).
* [Solução pré-configurada de monitoramento remoto com o Azure IoT (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

Para obter mais informações conceituais sobre a solução pré-configurada de monitoramento remota, consulte [Customize the preconfigured solution](iot-suite-remote-monitoring-customize.md) (Personalizar a solução pré-configurada).
