<properties linkid="web-sites-create-web-jobs" urlDisplayName="Use WebJobs to run background tasks in Microsoft Azure Websites" pageTitle="Use WebJobs to run background tasks in Microsoft Azure Websites" metaKeywords="Microsoft Azure Web Sites, Web Jobs, background tasks" description="Learn how to run background tasks in Microsoft Azure Websites." metaCanonical="" services="web-sites" documentationCenter="" title="Use WebJobs to run background tasks in Microsoft Azure Websites" authors="cephalin"  solutions="" writer="timamm" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Use o Trabalhos Web para executar tarefas em segundo plano nos Sites do Microsoft Azure

Os sites do Microsoft Azure permitem executar programas ou scripts no site de uma destas três maneiras: sob demanda, continuamente ou por agendamento. Não há custo adicional em usar Trabalhos Web do Microsoft Azure, a menos que você queira habilitar o recurso Sempre Visível descrito posteriormente neste artigo.

## Sumário

-   [Tipos de arquivo aceitáveis para scripts][Tipos de arquivo aceitáveis para scripts]
-   [Criar uma tarefa sob demanda][Criar uma tarefa sob demanda]
-   [Criar uma tarefa em execução continuamente][Criar uma tarefa em execução continuamente]
-   [Criar uma tarefa agendada][Criar uma tarefa agendada]
    -   [Trabalhos agendados e Agendador do Azure][Trabalhos agendados e Agendador do Azure]
-   [Exibir o histórico do trabalho][Exibir o histórico do trabalho]
-   [Observações][Observações]
-   [Próximas etapas][Próximas etapas]
    -   [Fazer mais com o SDK dos Trabalhos Web do Microsoft Azure][Fazer mais com o SDK dos Trabalhos Web do Microsoft Azure]
    -   [Métodos alternativos de implantação][Métodos alternativos de implantação]
    -   [Recursos adicionais][Recursos adicionais]

<a name="acceptablefiles"></a>

## Tipos de arquivo aceitáveis para scripts

Os seguintes tipos de arquivo são aceitos como scripts executáveis:

.cmd, .bat, .exe (usando o cmd do Windows)

.ps1 (usando o PowerShell)

.sh (usando bash)

.php (usando php)

.py (usando o python)

.js (usando nó)

<a name="CreateOnDemand"></a>

## Criar uma tarefa sob demanda

1.  Na barra de comandos da página **Trabalhos da Web**, clique em **Adicionar**. A caixa de diálogo **Novo Trabalho** é exibida.

    ![Tarefa sob demanda][Tarefa sob demanda]

2.  Em **Nome**, forneça um nome para a tarefa. O nome deve começar com uma letra ou um número e não pode conter caracteres especiais além de "-" e "\_".

3.  Na caixa **Conteúdo (Arquivos Zip - 100MB máx.)**, navegue até o arquivo zip que contém o script. O arquivo zip deve conter o executável (.exe, .cmd, .bat, .sh, .php, .py, .js), bem como os arquivos de suporte necessários para executar o script ou o programa.

4.  Na caixa **Como Executar**, escolha **Executado sob Demanda**.

5.  Marque a caixa de seleção no canto inferior direito da caixa de diálogo para carregar o script no site. O nome especificado para a tarefa aparece na lista:

    ![Lista de tarefas][Lista de tarefas]

6.  Para executar o script, selecione seu nome na lista e clique em **Executar uma vez** na barra de comandos na parte inferior da página do portal.

    ![Executar uma vez][Executar uma vez]

<a name="CreateContinuous"></a>

## Criar uma tarefa em execução continuamente

1.  Para criar uma tarefa em execução continuamente, siga as mesmas etapas para criar uma tarefa que é executada uma vez, mas na caixa **Como Executar**, escolha **Executar continuamente**.

    ![Nova tarefa contínua][Nova tarefa contínua]

2.  Para iniciar ou parar uma tarefa em execução continuamente, selecione a tarefa na lista e clique em **Iniciar** ou **Parar** na barra de comandos.

> [WACOM.NOTE] Se seu site for executado em mais de uma instância, uma tarefa em execução contínua será executada em todas as suas instâncias. Tarefas sob demanda e agendadas são executadas em uma única instância selecionada para o balanceamento de carga pelo Microsoft Azure.

> [WACOM.NOTE]
> Para tarefas contínuas, é recomendável habilitar **Sempre Ativo** na página Configurar de seu site. O recurso Sempre Ativo, disponível nos modos Básico e Padrão, impede que os sites sejam descarregados, mesmo que tenham permanecido ociosos por algum tempo. Se seu site estiver sempre carregado, a tarefa em execução contínua poderá ser executada de maneira mais confiável.

