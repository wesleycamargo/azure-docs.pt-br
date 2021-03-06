---
title: Aplicativos de produção de perfil no Azure com o Application Insights Profiler | Microsoft Docs
description: Identifique o afunilamento em seu código de servidor da Web com um criador de perfil de baixa capacidade.
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
ms.openlocfilehash: c07b325f3de6cd2cf3aaa436736786d2cdc42881
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498121"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Aplicativos de produção de perfil no Azure com o Application Insights
## <a name="enable-application-insights-profiler-for-your-application"></a>Habilitar o Application Insights Profiler para o aplicativo

O Azure Application Insights Profiler fornece rastreamentos de desempenho para aplicativos em execução na produção no Azure. O Profiler captura os dados automaticamente em escala, sem afetar negativamente os usuários. O Profiler ajuda você a identificar o caminho de código "quente" que leva mais tempo ao manipular uma determinada solicitação da Web. 

O Profiler trabalha com aplicativos .NET implantados nos serviços do Azure a seguir. Nos links abaixo, há instruções específicas para habilitar o Profiler para cada tipo de serviço.

* [Serviço de Aplicativo do Azure](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Serviços de Nuvem do Azure](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas Virtuais do Azure e Conjuntos de Dimensionamento de Máquinas Virtuais](profiler-vm.md?toc=/azure/azure-monitor/toc.json)
* [**VISUALIZAÇÃO** aplicativos do ASP.NET Core Web de Linux do Azure](profiler-aspnetcore-linux.md?toc=/azure/azure-monitor/toc.json) 

