---
title: Criar o perfil de aplicativos Web online no Azure com o Application Insights Profiler | Microsoft Docs
description: "Identifique o afunilamento em seu código de servidor da Web com um criador de perfil de baixa capacidade."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: mbullwin
ms.openlocfilehash: e66dc2af18785c6c8e83815129c8bca5b877d25b
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2017
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Criar o perfil de aplicativos Web dinâmicos do Azure com o Application Insights

*Geralmente, este recurso do Application Insights está disponível para o Serviço de Aplicativo do Azure e está em versão prévia para recursos de computação do Azure.*

Descubra quanto tempo é gasto em cada método em seu aplicativo Web online usando o [Application Insights Profiler](app-insights-overview.md). A ferramenta de criação de perfil do Application Insights mostra perfis detalhados de solicitações dinâmicas que foram atendidas pelo seu aplicativo e realça o *afunilamento* que usa mais tempo. O criador de perfil seleciona automaticamente exemplos que têm tempos de resposta diferentes e, em seguida, usa várias técnicas para minimizar a sobrecarga.

No momento, o criador de perfil funciona para aplicativos Web do ASP.NET em execução no Serviço de Aplicativo do Azure pelo menos na camada de serviço Básica.

## <a id="installation"></a> Habilitar o criador de perfil

[Instalar o Application Insights](app-insights-asp-net.md) em seu código. Se já estiver instalado, verifique se que você tem a versão mais recente. Para verificar a versão mais recente, no Gerenciador de Soluções, clique com o botão direito do mouse em seu projeto e, em seguida, selecione **Gerenciar pacotes NuGet** > **Atualizações** > **Atualizar todos os pacotes**. Em seguida, reimplante seu aplicativo.