<a name="CreateScheduled"></a>

## Criar uma tarefa agendada

1.  Para criar uma tarefa agendada, siga as mesmas etapas anteriores, mas, na caixa **Como Executar**, escolha **Executar segundo um cronograma**.

    ![Novo trabalho agendado][Novo trabalho agendado]

2.  Escolha a **Região do Agendador** para seu trabalho e, em seguida, clique na seta na parte inferior direita da caixa de diálogo para prosseguir para a próxima tela.

3.  Na caixa de diálogo **Criar Trabalho**, escolha o tipo de **Recorrência** desejado: **Trabalho avulso** ou **Trabalho recorrente**.

    ![Agendar recorrência][Agendar recorrência]

4.  Escolha também uma hora **Inicial**: **Agora** ou **Em uma hora específica**.

    ![Agendar hora inicial][Agendar hora inicial]

5.  Se desejar iniciar em uma hora específica, escolha os valores da hora inicial em **Início em**.

    ![Agendar o início em uma hora específica][Agendar o início em uma hora específica]

6.  Se escolher um trabalho recorrente, você tem a opção **Repetir a Cada** para especificar a frequência de ocorrência e a opção **Término em** para especificar uma hora de término.

    ![Agendar recorrência][1]

7.  Se escolher **Semanas**, você poderá selecionar a caixa **Em uma agenda específica** caixa e especificar os dias da semana em que deseja que o trabalho seja executado.

    ![Agendar em dias da semana][Agendar em dias da semana]

8.  Se você escolher **Meses** e selecionar a caixa **Em uma Agenda Específica**, poderá definir o trabalho a ser executado em determinados **Dias** numerados do mês.

    ![Agendar datas específicas no mês][Agendar datas específicas no mês]

9.  Se você escolher **Dias da Semana**, poderá selecionar em qual dia ou dias da semana no mês deseja que o trabalho seja executado.

    ![Agendar dias da semana específicos em um mês][Agendar dias da semana específicos em um mês]

10. Finalmente, você também pode usar a opção **Ocorrências** para escolher em qual semana do mês (primeira, segunda, terceira etc.) deseja que o trabalho seja executado em dias da semana especificados.

    ![Agendar dias da semana específicos em semanas específicas em um mês][Agendar dias da semana específicos em semanas específicas em um mês]

11. Depois que você tiver criado um ou mais trabalhos, os nomes serão exibidos na guia Trabalhos Web com status, tipo de cronograma e outras informações. As informações históricas das 30 últimas tarefas são mantidas.

    ![Lista de trabalhos][Lista de trabalhos]

<a name="Scheduler"></a>

### Trabalhos agendados e Agendador do Azure

Os trabalhos agendados também podem ser configurados no portal do Agendador do Azure.

1.  Na página Trabalhos Web, clique no link **cronograma** do trabalho para navegar até a página do portal do Agendador do Azure.

    ![Link para Agendador do Azure][Link para Agendador do Azure]

2.  Na página do Agendador, clique no trabalho.

    ![Trabalho na página do portal do Agendador][Trabalho na página do portal do Agendador]

3.  A página **Ação de Trabalho** é aberta, para que você possa configurá-lo.

    ![Ação de trabalho PageInScheduler][Ação de trabalho PageInScheduler]

<!-- ================ ViewJobHistory ================= -->

<a name="ViewJobHistory"></a>

## Exibir o histórico do trabalho

1.  Para visualizar o histórico de execução de um trabalho, inclusive trabalhos criados com o SDK de Trabalhos Web, clique no link correspondente na coluna **Logs**. (Se quiser, você poderá usar o ícone da área de transferência para copiar a URL da página do arquivo de log para a área de transferência.)

    ![Link de logs][Link de logs]

2.  O clique no link abre a página de detalhes de trabalhos Web da tarefa. Esta página mostra o nome do comando executado, os horários das execuções mais recentes, além do sucesso ou da falha. Em **Execuções de trabalho recentes**, clique em uma hora para ver mais detalhes.

    ![WebJobDetails][WebJobDetails]

3.  A página **Detalhes da Execução do Trabalho Web** é exibida. Clique em **Alternar Saída** para ver o texto do conteúdo do log. O log de saída está em formato de texto.

    ![Detalhes da execução do trabalho Web][Detalhes da execução do trabalho Web]

