---
title: Integridade preditiva do veículo e hábitos de condução - Azure | Microsoft Docs
description: Use os recursos do Cortana Intelligence para obter informações preditivas em tempo real sobre a integridade do veículo e hábitos de condução.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 09fad60b-2f48-488b-8a7e-47d1f969ec6f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: bradsev
ms.openlocfilehash: 02b3e0e0808cb9a1a8a2186b1abe6da7dd13e56e
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="vehicle-telemetry-analytics-solution-playbook"></a>Guia estratégico da Solução de Análise de Telemetria do Veículo
Este menu fornece links para os capítulos deste manual: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## <a name="overview"></a>Visão geral
Os supercomputadores foram retirados do laboratório e agora estão estacionados em garagens. Esses agora são posicionados no automóveis de ponta que contêm uma grande variedade sensores. Esses sensores permitem controlar e monitorar milhões de eventos a cada segundo. Até 2020, a maioria desses veículos estará conectado à Internet. Explorar essa riqueza de dados fornece maior segurança, confiabilidade e, portanto, uma experiência melhor de condução. A Microsoft torna esse sonho uma realidade com o Cortana Intelligence.

O Cortana Intelligence é um pacote de análise avançada e de Big Data totalmente gerenciado que pode ser usado para transformar seus dados em ação inteligente. O Modelo da Solução de Análise de Telemetria do Veículo do Cortana Intelligence demonstra como concessionárias, fabricantes de automóveis e seguradoras podem obter insights preditivos em tempo real sobre a integridade do veículo e os hábitos de condução.

A solução é implementada como um [padrão de arquitetura lambda](https://en.wikipedia.org/wiki/Lambda_architecture), que mostra o potencial completo da plataforma Cortana Intelligence para o processamento em lotes e em tempo real.

## <a name="architecture"></a>Arquitetura
A arquitetura da Solução de Análise de Telemetria do Veículo é ilustrada neste diagrama:

![Diagrama da arquitetura da solução](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)


Essa solução inclui os seguintes componentes do Cortana Intelligence e apresenta sua integração:

* Os **Hubs de Eventos do Azure** transmitem milhões de eventos de telemetria do veículo para o Azure.
* O **Azure Stream Analytics** fornece insights em tempo real sobre a integridade do veículo e persiste esses dados no armazenamento de longo prazo para uma análise em lote mais avançada.
* O **Azure Machine Learning** detecta anomalias em tempo real e usa o processamento em lotes para fornecer insights preditivos.
* O **Azure HDInsight** transforma os dados em escala.
* O **Azure Data Factory** cuida da orquestração, do agendamento, do gerenciamento de recursos e do monitoramento do pipeline do processamento em lotes.
* **PowerBI** fornece essa solução a um painel avançado para os dados em tempo real e as visualizações da análise preditiva.

Essa solução acessa duas fontes de dados diferentes: 

* **Sinais de veículo e diagnóstico simulados**: um simulador de telemática do veículo emite informações de diagnóstico e sinais que correspondem ao estado do veículo e ao padrão de condução em um determinado momento. 
* **Catálogo de veículos**: esse conjunto de dados de referência mapeia números de VINs para os modelos.

