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
ms.openlocfilehash: 5f691fb88c6764309bf012dfc65b561ec87afede
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2018
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Criar o perfil de aplicativos Web dinâmicos do Azure com o Application Insights

*Geralmente, este recurso do Application Insights está disponível para o Serviço de Aplicativo do Azure e está em versão prévia para recursos de computação do Azure.*

Descubra quanto tempo é gasto em cada método em seu aplicativo Web em tempo real ao usar o [Application Insights](app-insights-overview.md). A ferramenta de criação de perfil do Application Insights mostra perfis detalhados de solicitações dinâmicas que foram atendidas pelo seu aplicativo e realça o *afunilamento* que usa mais tempo. Solicitações com tempos de resposta diferentes são analisadas em uma base de amostragem. A sobrecarga para o aplicativo é minimizada usando várias técnicas.

O criador de perfil atualmente funciona para aplicativos Web do ASP.NET e ASP.NET Core em execução no Serviço de Aplicativo do Azure, pelo menos na camada de serviço **Básica**.

## <a id="installation"></a>Habilitar o criador de perfil para aplicativo Web dos Serviços de Aplicativos
Se você já tiver o aplicativo publicado nos Serviços de Aplicativos, mas não fez nada no código-fonte para usar o Application Insights, navegue até o painel de serviços de aplicativo no portal do Azure, vá para **Monitoramento | Application Insights**, siga as instruções no painel para criar um novo recurso ou selecione o recurso existente do Application Insights para monitorar seu Aplicativo Web. Observe que o Profiler funciona apenas com o plano de Serviços de Aplicativos **Básico** ou acima.

![Habilitar Insights de Aplicativo no portal de Serviços de Aplicativos][appinsights-in-appservices]

Se você tiver acesso ao código-fonte seu projeto, [instale o Application Insights](app-insights-asp-net.md). Se já estiver instalado, verifique se que você tem a versão mais recente. Para verificar a versão mais recente, no Gerenciador de Soluções, clique com o botão direito do mouse em seu projeto e, em seguida, selecione **Gerenciar pacotes NuGet** > **Atualizações** > **Atualizar todos os pacotes**. Em seguida, implante seu aplicativo.

Um aplicativo ASP.NET Core precisa instalar o pacote NuGet Microsoft.ApplicationInsights.AspNetCore 2.1.0-beta6 ou posterior para trabalhar com o criador de perfil. A partir de 27 de junho de 2017, não damos suporte a versões anteriores.