4.  Para ver o texto de saída em uma janela separada do navegador, clique no link **download**. Para baixar o texto propriamente dito, com o botão direito do mouse no link e use as opções do navegador para salvar o conteúdo do arquivo.

    ![Baixar saída do log][Baixar saída do log]

5.  O link **Trabalhos Web** na parte superior da página oferece uma maneira prática de obter a uma lista de trabalhos Web no painel de histórico.

    ![Vincular à lista de trabalhos Web][Vincular à lista de trabalhos Web]

    ![Lista de trabalhos no painel de histórico][Lista de trabalhos no painel de histórico]

    O clique em um desses links leva você até a página Detalhes do Trabalho Web do trabalho selecionado.

<a name="WHPNotes"></a>

## Observações

-   Desde março de 2014, os sites no modo Disponível podem atingir tempo limite depois de 20 minutos se não houver solicitações para o site do scm (implantação) e o portal do site não estiver aberto no Azure. As solicitações para o site real não redefinirão isso.

-   O código para um trabalho contínuo precisa ser escrito para ser executado em um loop infinito.

-   Trabalhos contínuos só são executados continuamente quando o site estiver funcionando.

-   Os modos Básico e Padrão oferecem o recurso Sempre Ativo que, quando habilitado, evita que os sites fiquem ociosos.

<a name="NextSteps"></a>

## Próximas etapas

<!-- ======= Do More with the Microsoft Azure WebJobs SDK ======== -->

<a name="WebJobsSDK"></a>

### Fazer mais com o SDK dos Trabalhos Web do Microsoft Azure

O SDK dos Trabalhos Web do Microsoft Azure simplifica a tarefa de programar um Trabalho Web que funcione com filas, blobs ou tabelas de Armazenamento do Azure, ou com filas do Barramento de Serviço do Azure. O painel, agora integrado ao portal do Azure, oferece monitoramento e diagnóstico avançados para os programas que você escreve usando o SDK. Os recursos de monitoramento e diagnóstico fazem parte do SDK e não requerem que você adicione nenhum código especial em seu programa.

Para obter mais informações, consulte o tutorial [Introdução ao SDK dos Trabalhos Web do Microsoft Azure][Introdução ao SDK dos Trabalhos Web do Microsoft Azure].

<!-- =========== Alternative Methods of Deployment ============= -->

<a name="AlternateDeployments"></a>

### Métodos alternativos de implantação

O Visual Studio 2013 possui recursos que automatizam a implantação de projetos de Aplicativos de console como o Trabalhos Web. Para obter mais informações, consulte [Como implantar os Trabalhos Web do Azure em sites do Azure][Como implantar os Trabalhos Web do Azure em sites do Azure].

Você também pode usar FTP, o Git ou a Implantação da Web para implantar o Trabalhos Web. Para obter mais informações, consulte [Implantando um aplicativo de console do .NET em Git no Azure usando Trabalhos Web][Implantando um aplicativo de console do .NET em Git no Azure usando Trabalhos Web] e [Como implantar o Trabalhos Web do Windows Azure][Como implantar o Trabalhos Web do Windows Azure].

<a name="AdditionalResources"></a>

### Recursos adicionais

-   Para obter uma lista comentada de links sobre o recurso Trabalhos Web, consulte [Trabalhos Web do Azure - Recursos Recomendados][Trabalhos Web do Azure - Recursos Recomendados].

-   Vídeos relacionados ao Trabalhos Web:

    [Trabalhos Web do Azure 101 - Trabalhos Web básicos com Jaime Espinosa][Trabalhos Web do Azure 101 - Trabalhos Web básicos com Jaime Espinosa]

    [Trabalhos Web do Azure 102 - Trabalhos Web agendados e Painel dos Trabalhos Web com Jaime Espinosa][Trabalhos Web do Azure 102 - Trabalhos Web agendados e Painel dos Trabalhos Web com Jaime Espinosa]

    [Agendador do Azure 101 - Kevin Lam explica como programar coisas][Agendador do Azure 101 - Kevin Lam explica como programar coisas]

### Introdução

Para começar a usar o Azure, consulte [Avaliação Gratuita do Microsoft Azure][Avaliação Gratuita do Microsoft Azure].

