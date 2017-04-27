---
title: Monitorar um aplicativo Web | Microsoft Docs
description: Saiba como configurar o Monitoramento em seu aplicativo Web
services: App-Service
keywords: 
author: btardif
ms.author: byvinyal
ms.date: 04/04/2017
ms.topic: article
ms.service: app-service-web
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 63bfc6922de224f56186003991f4a51d8f99ed35
ms.lasthandoff: 04/15/2017

---
# <a name="monitor-app-service"></a>Monitorar o Serviço de Aplicativo
Este tutorial mostra como usar as ferramentas internas da plataforma para monitorar e diagnosticar o aplicativo hospedado no Serviço de Aplicativo. 

## <a name="in-this-tutorial"></a>Neste Tutorial

1. [Gerenciador de Processos](#explorer)
    - Obter informações detalhadas sobre o aplicativo em execução entre instâncias do seu plano de Serviço de Aplicativo
1. [Métricas de Serviço de Aplicativo](#metrics) 
   - Saiba como monitorar seu aplicativo usando os gráficos internos
   - Configurar gráficos para atender às suas necessidades
   - Criar um painel personalizado fixando os gráficos personalizados
1. [Configurar Alertas](#alerts)
    - Saiba como configurar alertas tanto para o plano do Serviço de Aplicativo como para o Aplicativo
1. [Complemento do Serviço de Aplicativo](#Companion)
    - Monitorar e solucionar problemas do aplicativo usando um dispositivo móvel.
1. [Configurar o Registro em Log](#logging)
    - Saiba como coletar os logs do aplicativo e do servidor.
    - Saiba mais sobre os diferentes locais para armazenar seus logs e como alcançá-los.
1. [Streaming de Log](#streaming)
    - Use logs de Streaming para exibir o aplicativo e os logs wc3 conforme são emitidos.
1. [Depuração Remota](#remote)
    - Use o Visual Studio para depurar remotamente o projeto em execução no serviço de aplicativo.
1. [Diagnosticar e resolver problemas](#diagnose)
    - Identificar problemas com o aplicativo e obter informações sobre como resolvê-los.
1. [Application Insights](#insights)
    - Criação de perfil avançado e monitoramento do seu aplicativo

## <a name="before-you-begin"></a>Antes de começar

- Você precisa de um aplicativo Web para monitorar e seguir as etapas destacadas. 
    - É possível criar um aplicativo seguindo as etapas descritas no tutorial [Criar um aplicativo ASP.NET no Azure com Banco de dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md).

- Caso queira testar a **Depuração Remota** do seu aplicativo, você precisará do Visual Studio. 
    - Se ainda não tiver o Visual Studio 2017 instalado, é possível baixar e usar o [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) gratuito. 
    - Verifique se você habilitou o **desenvolvimento do Azure** durante a instalação do Visual Studio.

## <a name="explorer"></a> Etapa 1 - Gerenciador de Processos

O Gerenciador de Processos é uma ferramenta que permite obter informações detalhadas sobre o funcionamento interno de seu plano de Serviço de Aplicativo.

Use **Gerenciador de Processos** para:

- Enumere todos os processos entre diferentes instâncias do seu plano de Serviço de Aplicativo.
- Analise detalhadamente e exiba os identificadores e módulos associados a cada processo. 
- Exibir CPU, Conjunto de trabalho e contagem de Threads no nível de processo para ajudá-lo a identificar processos sem controle
- Localize identificadores de arquivos abertos e, até mesmo, encerre uma instância de processo específico.

O Gerenciador de Processos pode estar em **Monitoramento** > **Explorador de Processos**.

![Gerenciador de Processos](media/app-service-web-tutorial-monitoring/app-service-monitor-processexplorer.png)

## <a name="metrics"></a> Etapa 2 - Exibir métricas de Serviço de Aplicativo
As **Métricas** fornecem informações detalhadas sobre o aplicativo Web e suas interações com os usuários e a plataforma.

É possível usar métricas para obter informações sobre:
- quantos recursos seu aplicativo está usando
- volume de tráfego para seu aplicativo
- falhas/solicitações gerais
- volume de dados de entrada/saída

Para qualquer aplicativo hospedado no serviço de aplicativo, será necessário monitorar o aplicativo Web e o plano de Serviço de Aplicativo.

- Métricas de **Aplicativo** fornecem informações sobre falhas/solicitações http e tempo médio de resposta.
- Métricas do **Plano de Serviço de Aplicativo** fornecem informações sobre a utilização de recursos.

O portal do Azure tem uma maneira rápida para inspecionar visualmente as métricas de seu aplicativo, usando o **Azure Monitor**.

- Vá para a folha **Visão Geral** do aplicativo que deseja monitorar.

![Monitorar o aplicativo](media/app-service-web-tutorial-monitoring/app-service-monitor.png)

- É possível exibir as métricas como um bloco **Monitoramento**.
- Clique no bloco para editar e configurar as métricas e o intervalo de tempo a serem exibidos.

![Configurar o Gráfico](media/app-service-web-tutorial-monitoring/app-service-monitor-configure.png)

- É possível fixar gráficos personalizados ao painel para referência rápida e de fácil acesso.

![Gráfico de PIN](media/app-service-web-tutorial-monitoring/app-service-monitor-pin.png)

> [!TIP]
> Saiba mais sobre o Azure Monitor com os seguintes links:
> - [Introdução ao Azure Monitor](..\monitoring-and-diagnostics\monitoring-overview.md)
> - [Métricas do Azure](..\monitoring-and-diagnostics\monitoring-overview-metrics.md)
> - [Métricas compatíveis com o Azure Monitor](..\monitoring-and-diagnostics\monitoring-supported-metrics.md#microsoftwebsites-including-functions)
> - [Painéis do portal do Azure](..\azure-portal\azure-portal-dashboards.md)

## <a name="alerts"></a>Etapa 3 - Configurar Alertas

**Alertas** permitem automatizar o monitoramento do aplicativo.

Use alertas para ser notificado quando condições interessantes que afetam o aplicativo forem detectadas.

Para criar um alerta:
- Vá para a folha **Visão Geral** do aplicativo que deseja monitorar.
- A partir do menu, navegue até **Monitoramento** > **Alertas**
- Selecione **[+] Adicionar Alerta**
- Configure o alerta, conforme necessário.

![Alertas](media/app-service-web-tutorial-monitoring/app-service-monitor-alerts.png)

> [!TIP]
> Saiba mais sobre Alertas do Azure com os seguintes links:
> - [O que são alertas no Microsoft Azure?](..\monitoring-and-diagnostics\monitoring-overview-alerts.md)
> - [Executar uma Ação com Base nas Métricas](..\monitoring-and-diagnostics\monitoring-overview.md)

## <a name="companion"></a> Etapa 4 - Complemento do Serviço de Aplicativo
O **Complemento do Serviço de Aplicativo** oferece uma maneira conveniente para monitorar o aplicativo com uma experiência nativa no dispositivo móvel (iOS ou Android).

Use o Complemento do Serviço de Aplicativo para:
- Analisar as métricas de aplicativo
- Analisar e agir sobre alertas de aplicativos e recomendações
- Executar a solução de problemas básicos (procurar, iniciar, parar, reiniciar o aplicativo)
- Recebe notificações por push para eventos críticos.

![Complemento do Serviço de Aplicativo](media/app-service-web-tutorial-monitoring/app-service-companion.png)

[![Loja de Aplicativos do Complemento do Serviço de Aplicativo](media/app-service-web-tutorial-monitoring/app-service-companion-appStore.png)](https://itunes.apple.com/app/azure-app-service-companion/id1146659260)
[![Google Play do Complemento do Serviço de Aplicativo ](media/app-service-web-tutorial-monitoring/app-service-companion-googlePlay.png)](https://play.google.com/store/apps/details?id=azureApps.AzureApps)

É possível instalar o complemento do Serviço de Aplicativo pela [App Store](https://itunes.apple.com/app/azure-app-service-companion/id1146659260) ou pelo [Google Play](https://play.google.com/store/apps/details?id=azureApps.AzureApps)

## <a name="logging"></a> Etapa 5 - Registrar em Log
O registro em log permite coletar ambos os logs do **Diagnóstico de Aplicativos** e do **Diagnóstico de Servidor Web** para seu aplicativo Web.

Use Logs de Diagnóstico para compreender o comportamento do aplicativo, solucionar problemas de aplicativos e entender as condições de falha.

### <a name="application-diagnostics"></a>Diagnóstico de Aplicativos
O diagnóstico de aplicativo permite capturar rastreamentos produzidos pelo aplicativo em tempo de execução. 

Para habilitar o Log de Aplicativo:

- Vá para **Monitoramento** > **Logs de Diagnóstico**. 
- Habilite o Log de Aplicativo usando as alternâncias.

Os Logs de Aplicativo podem ser armazenados em um sistema de arquivos do Aplicativo Web ou enviados para armazenamento de blobs.

> [!TIP]
> Para cenários de produção, é recomendável usar o armazenamento de blobs

![Monitorar o aplicativo](media/app-service-web-tutorial-monitoring/app-service-monitor-applogs.png)

No ASP.NET, é possível registrar rastreamentos de aplicativo usando [System.Diagnostics.Trace clase](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx) para gerar eventos que são capturados pela infraestrutura de log. Você também pode especificar a severidade do rastreamento para filtragem mais fácil.

```csharp
public ActionResult Delete(Guid? id)
{
    System.Diagnostics.Trace.TraceInformation("GET /Todos/Delete/" + id);
    if (id == null)
    {
        System.Diagnostics.Trace.TraceError("/Todos/Delete/ failed, ID is null");
        return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
    }
    Todo todo = db.Todoes.Find(id);
    if (todo == null)
    {
        System.Diagnostics.Trace.TraceWarning("/Todos/Delete/ failed, ID: " + id + " could not be found");
        return HttpNotFound();
    }
    System.Diagnostics.Trace.TraceInformation("GET /Todos/Delete/" + id + "completed successfully");
    return View(todo);
}
```

> [!IMPORTANT]
> Habilitar o registro no log tem um impacto sobre o desempenho do aplicativo e a utilização de recursos. Para cenários de produção, logs de erros são recomendados. Somente habilite o registro no log mais detalhado quando investigar problemas.

 ### <a name="web-server-diagnostics"></a>Diagnóstico de Servidor Web
O Serviço de Aplicativo pode coletar três tipos diferentes de logs do servidor:

- **Log do Servidor Web** 
    - Informações sobre transações HTTP usando o [formato do arquivo de log estendido do W3C](https://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). 
    - Útil ao determinar métricas globais do site como o número de solicitações processadas ou a quantidade de solicitações de um endereço IP específico.
- **Logs de Erros Detalhados** 
    - Informações detalhadas de erros para códigos de status HTTP que indiquem uma falha (código de status 400 ou superior). 
- **Rastreamento de Solicitações com Falha** 
    - Informações detalhadas sobre solicitações com falha, incluindo um rastreamento dos componentes IIS usados para processar a solicitação e o tempo levado em cada componente. 
    - O logs de solicitações com falha são úteis ao tentar isolar a causa de um erro HTTP específico.

Para habilitar o Log de Servidor:
- vá para **Monitoramento** > **Logs de Diagnóstico**. 
- Habilite os diferentes tipos de Diagnóstico de Servidor Web usando as alternâncias.

![Monitorar o aplicativo](media/app-service-web-tutorial-monitoring/app-service-monitor-serverlogs.png)

> [!IMPORTANT]
> Habilitar o registro no log tem um impacto sobre o desempenho do aplicativo e a utilização de recursos. Para Cenários de Produção, os Logs de Erros são recomendados, Somente habilite o registro no log mais detalhado quando investigar problemas.

### <a name="accessing-logs"></a>Acessar Logs
Os logs armazenados no armazenamento de blobs são acessados usando o Gerenciador de Armazenamento do Azure.

Os logs armazenados no sistema de arquivos do aplicativo Web são acessados por meio de FTP nos seguintes caminhos:

- **Logs do aplicativo** - /LogFiles/Application/. 
    - Essa pasta contém um ou mais arquivos de texto que contêm informações produzidas pelo log do aplicativo.
- **Rastreamento de Solicitação Falha** - /LogFiles/W3SVC#########/. 
    - Esta pasta contém um arquivo XSL e um ou mais arquivos XML. 
- **Logs de erro do aplicativo** - /LogFiles/DetailedErrors/. 
    - Esta pasta contém um ou mais arquivos .htm com informações abrangentes sobre erros HTTP gerados pelo aplicativo.
- **Logs do Web Server** - /LogFiles/http/RawLogs. 
    - Esta pasta contém um ou mais arquivos de texto formatados usando o formato do arquivo de log estendido do W3C.

## <a name="streaming"></a> Etapa 6 - Streaming de Log
O Serviço de Aplicativo pode fazer streaming de **Logs do Aplicativo** e **Logs do Servidor Web** conforme eles são gerados. 

Logs de streaming são convenientes ao depurar um aplicativo, uma vez que economiza tempo em comparação com o acesso aos logs através de outros métodos FTP.

> [!TIP]
> Antes de tentar fazer streaming de logs certifique-se de que você habilitou a coleta de logs, conforme descrito na seção [Registrar em Log](#logging).

Para fazer streaming de logs:
- vá para **Monitoramento**> **Fluxo de Log**
- Selecione **Logs do Aplicativo** ou **Logs do Servidor Web**, dependendo de quais informações você está procurando.
- A partir daqui, também será possível pausar, reiniciar e limpar o buffer.

![Logs de streaming](media/app-service-web-tutorial-monitoring/app-service-monitor-logstream.png)

> [!TIP]
> Os logs são gerados apenas quando há tráfego no aplicativo, também é possível aumentar o nível de detalhes dos logs para obter mais eventos ou informações.

## <a name="remote"></a> Etapa 7 - Depuração Remota
A **Depuração remota** permite anexar um depurador ao seu aplicativo Web em execução na nuvem. É possível pode definir pontos de interrupção, manipular a memória diretamente, percorrer o código e até mesmo alterar o caminho do código, exatamente como para um aplicativo executado localmente.

Use a depuração remota juntamente com logs de diagnóstico para localizar e corrigir problemas com o aplicativo.

Para anexar o depurador ao seu aplicativo em execução na nuvem:

- Usando o Visual Studio 2017, abra a solução para o aplicativo que deseja depurar 
- Defina alguns pontos de interrupção, exatamente como faria para desenvolvimento local.
- Abra o **cloud explorer** (ctr + /, ctrl + x).
- Faça logon com suas credenciais do Azure, conforme necessário.
- Localize o aplicativo que deseja depurar
- Selecione **Anexar Depurador** do painel **Ações**.

![Depuração Remota](media/app-service-web-tutorial-monitoring/app-service-monitor-vsdebug.png)

O Visual Studio configura seu aplicativo para depuração remota e inicia uma janela do navegador que navega até o aplicativo. Navegue pelo aplicativo para disparar pontos de interrupção e percorrer o código.

> [!WARNING]
> A execução em modo de depuração em produção não é recomendada. Se o aplicativo de produção não for dimensionado para várias instâncias de servidor, a depuração impedirá que o servidor Web responda a outras solicitações. Para solucionar problemas de produção, os melhores recursos são [configurar o registro em log](#logging) e [Application Insight](#insights)

## <a name="diagnose"></a> Etapa 8 - Diagnosticar e resolver problemas
**Diagnosticar e resolver problemas** é uma ferramenta interna que verifica as últimas 24 horas de atividade do seu aplicativo Web. A Experiência de Usuário apresenta uma visão resumida de todos os problemas identificados.

Use esse recurso para ajudá-lo a distinguir os problemas de aplicativo dos problemas da plataforma e encontrar possíveis atenuações para melhorar seu Aplicativo Web.

![Diagnosticar e Resolver Problemas](media/app-service-web-tutorial-monitoring/app-service-monitor-diagnosis.png)

## <a name="insights"></a> Etapa 9 - Application Insights
O **Application Insights** fornece perfil de aplicativo e recursos avançados de monitoramento para seu aplicativo. 

Use o Insights de Aplicativos para detectar e diagnosticar exceções e problemas de desempenho no Aplicativo Web.

É possível habilitar o Application Insights para seu aplicativo Web em **Monitoramento** > **Application Insights** 

> [!NOTE]
> O Application Insights pode solicitar a instalação da extensão de site do Application Insights para iniciar a coleta de dados. Instalar a extensão de site causa o reinício do aplicativo.

![Application Insights](media/app-service-web-tutorial-monitoring/app-service-monitor-appinsights.png)

O Application Insights possui um avançado conjunto de recursos, para saber mais siga os links incluídos na seção [Próximas etapas](#next).

## <a name="next"></a> Próximas etapas

 - [O que é o Application Insights](..\application-insights\app-insights-overview.md)
 - [Monitorar o desempenho do aplicativo Web do Azure com o Application Insights](..\application-insights\app-insights-azure-web-apps.md)
 - [Monitorar a disponibilidade e capacidade de resposta de qualquer site da Web com o Application Insights](..\application-insights\app-insights-monitor-web-app-availability.md)
