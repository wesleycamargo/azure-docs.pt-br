---
title: Noções básicas sobre o ASC para arquitetura de agente de segurança de IoT Preview | Microsoft Docs
description: Entenda a arquitetura de segurança do agente para os agentes usados no ASC para o serviço de IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 7c8f256cf91a479c45f21b933efdb6a5e0212796
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541866"
---
# <a name="security-agent-reference-architecture"></a>Arquitetura de referência do agente de segurança

> [!IMPORTANT]
> ASC para IoT está atualmente em visualização pública.
> Esta versão de visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


ASC para IoT fornece a arquitetura de referência para os agentes de segurança que faça logon, processam, agregam e enviam dados de segurança por meio do hub IoT.

Agentes de segurança são projetados para funcionar em um ambiente restrito de IoT e são altamente personalizáveis em termos de valores que eles fornecem em comparação com os recursos que eles consomem.

Agentes de segurança suportam os seguintes recursos de solução de IoT:

- Colete eventos de segurança bruto do sistema operacional subjacente (Linux, Windows). Para saber mais sobre os coletores de dados de segurança disponíveis, consulte [ASC para configuração do agente IoT](concept-agent-configuration.md).

- Agregar os eventos de segurança brutos em mensagens enviadas por meio do hub IoT.

- Autenticar com a identidade do dispositivo existente ou uma identidade de módulo dedicado. Ver [métodos de autenticação do agente de segurança](concept-security-agent-authentication-methods.md) para saber mais.

- Configurar remotamente por meio do uso do **ascforiot** gêmeo do módulo. Para obter mais informações, consulte [configurar um ASC para agente do IoT](concept-agent-configuration.md).

ASC para agentes de segurança de IoT são desenvolvidas como projetos de código-fonte aberto e estão disponível no GitHub: 

- [IoT-ASC-Agent-C](https://github.com/Azure/IoT-ASC-Agent-C) 
- [IoT-ASC-Agent-CS](https://github.com/Azure/IoT-ASC-Agent-CS)

## <a name="agent-supported-platforms"></a>Plataformas com suporte do agente

ASC para IoT oferece agentes diferentes do instalador de 32 bits e Windows de 64 bits e o mesmo para 32 bits e 64 bits Linux. Verifique se que você tem o instalador do agente correto para cada um dos seus dispositivos de acordo com a tabela a seguir:

| 32 ou 64 bits | Linux |  Windows |    Detalhes|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32 bits  | C  | C#  ||
| 64 bits  | C#ou C           | C#      | Use o agente de C para dispositivos com o mínimo de recursos|

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre ASC para arquitetura de agente de segurança de IoT e os instaladores disponíveis.

Para continuar a introdução ao ASC para implantação de IoT, use os seguintes artigos:


- Examine o ASC para IoT [pré-requisitos de serviço](service-prerequisites.md)
- Saiba como [ASC habilitar para o serviço de IoT em seu IoT Hub](quickstart-onboard-iot-hub.md)
- Use o guia de início rápido para [configurar sua solução](quickstart-configure-your-solution.md)
- Entender [métodos de autenticação do agente de segurança](concept-security-agent-authentication-methods.md)
- Selecione e implante um [agente de segurança](select-deploy-agent.md)
- Saiba mais sobre o serviço do [ASC para perguntas frequentes sobre o IoT](resources-frequently-asked-questions.md)