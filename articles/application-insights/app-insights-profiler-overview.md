---
title: Aplicativos de produção de perfil no Azure com o Application Insights Profiler | Microsoft Docs
description: Identifique o afunilamento em seu código de servidor da Web com um criador de perfil de baixa capacidade.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: bba15ee901e7ccecc513f526339bde2fcc6277fa
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142685"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Aplicativos de produção de perfil no Azure com o Application Insights
## <a name="enable-profiler-for-your-application"></a>Ativar o Profiler para seu aplicativo

O Application Insights Profiler fornece rastreamentos de desempenho para aplicativos em execução na produção no Azure. Ele captura os dados automaticamente em escala, sem afetar negativamente seus usuários finais. O Profiler ajudará você a identificar o caminho de código "quente" que leva mais tempo ao manipular uma determinada solicitação da Web. 

O profiler trabalha com aplicativos .Net implantados nos seguintes serviços do Azure. Instruções específicas para habilitar o profiler para cada tipo de serviço estão nos links abaixo.

* [Serviços de Aplicativos](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Serviços de Nuvem](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Aplicativos do Service Fabric](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais e VM Scalesets](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Se você habilitou o Profiler, mas não estiver vendo os rastreamentos, verifique nossos [guia de solução de problemas.](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)

A execução do Profiler no local não é oficialmente suportada, mas temos algumas instruções [que você pode tentar.](https://docs.microsoft.com/azure/application-insights/enable-profiler-compute#enable-profiler-on-on-premises-servers)

## <a name="view-profiler-data"></a>Exibir dados do criador de perfil

Para que o profiler carregue rastreios, seu aplicativo manipula ativamente as solicitações. Se você estiver fazendo uma experiência, poderá gerar solicitações para seu aplicativo da Web usando [Testes de desempenho do Application Insights](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test). Se você tiver ativado recentemente o Profiler, poderá executar um teste de carga curta. Enquanto o teste de carga estiver em execução, pressione o botão **Perfil Agora** na página [**Configurações do Perfilador**](). Depois que o profiler estiver em execução, ele será exibido de forma aleatória aproximadamente uma vez a cada hora e por um período de dois minutos. Se seu aplicativo estiver lidando com um fluxo constante de solicitações, o Profiler fará o upload de rastreamentos a cada hora.

Depois que seu aplicativo receber algum tráfego e o profiler tiver tido tempo de fazer o upload dos transes, você deverá ter rastreamentos para visualizar. Esse processo pode levar de 5 a 10 minutos. Para visualizar os rastreios, acesse o painel **Desempenho**, selecione **Executar Ações** para visualizar os rastreios do profiler e, em seguida, selecione o botão **Profiler Traces**.

![Rastreamentos do Profiler de versão prévia do painel de desempenho do Application Insights][performance-blade]

Selecione um exemplo para exibir uma divisão de nível de código de tempo gasto na execução da solicitação.

![Explorador de rastreamento do Application Insights][trace-explorer]

O explorador de rastreamento exibe as seguintes informações:

* **Mostrar afunilamento**: abre o maior nó folha ou pelo menos algo próximo. Na maioria dos casos, esse nó está próximo de um gargalo de desempenho.
* **Rótulo**: O nome da função ou do evento. A árvore exibe uma combinação de código e eventos que ocorreram (como eventos SQL e HTTP). Os principais eventos representa a duração total da solicitação.
* **Decorrido**: o intervalo de tempo entre o início e o final da operação.
* **Quando**: o horário em que a função ou evento estava sendo executado com relação a outras funções.

## <a name="how-to-read-performance-data"></a>Como ler dados de desempenho

O criador de perfil de serviço Microsoft usa uma combinação de métodos de amostragem e instrumentação para analisar o desempenho do seu aplicativo. Quando a coleção detalhada está em andamento, o criador de perfil de serviço obtém a amostra do ponteiro de instrução de cada CPU do computador em cada milissegundo. Cada amostra captura a pilha de chamadas completa do thread que está em execução no momento. Ela oferece informações detalhadas sobre o que esse thread estava fazendo, ambos em um alto nível e um baixo nível de abstração. O criador de perfil de serviço também coleta outros eventos para rastrear a correlação e causalidade da atividade, incluindo eventos de alternância de contexto, eventos TPL (Biblioteca de Paralelismo de Tarefas) e eventos de pool de threads.

A pilha de chamadas mostrada na exibição de linha do tempo é o resultado da amostragem e da instrumentação. Como cada amostra captura a pilha de chamadas completa do thread, ela inclui o código do Microsoft .NET Framework e de outras estruturas que você referencia.

### <a id="jitnewobj"></a>Alocação de objetos (clr!JIT\_New ou clr!JIT\_Newarr1)

**clr!JIT\_New** e **clr!JIT\_Newarr1** são funções auxiliares no .NET Framework que alocam memória de um heap gerenciado. **clr!JIT\_New** é invocado quando um objeto é alocado. **clr!JIT\_Newarr1** é invocado quando uma matriz de objetos é alocada. Essas duas funções normalmente são rápidas e levam quantidades relativamente pequenas de tempo. Se você vir que **clr!JIT\_New** ou **clr!JIT\_Newarr1** levam uma quantidade de tempo significativa em sua linha do tempo, isso indica que o código talvez esteja alocando muitos objetos e consumindo quantidades significativas de memória.

### <a id="theprestub"></a>Carregando código (clr!ThePreStub)

**clr!ThePreStub** é uma função auxiliar no .NET Framework que prepara o código para ser executado pela primeira vez. Isso geralmente inclui, mas não se limita à compilação just-in-time (JIT). Para cada método C#, **clr!ThePreStub** deve ser invocado no máximo uma vez durante o tempo de vida de um processo.

Se **clr! ThePreStub** demorar muito para uma solicitação, isso indica que a solicitação é a primeira a executar esse método. O tempo para o tempo de execução do .NET Framework carregar o primeiro método é significativo. Você pode considerar usar um processo de aquecimento que executa essa parte do código antes que seus usuários a acessem ou considerar executar o Gerador de Imagem Nativa (ngen.exe) em seus assemblies.

### <a id="lockcontention"></a>Contenção de bloqueio (clr!JITutil\_MonContention ou clr!JITutil\_MonEnterWorker)

**clr!JITutil\_MonContention** ou **clr!JITutil\_MonEnterWorker** indica que o thread atual está aguardando um bloqueio ser liberado. Esse texto frequentemente é exibido ao executar uma instrução **LOCK** em C#, ao invocar o método **Monitor.Enter** ou ao invocar um método com o atributo **MethodImplOptions.Synchronized**. Normalmente, a contenção de bloqueio ocorre quando um thread _A_ adquire um bloqueio e um thread _B_ tenta adquirir o mesmo bloqueio antes que o thread _A_ o libere.

### <a id="ngencold"></a>Carregando código ([COLD])

Se o nome do método contiver **[COLD]**, como **mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined**, o tempo de execução do .NET Framework estará executando código pela primeira vez que ele não for otimizado pela <a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">otimização guiada por perfil</a>. Para cada método, ele deve ser exibido no máximo uma vez durante o tempo de vida do processo.

Se o carregamento de um código leva uma quantidade significativa de tempo para uma solicitação, isso indica que a solicitação é a primeira a executar a parte não otimizada do método. Considere usar um processo de aquecimento que execute essa parte do código antes que seus usuários a acessem.

### <a id="httpclientsend"></a>Enviar solicitação HTTP

Métodos como **HttpClient.Send** indicam que o código está aguardando a conclusão de uma solicitação HTTP.

### <a id="sqlcommand"></a>Operação do banco de dados

Método como **SqlCommand.Execute** indicam que o código está aguardando a conclusão de uma operação de banco de dados.

### <a id="await"></a>Aguardando (AWAIT\_TIME)

**AWAIT\_TIME** indica que o código está aguardando a conclusão de outra tarefa. Normalmente isso acontece com a instrução **AWAIT** em C#. Quando o código faz um C# **AWAIT**, o encadeamento desenrola e retorna o controle para o conjunto de encadeamentos, e não há encadeamento bloqueado aguardando o **AWAIT** terminar. No entanto, logicamente, o thread que fez o **AWAIT** está "bloqueado" e aguardando a conclusão da operação. A instrução **AWAIT\_TIME** indica o tempo bloqueado aguardando a conclusão da tarefa.

### <a id="block"></a>Tempo bloqueado

**BLOCKED_TIME** indica que o código está aguardando até que outro recurso esteja disponível. Por exemplo, ele pode estar aguardando até que um objeto de sincronização, que um thread esteja disponível ou que a solicitação seja concluída.

### <a id="cpu"></a>Tempo de CPU

A CPU está ocupada executando as instruções.

### <a id="disk"></a>Tempo de disco

O aplicativo está executando operações de disco.

### <a id="network"></a>Tempo de rede

O aplicativo está executando operações de rede.

### <a id="when"></a>Quando colunas

A coluna **Quando** é uma visualização de como as amostras INCLUSIVAS coletadas para um nó variam ao longo do tempo. O intervalo total da solicitação é dividido em 32 buckets de tempo. As amostras inclusivas para esse nó estão acumuladas nesses 32 buckets. Cada bucket é representado como uma barra. A altura da barra representa um valor em escala. Para nós marcados com **CPU_TIME** ou **BLOCKED_TIME** ou em que há uma relação óbvia de consumo de um recurso (por exemplo, CPU, disco, thread), a barra representa o consumo de um dos recursos durante o período de tempo desse bucket. Para essas métricas, é possível obter um valor de maior do que 100% consumindo vários recursos. Por exemplo, se você usar, em média, duas CPUs durante um intervalo, obterá 200%.

## <a name="limitations"></a>Limitações

O período de retenção de dados padrão é de cinco dias. O máximo de dados que é ingerido por dia é 10 GB.

Não há encargos para usar o serviço do Profiler. Para usar o serviço do Profiler, seu aplicativo Web deverá estar hospedado pelo menos na camada Básica dos Aplicativos Web.

## <a name="overhead-and-sampling-algorithm"></a>Algoritmo de sobrecarga e amostragem

O Profiler é executado aleatoriamente dois minutos a cada hora em cada máquina virtual que hospeda o aplicativo com o Profiler habilitado para capturar rastreamentos. Quando o Profiler estiver em execução, ele adiciona de 5% a 15% de sobrecarga de CPU ao servidor.

## <a name="next-steps"></a>Próximas etapas
Habilite o Profiler do Application Insights para seu aplicativo do Azure
* [Serviços de Aplicativos](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Serviços de Nuvem](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Aplicativos do Service Fabric](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais e VM Scalesets](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
