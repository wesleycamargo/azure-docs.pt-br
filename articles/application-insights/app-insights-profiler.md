---
title: "Criação de perfil de aplicativos Web ao vivo no Azure com o Application Insights | Microsoft Docs"
description: "Identifique o afunilamento em seu código de servidor da Web com um criador de perfil de baixa capacidade."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: ed685f0a4ed26fb8d1c766f87210a99d2b5270ac
ms.lasthandoff: 04/12/2017


---
# <a name="profiling-live-azure-web-apps-with-application-insights-preview"></a>Criação de perfil de aplicativos Web do Azure ao vivo com o Application Insights (visualização)

*Esse recurso do Application Insights está em modo de visualização.*

Descubra quanto tempo é gasto em cada método no seu aplicativo Web ao vivo usando a ferramenta de criação de perfil de [Azure Application Insights](app-insights-overview.md). Ele mostra perfis detalhados de solicitações ao vivo que foram atendidas por seu aplicativo e realça o 'hot path' que está usando mais tempo. Ele seleciona automaticamente exemplos que têm tempos de resposta diferentes. O criador de perfil usa várias técnicas para minimizar a sobrecarga. 

O criador de perfil atualmente funciona para aplicativos Web do ASP.NET em execução no serviços de aplicativo do Azure, pelo menos o tipo de preço Básico. (Se você estiver usando o ASP.NET Core, a estrutura de destino deve ser `.NetCoreApp`.)

<a id="installation"></a>
## <a name="enable-the-profiler"></a>Habilitar o criador de perfil

[Instalar o Application Insights](app-insights-asp-net.md) em seu código. Se já estiver instalado, verifique se que você tem a versão mais recente. (Para fazer isso, clique com o botão direito do mouse no projeto no Gerenciador de Soluções e escolha Gerenciar pacotes NuGet. Selecione as atualizações e todos os pacotes de atualização). Implante seu aplicativo novamente.

