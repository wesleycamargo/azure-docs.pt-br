---
title: "Integridade preditiva do veículo e hábitos de condução - Azure | Microsoft Docs"
description: "Use os recursos do Cortana Intelligence para obter informações preditivas em tempo real sobre a integridade do veículo e hábitos de condução."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 09fad60b-2f48-488b-8a7e-47d1f969ec6f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.openlocfilehash: bec5066a2e1ba0e4e5e81c4e1be28ed8eb93ceed
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="vehicle-telemetry-analytics-solution-playbook"></a>Guia estratégico da Solução de Análise de Telemetria do Veículo
Este menu fornece links para os capítulos deste guia estratégico: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## <a name="overview"></a>Visão geral
Os supercomputadores foram retirados do laboratório e agora estão estacionados em garagens. Esses automóveis de ponta contêm uma infinidade de sensores, que lhes dão a capacidade de acompanhar e monitorar milhões de eventos por segundo. Até 2020, a maioria desses veículos estará conectado à Internet. Explorar essa riqueza de dados pode fornecer maior segurança, confiabilidade e uma experiência melhor de condução. A Microsoft torna esse sonho uma realidade com o Cortana Intelligence.

O Cortana Intelligence é um pacote de análise avançada e de Big Data totalmente gerenciado que pode ser usado para transformar seus dados em ação inteligente. O Modelo da Solução de Análise de Telemetria do Veículo do Cortana Intelligence demonstra como concessionárias, fabricantes de automóveis e seguradoras podem obter insights preditivos em tempo real sobre a integridade do veículo e os hábitos de condução. 

A solução é implementada como um [padrão de arquitetura lambda](https://en.wikipedia.org/wiki/Lambda_architecture), que mostra o potencial completo da plataforma Cortana Intelligence para o processamento em lotes e em tempo real.

## <a name="architecture"></a>Arquitetura
A arquitetura da Solução de Análise de Telemetria do Veículo é ilustrada neste diagrama:

![Diagrama da arquitetura da solução](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)


Essa solução inclui os seguintes componentes do Cortana Intelligence e apresenta sua integração de ponta a ponta:

* Os **Hubs de Eventos do Azure** ingerem milhões de eventos de telemetria do veículo no Azure.
* O **Azure Stream Analytics** fornece insights em tempo real sobre a integridade do veículo e persiste esses dados no armazenamento de longo prazo para uma análise em lote mais avançada.
* O **Azure Machine Learning** detecta anomalias em tempo real e usa o processamento em lotes para fornecer insights preditivos.
* O **Azure HDInsight** transforma os dados em escala.
* O **Azure Data Factory** cuida da orquestração, do agendamento, do gerenciamento de recursos e do monitoramento do pipeline do processamento em lotes.
* **PowerBI** fornece essa solução a um painel avançado para os dados em tempo real e as visualizações da análise preditiva.

Essa solução acessa duas fontes de dados diferentes: 

* **Sinais de veículo e diagnóstico simulados**: um simulador de telemática do veículo emite informações de diagnóstico e sinais que correspondem ao estado do veículo e ao padrão de condução em um determinado momento. 
* **Catálogo de veículos**: esse conjunto de dados de referência mapeia VINs para os modelos.

