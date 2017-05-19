---
title: Monitoramento no Microsoft Azure | Microsoft Docs
description: "Opções para monitorar qualquer coisa no Microsoft Azure. Azure Monitor, Log Analytics do Application Insights"
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: robb
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: d4a94a92585420cf92018084437422fd0c66fa2d
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---

# <a name="overview-of-monitoring-in-microsoft-azure"></a>Visão Geral do Monitoramento no Microsoft Azure
Este artigo fornece uma visão geral das ferramentas disponíveis para monitorar o Microsoft Azure. Aplicável a 
- monitoramento de aplicativos em execução no Microsoft Azure 
- ferramentas/serviços executados fora do Azure que podem monitorar objetos no Azure. 

Ele aborda os vários produtos e serviços disponíveis e como eles funcionam juntos. Ele pode ajudar a determinar quais ferramentas são as mais apropriadas para você em quais casos.  

## <a name="why-use-monitoring-and-diagnostics"></a>Por que usar o monitoramento e o diagnóstico?

Problemas de desempenho em seu aplicativo de nuvem podem afetar seus negócios. Com diversos componentes interconectados e frequentes lançamentos, degradações podem ocorrer a qualquer momento. E se você estiver desenvolvendo um aplicativo, seus usuários normalmente descobrirão problemas que você não encontrou durante os testes. Você precisa tomar conhecimento dessas questões imediatamente e precisa ter ferramentas para diagnosticar e corrigir problemas. O Microsoft Azure tem uma variedade de ferramentas para identificar esses problemas.

## <a name="how-do-i-monitor-my-azure-cloud-apps"></a>Como posso monitorar meus aplicativos de nuvem do Azure?

Há uma variedade de ferramentas para monitorar serviços e aplicativos do Azure. Alguns de seus recursos se sobrepõem. Isso se deve em parte a razões históricas e, em parte, à fusão entre o desenvolvimento e a operação de um aplicativo. 

Estas são as ferramentas de entidade de serviço:

-    O **Azure Monitor** é uma ferramenta básica para monitorar serviços em execução no Azure. Ele fornece dados de nível da infraestrutura sobre a taxa de transferência de um serviço e o ambiente em redor. Se você estiver gerenciando todos os seus aplicativos no Azure ou decidindo se deseja expandir ou reduzir recursos, o Azure Monitor oferece o que você usa para iniciar.

-    O **Application Insights** pode ser usado para desenvolvimento e como uma solução de monitoramento de produção. Ele funciona instalando um pacote em seu aplicativo e, assim, fornecendo uma exibição mais interna do que está acontecendo. Seus dados incluem tempos de resposta de dependências, rastreamentos de exceção, instantâneos de depuração, perfis de execução. Ele fornece poderosas ferramentas inteligentes para analisar toda essa telemetria, tanto para ajudá-lo a depurar um aplicativo quanto para ajudá-lo a entender o que os usuários estão fazendo com ele. Você pode discernir se um pico no tempo de resposta se deve a algo em um aplicativo ou a algum problema de obtenção de recursos externo. Se você usar o Visual Studio e o aplicativo estiver apresentando falhas, você poderá ser levado diretamente para as linhas do código com o problema para que possa corrigi-lo.  

-    O **Log Analytics** é voltado para pessoas que precisam ajustar o desempenho e planejar a manutenção de aplicativos em execução na produção. Ele se baseia no Azure. Ele coleta e agrega dados de várias fontes, embora haja um atraso de 10 a 15 minutos. Ele fornece uma solução holística de gerenciamento de TI para a infraestrutura baseada em nuvem de terceiros (como o Amazon Web Services), do Azure e local. Ele fornece ferramentas mais sofisticadas para analisar dados de mais origens, permite consultas complexas em todos os logs e pode alertar proativamente sobre condições especificadas.  Você pode até mesmo coletar dados personalizados em seu repositório central para que possa consultar e visualizá-los. 

-    O **SCOM (System Center Operations Manager)** é voltado ao gerenciamento e monitoramento de grandes instalações de nuvem. Você pode já estar familiarizado como ele como uma ferramenta de gerenciamento para nuvens baseadas em Hyper-V e Windows Server local, mas ele também pode se integrar e gerenciar aplicativos do Azure. Entre outras coisas, ele pode instalar o Application Insights em aplicativos ativos existentes.  Se um aplicativo falhar, ele lhe informa em segundos. Observe que o Log Analytics não substitui o SCOM. Eles funcionam bem em conjunto.  


## <a name="accessing-monitoring-in-the-azure-portal"></a>Acessando o monitoramento no Portal do Azure
Agora, todos os serviços de monitoramento do Azure estão disponíveis em um único painel de interface do usuário. Para obter mais informações sobre como acessar essa área, consulte [Introdução ao Azure Monitor](monitoring-get-started.md). 

Você também pode acessar as funções de monitoramento de recursos específicos realçando esses recursos e fazendo uma busca detalhada em suas opções de monitoramento. 

## <a name="examples-of-when-to-use-which-tool"></a>Exemplos de quando usar qual ferramenta 

As seções a seguir mostram alguns cenários básicos e quais ferramentas devem ser usadas. 

