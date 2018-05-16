---
title: Opções do Microsoft Azure IoT | Microsoft Docs
description: Escolha como implementar sua solução IoT usando os aceleradores de solução do Azure IoT, a Central IoT da Microsoft ou o Hub IoT do Azure.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.assetid: 2d38d08a-4133-4e5c-8b28-f93cadb5df05
ms.service: iot-suite
ms.topic: get-started-article
ms.date: 11/10/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ddda6bc265cbbaa61025891cf9bc7c28c998160c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="compare-azure-iot-options"></a>Comparar opções do Azure IoT

O artigo [Azure e a Internet das Coisas](iot-suite-what-is-azure-iot.md) descreve uma arquitetura de solução de IoT típica com as seguintes camadas:

* Conectividade e gerenciamento de dispositivo
* Processamento de dados e análise
* Integração de apresentação e negócios

Para implementar essa arquitetura, o Azure IoT oferece várias opções, cada uma apropriada para diferentes conjuntos de requisitos do cliente:

* Os [aceleradores de solução do Azure IoT](index.md) é uma coleção de nível empresarial de [aceleradores de soluções](iot-suite-what-are-solution-accelerators.md) criada na plataforma como serviço do Azure (PaaS) e que permite acelerar o desenvolvimento de soluções personalizadas de IoT.

* A [Microsoft IoT Central](https://www.microsoft.com/internet-of-things/iot-central-saas-solutions) é uma solução de Software como Serviço (SaaS) que usa uma abordagem baseada em modelo para permitir que você crie soluções de IoT de nível empresarial sem a necessidade de experiência em desenvolvimento de solução de nuvem.

## <a name="azure-iot-hub"></a>Hub IoT do Azure

O Hub IoT do Azure é a principal PaaS do Azure usada pela Microsoft IoT Central e pelos aceleradores de solução do Azure IoT. O Hub IoT permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e uma solução de nuvem. O Hub IoT ajuda a solucionar os desafios de implementação de IoT como:

* Conectividade e gerenciamento de dispositivo de alto volume.
* Ingestão de telemetria de alto volume.
* Comando e controle de dispositivos.
* Imposição de segurança de dispositivo.

## <a name="compare-azure-iot-solution-accelerators-and-microsoft-iot-central"></a>Comparar os aceleradores de solução do Azure IoT e a Central IoT da Microsoft

Escolher seu produto do Azure IoT é uma parte essencial do planejamento da sua solução de IoT. O Hub IoT é um serviço individual do Azure que, por si só, não fornece uma solução de IoT de ponta a ponta. O Hub IoT pode ser usado como um ponto de partida para qualquer solução de IoT, e você não precisa dos aceleradores de solução do Azure IoT ou da Central da IoT da Microsoft para usá-lo. Os aceleradores de solução do Azure IoT e a Central IoT da Microsoft usam o Hub IoT junto com outros serviços do Azure. A tabela a seguir resume as principais diferenças entre os aceleradores de solução do Azure IoT e a Central IoT da Microsoft para ajudá-lo a escolher a opção correta para seus requisitos:

|                        | Aceleradores de solução do Azure IoT | Central IoT da Microsoft |
| ---------------------- | --------- | ----------- |
| Uso principal | Para acelerar o desenvolvimento de uma solução de IoT personalizada que precisa obter a máxima flexibilidade. | Para acelerar o tempo para colocação no mercado de soluções de IoT simples que não exigem personalização profunda de serviço. |
| Acesso a serviços PaaS subjacentes          | Você tem acesso aos serviços do Azure subjacentes para gerenciá-los ou substituí-los conforme necessário. | SaaS. Solução totalmente gerenciada, os serviços subjacentes não são expostos. |
| Flexibilidade            | Alta. O código para os microsserviços é de código aberto e pode ser modificado da maneira que desejar. Além disso, é possível personalizar a infraestrutura de implantação.| Média. Você pode usar a experiência do usuário baseada no navegador interno para personalizar o modelo da solução e os aspectos da interface do usuário. A infraestrutura não é personalizável porque os diferentes componentes não ficam expostos.|
| Nível de habilidade                 | Médio-alto. Você precisa habilidades no Java ou .NET para personalizar a solução de back-end. Você precisa de habilidades no JavaScript para personalizar a visualização. | Baixa. Você precisa de habilidades de modelagem para personalizar a solução. Nenhuma habilidade de codificação é necessária. |
| Experiência para começar | Os aceleradores de solução implementam cenários comuns de IoT. Podem ser implantados em minutos. | Modelos de aplicativo e de dispositivo fornecem modelos predefinidos. Podem ser implantados em minutos. |
| Preços                | Você pode ajustar os serviços para controlar o custo. | Estrutura de preços simples e previsíveis. |

A decisão de qual produto usar para criar sua solução de IoT acaba sendo determinada por:

* Seus requisitos de negócios.
* O tipo de solução que você deseja criar
* O conjunto de habilidades de sua organização para criar e manter a solução no longo prazo.

## <a name="next-steps"></a>Próximas etapas

Com base no produto escolhido e na abordagem, as próximas etapas sugeridas são:

* **Aceleradores de solução do Azure IoT**: [O que são os aceleradores de solução do Azure IoT?](iot-suite-what-are-solution-accelerators.md)
* **Central IoT da Microsoft**: [Central IoT da Microsoft](https://www.microsoft.com/internet-of-things/iot-central-saas-solutions).
* **Hub do IoT**: [visão geral do serviço do Hub IoT do Azure](../iot-hub/iot-hub-what-is-iot-hub.md).