*Usando o ASP.NET Core? [Marque aqui](#aspnetcore).*

Em [https://portal.azure.com](https://portal.azure.com), abra o recurso Application Insights para seu aplicativo Web. Abra **desempenho** e clique em **configurar**. Selecione seu aplicativo e siga o assistente.

![Na folha de desempenho, clique em configurar][performance-blade]

* *Nenhum botão Configurar? Use o [procedimento manual](#manual-installation).*

Se você precisar parar ou reiniciar o criador de perfil, você o encontrará **no recurso de serviço de aplicativo**, na **trabalhos Web**. Para excluí-lo, procure em **extensões**.

Se você usar WebDeploy para implantar as alterações ao seu aplicativo Web, certifique-se de que você exclua o **App_Data** pasta sejam excluídas durante a implantação. Caso contrário, os arquivos de extensão do criador de perfil serão excluídos quando você implanta em seguida o aplicativo Web no Azure.

## <a name="viewing-profiler-data"></a>Exibindo dados do criador de perfil

Abra a folha de desempenho e role para baixo até a lista de operações.




![Coluna de Exemplos da folha de desempenho do Application Insights][performance-blade-examples]

As colunas na tabela são:

* **Contagem de** -o número dessas solicitações no intervalo de tempo da folha.
* **Mediana** - tempo típica seu aplicativo leva para responder a uma solicitação. Metade de todas as respostas foram mais rápida do que isso.
* **95 Percentil** 95% de respostas foram mais rápido do que isso. Se esta figura é muito diferente do mediana, pode haver um problema intermitente com seu aplicativo. (Ou pode ser explicado por um recurso de design como armazenamento em cache).
* **Exemplos** -um ícone indica que o criador de perfil capturou rastreamentos de pilha para essa operação.

Clique no ícone de exemplos para abrir o Gerenciador de rastreamento. O explorer mostra vários exemplos que capturou o criador de perfil, classificados pelo tempo de resposta.

Selecione um exemplo para mostrar uma divisão de nível de código de tempo gasto na execução da solicitação.

![Gerenciador de rastreamento do Application Insights][trace-explorer]

**Mostrar o hot path** abre o maior de nó de folha ou pelo menos algo fechar. Na maioria dos casos, esse nó será adjacente para um afunilamento de desempenho.



* **Rótulo**: O nome da função ou do evento. A árvore mostra uma combinação de código e eventos que ocorreram (como eventos SQL e http). Os principais eventos representa a duração total da solicitação.
* **Métrica**: O tempo decorrido.
* **Quando**: mostra quando o evento de função/foi executada em relação a outras funções. 

## <a name="how-to-read-performance-data"></a>Como ler dados de desempenho

Criador de perfil de serviço Microsoft usa uma combinação do método de amostragem e instrumentação para analisar o desempenho do seu aplicativo. Quando detalhada coleção está em andamento, o criador de perfil de serviço amostras o ponteiro de instrução de cada CPU da máquina em cada milissegundo. Cada exemplo captura a pilha de chamadas completa do thread em execução no momento, oferecendo informações detalhadas e úteis sobre o que thread estava fazendo no altos e baixos níveis de abstração. Criador de perfil de serviço também coleta outros eventos, como eventos de alternância de contexto, eventos TPL e eventos de pool de threads para acompanhar causalidade e correlação da atividade. 

A pilha de chamadas mostrada na exibição de linha do tempo é o resultado da amostragem e instrumentação acima. Porque cada exemplo captura a pilha de chamadas completa do thread, ele inclui código do .NET framework, bem como outras estruturas que você faz referência.

### <a id="jitnewobj"></a>Alocação de objeto (`clr!JIT\_New or clr!JIT\_Newarr1`)
`clr!JIT\_New and clr!JIT\_Newarr1`são funções auxiliares dentro do .NET framework que aloca memória do heap gerenciado. `clr!JIT\_New`é chamado quando um objeto é alocado. `clr!JIT\_Newarr1`é invocado quando uma matriz de objeto é alocada. Essas duas funções são geralmente muito rápidas e devem levar uma quantidade relativamente pequena de tempo. Se você vir `clr!JIT\_New` ou `clr!JIT\_Newarr1` levar uma quantidade significativa de tempo na linha do tempo, é uma indicação de que o código pode ser alocando muitos objetos e consumindo uma quantidade significativa de memória. 

### <a id="theprestub"></a>Carregamento de código (`clr!ThePreStub`)
`clr!ThePreStub`é uma função auxiliar dentro do .NET framework que prepara o código seja executado pela primeira vez. Isso geralmente inclui, mas não se limitando a compilação JIT (apenas no tempo). Para cada método c#, `clr!ThePreStub` deve ser chamado no máximo uma vez durante a vida útil de um processo.

Se você vir `clr!ThePreStub` leva quantidade significativa de tempo para uma solicitação, ele indica que a solicitação é a primeira alteração que executa o método, e a hora de tempo de execução do .NET framework carregar esse método é significativa. Você pode considerar um processo de aquecimento que essa parte do código é executado antes dos usuários acessá-la ou considere executar NGen em seus assemblies. 

### <a id="lockcontention"></a>Contenção de bloqueio (`clr!JITutil\_MonContention` ou `clr!JITutil\_MonEnterWorker`)
`clr!JITutil\_MonContention`ou `clr!JITutil\_MonEnterWorker` indicar que o segmento atual está aguardando um bloqueio ser liberado. Isso normalmente aparece durante a execução de uma bloqueio instrução c#, invocando o método de monitor. Enter ou invocando um método com o atributo MethodImplOptions. Contenção de bloqueio geralmente ocorre quando um thread adquire um bloqueio, e o thread B tenta adquirir o mesmo bloqueio antes de libera um thread. 

### <a id="ngencold"></a>Carregamento de código (`[COLD]`)
Se o nome do método contém `[COLD]`, como `mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined`, isso significa que o tempo de execução do .NET framework está executando o código que não é otimizado por <a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">Otimização Guiada por perfil</a> pela primeira vez. Para cada método, ele deve aparecer no máximo uma vez durante o tempo de vida do processo. 

Se carregar código leva uma quantidade significativa de tempo para uma solicitação, ele indica que a solicitação é o primeiro para executar a parte não otimizada do método. Você pode considerar um processo que executa essa parte do código para que seus usuários acessá-lo de aquecimento. 

### <a id="httpclientsend"></a>Enviar solicitação HTTP
Métodos como `HttpClient.Send` indicam o código está aguardando uma solicitação HTTP para concluir.

### <a id="sqlcommand"></a>Operação de Banco de Dados
Método como SqlCommand.Execute indica que o código está aguardando concluir uma operação de banco de dados.

### <a id="await"></a>Aguardando (`AWAIT\_TIME`)
`AWAIT\_TIME`indica que o código está aguardando a conclusão de outra tarefa. Isso geralmente acontece com c# 'await' instrução. Quando o código faz c# 'await', o thread esvazia e retorna o controle para o pool de threads e não há nenhum thread está bloqueado aguardando o 'await' Concluir. No entanto, logicamente o thread que fez o await é 'bloqueado' aguardando a conclusão da operação. A `AWAIT\_TIME` indica o tempo bloqueado aguardando a tarefa seja concluída.

### <a id="block"></a>Tempo bloqueado
`BLOCKED_TIME`indica que o código está aguardando outro recurso esteja disponível, como aguardando um objeto de sincronização, aguardando um thread esteja disponível ou aguardando uma solicitação ser concluída. 

### <a id="cpu"></a>Tempo de CPU
A CPU está ocupada executando as instruções.

### <a id="disk"></a>Tempo de disco
O aplicativo está executando operações de disco.

### <a id="network"></a>Tempo de rede
O aplicativo está executando operações de rede.

### <a id="when"></a>Quando colunas
Essa é uma visualização de como as amostras INCLUSIVAS coletadas para um nó variam ao longo do tempo. O intervalo total da solicitação é dividido em 32 segmentos de tempo e as amostras inclusivas para esse nó são acumuladas nesses buckets 32. Cada bucket, em seguida, é representado como uma barra cuja altura representa um valor de escala. Para nós marcados `CPU_TIME` ou `BLOCKED_TIME`, ou onde há uma relação óbvia de consumir um recurso (cpu, disco, thread), a barra representa consumindo um desses recursos para o período de tempo do bucket. Para essas métricas você pode obter maior que 100%, consumindo vários recursos. Por exemplo, se em média usar duas CPUs em um intervalo você obter 200%.


## <a id="troubleshooting"></a>Solucionar problemas

### <a name="how-can-i-know-whether-application-insights-profiler-is-running"></a>Como saber se o criador de perfil do Application Insights está em execução?

O criador de perfil é executado como um trabalho Web contínuo no aplicativo Web. Você pode abrir o recurso de aplicativo Web em https://portal.azure.com e verificar o status de "ApplicationInsightsProfiler" na lâmina trabalhos Web. Se não estiver em execução, abra **Logs** para obter mais informações. 

### <a name="why-cant-i-find-any-stack-examples-even-though-the-profiler-is-running"></a>Por que não é possível encontrar nenhum exemplo de pilha mesmo que o criador de perfil está em execução?

Aqui estão algumas coisas que você pode verificar.

1. Verifique se seu plano do serviço de aplicativo Web é a camada básica e acima.
2. Verifique se seu aplicativo Web tem o Application Insights SDK 2.2 Beta e acima habilitado.
3. Verifique se que seu aplicativo Web tem a configuração APPINSIGHTS_INSTRUMENTATIONKEY com a mesma chave de instrumentação usada pelo SDK do Application Insights.
4. Verifique se o seu aplicativo Web está em execução no .net Framework 4.6.
5. Se for um aplicativo ASP.NET Core, também Verifique [as dependências necessárias](#aspnetcore).

Depois que o profiler for iniciado, há um período de aquecimento curto quando o criador de perfil coleta ativamente vários rastreamentos de desempenho. Depois disso, o criador de perfil coleta rastreamentos de desempenho de dois minutos em cada hora.  

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>Estava usando o criador de perfil de serviço do Azure. O que aconteceu?  

Quando você habilita o criador de perfil do Application Insights, agente do criador de perfil de serviço do Azure está desativado.

### <a id="double-counting"></a>Dois segmentos paralelos de contagem

Em alguns casos a métrica de tempo total no Visualizador de pilha é maior que a duração real da solicitação. 

Isso pode acontecer quando há dois ou mais threads associados a uma solicitação, operar em paralelo. O tempo total de threads é mais do que o tempo decorrido. Em muitos casos, um thread pode aguardando outro para concluir. O visualizador tenta detectar e omitir a espera interessantes, mas erra por mostrando muito em vez de omitindo o que pode ser informações críticas.  

Quando você vir threads paralelos em seus rastreamentos, você precisa determinar quais segmentos estão aguardando para que possa determinar o caminho crítico para a solicitação. Na maioria dos casos, o thread rapidamente entra em estado de espera é simplesmente aguardando outros threads. Concentrar-se nas outras e ignorar o tempo em que os threads em espera.

### <a id="issue-loading-trace-in-viewer"></a>Nenhum dado de criação de perfil

1. Se os dados que você está tentando exibir mais de duas semanas, tente limitar seu filtro de tempo e tente novamente.

2. Verificação de que um firewall ou proxies não tiver bloqueado o acesso à https://gateway.azureserviceprofiler.net. 

3. Verifique se a chave de instrumentação do Application Insights que você está usando em seu aplicativo é o mesmo que o recurso Application Insights com que tiver habilitado a criação de perfil. A chave é normalmente em applicationinsights. config, mas também pode ser encontrada no App. config ou Web. config.

### <a name="error-report-in-the-profiling-viewer"></a>Relatório de erro no Visualizador de criação de perfil

Emita um tíquete de suporte do portal. Inclua a ID de correlação da mensagem de erro.


## <a name="manual-installation"></a>Instalação manual

Quando você configura o criador de perfil, as seguintes atualizações são feitas para as configurações do aplicativo Web. Você pode fazê-los por conta própria manualmente:

1. Na folha de controle do aplicativo Web, abra as configurações.
2. Definir ".Net Framework versão" para v 4.6.
3. Defina "sempre" para em.
4. Adicionar configuração de aplicativo "__APPINSIGHTS_INSTRUMENTATIONKEY__" e defina o valor para a mesma chave de instrumentação usada pelo SDK.
5. Em **Extensões**, adicione “Application Insights Profiler”. Levará 2 ou 3 minutos para instalar.

## <a id="aspnetcore"></a>Suporte do ASP.NET Core

Aplicativo básico do ASP.NET é suportado em tempo de execução do .NET Core.

O aplicativo também precisa incluir os seguintes componentes para habilitar a criação de perfil.

1. [Application Insights para ASP.NET Core 2.0](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases/tag/v2.0.0)
2. [System.Diagnostics.DiagnosticSource 4.4.0-beta-25022-02](https://dotnet.myget.org/feed/dotnet-core/package/nuget/System.Diagnostics.DiagnosticSource/4.4.0-beta-25022-02)
    * No Visual Studio, selecione o menu "ferramentas-> NuGet Manager-> pacote Gerenciador de configurações do pacote".
    * Na caixa de diálogo Opções, selecione "Fontes de pacote do NuGet pacote Manager->".
    * Clique em "+" botão para adicionar uma nova origem de pacote com o nome "DotNet-Core-MyGet" e o valor "https://dotnet.myget.org/F/dotnet-core/api/v3/index.json".
    * Clique em "Atualizar" e feche a caixa de diálogo Opções.
    * Abra o Gerenciador de Soluções, clique com o botão direito do mouse no projeto do ASP. NET Core e selecione “Gerenciar Pacotes NuGet...”.
    * Clique na guia "Browse", selecione "origem do pacote: Core-DotNet-MyGet" e verifique se "Include prerelease".
    * Pesquisar "System.Diagnostics.DiagnosticSource" e escolha "__4.4.0-beta-25022-02__" para instalar.


## <a name="next-steps"></a>Próximas etapas

* [Trabalhar com o Application Insights no Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png

