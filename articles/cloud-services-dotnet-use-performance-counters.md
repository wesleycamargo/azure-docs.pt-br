<properties linkid="dev-net-commons-tasks-profiling" urlDisplayName="Performance Profiling" pageTitle="Use Performance Counters in Azure (.NET)" metaKeywords="Azure performance counters, Azure performance profiling, Azure performance counters C#, Azure performance profiling C#" description="Learn how to enable and collect data from performance counters in Azure applications. " metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Using performance counters in Azure" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ryanwi"></tags>

# Usando contadores de desempenho no Azure

Você pode usar os contadores de desempenho em um aplicativo do Azure para
coletar dados que podem ajudar a determinar os afunilamentos do
sistema e ajustar o desempenho do sistema e do aplicativo. Os contadores de desempenho disponíveis para o Windows Server 2008, o Windows Server 2012, o IIS e o ASP.NET podem ser coletados e usados para determinar a integridade de seu aplicativo do Azure.

Você pode configurar os contadores de desempenho antes da implantação ou em tempo de execução no Visual Studio 2012 ou no Visual Studio 2013 usando o SDK do Azure 2.0 ou posterior. Para obter mais informações, consulte [Configurando o Diagnóstico no Azure][Configurando o Diagnóstico no Azure]. Para obter mais informações sobre configurar manualmente os contadores de desempenho no seu aplicativo, consulte [Como configurar os contadores de desempenho][Como configurar os contadores de desempenho].

Para obter orientações detalhadas adicionais sobre como criar um log e uma estratégia de rastreamento e usar diagnóstico e outras técnicas para solucionar problemas, consulte [Práticas recomendadas para a solução de problemas no desenvolvimento de aplicativos do Azure][Práticas recomendadas para a solução de problemas no desenvolvimento de aplicativos do Azure].

## <a name="nextsteps"> </a>Próximas etapas

Siga estes links para saber como implementar cenários de solução de problemas mais complexos.

-   [Teste de desempenho de um serviço de nuvem][Teste de desempenho de um serviço de nuvem]
-   [Práticas recomendadas para a solução de problemas no desenvolvimento de aplicativos do Azure][Práticas recomendadas para a solução de problemas no desenvolvimento de aplicativos do Azure]
-   [Como monitorar serviços de nuvem][Como monitorar serviços de nuvem]
-   [Como usar o Bloco de Aplicativos de Dimensionamento Automático][Como usar o Bloco de Aplicativos de Dimensionamento Automático]
-   [Criando aplicativos de nuvem elásticos e resilientes][Criando aplicativos de nuvem elásticos e resilientes]

## <a name="additional"> </a> Recursos adicionais

-   [Habilitando o diagnóstico no Azure][Habilitando o diagnóstico no Azure]
-   [Coletando dados do log usando o Diagnóstico do Azure][Coletando dados do log usando o Diagnóstico do Azure]
-   [Depurando um aplicativo do Azure][Depurando um aplicativo do Azure]

  [Configurando o Diagnóstico no Azure]: http://msdn.microsoft.com/pt-BR/library/windowsazure/dn186185.aspx
  [Como configurar os contadores de desempenho]: http://msdn.microsoft.com/pt-BR/library/azure/dn535595.aspx
  [Práticas recomendadas para a solução de problemas no desenvolvimento de aplicativos do Azure]: http://msdn.microsoft.com/pt-BR/library/windowsazure/hh771389.aspx
  [Teste de desempenho de um serviço de nuvem]: http://msdn.microsoft.com/pt-BR/library/azure/hh369930.aspx
  [Como monitorar serviços de nuvem]: http://azure.microsoft.com/pt-BR/documentation/articles/cloud-services-how-to-monitor/
  [Como usar o Bloco de Aplicativos de Dimensionamento Automático]: http://azure.microsoft.com/pt-BR/documentation/articles/cloud-services-dotnet-autoscaling-application-block/
  [Criando aplicativos de nuvem elásticos e resilientes]: http://msdn.microsoft.com/pt-BR/library/hh680949(PandP.50).aspx
  [Habilitando o diagnóstico no Azure]: http://azure.microsoft.com/pt-BR/documentation/articles/cloud-services-dotnet-diagnostics/
  [Coletando dados do log usando o Diagnóstico do Azure]: http://msdn.microsoft.com/pt-BR/library/windowsazure/gg433048.aspx
  [Depurando um aplicativo do Azure]: http://msdn.microsoft.com/pt-BR/library/windowsazure/ee405479.aspx
