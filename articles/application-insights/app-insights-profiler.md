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
ms.date: 02/08/2018
ms.author: mbullwin
ms.openlocfilehash: c65ef9141898369b8fcadd4c52972b767aca7cfe
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Criar o perfil de aplicativos Web dinâmicos do Azure com o Application Insights

*Geralmente, este recurso do Azure Application Insights está disponível para o recurso de Aplicativos Web do Serviço de Aplicativo do Azure e está em versão prévia para recursos de computação do Azure.*

Este artigo aborda a quantidade de tempo gasto em cada método de seu aplicativo Web em tempo real quando você usa o [Application Insights](app-insights-overview.md). A ferramenta Application Insights Profiler exibe perfis detalhados de solicitações ao vivo que foram atendidas pelo seu aplicativo. O Profiler destaca o *afunilamento* que gasta o maior tempo. Solicitações com tempos de resposta variados são analisadas em uma base de amostragem. Usando uma variedade de técnicas, você pode minimizar a sobrecarga associada ao aplicativo.

O Profiler atualmente funciona para aplicativos Web do ASP.NET e ASP.NET Core em execução nos Aplicativos Web. A camada de serviço Básica ou superior é necessária para usar o Profiler.

## <a id="installation"></a> Habilitar o Profiler para o aplicativo web de seus Aplicativos Web
Se você já tiver o aplicativo publicado em um aplicativo Web, mas ainda não tiver feito nada no código-fonte para usar o Application Insights, faça o seguinte:
1. Vá ao painel **Serviços de Aplicativos** no portal do Azure.
2. Em **monitoramento**, selecione **Application Insights** e, em seguida, siga as instruções no painel para criar um novo recurso ou selecione um recurso existente do Application Insights para monitorar seu aplicativo Web.

   ![Habilitar Insights de Aplicativo no portal de Serviços de Aplicativos][appinsights-in-appservices]

3. Se você tiver acesso ao código-fonte seu projeto, [instale o Application Insights](app-insights-asp-net.md).  
   Se já estiver instalado, verifique se que você tem a versão mais recente. Para verificar a versão mais recente, no Gerenciador de Soluções, clique com o botão direito do mouse em seu projeto e, em seguida, selecione **Gerenciar pacotes NuGet** > **Atualizações** > **Atualizar todos os pacotes**. Em seguida, implante seu aplicativo.

Aplicativos ASP.NET Core requerem a instalação do pacote NuGet Microsoft.ApplicationInsights.AspNetCore 2.1.0-beta6 ou posterior para trabalhar com o Profiler. A partir de 27 de junho de 2017, não haverá suporte para versões anteriores.