No [Portal do Azure](https://portal.azure.com), abra o recurso do Application Insights para seu aplicativo Web. Selecione **Desempenho** > **Habilitar o Application Insights Profiler**.

![Selecionar a faixa Habilitar criador de perfil][enable-profiler-banner]

Como alternativa, você pode selecionar a configuração de **Criador de perfil** para exibir o status e habilitar ou desabilitar o criador de perfil.

![Em Desempenho, selecione configuração de Criador de perfil][performance-blade]

Os aplicativos Web configurados com o Application Insights são listados no painel de configuração **Criador de perfil**. Se você tiver seguido as etapas acima, o agente do criador de perfil já deve estar instalado. Selecione **Habilitar Criador de perfil** no painel de configuração **Criador de perfil**.

Siga as instruções para instalar o agente do criador de perfil, se necessário. Se nenhum aplicativo Web tiver sido configurado com o Application Insights, selecione **Adicionar aplicativos vinculados**.

![Configurar opções do painel][linked app services]

Ao contrario dos aplicativos Web hospedados por meio de planos do Serviço de Aplicativo, os aplicativos hospedados nos recursos de computação do Azure (por exemplo, Máquinas Virtuais do Azure, conjuntos de dimensionamento de máquinas virtuais, Azure Service Fabric ou Serviços de Nuvem do Azure) não são gerenciados diretamente pelo Azure. Nesse caso, não há nenhum aplicativo Web ao qual vincular. Em vez de vincular a um aplicativo, selecione o botão **Habilitar o criador de perfil**.

### <a name="enable-the-profiler-for-azure-compute-resources-preview"></a>Habilite o criador de perfil para recursos de computação do Azure (visualização)

Obtenha informações sobre um [recurso em versão prévia do criador de perfil para recursos de computação do Azure](https://go.microsoft.com/fwlink/?linkid=848155).

## <a name="view-profiler-data"></a>Exibir dados do criador de perfil

**Certifique-se de que o aplicativo está recebendo tráfegos.** Se você estiver fazendo um experimento, você pode gerar solicitações para seu aplicativo Web usando o [Testes de Desempenho do Application Insights](https://docs.microsoft.com/en-us/vsts/load-test/app-service-web-app-performance-test). Se você habilitou recentemente o Criador de perfil, você pode executar um teste de carga curto por cerca de 15 minutos e obter rastreamentos do criador de perfil. Se você já tiver o Criador de perfil habilitado há algum tempo, lembre-se de que o Criador de perfil é executado aleatoriamente por duas vezes a cada hora e dois minutos toda vez que for executado. Sugira executar o teste de carga por uma hora para certificar-se de que você obtenha rastreamentos de criador de perfil de exemplo.

Uma vez que o aplicativo recebeu algum tráfego, vá para a folha **Desempenho**, vá para a seção da página **Realizar Ação** para exibir rastreamentos do criador de perfil. Selecione o botão **Rastreamentos do criador de perfil**.

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
**clr!JIT\_New** e **clr!JIT\_Newarr1** são funções auxiliares no .NET Framework que alocam memória de um heap gerenciado. **clr!JIT\_New** é invocado quando um objeto é alocado. **clr!JIT\_Newarr1** é invocado quando uma matriz de objetos é alocada. Essas duas funções normalmente são rápidas e levam quantidades relativamente pequenas de tempo. Se você vir que **clr!JIT\_New** ou **clr!JIT\_Newarr1** levam uma quantidade de tempo significativa em sua linha do tempo, será uma indicação de que o código talvez esteja alocando muitos objetos e consumindo quantidades significativas de memória.

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

## <a name="limitations"></a>Limitações

A retenção de dados padrão é de cinco dias. A ingestão máxima de dados por dia é de 10 GB.

Não há encargos para usar o serviço do criador de perfil. Para usar o serviço do criador de perfil, seu aplicativo Web deverá estar hospedado pelo menos na camada Básica do Serviço de Aplicativo.

## <a name="overhead-and-sampling-algorithm"></a>Algoritmo de sobrecarga e amostragem

O criador de perfil é executado aleatoriamente dois minutos a cada hora em cada máquina virtual que hospeda o aplicativo que com o criador de perfil habilitado para capturar rastreamentos. Quando o criador de perfil estiver em execução, ele adicionará uma sobrecarga entre 5% e 15% de CPU ao servidor.
Quanto mais servidores estiverem disponíveis para hospedar o aplicativo, menor será o impacto do criador de perfil no desempenho geral do aplicativo. Isso ocorre porque o algoritmo de amostragem faz o criador de perfil ser executado em apenas 5% dos servidores a qualquer momento. Mais servidores estão disponíveis para tender as solicitações da Web para deslocar a sobrecarga do servidor causada pela execução do criador de perfil.

## <a name="disable-the-profiler"></a>Desabilitar o criador de perfil
Para parar ou reiniciar o criador de perfil para uma instância individual do Serviço de Aplicativo, em **Trabalhos da Web**, vá para o recurso do Serviço de Aplicativo. Para excluir o criador de perfil, vá para **Extensões**.

![Desabilitar o Criador de Perfil para Trabalhos da Web][disable-profiler-webjob]

Recomendamos que você tenha o criador de perfil habilitado em todos os seus aplicativos Web para descobrir problemas de desempenho o mais rápido possível.

Se você usar WebDeploy para implantar as alterações em seu aplicativo Web, certifique-se de excluir a pasta App_Data da exclusão durante a implantação. Caso contrário, os arquivos da extensão do criador de perfil serão excluídos na próxima vez em que você implantar o aplicativo Web no Azure.


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

## <a id="profileondemand"></a>Disparar criador de perfil manualmente
Quando desenvolvemos o criador de perfil adicionamos uma interface de linha de comando para podemos testar o criador de perfil nos serviços de aplicativos. Usando essa mesma interface, os usuários também podem personalizar como o criador de perfil é iniciado. Em um nível alto, o criador de perfil usa o Sistema Kudu do Serviço de Aplicativo para gerenciar a criação de perfil em segundo plano. Quando você instala a extensão do Application Insights, criamos um trabalho Web contínuo que hospeda o criador de perfil. Usamos essa mesma tecnologia para criar um novo trabalho Web que você pode personalizar de acordo com suas necessidades.

Esta seção explica como:

1.  Criar um trabalho Web, que pode iniciar o criador de perfil por dois minutos com o toque de um botão.
2.  Criar um trabalho Web, que pode agendar a execução do criador de perfil.
3.  Definir argumentos para o criador de perfil.


### <a name="set-up"></a>Configurar
Primeiro vamos nos familiarizar com o painel de controle do trabalho Web. Nas configurações, clique na guia WebJobs.

![folha webjobs](./media/app-insights-profiler/webjobs-blade.png)

Como você pode ver, o painel mostra todos os trabalhos Web que estão atualmente instalados no seu site. Você pode ver o trabalho Web ApplicationInsightsProfiler2, que tem o trabalho do criador de perfil em execução. É aqui que vamos criar nossos novos trabalhos Web para a criação de perfil manual e programada.

Primeiro, vamos obter os binários necessários.

1.  Vá para o site do Kudu. Na guia de ferramentas de desenvolvimento, clique na guia "Advanced Tools" (Ferramentas Avançadas) com o logotipo do Kudu. Clique em "Go" (Ir). Isso o levará a um novo site e fará seu logon automaticamente.
2.  Em seguida, precisamos baixar os binários do criador de perfil. Navegue até o Explorador de Arquivos pelo Debug Console -> CMD localizado na parte superior da página.
3.  Clique no site -> wwwroot -> App_Data -> jobs -> continuous. Você deve ver uma pasta "ApplicationInsightsProfiler2". Clique no ícone de download à esquerda da pasta. Isso baixará um arquivo "ApplicationInsightsProfiler2.zip".
4.  Isso baixará todos os arquivos que serão usados no futuro. Recomendo criar um diretório limpo para mover o arquivo zip antes de continuar.

### <a name="setting-up-the-web-job-archive"></a>Configurando o arquivo de trabalho Web
Quando você adiciona um novo serviço Web para o site do azure, basicamente cria um arquivo zip com um run.cmd dentro. O run.cmd informa ao sistema de trabalho Web o que fazer quando você executá-lo. Há outras opções que você poderá ler na documentação do trabalho Web, mas, para nosso objetivo, não precisamos de mais nada.

1.  Para iniciar criar uma nova pasta, chamei a minha de "RunProfiler2Minutes".
2.  Copie os arquivos da pasta ApplicationInsightProfiler2 extraída para essa nova pasta.
3.  Crie um novo arquivo run.cmd. (Abri esta pasta de trabalho no VS Code antes de iniciar por questão de conveniência)
4.  Adicione o comando `ApplicationInsightsProfiler.exe start --engine-mode immediate --single --immediate-profiling-duration 120` e salve o arquivo.
a.  O comando `start` manda o criador de perfil iniciar.
b.  `--engine-mode immediate` diz ao criador de perfil que desejamos iniciar a criação de perfil imediatamente.
c.  `--single` significa executar e parar automaticamente d.  `--immediate-profiling-duration 120` significa executar o criador de perfil por 120 segundos ou 2 minutos.
5.  Salve o arquivo.
6.  Arquive essa pasta, você pode clicar com o botão direito na pasta e escolher Enviar para -> Pasta compactada (zipada). Isso criará um arquivo .zip usando o nome da pasta.

![comando iniciar criador de perfil](./media/app-insights-profiler/start-profiler-command.png)

Agora temos um. zip de trabalho Web que podemos usar para configurar os trabalhos Web em nosso site.

### <a name="add-a-new-web-job"></a>Adicionar um novo serviço Web
Em seguida, adicionaremos um novo trabalho Web em nosso site. O exemplo mostra como adicionar um trabalho Web disparado manualmente. Depois de você aprender a fazer isso, o processo será quase o mesmo para os agendados. Você pode ler mais sobre trabalhos disparados agendados por conta própria.

1.  Vá para painel de trabalhos Web.
2.  Clique no comando Adicionar na barra de ferramentas.
3.  Nomeie seu trabalho Web (escolhi o nome correspondente ao meu arquivo para maior clareza) e abra-o até ter versões diferentes do run.cmd.
4.  Na parte de carregamento de arquivo do formulário, clique no ícone Abrir arquivo e encontre o arquivo .zip criado anteriormente.
5.  Como tipo, escolha Triggered.
6.  Como gatilhos, escolha Manual.
7.  Pressione OK para salvar.

![comando iniciar criador de perfil](./media/app-insights-profiler/create-webjob.png)

### <a name="run-the-profiler"></a>Executar o criador de perfil

Agora que temos um novo trabalho Web que podemos disparar manualmente, podemos tentar executá-lo.

1.  É proposital só poder ter um processo ApplicationInsightsProfiler.exe executando em um computador de cada vez. Portanto, para começar, desabilite o trabalho Web Contínuo deste painel. Clique na linha e pressione "Stop". Atualize a barra de ferramentas e confirme se o status confirma que o trabalho foi interrompido.
2.  Clique na linha com o novo trabalho Web adicionado e pressione Executar.
3.  Com a linha ainda selecionada, clique no comando Logs na barra de ferramentas; isso o levará a um painel de trabalhos Web para esse trabalho Web iniciado. Ele listará as execuções mais recentes e seus resultados.
4.  Clique na execução recém-iniciada.
5.  Se tudo correr bem, você deve ver alguns logs de diagnóstico provenientes do criador de perfil confirmando que começamos a criação de perfil.

### <a name="things-to-consider"></a>Itens a serem considerados

Embora esse método seja relativamente simples, há algumas coisas precisam ser levadas em conta.

1.  Como isso não é gerenciado pelo nosso serviço, não temos nenhuma maneira de atualizar os binários de agente para seu trabalho Web. Atualmente, não temos uma página de download estável para nosso binários e, portanto, a única maneira de obter a versão mais recente é atualizando sua extensão e buscando na pasta contínua, como fizemos acima.
2.  Como ela usa argumentos de linha de comando que foram originalmente criados para uso de desenvolvedor em vez de uso do usuário final, esses argumentos podem mudar no futuro e, portanto, esteja ciente disso na hora de atualizar. Isso não deve ser um problema porque você pode adicionar um trabalho Web, executá-lo e testar se funciona. Eventualmente, vamos criar uma interface do usuário para fazer isso sem o processo manual, mas é algo a ser levado em conta.
3.  O recurso trabalhos Web para Serviços de Aplicativos é único porque quando ele executa o trabalho Web, faz com que o processo tenha as mesmas variáveis de ambiente e configurações do aplicativo que o seu site terá. Isso significa que você não precisa passar a chave de instrumentação por meio da linha de comando para o criador de perfil; ele precisa apenas buscar a chave de instrumentação do ambiente. No entanto, se deseja executar o criador de perfil na caixa de desenvolvimento ou em um computador fora dos Serviços de Aplicativos, você precisa fornecer uma chave de instrumentação. Você pode fazer isso passando um argumento `--ikey <instrumentation-key>`. Observe que esse valor deve corresponder à chave de instrumentação que seu aplicativo está usando. A saída do log do criador de perfil dirá qual chave de instrumentação usada na inicialização do criador de perfil e se detectamos atividade da chave de instrumentação enquanto criamos os perfis.
4.  Os trabalhos Web disparados manualmente podem, na verdade, ser disparados por meio de webhook. Você pode obter essa URL clicando com o botão direito do mouse no trabalho da Web no painel e exibindo as propriedades, Ou escolhendo propriedades na barra de ferramentas depois de selecionar o trabalho Web na tabela. Há muitos artigos online sobre isso e, portanto, não entrarei muito em detalhes, mas isso cria a possibilidade de disparar o criador de perfil de seu pipeline de CI/CD (como VSTS) ou algo como o Microsoft Flow (https://flow.microsoft.com/pt-br/). Dependendo do grau de refinamento desejado para seu run.cmd, que, aliás, pode ser um run.ps1, as possibilidades são infinitas.  

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
