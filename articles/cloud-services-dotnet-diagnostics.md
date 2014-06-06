<properties linkid="dev-net-commons-tasks-diagnostics" urlDisplayName="Diagnóstico" pageTitle="Como usar diagnósticos (.NET) - Guia de recursos do Azure" metaKeywords="monitoramento de diagnóstico do Azure, logs de despejos de memória C#" description="Saiba como usar dados de diagnóstico no Azure para depuração, medição de desempenho, monitoramento, análise de tráfego e muito mais." metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Habilitação de diagnóstico no Azure" authors=""  solutions="" writer="ryanwi" manager="" editor=""  />





# Habilitando diagnóstico no Azure

O Diagnóstico do Azure permite coletar dados de diagnóstico de uma função de trabalho ou de uma função web em execução no Azure. Você pode usar dados de diagnóstico para tarefas de depuração e solução de problemas, medição de desempenho, monitoramento de uso de recursos, análise de tráfego e planejamento da capacidade e auditoria.

Você pode configurar o Diagnóstico antes da implantação ou em tempo de execução no Visual Studio 2012 ou no Visual Studio 2013 usando o SDK do Azure 2.0 ou posterior.  Para obter mais informações, consulte [Configurando o Diagnóstico no Azure][]. 

Para obter orientações detalhadas adicionais sobre como criar um log e uma estratégia de rastreamento e usar diagnóstico e outras técnicas para solucionar problemas, consulte [Práticas recomendadas para a solução de problemas no desenvolvimento de aplicativos do Azure][].

Para obter informações sobre como configurar manualmente o Diagnóstico em seu aplicativo ou alterar remotamente a configuração do monitor de diagnóstico, consulte [Coletar dados do log usando o Diagnóstico do Azure][].

<h2>Próximas etapas</h2>

-	[Alterar a configuração do monitor de diagnóstico remotamente][] - depois de implantar seu aplicativo, você pode modificar a configuração de Diagnóstico.
-	[Como monitorar serviços de nuvem] [] - você pode monitorar as métricas-chave de desempenho de seus Serviços de Nuvem no [Portal de Gerenciamento do Azure][].

<h2>Recursos adicionais</h2> 

- [Coletar dados do log usando o Diagnóstico do Azure][]
- [Depurando um aplicativo do Azure][]
- [Configurando o Diagnóstico no Azure][]

  [Práticas recomendadas para a solução de problemas no desenvolvimento de aplicativos do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh771389.aspx
  

  [Usando os contadores de desempenho no Azure]: ../cloud-services-performance-testing-visual-studio-profiler/
  [Como monitorar serviços de nuvem]: ../cloud-services-how-to-monitor/
  [Classe DiagnosticMonitorTraceListener (a página pode estar em inglês)]: http://msdn.microsoft.com/pt-br/library/windowsazure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx
  [Como usar o arquivo de configuração de Diagnóstico do Azure (a página pode estar em inglês)]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh411551.aspx
  [Adicionando solicitações de rastreamento com falha na referência de configuração do IIS 7.0 (a página pode estar em inglês)]: http://www.iis.net/ConfigReference/system.webServer/tracing/traceFailedRequests/add
  [Usando os contadores de desempenho no Azure (a página pode estar em inglês)]: /pt-br/develop/net/common-tasks/performance-profiling/
  [Como configurar recursos de armazenamento local (a página pode estar em inglês)]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee758708.aspx
  [Procurando recursos de armazenamento com o Gerenciador de Servidores (a página pode estar em inglês)]: http://msdn.microsoft.com/pt-br/library/windowsazure/ff683677.aspx
  [Pesquisador de Armazenamento do Azure (a página pode estar em inglês)]: http://azurestorageexplorer.codeplex.com/
  [Gerenciador de diagnóstico do Azure (a página pode estar em inglês)]: http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx
  [Coletar dados do log usando o Diagnóstico do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433048.aspx
  [Depurando um aplicativo do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee405479.aspx
  [Usar o arquivo de configuração de Diagnóstico do Azure (a página pode estar em inglês)]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh411551.aspx
  [Enumeração LogLevel (a página pode estar em inglês)]: http://msdn.microsoft.com/pt-br/library/windowsazure/microsoft.windowsazure.diagnostics.loglevel.aspx
  [Método OnStart]: http://msdn.microsoft.com/pt-br/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
  [Esquema de configuração de diagnóstico do Azure]: http://msdn.microsoft.com/pt-br/library/gg593185.aspx
  [Como usar o Serviço de Armazenamento de Tabela (a página pode estar em inglês)]: http://www.windowsazure.com/pt-br/develop/net/how-to-guides/table-services/
  [Como usar o Serviço de Armazenamento de Blob do Azure (a página pode estar em inglês)]: http://www.windowsazure.com/pt-br/develop/net/how-to-guides/blob-storage/
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Alterar a configuração do monitor de diagnóstico remotamente]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg432992.aspx
  [Configurando o Diagnóstico no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/dn186185.aspx  
   

