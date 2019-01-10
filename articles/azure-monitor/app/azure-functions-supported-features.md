---
title: Insights do Aplicativo do Azure - Funções do Azure Recursos Suportados | Microsoft Docs
description: Recursos suportados do Application Insights para funções do Azure
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: ''
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 10/05/2018
ms.reviewer: mbullwin
ms.author: tilee
ms.openlocfilehash: 9ad0579ff9c25753b1e4816b80948b4d8d1232f7
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54081516"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Recursos suportados do Application Insights para o Azure Functions

O Azure Functions oferece [integração interna](https://docs.microsoft.com/azure/azure-functions/functions-monitoring) com o Application Insights, que está disponível por meio da Interface do ILogger. Abaixo está a lista de recursos atualmente suportados. Examine o guia do Azure Functions para [Introdução ao](https://github.com/Azure/Azure-Functions/wiki/App-Insights).

## <a name="supported-features"></a>Recursos com suporte

| Funções do Azure                       | V1                | V2 (2018 do Ignite)  | 
|-----------------------------------    |---------------    |------------------ |
| **SDK .NET do Application Insights**   | **2.5.0**       | **2.7.2**         |
| | | | 
| **Coleta automática de**        |                 |                   |               
| &bull; Solicitações                     | SIM             | SIM               | 
| &bull; Exceções                   | SIM             | SIM               | 
| &bull; Dependências                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | SIM               | 
| &nbsp;&nbsp;&nbsp;&mdash; Barramento de serviço|                 | SIM               | 
| &nbsp;&nbsp;&nbsp;&mdash; hub de eventos  |                 | SIM               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | SIM               | 
| | | | 
| **Recursos com suporte**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | SIM             | SIM               | 
| &bull; Amostragem                     | SIM             | SIM               | 
| &bull; Pulsações                   |                 | SIM               | 
| | | | 
| **Correlação**                       |                   |                   |               
| &bull; Barramento de serviço                     |                   | SIM               | 
| &bull; hub de eventos                       |                   | SIM               | 
| | | | 
| **Configurável**                      |                   |                   |           
| &bull;Totalmente configurável.<br/>Ver [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) para obter instruções.<br/>Ver [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) para todas as opções.               |                   | SIM                   | 


## <a name="sampling"></a>amostragem

As funções do Azure ativam a amostragem por padrão em suas configurações. Para obter mais informações, consulte [configurar amostragem](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling).