<!-- LINKS --> 
<!-- IMAGES -->

  [Tipos de arquivo aceitáveis para scripts]: #acceptablefiles
  [Criar uma tarefa sob demanda]: #CreateOnDemand
  [Criar uma tarefa em execução continuamente]: #CreateContinuous
  [Criar uma tarefa agendada]: #CreateScheduled
  [Trabalhos agendados e Agendador do Azure]: #Scheduler
  [Exibir o histórico do trabalho]: #ViewJobHistory
  [Observações]: #WHPNotes
  [Próximas etapas]: #NextSteps
  [Fazer mais com o SDK dos Trabalhos Web do Microsoft Azure]: #WebJobsSDK
  [Métodos alternativos de implantação]: #AlternateDeployments
  [Recursos adicionais]: #AdditionalResources
  [Tarefa sob demanda]: ./media/web-sites-create-web-jobs/01aOnDemandWebJob.png
  [Lista de tarefas]: ./media/web-sites-create-web-jobs/02aWebJobsList.png
  [Executar uma vez]: ./media/web-sites-create-web-jobs/13RunOnce.png
  [Nova tarefa contínua]: ./media/web-sites-create-web-jobs/03aNewContinuousJob.png
  [Novo trabalho agendado]: ./media/web-sites-create-web-jobs/04aNewScheduledJob.png
  [Agendar recorrência]: ./media/web-sites-create-web-jobs/05SchdRecurrence.png
  [Agendar hora inicial]: ./media/web-sites-create-web-jobs/06SchdStart.png
  [Agendar o início em uma hora específica]: ./media/web-sites-create-web-jobs/07SchdStartOn.png
  [1]: ./media/web-sites-create-web-jobs/08SchdRecurEvery.png
  [Agendar em dias da semana]: ./media/web-sites-create-web-jobs/09SchdWeeksOnParticular.png
  [Agendar datas específicas no mês]: ./media/web-sites-create-web-jobs/10SchdMonthsOnPartDays.png
  [Agendar dias da semana específicos em um mês]: ./media/web-sites-create-web-jobs/11SchdMonthsOnPartWeekDays.png
  [Agendar dias da semana específicos em semanas específicas em um mês]: ./media/web-sites-create-web-jobs/12SchdMonthsOnPartWeekDaysOccurences.png
  [Lista de trabalhos]: ./media/web-sites-create-web-jobs/13WebJobsListWithSeveralJobs.png
  [Link para Agendador do Azure]: ./media/web-sites-create-web-jobs/31LinkToScheduler.png
  [Trabalho na página do portal do Agendador]: ./media/web-sites-create-web-jobs/32SchedulerPortal.png
  [Ação de trabalho PageInScheduler]: ./media/web-sites-create-web-jobs/33JobActionPageInScheduler.png
  [Link de logs]: ./media/web-sites-create-web-jobs/14WebJobLogs.png
  [WebJobDetails]: ./media/web-sites-create-web-jobs/15WebJobDetails.png
  [Detalhes da execução do trabalho Web]: ./media/web-sites-create-web-jobs/16WebJobRunDetails.png
  [Baixar saída do log]: ./media/web-sites-create-web-jobs/17DownloadLogOutput.png
  [Vincular à lista de trabalhos Web]: ./media/web-sites-create-web-jobs/18WebJobsLinkToDashboardList.png
  [Lista de trabalhos no painel de histórico]: ./media/web-sites-create-web-jobs/19WebJobsListInJobsDashboard.png
  [Introdução ao SDK dos Trabalhos Web do Microsoft Azure]: http://asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
  [Como implantar os Trabalhos Web do Azure em sites do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/websites-dotnet-deploy-webjobs
  [Implantando um aplicativo de console do .NET em Git no Azure usando Trabalhos Web]: http://blog.amitapple.com/post/73574681678/git-deploy-console-app
  [Como implantar o Trabalhos Web do Windows Azure]: http://blog.amitapple.com/post/74215124623/deploy-azure-webjobs
  [Trabalhos Web do Azure - Recursos Recomendados]: http://go.microsoft.com/fwlink/?LinkId=390226
  [Trabalhos Web do Azure 101 - Trabalhos Web básicos com Jaime Espinosa]: http://www.windowsazure.com/pt-br/documentation/videos/azure-webjobs-basics/
  [Trabalhos Web do Azure 102 - Trabalhos Web agendados e Painel dos Trabalhos Web com Jaime Espinosa]: http://www.windowsazure.com/pt-br/documentation/videos/azure-webjobs-schedule-and-dashboard/
  [Agendador do Azure 101 - Kevin Lam explica como programar coisas]: http://www.windowsazure.com/pt-br/documentation/videos/azure-scheduler-how-to/
  [Avaliação Gratuita do Microsoft Azure]: http://azure.microsoft.com/pt-br/pricing/free-trial/
