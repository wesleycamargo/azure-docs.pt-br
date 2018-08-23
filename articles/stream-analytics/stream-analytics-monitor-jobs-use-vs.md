---
title: Monitorar e gerenciar trabalhos do Azure Stream Analytics usando o Microsoft Visual Studio
description: Este artigo descreve como usar Microsoft Visual Studio para monitorar e gerenciar os trabalhos do Azure Stream Analytics.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: 9cc65fc5119bedc816f825b7aae6120f4bca3b12
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2018
ms.locfileid: "40246395"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-visual-studio"></a>Monitorar e gerenciar os trabalhos do Stream Analytics com o Microsoft Visual Studio

Este artigo demonstra como monitorar o trabalho do Stream Analytics no Microsoft Visual Studio. As ferramentas de Stream Analytics do Azure para o Microsoft Visual Studio fornecem uma experiência de monitoramento semelhante ao portal do Azure sem precisar sair do IDE. Você pode começar a monitorar um trabalho, assim que você **Enviar para o Azure** de seu **Script.asaql**, ou você pode monitorar os trabalhos existentes, independentemente de como eles foram criados. 

## <a name="job-summary"></a>Resumo do trabalho

O **Resumo do Trabalho** e **Métricas do Trabalho** dão a um instantâneo rápido do seu trabalho. Em uma visão rápida, você pode determinar um status de trabalho e informações do evento.]

<img src="./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-job-summary-metrics.png" alt="Stream Analytics job summary and job metrics" width="300px"/> 


## <a name="job-metrics"></a>Métricas do trabalho

Você pode recolher o **Resumo do Trabalho** e clicar na guia **Métricas do Trabalho** para exibir um gráfico com métricas importantes. Marque e desmarque os tipos de métricos para adicionar e remover do gráfico.

![Métricas do Stream Analytics](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-metrics.png)


## <a name="error-monitoring"></a>Monitoramento de erro

Você também pode monitorar erros, clicando na guia **Erros**.

![Erros do Stream Analytics](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-errors.png)


## <a name="get-support"></a>Obtenha suporte
Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) 

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Criar um trabalho do Azure Stream Analytics com o Microsoft Visual Studio](stream-analytics-quick-create-vs.md)
* [Instalar o Azure Stream Analytics Tools para o Microsoft Visual Studio](stream-analytics-tools-for-visual-studio-install.md) 


