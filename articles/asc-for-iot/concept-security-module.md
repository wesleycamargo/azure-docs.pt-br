---
title: Entender o ASC para Gêmeos de módulo de segurança de IoT visualização | Microsoft Docs
description: Saiba mais sobre o conceito de Gêmeos de módulo de segurança e como eles são usados no ASC para IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 63d21cc5027145ab87030bd2561bc5087298f16c
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541836"
---
# <a name="security-module"></a>Módulo de segurança

> [!IMPORTANT]
> ASC para IoT está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica como ASC para IoT usa dispositivos gêmeos e módulos. 

## <a name="device-twins"></a>Dispositivos gêmeos

Para soluções de IoT criadas no Azure, dispositivos gêmeos desempenham um papel fundamental no gerenciamento de dispositivo e automação de processo.  

ASC para IoT oferece uma integração completa com sua plataforma de gerenciamento de dispositivo IoT existente, permitindo que você gerencie seu status de segurança de dispositivo, bem como a marca usar recursos de controle de dispositivo existente. Integração é obtida por fazendo uso do IoT Hub de mecanismo de gêmeos.  

Saiba mais sobre o conceito de [dispositivo](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) gêmeos no IoT Hub do Azure. 

## <a name="security-module-twins"></a>Gêmeos de módulo de segurança

ASC para IoT mantém um gêmeo de módulo de segurança para cada dispositivo no serviço. O gêmeo do módulo de segurança contém todas as informações relevantes para a segurança de dispositivo para cada dispositivo específico em sua solução. Propriedades de segurança de dispositivo são mantidas em um gêmeo de módulo de segurança dedicado para comunicação mais segura e para habilitar as atualizações e manutenção que exigem menos recursos.  

Ver [gêmeo do módulo de segurança de criação](quickstart-create-security-twin.md) e [configurar agentes de segurança](concept-agent-configuration.md) para aprender a criar, personalizar e configurar o gêmeo. Ver [Gêmeos de módulo de compreensão](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) para saber mais sobre o conceito de módulos gêmeos no IoT Hub. 
 

## <a name="see-also"></a>Consulte também
- [ASC para versão prévia do IoT](overview.md)
- [Implantar agentes de segurança](select-deploy-agent.md)
- [Métodos de autenticação do agente de segurança](concept-security-agent-authentication-methods.md)