---
title: Solucionar problemas com o Profiler do Azure Application Insights | Microsoft Docs
description: Esta página contém etapas de solução de problemas e informações para ajudar os desenvolvedores que estão tendo problemas para habilitar ou usar o Profiler do Application Insights.
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
ms.openlocfilehash: d424316cb8287dcf2a9ff04cf8e6889d5bada2cd
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54081464"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Solucionar problemas ao habilitar ou exibir o Profiler do Application Insights

## <a id="troubleshooting"></a>Solução de problemas gerais

### <a name="profiles-are-only-uploaded-if-there-are-requests-to-your-application-while-the-profiler-is-running"></a>Os perfis são carregados apenas se houver solicitações para seu aplicativo enquanto o criador de perfil estiver em execução
O Application Insights Profiler coleta dados do criador de perfil por dois minutos a cada hora ou quando o botão [**Profile Now**](profiler-settings.md ?toc=/azure/azure-monitor/toc.json) é pressionado na página **Configure Profiler Insights do aplicativo**. Mas os dados de criação de perfil só são carregados quando podem ser anexados a uma solicitação que aconteceu enquanto o criador de perfil estava em execução. 

O criador de perfil grava mensagens de rastreamento e eventos personalizados no recurso de insights do aplicativo. Você pode usar esses eventos para ver como o criador de perfil está sendo executado. Se você acha que o criador de perfil deve estar em execução e capturando rastreios, mas não os está vendo na página Desempenho, é possível verificar como o criador de perfil está em execução:

1. Procure por mensagens de rastreio e eventos personalizados enviados pelo criador de perfil para o recurso Application Insights. Você pode usar essa cadeia de caracteres de pesquisa para encontrar os dados relevantes:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    Aqui está um exemplo de duas pesquisas de dois recursos diferentes da IA na captura de tela abaixo. 
    
    * O da esquerda é de um aplicativo que não está recebendo solicitações enquanto o profiler está em execução. Você pode ver a mensagem de que o upload foi cancelado devido a nenhuma atividade. 

    * No exemplo à direita, você pode ver que o criador de perfil iniciou e enviou eventos personalizados quando detectou solicitações que ocorreram enquanto o criador de perfil estava em execução. Se você vir o evento customizado ServiceProfilerSample, isso significa que o criador de perfil anexou um rastreio a uma solicitação e você pode visualizar o rastreio a partir da página de desempenho do Application Insights.

    * Se você não visualizar nenhuma telemetria, o gerenciador de perfis não estará em execução. Pode ser necessário ler as seções de solução de problemas para seu tipo de aplicativo específico neste documento abaixo.  

     ![Pesquisa Profiler Telemetry][profiler-search-telemetry]

1. Se houver solicitações durante o período de tempo que o criador de perfil executou, verifique se as solicitações são tratadas pela parte do seu aplicativo que possui o criador de perfil habilitado. Às vezes, os aplicativos consistem em vários componentes, mas o criador de perfil só é habilitado para alguns, nem todos, dos componentes. A página Configurar Perfil do Application Insights mostrará os componentes que carregaram rastreios.

### <a name="other-things-to-check"></a>Outros itens a serem verificados:
* Seu aplicativo está em execução no .NET Framework 4.6.
* Se seu aplicativo web é um aplicativo ASP.NET Core, deve estar executando pelo menos ASP.NET Core 2.0.
* Se os dados que você está tentando exibir mais de duas semanas, tente limitar seu filtro de tempo e tente novamente. Rastreamentos são excluídos após sete dias.
* Certifique-se de que um firewall ou proxies não tenham bloqueado o acesso a https://gateway.azureserviceprofiler.net.

### <a id="double-counting"></a>Dois segmentos paralelos de contagem

Em alguns casos, a métrica de tempo toral no visualizador de pilha é maior que a duração da solicitação.

Essa situação poderá ocorrer quando dois ou mais threads estiverem associados a uma solicitação e eles estiverem operando em paralelo. Nesse caso, o tempo total de thread é maior que o tempo decorrido. Um thread pode estar aguardando até que o outro seja concluído. O visualizador tenta detectar essa situação e omite a espera desinteressante, mas erra ao exibir informações demais em vez de omitir o que pode ser uma informação crítica.

Quando você vir threads paralelos em seus rastreamentos, determine quais threads estão aguardando para você poder verificar o caminho crítico para a solicitação. Na maioria dos casos, o thread rapidamente entra em estado de espera é simplesmente aguardando outros threads. Concentre-se nos outros threads e ignore o tempo nos threads em espera.

### <a name="error-report-in-the-profile-viewer"></a>Relatório de erros no visualizador de perfil
Envie um tíquete de suporte no portal. Certifique-se de incluir uma ID de correlação da mensagem de erro.

## <a name="troubleshooting-profiler-on-app-services"></a>Solução de problemas no Profiler nos serviços de aplicativo
### <a name="for-the-profiler-to-work-properly"></a>Para o criador de perfil funcione corretamente:
* Seu plano de serviço de aplicativo da web deve ser de nível Básico ou superior.
* Seu aplicativo da web deve ter o Application Insights ativado.
* Seu aplicativo da Web precisa ter a configuração do aplicativo **APPINSIGHTS_INSTRUMENTATIONKEY** configurada com a mesma chave de instrumentação usada pelo SDK do Application Insights.
* Seu aplicativo da Web precisa ter a configuração do aplicativo **APPINSIGHTS_PROFILERFEATURE_VERSION** definida e definida como 1.0.0.
* Seu aplicativo da web precisa ter a configuração de aplicativo **DiagnosticServices_EXTENSION_VERSION** definida e definir o valor como ~ 3.
* O **ApplicationInsightsProfiler3** deve estar executando o trabalho web. Você pode verificar o trabalho web acessando [Kudu](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/)e abrir o **painel de trabalhos Web** sob o menu Ferramentas. Como você pode ver nas capturas de tela abaixo, clicando no link ApplicationInsightsProfiler2, você pode ver detalhes do webjob, incluindo o log.

    Aqui está o link que você precisa clicar para ver os detalhes do webjob: 

    ![trabalho Web do criador de perfil]

    Aqui está a página que mostra os detalhes. Você pode baixar o log e enviá-lo para a nossa equipe, se você não consegue descobrir por que o criador de perfil não está funcionando para você.
    
    ![Criador de perfil de trabalho Web de log]

