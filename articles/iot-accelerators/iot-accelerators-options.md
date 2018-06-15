---
title: Opções do Microsoft Azure IoT | Microsoft Docs
description: Escolha como implementar sua solução IoT usando os aceleradores de solução do Azure IoT, o Azure IoT Central ou o Hub IoT do Azure.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: 56a19e9cbeb6e7d30171d23ff918a34ba423d2f2
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34725574"
---
# <a name="compare-azure-iot-options"></a>Comparar opções do Azure IoT

O artigo [Azure e a Internet das Coisas](iot-accelerators-what-is-azure-iot.md) descreve uma arquitetura de solução de IoT típica com as seguintes camadas:

* Conectividade e gerenciamento de dispositivo
* Processamento de dados e análise
* Integração de apresentação e negócios

Para implementar essa arquitetura, o Azure IoT oferece várias opções, cada uma apropriada para diferentes conjuntos de requisitos do cliente:

* Os [aceleradores de solução do Azure IoT](../active-directory-domain-services/index.yml) é uma coleção de nível empresarial de [aceleradores de soluções](iot-accelerators-what-are-solution-accelerators.md) criada na plataforma como serviço do Azure (PaaS) e que permite acelerar o desenvolvimento de soluções personalizadas de IoT.

* O [Azure IoT Central](https://www.microsoft.com/internet-of-things/iot-central-saas-solutions) é uma solução de Software como Serviço (SaaS) que usa uma abordagem baseada em modelo para permitir que você crie soluções de IoT de nível empresarial sem a necessidade de experiência em desenvolvimento de solução de nuvem.

## <a name="azure-iot-hub"></a>Hub IoT do Azure

O Hub IoT do Azure é a principal PaaS do Azure usada pela Azure IoT Central e pelos aceleradores de solução do Azure IoT. O Hub IoT permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e uma solução de nuvem. O Hub IoT ajuda a solucionar os desafios de implementação de IoT como:

* Conectividade e gerenciamento de dispositivo de alto volume.
* Ingestão de telemetria de alto volume.
* Comando e controle de dispositivos.
* Imposição de segurança de dispositivo.

## <a name="compare-azure-iot-solution-accelerators-and-azure-iot-central"></a>Comparar os aceleradores de solução do Azure IoT e a Azure IoT Central

Escolher seu produto do Azure IoT é uma parte essencial do planejamento da sua solução de IoT. O Hub IoT é um serviço individual do Azure que, por si só, não fornece uma solução de IoT de ponta a ponta. O Hub IoT pode ser usado como um ponto de partida para qualquer solução de IoT, e você não precisa dos aceleradores de solução do Azure IoT ou do Azure IoT Central para usá-lo. Os aceleradores de solução do Azure IoT e o Azure IoT Central usam o Hub IoT junto com outros serviços do Azure. A tabela a seguir resume as principais diferenças entre os aceleradores de solução do Azure IoT e o Azure IoT Central para ajudá-lo a escolher a opção correta para seus requisitos:

|                        | Aceleradores de solução de IoT do Azure | Azure IoT Central |
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

* **Aceleradores de solução do Azure IoT**: [O que são os aceleradores de solução do Azure IoT?](iot-accelerators-what-are-solution-accelerators.md)
* **Azure IoT Central**: [Azure IoT Central](https://www.microsoft.com/internet-of-things/iot-central-saas-solutions).
* **Hub do IoT**: [visão geral do serviço do Hub IoT do Azure](../iot-hub/iot-hub-what-is-iot-hub.md).
