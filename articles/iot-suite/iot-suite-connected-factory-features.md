---
title: Recursos da solução de Fábrica Conectada - Azure | Microsoft Docs
description: Uma visão geral dos recursos da solução de Fábrica Conectada pré-configurada.
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2018
ms.author: dobett
ms.openlocfilehash: fea9ccc53bd019039cf1e989d72db7a218e4517c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
---
# <a name="what-is-azure-iot-suite-connected-factory"></a>O que é Fábrica Conectada do Azure IoT Suite?

Fábrica Conectada é uma implementação da arquitetura de referência de IoT Industrial do Microsoft Azure, empacotada como uma solução de software livre. É possível utilizá-la como ponto de partida para um produto comercial. Você pode implantar uma versão pré-compilada da solução de Fábrica Conectada na sua assinatura do [Azure IoT Suite](https://www.azureiotsuite.com/#solutions/types/CF).

![Painel da solução de Fábrica Conectada](media/iot-suite-connected-factory-features/dashboard.png)

A Fábrica Conectada inclui os recursos a seguir:

## <a name="industrial-device-interoperability"></a>Interoperabilidade de dispositivo industrial

- Conecte ativos industriais com uma interface OPC UA.
- Use as linhas de produção simuladas (executando servidores OPC UA em contêineres do Docker) para ver a telemetria em tempo real a partir delas.
- Navegue pelo modelo de informações do OPC UA dos servidores OPC UA a partir de um painel de nuvem.

## <a name="remote-management"></a>Gerenciamento remoto

- Configure os ativos do OPC UA no painel de nuvem (métodos de chamada, leitura e gravação de dados).
- Publique e cancele publicação de dados telemétricos dos ativos do OPC UA a partir de um painel de nuvem.

## <a name="cloud-dashboard"></a>Painel de nuvem

- Visualize versões prévias de telemetria diretamente em um painel de nuvem.
- Visualize tendências em dados telemétricos e crie correlações usando o painel do Explorador do Time Series Insights.
- Consulte a OEE (Eficiência Geral do Equipamento) calculada e as KPIs (Indicadores Chave de Desempenho) de um painel de nuvem.
- Visualize hierarquias de ativos industriais em uma topologia de árvore, bem como em um mapa interativo.
- Visualizar, reconhecer e fechar alertas de um painel de nuvem.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- O [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) foi criado para armazenar, visualizar e consultar grandes quantidades de dados de séries temporais. A Fábrica Conectada aproveita esse serviço.
- A Fábrica Conectada integra-se a esse serviço, permitindo que você realize uma análise profunda e em tempo real dos dados do dispositivo.

## <a name="rules-and-alerts"></a>Regras e alertas

[Configure regras baseadas em limites para alertas](iot-suite-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Segurança de ponta a ponta

- Configurar permissões de segurança para usuários usando RBAC (Controle de Acesso Baseado em Função).
- A criptografia de ponta a ponta é implementada usando a autenticação OPC UA (usando certificados X.509), bem como tokens de segurança.

## <a name="customizability"></a>Personalização

- [Personalize](iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md) a solução para atender aos requisitos de negócios específicos.
- Código-fonte da solução completa disponível no GitHub. Consulte o repositório da [solução pré-configurada de Fábrica Conectada](https://github.com/Azure/azure-iot-connected-factory).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a solução pré-configurada de fábrica Conectada lendo os seguintes artigos:

* [Passo a passo de solução pré-configurada de fábrica conectada](iot-suite-connected-factory-sample-walkthrough.md)
* [Implantar um gateway para a fábrica Conectada]( iot-suite-connected-factory-gateway-deployment.md)
