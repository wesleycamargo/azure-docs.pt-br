---
title: ASC para visualização de pré-requisitos de IoT | Microsoft Docs
description: Detalhes de todos os componentes necessários para começar com o ASC para pré-requisitos do serviço de IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 213e6a95484b5f6953f8423474953125f8739015
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541821"
---
# <a name="asc-for-iot-prerequisites"></a>ASC para pré-requisitos de IoT

> [!IMPORTANT]
> ASC para IoT está atualmente em visualização pública.
> Esta versão de visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo fornece uma explicação sobre os diferentes blocos de construção da ASC para IoT serviço, o que você precisa para começar e conceitos básicos para ajudar a entender o serviço. 

## <a name="minimum-requirements"></a>Requisitos mínimos

- Camada Standard de Hub IoT
    - Função RBAC **proprietário** privilégios de nível 
- [O espaço de trabalho do log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Central de segurança do Azure (recomendado)
    - Embora o uso da Central de segurança do Azure é apenas uma recomendação e não um requisito, sem ele, não será capaz de exibir os recursos do Azure dentro do IoT Hub. 
 
## <a name="working-with-asc-for-iot-service"></a>Trabalhar com o ASC para o serviço de IoT

ASC para relatórios e insights de IoT estão disponíveis usando o IoT Hub do Azure e a Central de segurança do Azure. Para habilitar o ASC para IoT em seu Hub do IoT do Azure, uma conta com **proprietário** privilégios no nível é necessária. Depois de habilitar o ASC para IoT em seu IoT Hub, ASC para insights de IoT são exibidos como o **segurança** recurso no IoT Hub do Azure e como **IoT** na Central de segurança do Azure. 

## <a name="supported-service-regions"></a>Regiões de serviço com suporte 

ASC para IoT tem suporte atualmente para os Hubs de IoT nas seguintes regiões do Azure:
  - Centro dos EUA
  - Norte da Europa
  - Sudeste Asiático

## <a name="wheres-my-iot-hub"></a>Onde está meu Hub IoT?

Verifique seu local de IoT Hub para verificar a disponibilidade de serviço antes de começar. 

1. Abra o Hub IoT. 
2. Clique em **Visão Geral**. 
3. Verifique se o local indicado corresponde a um dos [regiões de serviço com suporte](#supported-service-regions). 


## <a name="supported-platforms-for-agents"></a>Plataformas com suporte para agentes 

ASC para agentes do IoT dá suporte a uma lista crescente de dispositivos e plataformas. Consulte a [suporte para a lista de plataformas](select-deploy-agent.md) para verificar sua biblioteca de dispositivo existente ou planejada.  

## <a name="next-steps"></a>Próximas etapas
- [Visão geral](overview.md)
- [Habilitar o serviço](quickstart-onboard-iot-hub.md)
- [ASC para perguntas Frequentes de IoT](resources-frequently-asked-questions.md)
- [Noções básicas sobre o ASC para alertas de IoT](concept-security-alerts.md)