*Usando o ASP.NET Core? Obtenha [mais informações](#aspnetcore).*

No [Portal do Azure](https://portal.azure.com), abra o recurso do Application Insights para seu aplicativo Web. Selecione **Desempenho** > **Habilitar o Application Insights Profiler**.

![Selecionar a faixa Habilitar criador de perfil][enable-profiler-banner]

Alternativamente, é possível selecionar **Configurar** para exibir o status e habilitar ou desabilitar o criador de perfil.

![Em Desempenho, selecione Configurar][performance-blade]

Os aplicativos Web configurados com o Application Insights estão listados em **Configurar**. Siga as instruções para instalar o agente do criador de perfil, se necessário. Se nenhum aplicativo Web tiver sido configurado com o Application Insights, selecione **Adicionar aplicativos vinculados**.

Para controlar o criador de perfil em todos os seus aplicativos Web vinculados, no painel **Configurar**, selecione **Habilitar o criador de perfil** ou **Desabilitar o criador de perfil**.

![Configurar opções do painel][linked app services]

Ao contrario dos aplicativos Web hospedados por meio de planos do Serviço de Aplicativo, os aplicativos hospedados nos recursos de computação do Azure (por exemplo, Máquinas Virtuais do Azure, conjuntos de dimensionamento de máquinas virtuais, Azure Service Fabric ou Serviços de Nuvem do Azure) não são gerenciados diretamente pelo Azure. Nesse caso, não há nenhum aplicativo Web ao qual vincular. Em vez de vincular a um aplicativo, selecione o botão **Habilitar o criador de perfil**.

## <a name="disable-the-profiler"></a>Desabilitar o criador de perfil
Para parar ou reiniciar o criador de perfil para uma instância individual do Serviço de Aplicativo, em **Trabalhos da Web**, vá para o recurso do Serviço de Aplicativo. Para excluir o criador de perfil, vá para **Extensões**.

![Desabilitar o Criador de Perfil para Trabalhos da Web][disable-profiler-webjob]

Recomendamos que você tenha o criador de perfil habilitado em todos os seus aplicativos Web para descobrir problemas de desempenho o mais rápido possível.

Se você usar WebDeploy para implantar as alterações em seu aplicativo Web, certifique-se de excluir a pasta App_Data da exclusão durante a implantação. Caso contrário, os arquivos da extensão do criador de perfil serão excluídos na próxima vez em que você implantar o aplicativo Web no Azure.

### <a name="using-profiler-with-azure-vms-and-azure-compute-resources-preview"></a>Usando o criador de perfil com VMs do Azure e recursos de computação do Azure (versão prévia)

Quando você [habilita o Application Insights para o Serviço de Aplicativo do Azure em tempo de execução](app-insights-azure-web-apps.md#run-time-instrumentation-with-application-insights), o Application Insights Profiler fica automaticamente disponível. Se você já tiver habilitado o Application Insights para o recurso, talvez seja necessário atualizar para a versão mais recente usando o assistente Configurar.

Obtenha informações sobre um [recurso em versão prévia do criador de perfil para recursos de computação do Azure](https://go.microsoft.com/fwlink/?linkid=848155).


## <a name="limitations"></a>Limitações

A retenção de dados padrão é de cinco dias. A ingestão máxima de dados por dia é de 10 GB.

Não há encargos para usar o serviço do criador de perfil. Para usar o serviço do criador de perfil, seu aplicativo Web deverá estar hospedado pelo menos na camada Básica do Serviço de Aplicativo.

## <a name="overhead-and-sampling-algorithm"></a>Algoritmo de sobrecarga e amostragem

O criador de perfil é executado aleatoriamente dois minutos a cada hora em cada máquina virtual que hospeda o aplicativo que com o criador de perfil habilitado para capturar rastreamentos. Quando o criador de perfil estiver em execução, ele adicionará uma sobrecarga entre 5% e 15% de CPU ao servidor.
Quanto mais servidores estiverem disponíveis para hospedar o aplicativo, menor será o impacto do criador de perfil no desempenho geral do aplicativo. Isso ocorre porque o algoritmo de amostragem faz o criador de perfil ser executado em apenas 5% dos servidores a qualquer momento. Mais servidores estão disponíveis para tender as solicitações da Web para deslocar a sobrecarga do servidor causada pela execução do criador de perfil.


## <a name="view-profiler-data"></a>Exibir dados do criador de perfil

Acesse o painel **Desempenho** e, em seguida, role para abaixo até a lista de operações.

![Coluna de exemplos do painel de desempenho do Application Insights][performance-blade-examples]

A tabela de operações tem estas colunas:

* **CONTAGEM**: o número dessas solicitações no intervalo de tempo do painel **CONTAGEM**.
* **MEDIANA**: o tempo típico que seu aplicativo leva para responder a uma solicitação. Metade de todas as respostas foram mais rápidas do que esse valor.
* **95º PERCENTIL**: 95% das respostas foram mais rápidas do que esse valor. Se esse valor for substancialmente diferente da mediana, poderá haver um problema intermitente com seu aplicativo. (Ou pode ser explicado por um recurso de design, como armazenamento em cache.)
* **RASTREAMENTOS DO CRIADOR DE PERFIL**: um ícone que indica que o criador de perfil capturou rastreamentos de pilha para esta operação.

Selecione **Exibição** para abrir o explorador de rastreamento. O explorer mostra vários exemplos que capturou o criador de perfil, classificados pelo tempo de resposta.

Se você estiver usando o painel **Visualizar desempenho**, acesse a seção **Executar ações** da página para exibir os rastreamentos do criador de perfil. Selecione o botão **Rastreamentos do criador de perfil**.

![Rastreamentos do criador de perfil de versão prévia do painel de desempenho do Application Insights][performance-blade-v2-examples]

Selecione um exemplo para mostrar uma divisão de nível de código de tempo gasto na execução da solicitação.

![Explorador de rastreamento do Application Insights][trace-explorer]

O explorador de rastreamento mostra as seguintes informações:

* **Mostrar afunilamento**: abre o maior nó folha ou pelo menos algo próximo. Na maioria dos casos, esse nó é adjacente a um gargalo de desempenho.
* **Rótulo**: O nome da função ou do evento. A árvore mostra uma combinação de código e eventos que ocorreram (como eventos SQL e HTTP). Os principais eventos representa a duração total da solicitação.
* **Decorrido**: o intervalo de tempo entre o início e o final da operação.
* **Quando**: quando a função ou evento estava sendo executado com relação a outras funções.

## <a name="how-to-read-performance-data"></a>Como ler dados de desempenho

O criador de perfil de serviço Microsoft usa uma combinação de métodos de amostragem e instrumentação para analisar o desempenho do seu aplicativo. Quando a coleção detalhada está em andamento, o criador de perfil de serviço obtém a amostra do ponteiro de instrução de cada CPU do computador em cada milissegundo. Cada amostra captura a pilha de chamadas completa do thread em execução no momento. Ela oferece informações detalhadas e úteis sobre o que esse thread estava fazendo, ambos em um alto nível e um baixo nível de abstração. O criador de perfil de serviço também coleta outros eventos para rastrear a correlação e causalidade da atividade, incluindo eventos de alternância de contexto, eventos TPL (Biblioteca de Paralelismo de Tarefas) e eventos de pool de threads.

A pilha de chamadas mostrada na exibição de linha do tempo é o resultado da amostragem e da instrumentação. Como cada amostra captura a pilha de chamadas completa do thread, ela inclui o código do Microsoft .NET Framework e de outras estruturas que você referencia.

### <a id="jitnewobj"></a>Alocação de objetos (clr!JIT\_New ou clr!JIT\_Newarr1)
**clr!JIT\_New** e **clr!JIT\_Newarr1** são funções auxiliares no .NET Framework que alocam memória de um heap gerenciado. **clr!JIT\_New** é invocado quando um objeto é alocado. **clr!JIT\_Newarr1** é invocado quando uma matriz de objetos é alocada. Normalmente, essas duas funções são muito rápidas e levam quantidades relativamente pequenas de tempo. Se você vir que **clr!JIT\_New** ou **clr!JIT\_Newarr1** levam uma quantidade de tempo significativa em sua linha do tempo, será uma indicação de que o código talvez esteja alocando muitos objetos e consumindo quantidades significativas de memória.

### <a id="theprestub"></a>Carregando código (clr!ThePreStub)
**clr!ThePreStub** é uma função auxiliar no .NET Framework que prepara o código para ser executado pela primeira vez. Normalmente isso inclui a compilação JIT (Just-In-Time), entre outras. Para cada método C#, **clr!ThePreStub** deve ser invocado no máximo uma vez durante o tempo de vida de um processo.

Se a solicitação de **clr!ThePreStub** levar uma quantidade significativa de tempo, isso indicará que a solicitação será a primeira que executará esse método. O tempo para o tempo de execução do .NET Framework carregar esse método é significativo. Você pode considerar usar um processo de aquecimento que executa essa parte do código antes que seus usuários a acessem ou considerar executar o Gerador de Imagem Nativa (ngen.exe) em seus assemblies.

### <a id="lockcontention"></a>Contenção de bloqueio (clr!JITutil\_MonContention ou clr!JITutil\_MonEnterWorker)
**clr!JITutil\_MonContention** ou **clr!JITutil\_MonEnterWorker** indica que o thread atual está aguardando um bloqueio ser liberado. Normalmente isso é exibido ao executar uma instrução **LOCK** em C#, ao invocar o método **Monitor.Enter** ou ao invocar o método com o atributo **MethodImplOptions.Synchronized**. Normalmente, a contenção de bloqueio ocorre quando um thread A adquire um bloqueio e um thread B tenta adquirir o mesmo bloqueio antes que o thread A o libere.

### <a id="ngencold"></a>Carregando código ([COLD])
Se o nome do método contiver **[COLD]**, como **mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined**, o tempo de execução do .NET Framework estará executando código pela primeira vez que ele não for otimizado pela <a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">otimização guiada por perfil</a>. Para cada método, ele deve aparecer no máximo uma vez durante o tempo de vida do processo.

Se o carregamento de um código leva uma quantidade significativa de tempo para uma solicitação, isso indica que a solicitação é a primeira a executar a parte não otimizada do método. Considere usar um processo de aquecimento que execute essa parte do código antes que seus usuários a acessem.

### <a id="httpclientsend"></a>Enviar solicitação HTTP
Métodos como **HttpClient.Send** indicam que o código está aguardando a conclusão de uma solicitação HTTP.

### <a id="sqlcommand"></a>Operação do banco de dados
Método como **SqlCommand.Execute** indicam que o código está aguardando a conclusão de uma operação de banco de dados.

### <a id="await"></a>Aguardando (AWAIT\_TIME)
**AWAIT\_TIME** indica que o código está aguardando a conclusão de outra tarefa. Normalmente isso acontece com a instrução **AWAIT** em C#. Quando o código faz **AWAIT** em C#, o thread é desenrolado e retorna o controle para o pool de threads e não há nenhum thread bloqueado aguardando a conclusão de **AWAIT**. No entanto, logicamente, o thread que fez o **AWAIT** está "bloqueado" e aguardando a conclusão da operação. A instrução **AWAIT\_TIME** indica o tempo bloqueado aguardando a conclusão da tarefa.

### <a id="block"></a>Tempo bloqueado
**BLOCKED_TIME** indica que o código está aguardando até que outro recurso esteja disponível. Por exemplo, ele pode estar aguardando até que um objeto de sincronização, que um thread esteja disponível ou que a solicitação seja concluída.

### <a id="cpu"></a>Tempo de CPU
A CPU está ocupada executando as instruções.

### <a id="disk"></a>Tempo de disco
O aplicativo está executando operações de disco.

### <a id="network"></a>Tempo de rede
O aplicativo está executando operações de rede.

### <a id="when"></a>Quando colunas
A coluna **Quando** é uma visualização de como as amostras INCLUSIVAS coletadas para um nó variam ao longo do tempo. O intervalo total da solicitação é dividido em 32 buckets de tempo. As amostras inclusivas para esse nó estão acumuladas nesses 32 buckets. Cada bucket é representado como uma barra. A altura da barra representa um valor em escala. Para nós marcados com **CPU_TIME** ou **BLOCKED_TIME** ou em que há uma relação óbvia de consumo de um recurso (CPU, disco, thread), a barra representa o consumo de um desses recursos durante o período de tempo desse bucket. Para essas métricas, é possível obter um valor de maior do que 100% consumindo vários recursos. Por exemplo, se você usar, em média, duas CPUs durante um intervalo, obterá 200%.


## <a id="troubleshooting"></a>Solucionar problemas

### <a name="too-many-active-profiling-sessions"></a>Número excessivo de sessões de criação de perfil ativas

No momento, é possível habilitar o criador de perfil em no máximo quatro aplicativos Web do Azure e slots de implantação em execução no mesmo plano de serviço. Se o trabalho Web do criador de perfil estiver relatando um número excessivo de sessões de criação de perfil ativas, mova alguns aplicativos Web para um plano de serviço diferente.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Como fazer para determinar se o Application Insights Profiler está em execução?

O criador de perfil é executado como um trabalho Web contínuo no aplicativo Web. É possível abrir o recurso do aplicativo Web no [Portal do Azure](https://portal.azure.com). Se o painel **WebJobs**, verifique o status de **ApplicationInsightsProfiler**. Se ele não estiver em execução, abra **Registros** para obter mais informações.

### <a name="why-cant-i-find-any-stack-examples-even-though-the-profiler-is-running"></a>Por que não é possível encontrar nenhum exemplo de pilha, mesmo com o criador de perfil em execução?

Aqui estão algumas coisas que você pode verificar:

* Certifique-se de que o seu plano de serviço de aplicativo Web é a camada básica ou acima.
* Certifique-se de que seu aplicativo Web tem o Application Insights SDK 2.2 Beta ou posterior habilitado.
* Certifique-se de que seu aplicativo Web tem a configuração **APPINSIGHTS_INSTRUMENTATIONKEY** com a mesma chave de instrumentação usada pelo SDK do Application Insights.
* Certifique-se de que seu aplicativo Web está sendo executado no .NET Framework 4.6.
* Se seu aplicativo Web for um aplicativo ASP.NET Core, verifique [as dependências necessárias](#aspnetcore).

Depois que o criador de perfil é iniciado, há um período de aquecimento curto durante o qual o criador de perfil coleta ativamente vários rastreamentos de desempenho. Depois disso, o criador de perfil coleta rastreamentos de desempenho de dois minutos em cada hora.  

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>Estava usando o criador de perfil de serviço do Azure. O que aconteceu?  

Quando você habilita o Application Insights Profiler, o agente do Criador de Perfil de Serviço do Azure está desabilitado.

### <a id="double-counting"></a>Dois segmentos paralelos de contagem

Em alguns casos, a métrica de tempo toral no visualizador de pilha é maior que a duração da solicitação.

Isso poderá ocorrer quando houver dois ou mais threads associados a uma solicitação e eles estiverem operando em paralelo. Nesse caso, o tempo total de thread é maior que o tempo decorrido. Um thread pode estar aguardando até que o outro seja concluído. O visualizador tenta detectar este e omite a espera desinteressante, mas erra ao mostrar muito em vez de omitir o que poderiam ser informações críticas.  

Quando você vir threads paralelos em seus rastreamentos, determine quais threads estão aguardando para você poder determinar o caminho crítico para a solicitação. Na maioria dos casos, o thread rapidamente entra em estado de espera é simplesmente aguardando outros threads. Concentre-se nos outros threads e ignore o tempo nos threads em espera.

### <a id="issue-loading-trace-in-viewer"></a>Nenhum dado de criação de perfil

Aqui estão algumas coisas que você pode verificar:

* Se os dados que você está tentando exibir mais de duas semanas, tente limitar seu filtro de tempo e tente novamente.
* Verificação de que um firewall ou proxies não tiver bloqueado o acesso à https://gateway.azureserviceprofiler.net.
* Verifique se a chave de instrumentação do Application Insights que você está usando em seu aplicativo é a mesma que o recurso do Application Insights usado para habilitar a criação de perfil. Geralmente a chave está em ApplicationInsights.config, mas ela também pode estar localizada nos arquivos web.config ou app.config.

### <a name="error-report-in-the-profiling-viewer"></a>Relatório de erro no Visualizador de criação de perfil

Envie um tíquete de suporte no portal. Certifique-se de incluir uma ID de correlação da mensagem de erro.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Erro de implantação: diretório não vazio 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Se você estiver reimplantando seu aplicativo Web em um recurso do Serviço de Aplicativo com o criador de perfil habilitado, talvez você veja uma mensagem com a seguinte aparência:

Diretório não vazio 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Este erro ocorrerá se você executar a Implantação da Web de scripts ou do Pipeline de implantação do Visual Studio Team Services. A solução é adicionar os seguintes parâmetros de implantação adicionados à tarefa de Implantação da Web:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Esses parâmetros excluem a pasta usada pelo Application Insights Profiler e desbloqueiam o processo de reimplantação. Eles não afetam a instância do criador de perfil em execução no momento.


## <a name="manual-installation"></a>Instalação manual

Quando você configura o criador de perfil, são feitas atualizações nas configurações do aplicativo Web. Será possível aplicar as atualizações manualmente se seu ambiente exigir. Por exemplo, se seu aplicativo for executado no Ambiente do Serviço de Aplicativo para PowerApps.

1. No painel de controle do aplicativo Web, abra **Configurações**.
2. Defina a **versão do .Net Framework** como **v4.6**.
3. Defina **Sempre ativo** como **Ativado**.
4. Adicione a configuração de aplicativo __APPINSIGHTS_INSTRUMENTATIONKEY__ e defina o valor como a mesma chave de instrumentação usada pelo SDK.
5. Abra **Ferramentas avançadas**.
6. Selecione **Ir** para abrir o site do Kudu.
7. No site do Kudu, selecione **Extensões de site**.
8. Instale o __Application Insights__ da Galeria de Aplicativos Web do Azure.
9. Reinicie o aplicativo Web.

## <a id="aspnetcore"></a>Suporte do ASP.NET Core

Um aplicativo ASP.NET Core precisa instalar o pacote NuGet Microsoft.ApplicationInsights.AspNetCore 2.1.0-beta6 ou posterior para trabalhar com o criador de perfil. A partir de 27 de junho de 2017, não damos suporte a versões anteriores.

## <a name="next-steps"></a>Próximas etapas

* [Trabalhar com o Application Insights no Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png