Se você habilitou o Profiler, mas não está vendo os rastreamentos, verifique nosso [Guia de solução de problemas](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

## <a name="view-profiler-data"></a>Exibir dados do Profiler

Para que o Profiler carregue rastreios, o aplicativo precisa estar manipulando ativamente as solicitações. Se você estiver fazendo uma experiência, poderá gerar solicitações para seu aplicativo Web usando [testes de desempenho do Application Insights](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test). Se você tiver ativado recentemente o Profiler, poderá executar um teste de carga curta. Enquanto o teste de carga estiver em execução, selecione o botão **Criar Perfil Agora** no painel [**Configurações do Profiler**](profiler-settings.md#profiler-settings-pane). Depois que o Profiler estiver em execução, ele será exibido de forma aleatória por uma duração de dois minutos, aproximadamente uma vez a cada hora. Se o aplicativo estiver lidando com um fluxo constante de solicitações, o Profiler carregará rastreamentos a cada hora.

Depois que seu aplicativo receber algum tráfego e o Profiler tiver tido tempo de fazer o upload dos rastreamentos, você deverá ter rastreamentos para exibir. Esse processo pode levar de 5 a 10 minutos. Para exibir os rastreios, acesse o painel **Desempenho**, selecione **Executar Ações** e, em seguida, selecione o botão **Rastreamentos do Profiler**.

![Rastreamentos do Profiler de versão prévia do painel de desempenho do Application Insights][performance-blade]

Selecione um exemplo para exibir uma divisão de nível de código de tempo gasto na execução da solicitação.

![Explorador de rastreamento do Application Insights][trace-explorer]

O explorador de rastreamento exibe as seguintes informações:

* **Mostrar Afunilamento**: Abre o nó folha maior ou, pelo menos, alguma próximo. Na maioria dos casos, esse nó está próximo de um gargalo de desempenho.
* **Rótulo**: O nome da função ou do evento. A árvore exibe uma combinação de código e eventos que ocorreram, tais como eventos SQL e HTTP. Os principais eventos representa a duração total da solicitação.
* **Decorrido**: Intervalo de tempo entre o início e o final da operação.
* **Quando**: Horário em que a função ou evento estava sendo executado com relação a outras funções.

## <a name="how-to-read-performance-data"></a>Como ler dados de desempenho

O criador de perfil de serviço Microsoft usa uma combinação de métodos de amostragem e instrumentação para analisar o desempenho do seu aplicativo. Quando a coleção detalhada está em andamento, o criador de perfil de serviço obtém a amostra do ponteiro de instrução de cada CPU do computador em cada milissegundo. Cada amostra captura a pilha de chamadas completa do thread que está em execução no momento. Ela oferece informações detalhadas sobre o que esse thread estava fazendo, em ambos um nível alto e um baixo de abstração. O criador de perfil de serviço também coleta outros eventos para rastrear a correlação e causalidade da atividade, incluindo eventos de alternância de contexto, eventos TPL (Biblioteca de Paralelismo de Tarefas) e eventos de pool de threads.

A pilha de chamadas mostrada na exibição de linha do tempo é o resultado da amostragem e da instrumentação. Como cada amostra captura a pilha de chamadas completa do thread, ela inclui o código do Microsoft .NET Framework e de outras estruturas que você referencia.

### <a id="jitnewobj"></a>Alocação de objetos (clr!JIT\_New ou clr!JIT\_Newarr1)

**clr!JIT\_New** e **clr!JIT\_Newarr1** são funções auxiliares no .NET Framework que alocam memória de um heap gerenciado. **clr!JIT\_New** é invocado quando um objeto é alocado. **clr!JIT\_Newarr1** é invocado quando uma matriz de objetos é alocada. Essas duas funções normalmente são rápidas e levam quantidades relativamente pequenas de tempo. Se o **clr!JIT\_New** ou o **clr!JIT\_Newarr1** ocupar uma quantidade de tempo significativa em sua linha do tempo, o código talvez esteja alocando muitos objetos e consumindo quantidades significativas de memória.

### <a id="theprestub"></a>Carregando código (clr!ThePreStub)

**clr!ThePreStub** é uma função auxiliar no .NET Framework que prepara o código para ser executado pela primeira vez. Essa execução geralmente inclui, mas não se limita à compilação JIT (Just-In-Time). Para cada método C#, **clr!ThePreStub** deve ser invocado no máximo uma vez durante um processo.

Se **clr!ThePreStub** demorar muito para uma solicitação, essa solicitação será a primeira a executar esse método. O tempo necessário para o tempo de execução do .NET Framework carregar o primeiro método é significativo. Você pode considerar usar um processo de aquecimento que executa essa parte do código antes que seus usuários a acessem ou considerar executar o Gerador de Imagem Nativa (ngen.exe) em seus assemblies.

### <a id="lockcontention"></a>Contenção de bloqueio (clr!JITutil\_MonContention ou clr!JITutil\_MonEnterWorker)

**clr!JITutil\_MonContention** ou **clr!JITutil\_MonEnterWorker** indica que o thread atual está aguardando um bloqueio ser liberado. Esse texto frequentemente é exibido ao executar uma instrução **LOCK** em C#, ao invocar o método **Monitor.Enter** ou ao invocar um método com o atributo **MethodImplOptions.Synchronized**. Normalmente, a contenção de bloqueio ocorre quando um thread _A_ adquire um bloqueio e um thread _B_ tenta adquirir o mesmo bloqueio antes que o thread _A_ o libere.

### <a id="ngencold"></a>Carregando código ([COLD])

Se o nome do método contiver **[COLD]**, como **mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined**, o tempo de execução do .NET Framework estará executando código pela primeira vez que ele não for otimizado pela [otimização guiada por perfil](/cpp/build/profile-guided-optimizations). Para cada método, ele deve ser exibido no máximo uma vez durante o processo.

Se o carregamento de um código leva uma quantidade significativa de tempo para uma solicitação, essa solicitação é a primeira a executar a parte não otimizada do método. Considere usar um processo de aquecimento que execute essa parte do código antes que seus usuários a acessem.

### <a id="httpclientsend"></a>Enviar solicitação HTTP

Métodos como **HttpClient.Send** indicam que o código está aguardando a conclusão de uma solicitação HTTP.

### <a id="sqlcommand"></a>Operação do banco de dados

Método como **SqlCommand.Execute** indicam que o código está aguardando a conclusão de uma operação de banco de dados.

### <a id="await"></a>Aguardando (AWAIT\_TIME)

**AWAIT\_TIME** indica que o código está aguardando a conclusão de outra tarefa. Esse atraso normalmente acontece com a instrução **AWAIT** em C#. Quando o código faz um C# **AWAIT**, o encadeamento desenrola e retorna o controle para o conjunto de encadeamentos, e não há encadeamento bloqueado aguardando o **AWAIT** terminar. No entanto, logicamente, o thread que fez o **AWAIT** está "bloqueado" e aguardando a conclusão da operação. A instrução **AWAIT\_TIME** indica o tempo bloqueado aguardando a conclusão da tarefa.

### <a id="block"></a>Tempo bloqueado

**BLOCKED_TIME** indica que o código está aguardando até que outro recurso esteja disponível. Por exemplo, ele pode estar aguardando até que um objeto de sincronização, que um thread esteja disponível ou que a solicitação seja concluída.

### <a name="unmanaged-async"></a>Assíncrono Não Gerenciado

.NET framework emite eventos ETW e passa as ids de atividade entre threads para que as chamadas assíncronas podem ser controladas entre threads. Código não gerenciado (código nativo) e alguns estilos mais antigos do código assíncrono não têm esses eventos e ids de atividade, portanto, o criador de perfil não pode determinar qual thread e quais funções estão em execução no thread. Isso é rotulado 'Async não gerenciada' na pilha de chamadas. Se você baixar o arquivo ETW, você poderá usar [PerfView](https://github.com/Microsoft/perfview/blob/master/documentation/Downloading.md) para obter mais informações sobre o que está acontecendo.

### <a id="cpu"></a>Tempo de CPU

A CPU está ocupada executando as instruções.

### <a id="disk"></a>Tempo de disco

O aplicativo está executando operações de disco.

### <a id="network"></a>Tempo de rede

O aplicativo está executando operações de rede.

### <a id="when"></a>Quando colunas

A coluna **Quando** é uma visualização de como as amostras INCLUSIVAS coletadas para um nó variam ao longo do tempo. O intervalo total da solicitação é dividido em 32 buckets de tempo. As amostras inclusivas para esse nó estão acumuladas nesses 32 buckets. Cada bucket é representado como uma barra. A altura da barra representa um valor em escala. Para nós marcados com **CPU_TIME** ou **BLOCKED_TIME** ou em que há uma relação óbvia de consumo de um recurso (por exemplo, CPU, disco, thread), a barra representa o consumo de um dos recursos durante o bucket. Para essas métricas, é possível obter um valor de maior do que 100% consumindo vários recursos. Por exemplo, se você usar, em média, duas CPUs durante um intervalo, obterá 200%.

## <a name="limitations"></a>Limitações

O período de retenção de dados padrão é de cinco dias. O máximo de dados que é ingerido por dia é 10 GB.

Não há encargos para usar o serviço do Profiler. Para que você o use, seu aplicativo Web deverá estar hospedado pelo menos na camada Básica do recurso de Aplicativos Web do Serviço de Aplicativo do Azure.

## <a name="overhead-and-sampling-algorithm"></a>Algoritmo de sobrecarga e amostragem

O Profiler é executado aleatoriamente dois minutos a cada hora em cada máquina virtual que hospeda o aplicativo com o Profiler habilitado para capturar rastreamentos. Quando o Profiler está em execução, ele adiciona de 5% a 15% de sobrecarga de CPU ao servidor.

## <a name="next-steps"></a>Próximas etapas
Habilite o Application Insights Profiler para seu aplicativo do Azure. Veja também:
* [Serviços de Aplicativos](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Serviços de Nuvem do Azure](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas Virtuais do Azure e Conjuntos de Dimensionamento de Máquinas Virtuais](profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/profiler-overview/performance-blade-v2-examples.png
[trace-explorer]: ./media/profiler-overview/trace-explorer.png
