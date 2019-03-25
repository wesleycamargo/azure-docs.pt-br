---
title: Criar perfil de aplicativos ativos do Azure Service Fabric com o Application Insights | Microsoft Docs
description: Habilitar o Profiler para um aplicativo do Service Fabric
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 0b1a06d181fc4d2a44d389d47d1f9480c2fdcb40
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401098"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Criar perfil de aplicativos ativos do Azure Service Fabric com o Application Insights

Você também pode implantar o Application Insights Profiler nesses serviços:
* [Serviço de Aplicativo do Azure](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Serviços de nuvem do Azure](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas Virtuais do Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>Configurar a definição de implantação do ambiente

O Application Insights Profiler está incluído com o Diagnóstico do Azure. Você pode instalar a extensão de Diagnóstico do Azure usando um modelo do Azure Resource Manager para seu cluster do Service Fabric. Obtenha um [Modelo que instala o Diagnóstico do Azure em um Cluster do Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json).

Para configurar seu ambiente, faça o seguinte:

1. Para garantir que você esteja usando o [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ou posterior, basta confirmar se o sistema operacional implantado é o `Windows Server 2012 R2` ou posterior.

1. Pesquise a extensão [Diagnóstico do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) no arquivo de modelo de implantação.

1. Adicione a seguinte seção `SinksConfig` como um elemento filho de `WadCfg`. Substitua o valor da propriedade `ApplicationInsightsProfiler` pela sua própria chave de instrumentação do Application Insights:  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      Para obter informações sobre como adicionar a extensão de Diagnóstico em seu modelo de implantação, consulte [Usar monitoramento e diagnóstico com uma VM Windows e modelos do Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

1. Usando o modelo do Azure Resource Manager, implante seu cluster do Service Fabric.  
  Se as configurações estiverem corretas, o Application Insights Profiler será instalado e habilitado quando a extensão do Diagnóstico do Azure for instalada. 

1. Adicione o Application Insights ao seu aplicativo do Service Fabric.  
  Para o Profiler coletar os perfis para suas solicitações, seu aplicativo deve controlar operações com o Application Insights. Para APIs sem monitoração de estado, você pode consultar as instruções para [Controlando solicitações para criação de perfil](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json). Para obter mais informações sobre como acompanhar operações personalizadas em outros tipos de aplicativos, consulte [acompanhar operações personalizadas com o SDK do .NET do Application Insights](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json).

1. Reimplante o aplicativo.

> [DICA] Para máquinas virtuais, uma alternativa às etapas baseadas em JSON acima é navegar no portal do Azure para **Máquinas Virtuais** > **Configurações de Diagnóstico** > **Coletores** > **Definir a opção Enviar dados de diagnóstico para o Application Insights como Habilitado** e selecionar uma conta do Application Insights ou uma ikey específica.

## <a name="next-steps"></a>Próximas etapas

* Gere tráfego para seu aplicativo (por exemplo, inicie um [teste de disponibilidade](monitor-web-app-availability.md)). Em seguida, espere de 10 a 15 minutos para que os rastreamentos comecem a ser enviados à instância do Application Insights.
* Consulte [Rastreamentos do criador de perfil](profiler-overview.md?toc=/azure/azure-monitor/toc.json) no portal do Azure.
* Para obter ajuda para a solução de problemas do Profiler, confira [Solução de problemas do Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
