---
title: "Como configurar a retenção no ambiente do Azure Time Series Insights | Microsoft Docs"
description: "Este artigo descreve como configurar a retenção no ambiente do Azure Time Series Insights."
services: time-series-insights
ms.service: time-series-insights
author: anshan
ms.author: anshan
manager: kfile
editor: MicrosoftDocs/tsidocs
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 02/09/2018
ms.openlocfilehash: bd688f516e200a37a6c88a8779282f7391eaf8b8
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2018
---
# <a name="configuring-retention-in-time-series-insights"></a>Configurando a retenção no Azure Time Series Insights
Este artigo descreve como configurar o **Tempo de retenção de dados** e o **Comportamento em caso de limite de armazenamento excedido** no Azure Time Series Insights.

Cada ambiente do TSI (Time Series Insights) tem uma configuração para configurar o **Tempo de retenção de dados**. O valor varia de 1 a 400 dias. Os dados são excluídos com base na capacidade de armazenamento do ambiente ou na duração da retenção (1-400), o que vier primeiro.

Cada ambiente TSI tem uma configuração adicional **Comportamento em caso de limite de armazenamento excedido**. Essa configuração controla o comportamento de ingresso e de limpeza quando a capacidade máxima de um ambiente é atingida. Há dois comportamentos dentre os quais escolher:
- **Limpar dados antigos** (padrão)  
- **Pausar a entrada**

Para mais informações detalhadas a fim de compreender melhor essas configurações, veja [Noções básicas sobre retenção no Time Series Insights](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Configurar retenção de dados

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Localize seu ambiente Time Series Insights existente. Selecione **Todos os recursos** no menu à esquerda do portal do Azure. Selecione o seu ambiente de Análise de Séries Temporais.

3. No cabeçalho **CONFIGURAÇÕES**, selecione **Configurar**.

4. Selecione o **Tempo de retenção de dados** para configurar a retenção usando a barra do controle deslizante ou digite um número na caixa de texto.

5. Observe a configuração **Capacidade**, já que essa configuração afeta a quantidade máxima de eventos de dados e a capacidade de armazenamento total para armazenamento de dados. 

6. Mude a configuração de **Comportamento em caso de limite de armazenamento excedido**. Selecione o comportamento **Limpar dados antigos** ou **Colocar a entrada em pausa**.

7. Selecione **Salvar** para configurar as alterações.

## <a name="next-steps"></a>Próximas etapas
Para mais informações, veja [Noções básicas sobre retenção no Time Series Insights](time-series-insights-concepts-retention.md).
