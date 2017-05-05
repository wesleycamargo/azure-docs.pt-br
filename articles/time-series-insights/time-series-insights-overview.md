---
title: "Visão geral das Análise de Séries Temporais do Azure | Microsoft Docs"
description: "Introdução à Análise de Séries Temporais do Azure, que é um novo serviço para soluções de IoT e de análises de dados de séries temporais"
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/20/2017
ms.author: omravi
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: cc35c00bec18ee1d9ed52b29b68ddacc1136dc70
ms.lasthandoff: 04/26/2017

---

# <a name="overview-of-azure-time-series-insights"></a>Visão geral das Análise de Séries Temporais do Azure

A Análise de Séries Temporais são um serviço de nuvem totalmente gerenciado com componentes de armazenamento, análise e visualização que facilitam muito ingerir, armazenar, explorar e analisar bilhões de eventos simultaneamente. A Análise de Séries Temporais oferecem uma visão global dos dados, permitindo que você valide rapidamente sua solução IoT e evite o tempo de inatividade dispendioso para dispositivos críticos ajudando você a descobrir tendências ocultas e anomalias e realizar análises de causa raiz em tempo quase real. A Análise de Séries Temporais ingerem os dados de série temporal de agentes de evento (por exemplo, Hubs IoT ou Hubs de eventos), indexa os dados e desativa os dados com base em uma política de retenção configurável. Os usuários consomem os dados para o respectivo seu cenário de negócios por meio de uma experiência do usuário intuitiva ou APIs de Consulta REST.

![Visão geral da Análise de Séries Temporais](media/overview/time-series-insights-overview-flow.png)

## <a name="primary-scenarios"></a>Principais cenários

* Validação e monitoramento de soluções IoT em minutos
* Visualização intuitiva e análise de dados de IoT em escala
* Como acelerar a detecção de anomalias e análise de causas raiz
* Criando uma exibição global de vários dispositivos, plantas e dados

## <a name="key-capabilities-and-benefits"></a>Principais recursos e benefícios

* **É fácil começar**: a Análise de Séries Temporais do Azure não exigem nenhuma preparação de dados inicial e são incrivelmente rápidas, portanto, você pode se conectar aos bilhões de eventos no Hub IoT do Azure ou Hub de Eventos em minutos. Uma vez conectado, você pode visualizar e interagir com os dados do sensor em segundos para validar rapidamente suas soluções IoT. A Análise de Séries Temporais é incrivelmente intuitiva e fácil de usar; você pode interagir com os seus dados sem ter que escrever uma única linha de código.  Além disso, não há nenhuma nova linguagem a aprender, já que a Análise de Séries Temporais fornece uma superfície de consulta de texto livre granular para usuários avançados, assim como a exploração do tipo apontar e clicar, para iniciantes.

* **Análises quase em tempo real em segundos**: Obtenha o máximo proveito de seus dados de séries temporais com componentes de armazenamento, análise e visualização de Análises de Séries Temporais, em um só lugar. As Análises de Séries Temporais podem incluir centenas de milhões de eventos de sensor por dia, com latência de um minuto, então você pode reagir às alterações rapidamente. A Análise de Séries Temporais fornece uma análise mais aprofundada dos seus dados de sensor ajudando você a identificar tendências e anomalias rapidamente, permitindo que você realize facilmente análises complexas de causas raiz e evitar um tempo de inatividade dispendioso. Ao habilitando a correlação cruzada entre dados de histórico e em tempo real, a Análise de Séries Temporais permite aos usuários desbloquear tendências ocultas em seus dados.

* **Crie soluções personalizadas**: Integre a Análise de Séries Temporais do Azure aos seus aplicativos existentes ou crie novas soluções personalizadas com as APIs de consulta REST da Análise de Séries Temporais.  Além disso, criar e compartilhar exibições personalizadas são moleza, tornando mais fácil para outras pessoas explorar as descobertas feitas.

* **Escalabilidade**: A Análise de Séries Temporais foi projetada para suportar escala de IoT. Na visualização, é possível inserir de 1 milhão a 100 milhões de eventos por dia, com um período de tempo de retenção padrão de 31 dias. A Análise de Séries Temporais permite aos usuários visualizar e analisar os fluxos de dados ativos quase em tempo real, juntamente com grandes quantidades de dados de histórico. Mais adiante, taxas de entrada e retenção aumentarão para acomodar uma escala empresarial em constante evolução.

## <a name="taxonomy-of-time-series-insights"></a>Taxonomia da Análise de Séries Temporais

* **Ambiente**: Um é um recurso do Azure com capacidade de entrada e armazenamento.  Os clientes provisionam os ambientes por meio do portal do Azure com a capacidade necessária.
* **Origem de Evento**: Uma Origem de Evento é derivada de um agente de evento, como o Hubs de Eventos do Azure.  A Análise de Séries Temporais se conecta diretamente às Origens de Evento, ingerindo o fluxo de dados sem exigir que os usuários escrevam uma única linha de código. Atualmente, a Análise de Séries Temporais oferece suporte aos Hubs de Eventos do Azure e Hubs IoT do Azure e no futuro, mais Origens de Eventos serão adicionadas.
* **Dados de referência**: A Análise de Séries Temporais fornece aos usuários a capacidade de unir dados de séries temporais com os dados de referência.  Os dados de referência podem incluir metadados sobre dispositivos ou outros dados estáticos que não são alterados com tanta frequência. A Análise de Séries Temporais une dados de referência com fluxos de dados, permitindo aos usuários visualizar e analisar esses dados quase em tempo real.

