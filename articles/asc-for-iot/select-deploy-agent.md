---
title: Selecione e implante um ASC para visualização do agente de IoT | Microsoft Docs
description: Saiba mais sobre como selecionar e implantar o ASC para agentes de segurança de IoT nos dispositivos de IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 5ce583fe98acb7e0a4aaeb720cb2d5ed5eebb9e3
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541956"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Selecione e implantar um agente de segurança em seu dispositivo IoT

> [!IMPORTANT]
> ASC para IoT está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


ASC para IoT fornece arquiteturas de referência para os agentes de segurança que monitoram e coletam dados de dispositivos IoT.

Agentes de segurança são desenvolvidos como projetos de software livre e estão disponíveis em duas versões: <br> [C](https://aka.ms/iot-security-github-c), e [ C# ](https://aka.ms/iot-security-github-cs).

## <a name="supported-platforms-for-asc-for-iot-agents"></a>Plataformas com suporte para o ASC para agentes de IoT

A lista a seguir inclui todas as plataformas com suporte no momento.  

|ASC para agente do IoT |Sistema operacional |Arquitetura |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |   x64|
|C|Ubuntu 18.04 |   x64|
|C|Debian 9 |   x64, x86|
|C#|Ubuntu 16.04    |x64|
|C#|Ubuntu 18.04    |x64|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core build 17763 |x64|


## <a name="which-flavor-should-i-use"></a>Qual tipo deve usar?

Considere as seguintes perguntas em relação a seus dispositivos IoT:

- Você está usando _Windows Server_ ou _Windows IoT Core_? 

    Use [ASC para instalação de IoT para Windows com o C#-com base em agente security](tutorial-deploy-windows-cs.md).

- Você está usando uma distribuição Linux com x86 arquitetura? 

    Use [ASC para instalação de IoT para Linux com o agente de segurança com base em C](tutorial-deploy-linux-c.md).
- Você está usando uma distribuição Linux com x64 arquitetura?

    Você pode usar ambos os tipos. <br>
    [ASC para instalação de IoT para Linux com o agente de segurança com base em C](tutorial-deploy-linux-c.md) e/ou [ASC para instalação de IoT para Linux com o C#-com base em agente de segurança](tutorial-deploy-linux-cs.md).

Ambos os tipos têm o mesmo conjunto de recursos e as opções de configuração semelhante de suporte. O agente de segurança com base em C tem uma pegada de memória inferior.


## <a name="next-steps"></a>Próximas etapas
- [Visão geral](overview.md)
- [Agentes de segurança](security-agent-architecture.md)
- [Métodos de autenticação do agente de segurança](concept-security-agent-authentication-methods.md)
- [ASC para perguntas Frequentes de IoT](resources-frequently-asked-questions.md)