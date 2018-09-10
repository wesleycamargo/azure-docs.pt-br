---
title: Criar o perfil de aplicativos Web online no Azure com o Application Insights Profiler | Microsoft Docs
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
ms.openlocfilehash: 6048a17bf50ecac691c7cf687f87e454c54ee9d9
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521876"
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Criar o perfil de aplicativos Web dinâmicos do Azure com o Application Insights

De modo geral, este recurso do Azure Application Insights está disponível para o recurso de Aplicativos Web do Serviço de Aplicativo do Azure e está em versão prévia para os recursos de computação do Azure. Para obter informações sobre o [uso local do criador de perfil](https://docs.microsoft.com/azure/application-insights/enable-profiler-compute#enable-profiler-on-on-premises-servers).

Este artigo aborda a quantidade de tempo gasto em cada método de seu aplicativo Web em tempo real quando você usa o [Application Insights](app-insights-overview.md). A ferramenta Application Insights Profiler exibe perfis detalhados de solicitações ao vivo que foram atendidas pelo seu aplicativo. O Profiler destaca o *afunilamento* que gasta o maior tempo. Solicitações com tempos de resposta variados são analisadas em uma base de amostragem. Usando uma variedade de técnicas, você pode minimizar a sobrecarga associada ao aplicativo.

O Profiler atualmente funciona para aplicativos Web do ASP.NET e ASP.NET Core em execução nos Aplicativos Web. A camada de serviço Básica ou superior é necessária para usar o Profiler.

## <a id="installation"></a> Habilitar o Profiler para seus Aplicativos Web

Depois de implantar um Aplicativo Web, independentemente de ter incluído o SDK do App Insights no código-fonte, faça o seguinte:

1. Vá ao painel **Serviços de Aplicativos** no portal do Azure.
1. Navegue até o painel **Configurações > Monitoramento**.

   ![Habilitar Insights de Aplicativo no portal de Serviços de Aplicativos](./media/app-insights-profiler/AppInsights-AppServices.png)

1. Siga as instruções no painel para criar um novo recurso ou selecione um recurso existente do App Insights para monitorar seu aplicativo Web. Aceite todas as opções padrão. O **diagnóstico no nível do código** fica ativado por padrão e habilita o Profiler.

   ![Adicione a extensão de site do App Insights][Enablement UI]

1. Agora, o Profiler está instalado com a extensão de site do App Insights e é habilitado usando uma configuração de aplicativo dos Serviços de Aplicativos.

    ![Configuração de aplicativo para o Profiler][profiler-app-setting]

### <a name="enable-profiler-for-azure-compute-resources-preview"></a>Habilite o Profiler para recursos de computação do Azure (visualização)

Para obter informações, consulte a [versão prévia do Profiler para recursos de computação do Azure](https://go.microsoft.com/fwlink/?linkid=848155).

## <a name="view-profiler-data"></a>Exibir dados do criador de perfil

Certifique-se de que o aplicativo está recebendo tráfego. Se estiver fazendo um experimento, você pode gerar solicitações para seu aplicativo Web usando [Testes de Desempenho do Application Insights](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test). Se você tiver habilitado recentemente o Profiler, pode executar um teste de carga curto por cerca de 15 minutos, que deve gerar rastreamentos do criador de perfil. Se você já habilitou o Profiler há algum tempo, lembre-se de que o ele é executado aleatoriamente duas vezes a cada hora e por uma duração de dois minutos toda vez que é executado. Sugerimos executar primeiro o teste de carga por uma hora para certificar-se de que você obtenha rastreamentos de criador de perfil de exemplo.

Quando o aplicativo receber algum tráfego, vá para o painel **Desempenho** > **Realizar Ação** para exibir rastreamentos do criador de perfil e, em seguida, selecione o botão **Rastreamentos do Profiler**.

![Rastreamentos do Profiler de versão prévia do painel de desempenho do Application Insights][performance-blade-v2-examples]

Selecione um exemplo para exibir uma divisão de nível de código de tempo gasto na execução da solicitação.

![Explorador de rastreamento do Application Insights][trace-explorer]

O explorador de rastreamento exibe as seguintes informações:

* **Mostrar afunilamento**: abre o maior nó folha ou pelo menos algo próximo. Na maioria dos casos, esse nó é adjacente a um gargalo de desempenho.
* **Rótulo**: O nome da função ou do evento. A árvore exibe uma combinação de código e eventos que ocorreram (como eventos SQL e HTTP). Os principais eventos representa a duração total da solicitação.
* **Decorrido**: o intervalo de tempo entre o início e o final da operação.
* **Quando**: o horário em que a função ou evento estava sendo executado com relação a outras funções.

## <a name="how-to-read-performance-data"></a>Como ler dados de desempenho

O criador de perfil de serviço Microsoft usa uma combinação de métodos de amostragem e instrumentação para analisar o desempenho do seu aplicativo. Quando a coleção detalhada está em andamento, o criador de perfil de serviço obtém a amostra do ponteiro de instrução de cada CPU do computador em cada milissegundo. Cada amostra captura a pilha de chamadas completa do thread que está em execução no momento. Ela oferece informações detalhadas sobre o que esse thread estava fazendo, ambos em um alto nível e um baixo nível de abstração. O criador de perfil de serviço também coleta outros eventos para rastrear a correlação e causalidade da atividade, incluindo eventos de alternância de contexto, eventos TPL (Biblioteca de Paralelismo de Tarefas) e eventos de pool de threads.

A pilha de chamadas mostrada na exibição de linha do tempo é o resultado da amostragem e da instrumentação. Como cada amostra captura a pilha de chamadas completa do thread, ela inclui o código do Microsoft .NET Framework e de outras estruturas que você referencia.

### <a id="jitnewobj"></a>Alocação de objetos (clr!JIT\_New ou clr!JIT\_Newarr1)

**clr!JIT\_New** e **clr!JIT\_Newarr1** são funções auxiliares no .NET Framework que alocam memória de um heap gerenciado. **clr!JIT\_New** é invocado quando um objeto é alocado. **clr!JIT\_Newarr1** é invocado quando uma matriz de objetos é alocada. Essas duas funções normalmente são rápidas e levam quantidades relativamente pequenas de tempo. Se você vir que **clr!JIT\_New** ou **clr!JIT\_Newarr1** levam uma quantidade de tempo significativa em sua linha do tempo, isso indica que o código talvez esteja alocando muitos objetos e consumindo quantidades significativas de memória.

### <a id="theprestub"></a>Carregando código (clr!ThePreStub)

**clr!ThePreStub** é uma função auxiliar no .NET Framework que prepara o código para ser executado pela primeira vez. Normalmente isso inclui a compilação JIT (Just-In-Time), entre outras. Para cada método C#, **clr!ThePreStub** deve ser invocado no máximo uma vez durante o tempo de vida de um processo.

Se a solicitação de **clr!ThePreStub** levar uma quantidade significativa de tempo, isso indicará que a solicitação será a primeira que executará esse método. O tempo para o tempo de execução do .NET Framework carregar o primeiro método é significativo. Você pode considerar usar um processo de aquecimento que executa essa parte do código antes que seus usuários a acessem ou considerar executar o Gerador de Imagem Nativa (ngen.exe) em seus assemblies.

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

A coluna **Quando** é uma visualização de como as amostras INCLUSIVAS coletadas para um nó variam ao longo do tempo. O intervalo total da solicitação é dividido em 32 buckets de tempo. As amostras inclusivas para esse nó estão acumuladas nesses 32 buckets. Cada bucket é representado como uma barra. A altura da barra representa um valor em escala. Para nós marcados com **CPU_TIME** ou **BLOCKED_TIME** ou em que há uma relação óbvia de consumo de um recurso (por exemplo, CPU, disco, thread), a barra representa o consumo de um dos recursos durante o período de tempo desse bucket. Para essas métricas, é possível obter um valor de maior do que 100% consumindo vários recursos. Por exemplo, se você usar, em média, duas CPUs durante um intervalo, obterá 200%.

## <a name="limitations"></a>Limitações

O período de retenção de dados padrão é de cinco dias. O máximo de dados que é ingerido por dia é 10 GB.

Não há encargos para usar o serviço do Profiler. Para usar o serviço do Profiler, seu aplicativo Web deverá estar hospedado pelo menos na camada Básica dos Aplicativos Web.

## <a name="overhead-and-sampling-algorithm"></a>Algoritmo de sobrecarga e amostragem

O Profiler é executado aleatoriamente dois minutos a cada hora em cada máquina virtual que hospeda o aplicativo com o Profiler habilitado para capturar rastreamentos. Quando o Profiler estiver em execução, ele adiciona de 5% a 15% de sobrecarga de CPU ao servidor.

Quanto mais servidores estiverem disponíveis para hospedar o aplicativo, menor será o impacto do Profiler no desempenho geral do aplicativo. Isso ocorre porque o algoritmo de amostragem faz o Profiler ser executado em apenas 5% dos servidores a qualquer momento. Mais servidores estão disponíveis para tender as solicitações da Web para deslocar a sobrecarga do servidor causada pela execução do Profiler.

## <a name="disable-profiler"></a>Desabilitar o Profiler

Para parar ou reiniciar o Profiler para uma instância individual do aplicativo Web, em **Trabalhos Web**, vá para o recurso dos Aplicativos Web. Para excluir o Profiler, vá para **Extensões**.

![Desabilitar o Profiler para trabalhos da web][disable-profiler-webjob]

Recomendamos que você tenha o Profiler habilitado em todos os seus aplicativos Web para descobrir problemas de desempenho o mais rápido possível.

Se você usar WebDeploy para implantar as alterações em seu aplicativo Web, certifique-se de excluir a pasta App_Data da exclusão durante a implantação. Caso contrário, os arquivos da extensão do Profiler serão excluídos na próxima vez em que você implantar o aplicativo Web no Azure.


## <a id="troubleshooting"></a>Solucionar problemas

### <a name="too-many-active-profiling-sessions"></a>Número excessivo de sessões de criação de perfil ativas

No momento, é possível habilitar o Profiler em no máximo quatro aplicativos Web do Azure e slots de implantação em execução no mesmo plano de serviço. Se o trabalho Web do Profiler estiver relatando um número excessivo de sessões de criação de perfil ativas, mova alguns aplicativos Web para um plano de serviço diferente.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Como fazer para determinar se o Application Insights Profiler está em execução?

O Profiler é executado como um trabalho Web contínuo no aplicativo Web. É possível abrir o recurso do aplicativo Web no [Portal do Azure](https://portal.azure.com). Se o painel **WebJobs**, verifique o status de **ApplicationInsightsProfiler**. Se ele não estiver em execução, abra **Registros** para obter mais informações.

### <a name="why-cant-i-find-any-stack-examples-even-though-profiler-is-running"></a>Por que não é possível encontrar nenhum exemplo de pilha, mesmo com o Profiler em execução?

Aqui estão algumas coisas que você pode verificar:

* Certifique-se de que o seu plano de serviço de aplicativo Web é a camada básica ou superior.
* Certifique-se de que seu aplicativo Web tem o Application Insights SDK 2.2 Beta ou posterior habilitado.
* Certifique-se de que seu aplicativo Web tem a configuração **APPINSIGHTS_INSTRUMENTATIONKEY** com a mesma chave de instrumentação usada pelo SDK do Application Insights.
* Certifique-se de que seu aplicativo Web está sendo executado no .NET Framework 4.6.
* Se seu aplicativo web é um aplicativo ASP.NET Core, deve estar executando pelo menos ASP.NET Core 2.0.

Depois que o Profiler é iniciado, há um período de aquecimento curto durante o qual ele coleta ativamente vários rastreamentos de desempenho. Depois disso, o Profiler coleta rastreamentos de desempenho de dois minutos a cada hora.

> [!NOTE]
> Há um bug no agente do criador de perfil que o impede de carregar os rastreamentos feitos em aplicativos executados no ASP.NET Core 2.1. Estamos trabalhando em uma correção e estará pronta em breve.

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>Estava usando o criador de perfil de serviço do Azure. O que aconteceu?

Quando você habilita o Application Insights Profiler, o agente do Criador de Perfil de Serviço do Azure está desabilitado.

### <a id="double-counting"></a>Dois segmentos paralelos de contagem

Em alguns casos, a métrica de tempo toral no visualizador de pilha é maior que a duração da solicitação.

Essa situação poderá ocorrer quando dois ou mais threads estiverem associados a uma solicitação e eles estiverem operando em paralelo. Nesse caso, o tempo total de thread é maior que o tempo decorrido. Um thread pode estar aguardando até que o outro seja concluído. O visualizador tenta detectar este e omite a espera desinteressante, mas erra ao exibir muitas informações em vez de omitir o que poderiam ser informações críticas.

Quando você vir threads paralelos em seus rastreamentos, determine quais threads estão aguardando para você poder verificar o caminho crítico para a solicitação. Na maioria dos casos, o thread rapidamente entra em estado de espera é simplesmente aguardando outros threads. Concentre-se nos outros threads e ignore o tempo nos threads em espera.

### <a id="issue-loading-trace-in-viewer"></a>Nenhum dado de criação de perfil

Aqui estão algumas coisas que você pode verificar:

* Se os dados que você está tentando exibir mais de duas semanas, tente limitar seu filtro de tempo e tente novamente.
* Certifique-se de que um firewall ou proxies não tenham bloqueado o acesso a https://gateway.azureserviceprofiler.net.
* Certifique-se de que a chave de instrumentação do Application Insights que você está usando em seu aplicativo seja a mesma que a do recurso do Application Insights que você usou para habilitar a criação de perfil. Geralmente a chave está em ApplicationInsights.config, mas ela também pode estar nos arquivos web.config ou app.config.

### <a name="error-report-in-the-profiling-viewer"></a>Relatório de erro no Visualizador de criação de perfil

Envie um tíquete de suporte no portal. Certifique-se de incluir uma ID de correlação da mensagem de erro.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Erro de implantação: diretório não vazio 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Se você estiver reimplantando seu aplicativo Web em um recurso dos Aplicativos Web com o Profiler habilitado, talvez você veja uma mensagem como a seguinte:

*Diretório não vazio 'D:\\home\\site\\wwwroot\\App_Data\\jobs'*

Este erro ocorrerá se você executar a Implantação da Web de scripts ou do Pipeline de implantação do Visual Studio Team Services. A solução é adicionar os seguintes parâmetros de implantação adicionados à tarefa de Implantação da Web:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Esses parâmetros excluem a pasta usada pelo Application Insights Profiler e desbloqueiam o processo de reimplantação. Eles não afetam a instância do Profiler em execução no momento.

## <a name="manual-installation"></a>Instalação manual

Quando você configura o Profiler, são feitas atualizações nas configurações do aplicativo Web. Será possível aplicar as atualizações manualmente se seu ambiente exigir. Um exemplo pode ser que seu aplicativo está sendo executado em um ambiente de Aplicativos Web para o PowerApps.

1. No painel **Controle do Aplicativo Web**, abra **Configurações**.
1. Defina a **versão do .Net Framework** como **v4.6**.
1. Defina **Sempre ativo** como **Ativado**.
1. Adicione a configuração de aplicativo **APPINSIGHTS_INSTRUMENTATIONKEY** e defina o valor como a mesma chave de instrumentação usada pelo SDK.
1. Abra **Ferramentas avançadas**.
1. Selecione **Ir** para abrir o site do Kudu.
1. No site do Kudu, selecione **Extensões de site**.
1. Instale o **Application Insights** da Galeria de Aplicativos Web do Azure.
1. Reinicie o aplicativo Web.

## <a id="profileondemand"></a>Disparar o Profiler manualmente

O Profiler pode ser acionado manualmente clicando em um botão. Imagine que você está executando um teste de desempenho na Web. Você precisará de rastreamentos para ajudá-lo a entender o desempenho de seu aplicativo Web sob carga. Ter controle de quando os rastreamentos são capturados é crucial, uma vez que você sabe quando o teste de carga será executado, mas o intervalo de amostragem aleatório poderá perdê-lo.
As etapas a seguir ilustram como esse cenário funciona:

### <a name="optional-step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>(Opcional) Etapa 1: Gerar tráfego para seu aplicativo Web iniciando um teste de desempenho na Web

Se seu aplicativo Web já tiver tráfego de entrada ou se você apenas quiser gerar manualmente o tráfego, ignore esta seção e vá para a Etapa 2.

Navegue até o portal do Application Insights, **Configurar > Teste de Desempenho**. Clique no botão Novo para iniciar um novo teste de desempenho.
![criar novo teste de desempenho][create-performance-test]

No painel **Novo teste de desempenho**, configure a URL de destino do teste. Aceite todas as configurações padrão e comece a execução do teste de carga.

![Configurar o teste de carga][configure-performance-test]

Você verá que o novo teste é colocada na fila primeiro, seguido pelo status "em andamento".

![o teste de carga é enviado e colocado na fila][load-test-queued]

![o teste de carga está com a execução em andamento][load-test-in-progress]

### <a name="step-2-start-profiler-on-demand"></a>Etapa 2: Iniciar o criador de perfil sob demanda

Quando o teste de carga estiver em execução, poderemos iniciar o criador de perfil para capturar rastreamentos no aplicativo Web enquanto ele recebe a carga.
Navegue até o painel Configurar Profiler:

![entrada no painel configurar profiler][configure-profiler-entry]

No **painel Configurar Profiler**, há um botão **Perfil Agora** para acionar o criador de perfil em todas as instâncias dos aplicativos Web vinculados. Além disso, você tem visibilidade de quando o criador de perfil esteve em execução no passado.

![Profiler sob demanda][profiler-on-demand]

Você verá a notificação e a alteração do status no status de execução do criador de perfil.

### <a name="step-3-view-traces"></a>Etapa 3: Exibir rastreamentos

Após a conclusão da execução do criador de perfil, siga as instruções na notificação para ir até a folha Desempenho e exibir os rastreamentos.

### <a name="troubleshooting-on-demand-profiler"></a>Solução de problemas do criador de perfil sob demanda

Às vezes, você poderá ver uma mensagem de erro de tempo limite do Profiler após uma sessão sob demanda:

![Erro de tempo limite do Profiler][profiler-timeout]

Pode haver dois motivos para você ver este erro:

1. A sessão de criador de perfil sob demanda foi bem-sucedida, mas o Application Insights demorou muito para processar os dados coletados. Se os dados não terminarem de ser processados em 15 minutos, o portal exibirá uma mensagem de tempo limite. No entanto, após algum tempo, os rastreamentos do Profiler serão exibidos. Se isso acontecer, basta ignorar a mensagem de erro por enquanto. Estamos trabalhando de forma ativa para solucionar o problema

1. Seu aplicativo Web tem uma versão mais antiga do agente do Profiler que não tem o recurso sob demanda. Se você tiver habilitado o perfil do Application Insights anteriormente, é provável que você precise atualizar o agente do Profiler para começar a usar o recurso sob demanda.
  
Siga estas etapas para verificar e instalar o Profiler mais recente:

1. Vá até as Configurações de Aplicativo dos Serviços de Aplicativos e verifique se as seguintes configurações estão definidas:
    * **APPINSIGHTS_INSTRUMENTATIONKEY**: substitua pela chave de instrumentação correta do Application Insights.
    * **APPINSIGHTS_PORTALINFO**: ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0 Se alguma dessas configurações não estiver definida, vá até o painel de habilitação do Application Insights para instalar a extensão de site mais recente.

1. Vá até o painel do Application Insights no portal dos Serviços de Aplicativos.

    ![Habilite o Application Insights no portal dos Serviços de Aplicativos][enable-app-insights]

1. Se vir um botão "Atualizar" na página seguinte, clique nele para atualizar a extensão de site do Application Insights que instalará o agente do Profiler mais recente.
![Atualizar extensão do site][update-site-extension]

1. Em seguida, clique em **alterar** para garantir que o Profiler seja ativado e selecione **OK** para salvar as alterações.

    ![Alterar e salvar o Application insights][change-and-save-appinsights]

1. Volte para a guia **Configurações do Aplicativo** do Serviço de Aplicativo para verificar novamente se os seguintes itens de configurações do aplicativo estão definidos:
    * **APPINSIGHTS_INSTRUMENTATIONKEY**: substitua pela chave de instrumentação correta do Application Insights.
    * **APPINSIGHTS_PORTALINFO**: ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

    ![Configurações de aplicativo para o criador de perfil][app-settings-for-profiler]

1. Você também pode verificar a versão da extensão e certificar-se de que não há nenhuma atualização disponível.

    ![verificar atualização da extensão][check-for-extension-update]

## <a name="next-steps"></a>Próximas etapas

* [Trabalhar com o Application Insights no Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
[Enablement UI]: ./media/app-insights-profiler/Enablement_UI.png
[profiler-app-setting]:./media/app-insights-profiler/profiler-app-setting.png
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
[create-performance-test]: ./media/app-insights-profiler/new-performance-test.png
[configure-performance-test]: ./media/app-insights-profiler/configure-performance-test.png
[load-test-queued]: ./media/app-insights-profiler/load-test-queued.png
[load-test-in-progress]: ./media/app-insights-profiler/load-test-inprogress.png
[profiler-on-demand]: ./media/app-insights-profiler/Profiler-on-demand.png
[configure-profiler-entry]: ./media/app-insights-profiler/configure-profiler-entry.png
[enable-app-insights]: ./media/app-insights-profiler/enable-app-insights-blade-01.png
[update-site-extension]: ./media/app-insights-profiler/update-site-extension-01.png
[change-and-save-appinsights]: ./media/app-insights-profiler/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/app-insights-profiler/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/app-insights-profiler/check-extension-update-01.png
[profiler-timeout]: ./media/app-insights-profiler/profiler-timeout.png
