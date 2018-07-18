---
title: Opções do Microsoft Azure IoT | Microsoft Docs
description: Escolha como implementar sua solução do Azure IoT usando o Azure IoT Central, os aceleradores de solução do IoT ou o Hub IoT.
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 0314bf4d26fb0f777fd88debbf09814479ab225a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34630520"
---
# <a name="compare-azure-iot-central-and-azure-iot-options"></a>Comparar as opções do Azure IoT Central e do Azure IoT

Para implementar uma solução de IoT, o Microsoft Azure IoT Central e o Azure IoT oferecem várias opções, cada uma apropriada para diferentes conjuntos de requisitos do cliente:

* O [Azure IoT Central](overview-iot-central.md) é uma solução de Software como Serviço (SaaS) que usa uma abordagem baseada em modelo para permitir que você crie soluções de IoT de nível empresarial sem a necessidade de experiência em desenvolvimento de solução de nuvem.

* Os [aceleradores de solução do Azure IoT](https://docs.microsoft.com/azure/iot-accelerators/) é uma coleção de nível empresarial de [aceleradores de soluções](../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md) criada na plataforma como serviço do Azure (PaaS) e que permite acelerar o desenvolvimento de soluções personalizadas de IoT.

## <a name="azure-iot-hub"></a>Hub IoT do Azure

O Hub IoT do Azure é a principal PaaS do Azure usada pela Azure IoT Central e pelos aceleradores de solução do Azure IoT. O Hub IoT permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e uma solução de nuvem. O Hub IoT ajuda a solucionar os desafios de implementação de IoT como:

* Conectividade e gerenciamento de dispositivo de alto volume.
* Ingestão de telemetria de alto volume.
* Comando e controle de dispositivos.
* Imposição de segurança de dispositivo.

## <a name="compare-azure-iot-central-and-azure-iot-solution-accelerators"></a>Comparar os aceleradores de solução do Azure IoT e o Azure IoT Central

Escolher seu produto do Azure IoT é uma parte essencial do planejamento da sua solução de IoT. O Hub IoT é um serviço individual do Azure que, por si só, não fornece uma solução de IoT de ponta a ponta. O Hub IoT pode ser usado como um ponto de partida para qualquer solução de IoT, e você não precisa dos aceleradores de solução do Azure IoT ou do Azure IoT Central para usá-lo. Os aceleradores de solução do IoT e o Azure IoT Central usam o Hub IoT junto com outros serviços do Azure. A tabela a seguir resume as principais diferenças entre os aceleradores de solução do IoT e o Azure IoT Central, para ajudá-lo a escolher a opção correta para seus requisitos:

|     | Azure IoT Central | Aceleradores de solução de IoT do Azure |
| --- | ----------- | --------- |
| Uso principal                      | Para acelerar o tempo para colocação no mercado de soluções de IoT simples que não exigem personalização profunda de serviço.                                                    | Para acelerar o desenvolvimento de uma solução de IoT personalizada que precisa obter a máxima flexibilidade.                                                                                                                             |
| Acesso a serviços PaaS subjacentes | SaaS. Solução totalmente gerenciada, os serviços subjacentes não são expostos.                                                                                            | Você tem acesso aos serviços do Azure subjacentes para gerenciá-los ou substituí-los conforme necessário.                                                                                                                    |
| Flexibilidade                        | Média. Você pode usar a experiência do usuário baseada no navegador interno para personalizar o modelo da solução e os aspectos da interface do usuário. A infraestrutura não é personalizável porque os diferentes componentes não ficam expostos. | Alta. O código para os microsserviços é de código aberto e pode ser modificado da maneira que desejar. Além disso, é possível personalizar a infraestrutura de implantação.                                               |
| Nível de habilidade                        | Baixa. Você precisa de habilidades de modelagem para personalizar a solução. Nenhuma habilidade de codificação é necessária.                                                                          | Médio-alto. Você precisa habilidades no Java ou .NET para personalizar a solução de back-end. Você precisa de habilidades no JavaScript para personalizar a visualização.                                                                       |
| Experiência para começar             | Modelos de aplicativo e de dispositivo fornecem modelos predefinidos. Podem ser implantados em minutos.                                                                                                  | Soluções pré-configuradas implementam cenários comuns de IoT. Podem ser implantados em minutos.                                                                                                                            |
| Preços                            | Estrutura de preços simples e previsíveis.                                                                                                                           | Você pode ajustar os serviços para controlar o custo.                                                                                                                                                            |

A decisão de qual produto usar para criar sua solução de IoT acaba sendo determinada por:

* Seus requisitos de negócios.
* O tipo de solução que você deseja criar
* O conjunto de habilidades de sua organização para criar e manter a solução no longo prazo.

## <a name="next-steps"></a>Próximas etapas

Com base no produto escolhido e na abordagem, as próximas etapas sugeridas são:

* **Azure IoT Central**: [Azure IoT Central](overview-iot-central.md).
* **Aceleradores de solução do IoT**: [O que são os aceleradores de solução do Azure IoT?](../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md).
* **Hub do IoT**: [visão geral do serviço do Hub IoT do Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-what-is-iot-hub).