### <a name="scenario-1--fix-errors-in-an-azure-application-under-development"></a>Cenário 1 – Corrigir erros em um aplicativo do Azure em desenvolvimento   

**A melhor opção é usar o Application Insights, o Azure Monitor e o Visual Studio juntos**

Agora, o Azure agora fornece toda a capacidade do depurador do Visual Studio na nuvem. Configure o Azure Monitor para enviar telemetria ao Application Insights. Habilite o Visual Studio para incluir o SDK do Application Insights em seu aplicativo. Uma vez no Application Insights, você pode usar o Mapa do aplicativo para descobrir visualmente quais partes do seu aplicativo em execução estão íntegras ou não. Para as partes que não estão íntegras, erros e exceções já estão disponíveis para exploração. Você pode usar as diferentes análises no Application Insights para obter mais detalhes. Se não tiver certeza quanto ao erro, você poderá usar o depurador do Visual Studio para rastrear o código e identificar o problema. 

Para obter mais informações, consulte [Monitorando Aplicativos Web](../application-insights/app-insights-azure-web-apps.md) e consulte o sumário à esquerda para obter instruções sobre diversos tipos de aplicativos e linguagens.  

### <a name="scenario-2--debug-an-azure-net-web-application-for-errors-that-only-show-in-production"></a>Cenário 2 – Depurar um aplicativo Web .NET do Azure para encontrar erros que aparecem somente na produção 

> [!NOTE]
> Esses recursos estão no modo de versão prévia. 

**A melhor opção é usar o Application Insights e, se possível, o Visual Studio para ter a experiência de depuração completa.**

Use o Depurador instantâneo do Application Insights para depurar seu aplicativo. Quando um determinado limite de erro ocorre com componentes de produção, o sistema captura automaticamente telemetria em janelas de tempo chamadas "instantâneos". O valor capturado é seguro para uma nuvem de produção porque é pequeno o suficiente para não afetar o desempenho, mas significativo o suficiente para permitir o rastreamento.  O sistema pode capturar vários instantâneos. Você pode examinar um ponto no tempo no Portal do Azure ou usar o Visual Studio para ter a experiência completa. Com o Visual Studio, os desenvolvedores podem percorrer o instantâneo como se estivessem depurando em tempo real. Quadros, memória, parâmetros e variáveis locais estão disponíveis. Os desenvolvedores devem obter acesso a esses dados de produção por meio de uma função RBAC.  

Para obter mais informações, consulte [Depuração instantânea](../application-insights/app-insights-snapshot-debugger.md). 

### <a name="scenario-3--debug-an-azure-application-that-uses-containers-or-microservices"></a>Cenário 3 – Depurar um aplicativo do Azure que usa contêineres ou microsserviços 

**O mesmo que o cenário 1. Use o Application Insights, o Azure Monitor e o Visual Studio juntos** O Application Insights também dá suporte à coleta de telemetria de processos em execução dentro de contêineres e de microsserviços (Kubernetes, Docker, Service Fabric do Azure). Para obter mais informações, [assista a este vídeo sobre a depuração de contêineres e microsserviços](https://go.microsoft.com/fwlink/?linkid=848184). 


### <a name="scenario-4--fix-performance-issues-in-your-azure-application"></a>Cenário 4 – Solucionar problemas de desempenho em seu aplicativo do Azure

O [Application Insights Profiler](../application-insights/app-insights-profiler.md) foi projetado para ajudar a solucionar esses tipos de problemas. Você pode identificar e solucionar problemas de desempenho para aplicativos em execução nos Serviços de Aplicativos (Aplicativos Web, Aplicativos Lógicos, Aplicativos Móveis, Aplicativos de API) e outros recursos de computação, como Máquinas Virtuais, VMSS (conjunto de dimensionamento de máquinas virtuais), Serviços de Nuvem e Service Fabric. 

> [!NOTE]
> A capacidade de criar perfis de Máquinas Virtuais, VMSS (conjunto de dimensionamento de máquinas virtuais), Serviços de Nuvem e Service Fabric está em versão prévia.   

Além disso, você será notificado proativamente por email sobre determinados tipos de erros, como lentidão para o carregamento de páginas, pela ferramenta de Detecção Inteligente.  Você não precisa fazer nenhuma configuração nessa ferramenta. Para obter mais informações, consulte [Detecção Inteligente – anomalias de desempenho](../application-insights/app-insights-proactive-performance-diagnostics.md) e [Detecção Inteligente – anomalias de desempenho](https://azure.microsoft.com/blog/Enhancments-ApplicationInsights-SmartDetection/preview).



## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre

* [Azure Monitor em um vídeo do Ignite 2016](https://myignite.microsoft.com/videos/4977)
* [Introdução ao Azure Monitor](monitoring-get-started.md)
* [Diagnóstico do Azure](../azure-diagnostics.md) se você estiver tentando diagnosticar problemas em seu Serviço de Nuvem, Máquina Virtual, conjunto de dimensionamento de máquinas virtuais ou aplicativo do Service Fabric.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) se você tiver problemas de diagnóstico em seu aplicativo Web do Serviço de Aplicativo.
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) e a solução de monitoramento de produção [Operations Management Suite](https://www.microsoft.com/oms/)