1. No [Portal do Azure](https://portal.azure.com), abra o recurso do Application Insights para seu aplicativo Web. 
2. Selecione **Desempenho** > **Habilitar o Application Insights Profiler**.

   ![Selecionar a faixa Habilitar criador de perfil][enable-profiler-banner]

3. Como alternativa, você pode selecionar a configuração do **Profiler** para exibir o status e habilitar ou desabilitar o Profiler.

   ![Selecionar configuração do Profiler][performance-blade]

   Os aplicativos Web configurados com o Application Insights são listados no painel de configuração **Criador de perfil**. Se você tiver seguido as etapas anteriores, o agente do Profiler deve estar instalado. 

4. No painel de configuração **Profiler**, Selecione **Habilitar Profiler**.

5. Se necessário, siga as instruções para instalar o agente do Profiler. Se nenhum aplicativo Web tiver sido configurado com o Application Insights, selecione **Adicionar aplicativos vinculados**.

   ![Configurar opções do painel][linked app services]

Ao contrario dos aplicativos Web hospedados por meio de planos do Aplicativos Web, os aplicativos hospedados nos recursos de computação do Azure (por exemplo, Máquinas Virtuais do Azure, conjuntos de dimensionamento de máquinas virtuais, Azure Service Fabric ou Serviços de Nuvem do Azure) não são gerenciados diretamente pelo Azure. Nesse caso, não há nenhum aplicativo Web ao qual vincular. Em vez de vincular a um aplicativo, selecione o botão **Habilitar o criador de perfil**.

### <a name="enable-profiler-for-azure-compute-resources-preview"></a>Habilite o Profiler para recursos de computação do Azure (visualização)

Para obter informações, consulte a [versão prévia do Profiler para recursos de computação do Azure](https://go.microsoft.com/fwlink/?linkid=848155).

## <a name="view-profiler-data"></a>Exibir dados do criador de perfil

Certifique-se de que o aplicativo está recebendo tráfego. Se estiver fazendo um experimento, você pode gerar solicitações para seu aplicativo Web usando [Testes de Desempenho do Application Insights](https://docs.microsoft.com/en-us/vsts/load-test/app-service-web-app-performance-test). Se você tiver habilitado recentemente o Profiler, pode executar um teste de carga curto por cerca de 15 minutos, que deve gerar rastreamentos do criador de perfil. Se você já habilitou o Profiler há algum tempo, lembre-se de que o ele é executado aleatoriamente duas vezes a cada hora e por uma duração de dois minutos toda vez que é executado. Sugerimos executar primeiro o teste de carga por uma hora para certificar-se de que você obtenha rastreamentos de criador de perfil de exemplo.

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
**clr!JITutil\_MonContention** ou **clr!JITutil\_MonEnterWorker** indica que o thread atual está aguardando um bloqueio ser liberado. Esse texto normalmente é exibido ao executar uma instrução **LOCK** em C#, ao invocar o método **Monitor.Enter** ou ao invocar o método com o atributo **MethodImplOptions.Synchronized**. Normalmente, a contenção de bloqueio ocorre quando um thread _A_ adquire um bloqueio e um thread _B_ tenta adquirir o mesmo bloqueio antes que o thread _A_ o libere.

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
Para parar ou reiniciar o Profiler para uma instância individual do aplicativo Web, em **Trabalhos da Web**, vá para o recurso dos Aplicativos Web. Para excluir o Profiler, vá para **Extensões**.

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
* Se seu aplicativo Web for um aplicativo ASP.NET Core, verifique [as dependências necessárias](#aspnetcore).

Depois que o Profiler é iniciado, há um período de aquecimento curto durante o qual ele coleta ativamente vários rastreamentos de desempenho. Depois disso, o Profiler coleta rastreamentos de desempenho de dois minutos a cada hora.

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
* Certifique-se de que a chave de instrumentação do Application Insights que você está usando em seu aplicativo é a mesma que o recurso do Application Insights usau para habilitar a criação de perfil. Geralmente a chave está em ApplicationInsights.config, mas ela também pode estar nos arquivos web.config ou app.config.

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
2. Defina a **versão do .Net Framework** como **v4.6**.
3. Defina **Sempre ativo** como **Ativado**.
4. Adicione a configuração de aplicativo **APPINSIGHTS_INSTRUMENTATIONKEY** e defina o valor como a mesma chave de instrumentação usada pelo SDK.
5. Abra **Ferramentas avançadas**.
6. Selecione **Ir** para abrir o site do Kudu.
7. No site do Kudu, selecione **Extensões de site**.
8. Instale o **Application Insights** da Galeria de Aplicativos Web do Azure.
9. Reinicie o aplicativo Web.

## <a id="profileondemand"></a>Disparar o Profiler manualmente
Quando desenvolvemos o Profiler, adicionamos uma interface de linha de comando para podermos testá-lo nos serviços de aplicativos. Ao usar essa mesma interface, os usuários também podem personalizar como o Profiler é iniciado. Em um nível alto, o Profiler usa o Sistema Kudu dos Aplicativos Web para gerenciar a criação de perfil em segundo plano. Quando você instala a extensão do Application Insights, criamos um trabalho Web contínuo que hospeda o Profiler. Usamos essa mesma tecnologia para criar um novo trabalho Web que você pode personalizar de acordo com suas necessidades.

Esta seção explica como:

* Criar um trabalho Web, que pode iniciar o Profiler por dois minutos com o toque de um botão.
* Criar um trabalho Web, que pode agendar a execução do Profiler.
* Definir argumentos para o Profiler.


### <a name="set-up"></a>Configurar
Primeiro, familiarize-se com o painel de controle do trabalho Web. Em **Configurações**, selecione a guia **Trabalhos Web**.

![folha webjobs](./media/app-insights-profiler/webjobs-blade.png)

Como você pode ver, o painel exibe todos os trabalhos Web que estão atualmente instalados no seu site. Você pode ver o trabalho Web ApplicationInsightsProfiler2, que tem o trabalho do Profiler em execução. É aqui que criamos novos trabalhos Web para a criação de perfil manual e programada.

Para obter os binários necessários faça o seguinte:

1.  No site do Kudu, na guia **Ferramentas de desenvolvimento**, selecione a guia **Ferramentas Avançadas** com o logotipo do Kudu e, em seguida, selecione **Ir**.  
   Abre um novo site e você é conectado automaticamente.
2.  Para fazer o download dos binários do Profiler, vá para o Explorador de Arquivos por meio de **Depurar Console** > **CMD**, que está localizado na parte superior da página.
3.  Selecione **Site** > **wwwroot** > **App_Data** > **Trabalhos** > **Contínuos**.  
   Você deve ver uma pasta chamada *ApplicationInsightsProfiler2*. 
4. Do lado esquerdo da pasta, selecione o ícone **Download**.  
   Essa ação faz o download do arquivo *ApplicationInsightsProfiler2.zip*. Recomendamos criar um diretório limpo para o qual mover o arquivo zip.

### <a name="setting-up-the-web-job-archive"></a>Configurando o arquivo de trabalho Web
Quando adiciona um novo serviço Web ao site do Azure, você basicamente cria um arquivo zip com um arquivo *run.cmd* dentro. O arquivo *run.cmd* informa ao sistema de trabalho Web o que fazer quando você o executa.

1.  Crie uma nova pasta (por exemplo, *RunProfiler2Minutes*).
2.  Copie os arquivos da pasta *ApplicationInsightProfiler2* extraída para essa nova pasta.
3.  Crie um novo arquivo *run.cmd*.  
    Para sua conveniência, você pode abrir a pasta de trabalho no Visual Studio Code antes de iniciar.
4.  No arquivo, adicione o comando `ApplicationInsightsProfiler.exe start --engine-mode immediate --single --immediate-profiling-duration 120`. Os comandos são descritos da seguinte maneira:

    * `start`: fala para o Profiler para iniciar.  
    * `--engine-mode immediate`: fala para o Profiler para iniciar a criação de perfil imediatamente.  
    * `--single`: fala para o Profiler executar e parar automaticamente.  
    * `--immediate-profiling-duration 120`: fala para o Profiler ser executado por 120 segundos ou 2 minutos.

5.  Salve suas alterações.
6.  Arquive a pasta clicando com o botão direito do mouse e, em seguida, selecionando **Enviar para** > **Pasta de compactadas (zipada)**.  
   Essa ação cria um arquivo .zip que usa o nome da pasta.

![Comando Start Profiler](./media/app-insights-profiler/start-profiler-command.png)

Você criou um arquivo.zip de trabalho Web que pode usar para configurar os trabalhos de Web no seu site.

### <a name="add-a-new-web-job"></a>Adicionar um novo serviço Web
Nesta seção, você pode adicionar um novo trabalho Web em seu site. O exemplo a seguir mostra como adicionar um trabalho Web disparado manualmente. Depois de adicionar o trabalho Web disparado manualmente, o processo é quase o mesmo para um trabalho Web agendado.

1.  Vá para painel de **Trabalhos Web**.
2.  Na barra de ferramentas, selecione **Adicionar**.
3.  Dê um nome ao trabalho Web.  
    Para maior clareza, pode ajudar corresponder o nome do seu arquivo e abri-lo para uma variedade de versões do arquivo *run.cmd*.
4.  Na área **Carregamento de arquivos** do formulário, selecione o ícone **Abrir arquivo** e, em seguida, procure o arquivo .zip criado na seção anterior.

    a.  Na caixa **Tipo**, selecione **Disparado**.  
    b.  Na caixa **Gatilhos**, selecione **Manual**.

5.  Selecione **OK**.

![Comando Start Profiler](./media/app-insights-profiler/create-webjob.png)

### <a name="run-profiler"></a>Executar Profiler

Agora que você tem um novo trabalho Web que pode disparar manualmente, pode tentar executá-lo seguindo as instruções nesta seção.

É proposital só poder ter um processo *ApplicationInsightsProfiler.exe* executando em um computador de cada vez. Portanto, para começar, desabilite o trabalho Web *Contínuo* deste painel. 
1. Selecione a linha com o novo trabalho Web e, em seguida, selecione **Parar**. 
2. Na barra de ferramentas, selecione **Atualizar** e confirme se o status indica que o trabalho foi interrompido.
3. Selecione a linha com o novo trabalho Web e, em seguida, selecione **Executar**.
4. Com a linha ainda selecionada, na barra de ferramentas, selecione o comando **Logs**.  
    Essa ação abre um painel de trabalhos Web para o novo trabalho Web e lista as execuções mais recentes e seus resultados.
5. Selecione a instância de execução recém-iniciada.  
    Se tiver disparado com êxito o novo trabalho Web, você pode exibir alguns logs de diagnóstico provenientes do Profiler que confirmam que a criação de perfil foi iniciada.

### <a name="things-to-consider"></a>Itens a serem considerados

Embora esse método seja relativamente simples, leve em conta o seguinte:

* Como seu trabalho Web não é gerenciado pelo nosso serviço, não temos nenhuma maneira de atualizar os binários de agente para ele. Atualmente, não temos uma página de download estável para nossos binários e, portanto, a única maneira de obter a versão mais recente é atualizando sua extensão e buscar na pasta *Contínua*, como fizemos nas etapas anteriores.

* Como esse processo utiliza argumentos de linha de comando que foram originalmente projetados para desenvolvedores em vez de usuários finais, os argumentos podem ser alterados no futuro. Lembre-se de possíveis alterações ao atualizar. Isso não deve ser um problema porque você pode adicionar um trabalho Web, executá-lo e testá-lo para garantir que funciona. Por fim, vamos criar uma IU para lidar com isso sem o processo manual.

* O recurso de trabalhos Web de Aplicativos Web é exclusivo. Quando ele executa o trabalho Web, faz com que o processo tenha as mesmas variáveis de ambiente e configurações do aplicativo que o seu site terá. Isso significa que você não precisa passar a chave de instrumentação por meio da linha de comando para o Profiler. O Profiler deve apenas escolher a chave de instrumentação do ambiente. No entanto, se deseja executar o Profiler na caixa de desenvolvimento ou em um computador fora dos Aplicativos Web, você precisa fornecer uma chave de instrumentação. Você pode fazer isso passando um argumento, `--ikey <instrumentation-key>`. Esse valor deve corresponder à chave de instrumentação que seu aplicativo está usando. A saída do log do Profiler diz qual chave de instrumentação foi usada na sua inicialização e se detectamos atividade da chave de instrumentação enquanto estávamos criando os perfis.

* Os trabalhos Web disparados manualmente podem ser disparados por meio de webhook. Você pode obter essa URL clicando com o botão direito do mouse no trabalho Web no painel e exibindo as propriedades. Ou, na barra de ferramentas, você pode selecionar **Propriedades** depois de selecionar o trabalho Web na tabela. Essa abordagem abre infinitas possibilidades, como acionar o Profiler de seu pipeline de CI/CD (como VSTS) ou algo parecido com o Microsoft Flow (https://flow.microsoft.com/en-us/). Por fim, sua escolha depende da complexidade que você deseja atribuir ao arquivo *run.cmd* (que também pode ser um arquivo *run.ps1*), mas há flexibilidade.

## <a name="next-steps"></a>Próximas etapas

* [Trabalhar com o Application Insights no Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
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
