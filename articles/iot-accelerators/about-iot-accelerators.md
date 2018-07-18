---
title: Introdução aos aceleradores de solução do Azure IoT | Microsoft Docs
description: Saiba mais sobre os aceleradores de solução de IoT do Azure. Os aceleradores de solução de IoT são soluções de IoT completas, de ponta a ponta e prontas para implantação.
author: dominicbetts
ms.author: dobett
ms.date: 06/07/2018
ms.topic: overview
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: timlt
ms.openlocfilehash: 2a4f0b035ce80809a678731a50921791fc0db928
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097981"
---
# <a name="what-are-azure-iot-solution-accelerators"></a>O que são aceleradores de solução do Azure IoT?

Uma solução de IoT baseada em nuvem normalmente usa o código personalizado e vários serviços de nuvem para gerenciar a conectividade do dispositivo, o processamento de dados e análise e apresentação.

Os aceleradores de solução de IoT são uma coleção de soluções de IoT completas, prontas para implantação, que implementam cenários comuns de IoT, como monitoramento remoto, fábrica conectada e manutenção preditiva. Quando você implanta um acelerador de solução, a implantação inclui todos os serviços baseados em nuvem necessários junto com qualquer código de aplicativo necessário.

Os aceleradores de solução são pontos de partida para suas próprias soluções de IoT. O código-fonte para todos os aceleradores de solução é um software livre e está disponível no GitHub. Recomenda-se baixar e [personalizar](iot-accelerators-remote-monitoring-customize.md) os aceleradores de solução para atender às suas necessidades.

Você também pode usar os aceleradores de solução como ferramentas de aprendizado antes de compilar uma solução de IoT personalizada do zero. Os aceleradores de solução implementam práticas comprovadas para soluções de IoT baseadas em nuvem para você seguir.

O código do aplicativo em cada acelerador de solução inclui um painel que permite que você gerencie o acelerador de solução. Por exemplo, você pode usar um painel para exibir a telemetria dos seus dispositivos conectados, provisionar novos dispositivos ou atualizar o firmware em seus dispositivos conectados:

[![Painel da solução](./media/about-iot-accelerators/dashboard-inline.png)](./media/about-iot-accelerators/dashboard-expanded.png#lightbox)

## <a name="supported-iot-scenarios"></a>Cenários de IoT com suporte

Atualmente, há quatro aceleradores de soluções disponíveis para implantação:

### <a name="remote-monitoring"></a>Monitoramento remoto

Use este acelerador de solução para coletar a telemetria de vários dispositivos remotos e controlá-los. Os exemplos de dispositivo incluem sistemas de arrefecimento instalados no local dos seus clientes ou válvulas instaladas em estações de bombeamento remotas.

### <a name="connected-factory"></a>Fábrica conectada

Use este acelerador de solução para coletar a telemetria de ativos industriais com uma interface de [Arquitetura Unificada OPC](https://opcfoundation.org/about/opc-technologies/opc-ua/) e para controlá-los. Os ativos industriais podem incluir montagem e teste de estações em uma linha de produção de fábrica.

### <a name="predictive-maintenance"></a>Manutenção preditiva

Use este acelerador de solução para prever quando um dispositivo remoto deve falhar, para que você possa realizar manutenção antes que a falha prevista ocorra. Este acelerador de solução usa algoritmos de aprendizado de máquina para prever falhas de telemetria do dispositivo. Os exemplos de dispositivo incluem motores de avião ou elevadores.

### <a name="device-simulation"></a>Simulação de dispositivo

Use este acelerador de solução para executar vários dispositivos simulados que geram telemetria realista. Você pode usar este acelerador de solução para testar o comportamento de outros aceleradores de solução ou testar suas próprias soluções personalizadas de IoT.

## <a name="design-principles"></a>Princípios de design

Todos os aceleradores de solução seguem os mesmos princípios de design e metas. Eles são projetados para serem:

* **Escalonáveis**, permitindo que você conecte e gerencie milhões de dispositivos conectados.
* **Extensíveis**, permitindo que você personalize-os para atender às suas necessidades.
* **Compreensíveis**, permitindo que você entenda como eles funcionam e como eles são implementados.
* **Modulares**, permitindo que você troque os serviços por alternativas.
* **Seguros**, combinando a segurança do Azure com recursos internos de segurança do dispositivo e de conectividade.

## <a name="architectures-and-languages"></a>Arquiteturas e linguagens

Os aceleradores de solução originais foram escritos usando o .NET usando uma arquitetura de model-view-controller (MVC). A Microsoft está atualizando os aceleradores de solução para uma nova arquitetura de microsserviços. Ambas as versões [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) e [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) de cada microsserviço estão disponíveis no GitHub. A tabela a seguir mostra o status atual dos aceleradores de solução:

| Acelerador de solução   | Arquitetura  | Linguagens     |
| ---------------------- | ------------- | ------------- |
| Monitoramento remoto      | Microsserviços | Java e .NET |
| Manutenção preditiva | MVC           | .NET          |
| Fábrica conectada      | MVC           | .NET          |

Para saber mais sobre as arquiteturas de microsserviço, confira [Arquitetura do Aplicativo .NET](https://www.microsoft.com/net/learn/architecture) e [Microsserviços: uma revolução de aplicativo fornecida pela nuvem](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="deployment-options"></a>Opções de implantação

Você pode implantar os aceleradores de solução baseados em microsserviços nas seguintes configurações:

* **Standard**: Implantação de infraestrutura expandida para o desenvolvimento de uma implantação de produção. O Serviço de Contêiner do Azure implanta os microsserviços em várias máquinas virtuais do Azure. O Kubernetes orquestra os contêineres do Docker que hospedam os microsserviços individuais.
* **Básica**: Versão de custo reduzido para obter uma demonstração ou testar uma implantação. Todos os microsserviços implantam em uma única máquina virtual do Azure.
* **Local:** Implantação da máquina local para teste e desenvolvimento. Essa abordagem implanta os microsserviços em um contêiner do Docker local e conecta-se ao Hub IoT, Azure Cosmos DB e serviços de armazenamento do Azure na nuvem.

O custo de executar um acelerador de solução é uma agregação do [custo de serviços subjacentes do Azure](https://azure.microsoft.com/pricing). Você verá os detalhes dos serviços do Azure usados quando você escolher as opções de implantação.

## <a name="next-steps"></a>Próximas etapas

Para testar um acelerador de solução de IoT, confira o início rápido para [Implantar uma solução de monitoramento remoto baseada em nuvem](quickstart-remote-monitoring-deploy.md).
