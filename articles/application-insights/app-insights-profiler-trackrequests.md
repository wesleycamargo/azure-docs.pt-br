---
title: Escrever código para acompanhar as solicitações com o Azure Application Insights | Microsoft Docs
description: Escrever código para acompanhar as solicitações com o Application Insights para que você possa obter perfis para suas solicitações
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 20f408d9dd32c3fd7a0e319e4051483e3aa54dd9
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52722115"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Escrever código para acompanhar as solicitações com o Application Insights

Para ver os perfis para o seu aplicativo na página de desempenho, o Application Insights precisa estar controlando solicitações para seu aplicativo. O Application Insights pode acompanhar automaticamente as solicitações para aplicativos que são criados em estruturas que já estão instrumentadas, como ASP.net e ASP.Net Core. Mas para outros aplicativos, como funções de trabalho do serviço de nuvem do Azure e APIs sem monitoração de estado do Service Fabric, sua necessidade de escrever código para dizer o Application Insights onde suas solicitações começam e terminam. Uma vez que você escreveu esse código, solicitações de telemetria serão enviadas ao Application Insights e você verá a telemetria na página desempenho e perfis serão coletados para essas solicitações. 

Aqui estão as etapas necessárias para controlar as solicitações manualmente:


  1. No início do tempo de vida do aplicativo, adicione o código a seguir:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Para obter mais informações essa configuração de chave de Instrumentação global, consulte [Use Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md) (Usando o Service Fabric com o Application Insights).  

  1. Para qualquer parte do código que você deseje instrumentar, adicione-a entre uma instrução `StartOperation<RequestTelemetry>` **USING**, como no exemplo a seguir:

        ```csharp
        using Microsoft.ApplicationInsights;
        using Microsoft.ApplicationInsights.DataContracts;
        ...
        var client = new TelemetryClient();
        ...
        using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
        {
          // ... Code I want to profile.
        }
        ```

        Não há suporte para chamar `StartOperation<RequestTelemetry>` dentro de outro escopo `StartOperation<RequestTelemetry>`. Você pode usar `StartOperation<DependencyTelemetry>` no escopo aninhado, em vez disso. Por exemplo:   
        
        ```csharp
        using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
        {
        try
        {
          ProductDetail details = new ProductDetail() { Id = productId };
          getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();
        
          // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
          using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
          {
              double price = await _priceDataBase.GetAsync(productId);
              if (IsTooCheap(price))
              {
                  throw new PriceTooLowException(productId);
              }
              details.Price = price;
          }
        
          // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
          using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
          {
              details.Reviews = await _reviewDataBase.GetAsync(productId);
          }
        
          getDetailsOperation.Telemetry.Success = true;
          return details;
        }
        catch(Exception ex)
        {
          getDetailsOperation.Telemetry.Success = false;
        
          // This exception gets linked to the 'GetProductDetails' request telemetry.
          client.TrackException(ex);
          throw;
        }
        }
        ```