### <a name="manual-installation"></a>Instalação manual

Quando você configura o Profiler, são feitas atualizações nas configurações do aplicativo Web. Será possível aplicar as atualizações manualmente se seu ambiente exigir. Um exemplo pode ser que seu aplicativo está sendo executado em um ambiente de Aplicativos Web para o PowerApps.

1. No painel **Controle do Aplicativo Web**, abra **Configurações**.
1. Defina a **versão do .Net Framework** como **v4.6**.
1. Defina **Sempre ativo** como **Ativado**.
1. Adicione a configuração de aplicativo **APPINSIGHTS_INSTRUMENTATIONKEY** e defina o valor como a mesma chave de instrumentação usada pelo SDK.
1. Adicione a configuração do aplicativo **APPINSIGHTS_PROFILERFEATURE_VERSION** e defina o valor como 1.0.0.
1. Adicione o **DiagnosticServices_EXTENSION_VERSION** configuração de aplicativo e defina o valor para ~ 3.

### <a name="too-many-active-profiling-sessions"></a>Número excessivo de sessões de criação de perfil ativas

No momento, é possível habilitar o Profiler em no máximo quatro aplicativos Web do Azure e slots de implantação em execução no mesmo plano de serviço. Se você tiver mais aplicativos da Web do que aqueles em execução em um plano de serviço de aplicativo, poderá ver um Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException lançado pelo criador de perfil. O criador de perfil é executado separadamente para cada aplicativo da Web e tenta iniciar uma sessão do ETW para cada aplicativo. Mas há um número limitado de sessões do ETW que podem estar ativas ao mesmo tempo. Se o trabalho Web do Profiler estiver relatando um número excessivo de sessões de criação de perfil ativas, mova alguns aplicativos Web para um plano de serviço diferente.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Erro de implantação: Diretório não vazio 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Se você estiver reimplementando seu aplicativo da Web em um recurso de Web Apps com o Profiler habilitado, poderá ver a seguinte mensagem:

*Diretório não vazio 'D:\\home\\site\\wwwroot\\App_Data\\jobs'*

Este erro ocorrerá se você executar a Implantação da Web de scripts ou do Pipeline de Implantação do Azure DevOps. A solução é adicionar os seguintes parâmetros de implantação adicionados à tarefa de Implantação da Web:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Esses parâmetros excluem a pasta usada pelo Application Insights Profiler e desbloqueiam o processo de reimplantação. Eles não afetam a instância do Profiler em execução no momento.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Como fazer para determinar se o Application Insights Profiler está em execução?

O Profiler é executado como um trabalho Web contínuo no aplicativo Web. É possível abrir o recurso do aplicativo Web no [Portal do Azure](https://portal.azure.com). Se o painel **WebJobs**, verifique o status de **ApplicationInsightsProfiler**. Se ele não estiver em execução, abra **Registros** para obter mais informações.

## <a name="troubleshooting-problems-with-profiler-and-wad"></a>Solucionando problemas com o Profiler e o WAD

Há três coisas para verificar para ver se o criador de perfil está configurado corretamente pelo WAD. Primeiro, você precisa verificar se o conteúdo da configuração do WAD implantado é o esperado. Segundo, você precisa verificar se o WAD passa o iKey adequado na linha de comando do criador de perfil. Terceiro, você pode verificar o arquivo de log do criador de perfil para ver se o criador de perfil foi executado, mas está recebendo um erro. 

Para verificar as configurações que foram usadas para configurar o WAD, você precisa efetuar login na VM e abrir o arquivo de log neste local: 
```
c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.logs  
```
Nesse arquivo, você pode procurar a cadeia de caracteres "WadCfg" e você encontrará as configurações que foram passadas para a VM para configurar o WAD. Você pode verificar esse iKey utilizado pelo coletor de criador de perfil está correta.

Segundo, você pode verificar a linha de comando usada para iniciar o criador de perfil. O arquivo a seguir contém os argumentos usados para iniciar o criador de perfil.
```
D:\ProgramData\ApplicationInsightsProfiler\config.json
```
Verifique se o ikey na linha de comando do criador de perfil está correto. 

Terceiro, usando o caminho encontrado no arquivo config.json acima, verifique o arquivo de log do criador de perfil. Ele mostrará informações de depuração indicando as configurações que o criador de perfil está usando e mensagens de status e erro do criador de perfil. Se o criador de perfil estiver em execução enquanto o aplicativo estiver recebendo solicitações, a seguinte mensagem será exibida: Atividade detectada de iKey. Quando o rastreamento estiver sendo carregado, a seguinte mensagem será exibida: Iniciar upload de rastreamento. 


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[trabalho Web do criador de perfil]:./media/profiler-troubleshooting/Profiler-webjob.png
[Criador de perfil de trabalho Web de log]:./media/profiler-troubleshooting/Profiler-webjob-log.png








