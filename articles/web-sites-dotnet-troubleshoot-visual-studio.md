<properties title="Troubleshooting Azure Websites in Visual Studio" pageTitle="Troubleshooting Azure Websites in Visual Studio" metaKeywords="troubleshoot debug azure web site tracing logging" description="Learn how to troubleshoot an Azure Website by using remote debugging, tracing, and logging tools that are built in to Visual Studio 2013." metaCanonical="" services="web-sites" documentationCenter=".NET" authors="tdykstra" manager="wpickett" solutions="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="tdykstra" />

# Solucionando problemas de Sites do Azure no Visual Studio.

Durante o desenvolvimento e o teste de um aplicativo Web, você pode solucionar problemas [em execução no modo de depuração][em execução no modo de depuração] ou usando o [IntelliTrace][IntelliTrace]. Você pode executar em modo de depuração localmente no IIS Express ou remotamente em um site do Azure. Mas para erros que ocorrem somente na produção, a melhor maneira de depurar pode ser examinando os logs criados pelo código do aplicativo ou pelo servidor Web. Este tutorial mostra como usar ferramentas do Visual Studio que ajudam a depurar um aplicativo enquanto ele é executado em um site do Azure, seja executando o modo de depuração remotamente ou exibindo logs do aplicativo e do servidor Web.

Você aprenderá a:

-   Quais funções de gerenciamento de site do Azure estão disponíveis no Visual Studio.
-   Como usar a exibição remota do Visual Studio para fazer alterações rápidas em um site remoto.
-   Como executar o modo de depuração remotamente enquanto um projeto está em execução em um site do Azure.
-   Como criar logs de rastreamento de aplicativos e exibi-los enquanto o aplicativo os está criando.
-   Como exibir logs do servidor Web, inclusive mensagens de erro detalhadas e rastreamento de solicitação com falha.
-   Como enviar logs de diagnóstico para uma conta de armazenamento do Azure e exibi-los nela.

### Segmentos do tutorial

1.  [Pré-requisitos][Pré-requisitos]
2.  [Gerenciamento e configuração de site][Gerenciamento e configuração de site]
3.  [Exibição remota][Exibição remota]
4.  [Depuração remota][Depuração remota]
5.  [Visão geral dos logs de diagnóstico][Visão geral dos logs de diagnóstico]
6.  [Criar e exibir logs de rastreamento de aplicativos][Criar e exibir logs de rastreamento de aplicativos]
7.  [Exibir logs de servidor Web][Exibir logs de servidor Web]
8.  [Exibir logs de mensagens de erro detalhadas][Exibir logs de mensagens de erro detalhadas]
9.  [Baixar logs do sistema de arquivos][Baixar logs do sistema de arquivos]
10. [Exibir logs de armazenamento][Exibir logs de armazenamento]
11. [Exibir logs de solicitações com falha][Exibir logs de solicitações com falha]
12. [Próximas etapas][Próximas etapas]

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial funciona com o ambiente de desenvolvimento, o projeto Web e o site do Azure que você configurou na [Introdução ao Azure e ao ASP.NET][Introdução ao Azure e ao ASP.NET]. Os exemplos de código mostrados neste tutorial são para um aplicativo Web MVC em C#, mas os procedimentos para solução de problemas são os mesmos para aplicativos em Visual Basic e Web Forms.

A depuração remota exige o Visual Studio 2013 ou o Visual Studio 2012 com a Atualização 4. Outros recursos mostrados no tutorial também funcionam no Visual Studio 2013 Express para Web e no Visual Studio 2012 Express para Web.

O recurso de logs de streaming funciona apenas para aplicativos que tenham como alvo o .NET Framework 4 ou posterior.

## <a name="sitemanagement"></a>Gerenciamento e configuração de site

O Visual Studio fornece acesso a um subconjunto das funções de gerenciamento e das definições de configuração disponíveis no portal de gerenciamento. Nesta seção, você verá o que está disponível.

1.  Se você ainda não entrou no Azure usando o Visual Studio, clique no botão **Conectar ao Azure** do **Gerenciador de Servidores**.

    Uma alternativa é instalar um certificado de gerenciamento que dê acesso à conta. O certificado de gerenciamento dá para o Gerenciador de Servidores acesso a serviços adicionais do Azure (Banco de Dados SQL e Serviços Móveis). Se você optar por instalar um certificado, clique com o botão direito do mouse no nó **Azure** do **Gerenciador de Servidores** e clique em **Gerenciar Assinaturas** no menu de contexto. Na caixa de diálogo **Gerenciar Assinaturas do Azure**, clique na guia **Certificados** e em **Importar**. Siga as instruções para baixar e importe um arquivo de assinatura (também chamado de arquivo *.publishsettings*) para a conta do Azure.

    > [WACOM.NOTE]
    > Se você baixar um arquivo de assinatura, salve-o em uma pasta fora dos diretórios de código-fonte (por exemplo, na pasta Downloads) e exclua-o após a conclusão da importação. Um usuário mal-intencionado que consiga acesso ao arquivo de assinatura pode editar, criar e excluir os serviços do Azure.

    Para obter mais informações sobre como se conectar aos recursos do Azure no Visual Studio, consulte [Gerenciar contas, assinaturas e funções administrativas][Gerenciar contas, assinaturas e funções administrativas].

2.  No **Gerenciador de Servidores**, expanda **Azure** e **Sites**.

3.  Clique com o botão direito do mouse no nó do site que você criou em [Introdução ao Azure e ao ASP.NET][Introdução ao Azure e ao ASP.NET] e clique em **Exibir Configurações**.

    ![Exibir Configurações no Gerenciador de Servidores][Exibir Configurações no Gerenciador de Servidores]

    A guia **Site do Azure** aparece e você pode ver as tarefas de configuração e gerenciamento de sites que estão disponíveis no Visual Studio.

    ![Janela Site do Azure][Janela Site do Azure]

    Neste tutorial, você usará os menus suspensos de log e rastreamento. Você também usará a depuração remota, mas utilizará um método diferente para habilitá-la.

    Para obter informações sobre as caixas Configurações do Aplicativo e Cadeias de Conexão nessa janela, consulte [Sites do Azure: Como as Cadeias de caracteres do aplicativo e as Cadeias de conexão funcionam][Sites do Azure: Como as Cadeias de caracteres do aplicativo e as Cadeias de conexão funcionam].

    Se você desejar executar uma tarefa de gerenciamento de site que não possa ser executada nessa janela, você pode clicar em **Configurações Completas de Sites** para abrir uma janela do navegador para o portal de gerenciamento. Para obter mais informações, consulte [Como configurar sites][Como configurar sites].

## <a name="remoteview"></a>Exibição remota

Você normalmente implanta um site com o sinalizador `customErrors` no arquivo Web.config definido como `On` ou `RemoteOnly`, o que significa que não recebe uma mensagem de erro útil quando algo dá errado. Para muitos erros, tudo o que você recebe é uma página como uma das páginas a seguir.

**Erro de servidor no aplicativo '/':**
![Página de erro inútil][Página de erro inútil]

**Ocorreu um erro:**
![Página de erro inútil][1]

**O site não pode exibir a página**
![Página de erro inútil][2]

Normalmente, a maneira mais fácil de encontrar a causa do erro é habilitar mensagens de erro detalhadas, que a primeira das capturas de tela anteriores explica como fazer. Isso exige uma alteração no arquivo Web.config implantado. Você pode editar o arquivo *Web.config* no projeto e reimplantar o projeto ou criar uma [transformação de Web.config][transformação de Web.config] e implantar uma compilação de depuração, mas há uma maneira mais rápida: no **Gerenciador de Soluções**, você pode exibir e editar diretamente arquivos no site remoto usando o recurso *exibição remota*.

1.  No **Gerenciador de Servidores**, expanda **Azure**, expanda **Sites** e expanda o nó do site em que você está implantando.

    Você verá nós que dão acesso aos arquivos de conteúdo e de log do site.

    ![Arquivo e arquivos de log][Arquivo e arquivos de log]

2.  Expanda o nó **Arquivos** e clique duas vezes no arquivo *Web.config*.

    ![Abrir Web.config][Abrir Web.config]

    O Visual Studio abre o arquivo Web.config do site remoto e mostra [Remoto] ao lado do nome do arquivo na barra de título.

3.  Adicione a seguinte linha ao elemento system.web`system.web`:

    `<customErrors mode="off"></customErrors>`

    ![Editar Web.config][Editar Web.config]

4.  Atualize o navegador que está mostrando a mensagem de erro inútil, e agora você recebe uma mensagem de erro detalhada, como o seguinte exemplo:

    ![Mensagem de erro detalhada][Mensagem de erro detalhada]

    (O erro mostrado foi criado adicionando-se a linha mostrada em vermelho a *Views\\Home\\Index.cshtml*.)

A edição do arquivo Web.config é apenas um exemplo dos cenários em que a capacidade de ler e editar arquivos no site do Azure facilita a solução de problemas.

## <a name="remotedebug"></a>Depuração Remota

Se a mensagem de erro detalhada não oferecer informações suficientes e não for possível recriar o erro localmente, outra maneira de solucionar o problema é executar no modo de depuração remotamente. Você pode definir os pontos de interrupção, manipular diretamente a memória, percorrer o código e até mesmo alterar o caminho do código.

A depuração remota não funciona em edições Express do Visual Studio.

Esta seção mostra como depurar remotamente usando o projeto criado por você cria em [Introdução ao Azure e ao ASP.NET][Introdução ao Azure e ao ASP.NET].

1.  Abra o projeto Web que você criou em [Introdução ao Azure e ao ASP.NET][Introdução ao Azure e ao ASP.NET].

2.  Abra o *Controllers\\HomeController.cs*.

3.  Exclua o método `About()` e insira o código a seguir em seu lugar.

        public ActionResult About()
        {
            string currentTime = DateTime.Now.ToLongTimeString();
            ViewBag.Message = "The current time is " + currentTime;
            return View();
        }

4.  [Defina um ponto de interrupção][em execução no modo de depuração] na linha `ViewBag.Message`.

5.  No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e clique em **Publicar**.

6.  Na lista suspensa **Perfil**, selecione o mesmo perfil que você usou em [Introdução ao Azure e ao ASP.NET][Introdução ao Azure e ao ASP.NET].

7.  Clique na guia **Configurações** e altere **Configuração** para **Depuração** e, em seguida, clique em **Publicar**.

    ![Publicar em modo de depuração][Publicar em modo de depuração]

8.  Quando a implantação for concluída e o navegador abrir a URL do Azure do site, feche o navegador.

9.  Para Visual Studio 2013: No **Gerenciador de Servidores**, expanda **Azure**, expanda **Sites**, clique com o botão direito do mouse no site e depois clique em **Anexar Depurador**.

    ![Anexar depurador][Anexar depurador]

    O navegador é aberto automaticamente na home page em execução no Azure. Convém aguardar cerca de 20 segundos enquanto o Azure configura o servidor para depuração. Esse atraso ocorre apenas na primeira vez que você executa em modo de depuração em um site. Nas próximas vezes, durante as próximas 48 horas, quando você iniciar a depuração novamente não haverá atraso.

10. Para o Visual Studio 2012 com Atualização 4:<span id="vs2012"></span></a>

    -   No Portal de Gerenciamento do Azure, vá até a guia **Configurar** do site e role para baixo até a seção **Diagnóstico do Site**.

    -   Defina a **Depuração Remota** como **Ativa** e defina a **Versão do Visual Studio para Depuração Remota** como **2012**.

    ![Configurar a depuração remota no portal de gerenciamento][Configurar a depuração remota no portal de gerenciamento]

    -   No menu **Depurar** do Visual Studio, clique em **Anexar ao Processo**.

    -   Na caixa **Qualificador**, digite a URL do site, sem o prefixo `http://`.

    -   Selecione **Mostrar processos de todos os usuários**.

    -   Quando for solicitado a informar as credenciais, digite o nome de usuário e a senha que tem permissões para publicar o site. Para obter essas credenciais, vá para a guia Painel de seu site no portal de gerenciamento e clique em **Baixar o perfil de publicação**. Abra o arquivo em um editor de texto e você encontrará o nome do usuário e a senha após as primeiras ocorrências de **userName =** e **userPWD =**.

    -   Quando os processos aparecerem na tabela **Processos Disponíveis**, selecione **w3wp.exe** e, em seguida, clique em **Anexar**.

    -   Abra um navegador para a URL do site.

    Convém aguardar cerca de 20 segundos enquanto o Azure configura o servidor para depuração. Esse atraso ocorre apenas na primeira vez que você executa em modo de depuração em um site. Nas próximas vezes, durante as próximas 48 horas, quando você iniciar a depuração novamente não haverá atraso.

11. Clique em **Sobre** no menu.

    O Visual Studio para no ponto de interrupção, e o código está em execução no Azure, e não no computador local.

12. Focalize a variável `currentTime` para ver o valor da hora.

    ![Exibir variável no modo de depuração em execução no Azure][Exibir variável no modo de depuração em execução no Azure]

    A hora que você vê é a hora do servidor do Azure, que pode estar em um fuso horário diferente do computador local.

13. Insira um novo valor para a variável `currentTime`, como "Agora em execução no Azure".

14. Pressione F5 para continuar a execução.

    A página Sobre em execução no Azure exibe o novo valor que você inseriu na variável currentTime.

    ![A página Sobre com o novo valor][A página Sobre com o novo valor]

### Observações sobre a depuração remota

-   A execução em modo de depuração em produção não é recomendada. Se o seu site de produção não estiver dimensionado para várias instâncias de servidor, a depuração interromperá o tráfego para sua única instância de servidor web. Se tiver várias instâncias de servidor web, quando você anexar o depurador você obterá uma instância aleatória e não há como garantir que suas solicitações de navegador irão para essa instância. Além disso, normalmente você não implanta uma compilação de depuração em produção e as otimizações do compilador para compilações de versão podem impossibilitar a exibição do que está acontecendo linha por linha em seu código-fonte. Para solucionar problemas de produção, os melhores recursos são o rastreamento do aplicativo e os logs do servidor Web.

-   Evite paradas longas em pontos de interrupção durante a depuração remota. O Azure trata um processo parado por mais de alguns minutos como um processo sem resposta e o desliga.

-   Enquanto você está depurando, o servidor está enviando dados para o Visual Studio, o que pode afetar os encargos de largura de banda. Para obter informações sobre as taxas de largura de banda, consulte [Preço do Azure][Preço do Azure].

-   Verifique se o atributo `debug` do elemento `compilation` no arquivo *Web.config* está definido como verdadeiro (true). Por padrão, ele é definido como true quando você publica uma configuração de compilação de depuração.

        <system.web>
          <compilation debug="true" targetFramework="4.5" />
          <httpRuntime targetFramework="4.5" />
        </system.web>

-   Se você descobrir que o depurador não entra no código que você deseja depurar, será preciso alterar a configuração de Just My Code. Para obter mais informações, consulte [Restringir a entrada em Just My Code][Restringir a entrada em Just My Code]

-   Um timer é iniciado no servidor quando você habilita o recurso de depuração remota e, após 48 horas, o recurso é desativado automaticamente. Esse limite de 48 horas é definido por razões de segurança e desempenho. Você pode reativá-lo facilmente quantas vezes quiser. É recomendável deixá-lo desabilitado quando você não está depurando ativamente.

-   Você pode anexar manualmente o depurador a qualquer processo. Você pode não apenas depurar o processo do site (w3wp.exe), mas também depurar outros processos como [WebJobs][WebJobs]. Para obter mais informações sobre como usar o modo de depuração no Visual Studio, consulte [Depurando no Visual Studio][Depurando no Visual Studio].

## <a name="logsoverview"></a><span class="short-header">Visão geral de logs</span>Visão geral de logs de diagnóstico

Um aplicativo em ASP.NET executado em um site do Azure pode criar os seguintes tipos de logs:

-   **Logs de rastreamento de aplicativos** <br />
    O aplicativo cria esses logs chamando métodos da classe [System.Diagnostics.Trace][System.Diagnostics.Trace].
-   **Logs de servidor da Web** <br />
     O servidor web cria uma entrada no log para cada solicitação HTTP ao site.
-   **Logs de mensagens de erro detalhadas** <br />
     O servidor Web cria uma página HTML com algumas informações adicionais para solicitações HTTP com falha (aquelas que resultam em um código de status 400 ou superior).
-   **Logs de rastreamento de solicitações com falha** <br />
     O servidor Web cria um arquivo XML com informações de rastreamento detalhadas para solicitações HTTP com falha. O servidor Web também fornece um arquivo XSL para formatar o XML em um navegador.

Como o registro em log afeta o desempenho do site, o Azure oferece a possibilidade de habilitar ou desabilitar cada tipo de log conforme necessário. Para logs de aplicativo, você pode especificar que apenas os logs acima de um determinado nível de gravidade devem ser gravados. Por padrão, quando você cria um novo site, todo o registro em log está desabilitado.

Os logs são gravados em arquivos em uma pasta *LogFiles* no sistema de arquivos do seu site e são acessíveis via FTP. Os logs de servidor Web e de aplicativo também podem ser gravados em uma conta de armazenamento do Azure. Você pode manter um volume maior de logs em uma conta de armazenamento do que o que é possível no sistema de arquivos. Você está limitado a um máximo de 100 megabytes de logs ao usar o sistema de arquivos. (Os logs do sistema de arquivos são apenas para retenção de curto prazo. O Azure exclui arquivos de log antigos para abrir espaço para novos quando o limite é atingido.)

## <a name="apptracelogs"></a><span class="short-header">Logs de aplicativos</span>Criar e exibir logs de rastreamento de aplicativos

Nesta seção, você executará as seguintes tarefas:

-   Adicione instruções de rastreamento ao projeto Web que você criou no [tutorial anterior][Introdução ao Azure e ao ASP.NET].
-   Exiba os logs ao executar o projeto localmente.
-   Exiba os logs conforme eles são gerados pelo aplicativo em execução no Azure.

### Adicionar instruções de rastreamento ao aplicativo

1.  Abra *Controllers\\HomeController.cs* e substitua o conteúdo do arquivo pelo seguinte código para adicionar instruções `Trace` e uma instrução `using` para `System.Diagnostics`:

        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.Diagnostics;
        using System.Linq;
        using System.Web;
        using System.Web.Configuration;
        using System.Web.Mvc;
        namespace MyExample.Controllers
        {
            public class HomeController : Controller
            {
                public ActionResult Index()
                {
                    Trace.WriteLine("Entering Index method");
                    ViewBag.Message = "Modify this template to jump-start your ASP.NET MVC application.";
                    Trace.TraceInformation("Displaying the Index page at " + DateTime.Now.ToLongTimeString());
                    Trace.WriteLine("Leaving Index method");
                    return View();
                }

                public ActionResult About()
                {
                    Trace.WriteLine("Entering About method");
                    ViewBag.Message = "Your app description page.";
                    Trace.TraceWarning("Transient error on the About page at " + DateTime.Now.ToShortTimeString());
                    Trace.WriteLine("Leaving About method");
                    return View();
                }

                public ActionResult Contact()
                {
                    Trace.WriteLine("Entering Contact method");
                    ViewBag.Message = "Your contact page.";
                    Trace.TraceError("Fatal error on the Contact page at " + DateTime.Now.ToLongTimeString());
                    Trace.WriteLine("Leaving Contact method");
                    return View();
                }
            }
        }

### Exibir a saída do rastreamento localmente

1.  Pressione F5 para executar o aplicativo em modo de depuração.

    O ouvinte de rastreamento padrão grava toda a saída de rastreamento na janela **Saída**, junto com as outras saídas de Depuração. A ilustração a seguir mostra a saída das instruções de rastreamento que você adicionou ao método `Index`.

    ![Rastreamento na janela Depuração][Rastreamento na janela Depuração]

    As etapas a seguir mostram como exibir a saída do rastreamento em uma página da web, sem compilação em modo de depuração.

2.  Abra o arquivo Web.config do aplicativo (localizado na pasta do projeto) e adicione um elemento `<system.diagnostics>` ao final do arquivo pouco antes do elemento de fechamento `</configuration>`:

        <system.diagnostics>
            <trace>
              <listeners>
                <add name="WebPageTraceListener"
                    type="System.Web.WebPageTraceListener, 
                    System.Web, 
                    Version=4.0.0.0, 
                    Culture=neutral,
                    PublicKeyToken=b03f5f7f11d50a3a" />
              </listeners>
            </trace>
          </system.diagnostics>

    O `WebPageTraceListener` permite exibir a saída do rastreamento navegando até `/trace.axd`.

3.  Adicione um [elemento trace][elemento trace] em `<system.web>` no arquivo Web.config, como no seguinte exemplo:

        <trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />

4.  Pressione CTRL+F5 para executar o aplicativo.

5.  Na barra de endereços da janela do navegador, adicione *trace.axd* à URL e pressione Enter (a URL será semelhante a <http://localhost:53370/trace.axd>).

6.  Na página **Rastreamento do Aplicativo**, clique em **Exibir Detalhes**.

    ![trace.axd][trace.axd]

    A página **Solicitar Detalhes** é exibida e, na seção **Informações de Rastreamento**, você vê a saída das instruções de rastreamento que adicionou ao método `Index`.

    ![trace.axd][3]

    Por padrão, `trace.axd` só está disponível localmente. Se quisesse disponibilizá-lo em um site remoto, você poderia adicionar `localOnly="false"` ao elemento `trace` no arquivo *Web.config*, conforme mostrado no seguinte exemplo:

        <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    No entanto, a habilitação de `trace.axd` em um site de produção não costuma ser recomendada por motivos de segurança e, nas seções a seguir, você verá uma maneira mais fácil de ler logs de rastreamento em um site do Azure.

### Exibir a saída de rastreamento no Azure

1.  No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto Web e clique em **Publicar**.

2.  Na caixa de diálogo **Publicar na Web**, clique em **Publicar**.

    Depois de publicar sua atualização, o Visual Studio abre uma janela do navegador em sua home page (supondo que você não limpou a **URL de destino** na guia **Conexão**).

3.  No **Gerenciador de Servidores**, clique com o botão direito do mouse no site e selecione **Exibir Logs de Streaming na Janela de Saída**.

    ![Exibir Logs de Streaming no menu de contexto][Exibir Logs de Streaming no menu de contexto]

    A janela **Saída** mostra que você está conectado ao serviço de streaming de log e adiciona uma linha de notificação a cada minuto que passa sem um log para exibir.

    ![Exibir Logs de Streaming no menu de contexto][4]

4.  Na janela do navegador que mostra a home page do aplicativo, clique em **Contato**.

    Em alguns segundos, a saída do rastreamento em nível de erro adicionado ao método `Contact` é exibida na janela **Saída**.

    ![Rastreamento de erro na janela Saída][Rastreamento de erro na janela Saída]

    O Visual Studio está mostrando apenas rastreamentos em nível de erro porque essa é a configuração padrão quando você habilita o serviço de monitoramento de log. Quando você cria um novo site do Azure, todo registro em log é desabilitado por padrão, como você viu quando abriu a página de configurações do site anteriormente:

    ![Log do aplicativo desativado][Log do aplicativo desativado]

    No entanto, quando você selecionou **Exibir Logs de Streaming na Janela Saída**, o Visual Studio alterou automaticamente **Log de Aplicativos (Sistema de Arquivos)** para **Erro**, o que significa que os logs em nível de erro são relatados. Para ver todos os logs de rastreamento, você pode alterar essa configuração para **Detalhado**. Quando você seleciona um nível de severidade inferior a erro, todos os logs para níveis de severidade mais altos são relatados. Portanto, quando selecionar detalhado, você também verá logs de informações, avisos e erros.

5.  No **Gerenciador de Servidores**, clique com o botão direito do mouse no site e clique em **Exibir Configurações**, como você fez anteriormente.

6.  Altere **Log de Aplicativos (Arquivos do Sistema)** para **Detalhado** e, em seguida, clique em **Salvar**.

    ![Configurando o rastreamento como Detalhado][Configurando o rastreamento como Detalhado]

7.  Na janela do navegador que está mostrando sua página **Contato**, clique em **Início**, em seguida, clique em <strong>Sobree</strong>, em seguida, em **Contato**.

    Em poucos segundos, a janela **Saída** janela mostra toda a saída de rastreamento.

    ![Saída de rastreamento detalhado][Saída de rastreamento detalhado]

    Nesta seção, você habilitou e desabilitou o registro em log usando as configurações de site do Azure. Você também pode habilitar e desabilitar os ouvintes de rastreamento modificando o arquivo Web.config. No entanto, a modificação do arquivo Web.config faz com que o domínio de aplicativo seja reciclado, enquanto a habilitação por meio do site não faz isso. Se o problema levar muito tempo para ser reproduzido, ou for intermitente, a reciclagem do domínio de aplicativo pode "corrigir" isso e forçar você a esperar até que o problema ocorra novamente. Como a habilitação do diagnóstico no Azure não faz isso, você não pode começar a capturar informações de erro imediatamente.

### Recursos da janela Saída

A guia **Logs do Azure** da janela **Saída** possui vários botões e uma caixa de texto:

![Botões da guia Logs][Botões da guia Logs]

Desempenham as seguintes funções:

-   Limpe a janela **Saída**.
-   Habilite ou desabilite a quebra automática de linha.
-   Inicie ou pare logs de monitoramento.
-   Especifique quais logs monitorar.
-   Baixe logs.
-   Filtre logs com base em uma cadeia de caracteres de pesquisa ou uma expressão regular.
-   Feche a janela **Saída**.

Se você inserir uma cadeia de caracteres de pesquisa ou uma expressão regular, o Visual Studio filtrará as informações de log no cliente. Isso significa que você pode inserir os critérios depois que os logs são exibidos na janela **Saída** e pode alterar os critérios de filtragem sem precisar gerar os logs novamente.

## <a name="webserverlogs"></a><span class="short-header">Logs do servidor Web</span>Exibir os logs do servidor Web

Os logs do servidor Web registram toda a atividade HTTP no site. Para vê-los na janela **Saída**, você precisa habilitá-los no site e informar ao Visual Studio que deseja monitorá-los.

1.  Na guia **Configuração de Site do Azure** que você abriu no **Gerenciador de Servidores**, altere o Log de Servidor Web para **Ativado** e clique em **Salvar**.

    ![Habilitar o log de servidor web][Habilitar o log de servidor web]

2.  Na janela **Saída**, clique no botão **Especificar quais logs do Azure monitorar**.

    ![Especificar quais logs do Azure monitorar][Especificar quais logs do Azure monitorar]

3.  Na caixa de diálogo **Opções de Registro em Log do Azure**, selecione **Logs do servidor Web** e clique em **OK**.

    ![Monitorar logs de servidor Web][Monitorar logs de servidor Web]

4.  Na janela do navegador que mostra o site, clique em **Início**, em **Sobre** e em **Contato**.

    Os logs de aplicativo geralmente aparecem primeiro, seguidos dos logs de servidor web. Talvez seja necessário esperar algum tempo para que os logs sejam exibidos.

    ![Logs de servidor Web na janela Saída][Logs de servidor Web na janela Saída]

Por padrão, quando você habilita os logs do servidor Web pela primeira vez usando o Visual Studio, o Azure grava os logs no sistema de arquivos. Como alternativa, você pode usar o portal de gerenciamento para especificar que os logs de servidor Web devem ser gravados em um contêiner de blob em uma conta de armazenamento. Para obter mais informações, consulte a seção **diagnóstico de site** em [Como configurar sites][Como configurar sites].

Se você usar o portal de gerenciamento para habilitar o registro em log do servidor Web para uma conta de armazenamento do Azure e desabilitar o registro em log no Visual Studio, quando reabilitar o registro em log no Visual Studio, as configurações da conta de armazenamento serão restauradas.

## <a name="detailederrorlogs"></a><span class="short-header">Logs de erro</span>Exibir logs detalhados de mensagens de erro

Os logs detalhados de erro fornecem algumas informações adicionais sobre solicitações HTTP que resultam em códigos de resposta de erro (400 ou acima). Para vê-los na janela **Saída**, você precisa habilitá-los no site e informar ao Visual Studio que deseja monitorá-los.

1.  Na guia **Configuração de Site do Azure** que você abriu no **Gerenciador de Servidores**, altere **Mensagens de Erro Detalhadas** para **Ativado**, depois clique em **Salvar**.

    ![Habilitar mensagens de erro detalhadas][Habilitar mensagens de erro detalhadas]

2.  Na janela **Saída**, clique no botão **Especificar quais logs do Azure monitorar**.

3.  Na caixa de diálogo **Opções de Registro em Log do Azure**, clique em **Todos os logs** e em **OK**.

    ![Monitorar todos os logs][Monitorar todos os logs]

4.  Na barra de endereços da janela do navegador, adicione um caractere extra à URL para provocar um erro 404 (por exemplo, `http://localhost:53370/Home/Contactx`) e pressione Enter.

    Após vários segundos o log detalhado de erros é exibido na janela **Saída** do Visual Studio.

    ![Log detalhado de erros na janela Saída][Log detalhado de erros na janela Saída]

    Clique no link mantendo a tecla Control pressionada para ver a saída do log formatada em um navegador:

    ![Log detalhado de erros na janela do navegador][Log detalhado de erros na janela do navegador]

## <a name="downloadlogs"></a><span class="short-header">Baixar logs</span>Baixar logs do sistema de arquivos

Todos os logs que podem ser monitorados na janela **Saída** também podem ser baixados como um arquivo *. zip*.

1.  Na janela **Saída**, clique em **Baixar Logs de Streaming**.

    ![Botões da guia Logs][5]

    O Explorador de Arquivos abre em sua pasta *Downloads* com o arquivo baixado selecionado.

    ![Arquivo baixado][Arquivo baixado]

2.  Extraia o arquivo *. zip* e você verá a seguinte estrutura de pastas:

    ![Arquivo baixado][6]

    -   Os logs de rastreamento de aplicativos estão em arquivos *.txt* na pasta *LogFiles\\Application*.
    -   Os logs de servidor Web estão em arquivos *.log* na pasta *LogFiles\\http\\RawLogs*. Você pode usar uma ferramenta como o [Analisador de log (a página pode estar em inglês)][Analisador de log (a página pode estar em inglês)] para exibir e manipular esses arquivos.
    -   Os logs de mensagens de erro detalhadas estão em arquivos *.html* na pasta *LogFiles\\DetailedErrors*.

    (A pasta *deployments* é para os arquivos criados pela publicação de controle do código-fonte; ela não tem nada relacionado à publicação do Visual Studio. A pasta *Git* é para rastreamentos relacionados à publicação de código-fonte e o serviço de streaming de arquivos de log.)

## <a name="storagelogs"></a><span class="short-header">Logs de armazenamento</span>Exibir logs de armazenamento

Os logs de rastreamento de aplicativos também podem ser enviados para uma conta de armazenamento do Azure, e você pode exibi-los no Visual Studio. Para fazer isso, você criará uma conta de armazenamento, habilitará os logs de armazenamento no portal de gerenciamento e os exibirá na guia **Logs** da janela **Site do Azure**.

Você pode enviar logs para qualquer um ou todos os três destinos:

-   O sistema de arquivos.
-   Tabelas de conta de armazenamento.
-   Blobs de conta de armazenamento.

Você pode especificar um nível de gravidade diferente para cada destino.

Tabelas facilitam a exibição de detalhes de logs online e dão suporte ao streaming; você pode consultar logs em tabelas e ver novos logs à medida que são criados. Blobs facilitam o download de logs em arquivos e a análise deles usando HDInsight, porque HDInsight sabe como trabalhar com o armazenamento de blob. Para obter mais informações, consulte **Hadoop e MapReduce** em [Opções de armazenamento de dados (Compilando aplicativos de nuvem do mundo real com o Azure)][Opções de armazenamento de dados (Compilando aplicativos de nuvem do mundo real com o Azure)].

Você tem logs de sistema de arquivos definidos como nível detalhado no momento; as etapas a seguir orientam você em meio à configuração dos logs de nível de informações para ir até tabelas de conta de armazenamento. O nível de informações significa que todos os logs criados chamando `Trace.TraceInformation``Trace.TraceWarning` e `Trace.TraceError` serão exibidos, mas não os logs criados chamando `Trace.WriteLine`.

As contas de armazenamento oferecem mais armazenamento e retenção por mais tempo para logs em comparação com o sistema de arquivos. Outra vantagem do envio dos logs de rastreamento de aplicativos para o armazenamento é que você obtém informações adicionais com cada log que não obtém dos logs do sistema de arquivos.

1.  No **Gerenciador de Servidores**, clique com o botão direito do mouse no site e clique em **Abrir no Portal de Gerenciamento**.

2.  No Portal de Gerenciamento, clique na guia **Armazenamento** e, em seguida, clique em **Criar uma Conta de Armazenamento**.

    ![Criar uma conta de armazenamento][Criar uma conta de armazenamento]

3.  Insira uma URL exclusiva a ser usada para a conta de armazenamento e, em seguida, clique em **Criar Conta de Armazenamento**.

    ![Digitar uma URL][Digitar uma URL]

4.  Na janela **Site do Azure** do Visual Studio, clique na guia **Logs** e depois em **Configurar Registro em Log**.

    ![Arquivo baixado][7]

    Isso abre a guia **Configurar** no portal de gerenciamento do seu site. Outra maneira de fazer isso é clicar na guia **Sites**, clicar em seu site e, em seguida, na guia **Configurar**.

5.  Na guia **Configurar** do portal de gerenciamento, role até a seção de diagnóstico do aplicativo e altere o **Registro em Log do Aplicativo (Armazenamento)** para **Ativado**.

6.  Altere o **Nível de Log** para **Informações**.

7.  Clique em **Gerenciar Armazenamento da Tabela**.

    ![Clicar em Gerenciar Armazenamento da Tabela][Clicar em Gerenciar Armazenamento da Tabela]

    Na caixa de diálogo **Gerenciar armazenamento da tabela para diagnóstico do aplicativo**, você poderá escolher a conta de armazenamento, se tiver mais de uma. Você pode criar uma nova tabela ou usar uma existente.

    ![Gerenciar armazenamento da tabela][Gerenciar armazenamento da tabela]

8.  Na caixa **Gerenciar armazenamento da tabela para diagnóstico de aplicativo**, clique na marca de seleção para fechar a caixa.

9.  Na guia **Configurar** do portal de gerenciamento, clique em **Salvar**.

10. Na janela do navegador que exibe o site do aplicativo, clique em **Início**, em **Sobre** e em **Contato**.

    As informações de log produzidas pela navegação nessas páginas da Web serão gravadas na conta de armazenamento.

11. Na guia **Logs** da janela **Site do Azure** no Visual Studio, clique em **Atualizar** sob **Resumo do Diagnóstico**.

    ![Clicar em Atualizar][Clicar em Atualizar]

    Por padrão, a seção **Resumo do Diagnóstico** mostra logs dos últimos 15 minutos. Você pode alterar o período para ver mais logs.

    (Se você receber um erro "tabela não encontrada", verifique se navegou até as páginas que fazem o rastreamento depois de habilitar **Registro em Log do Aplicativo (Armazenamento)** e clicar em **Salvar**.)

    ![Logs de armazenamento][Logs de armazenamento]

    Observe que nessa exibição você vê a **ID do Processo** e a **ID do thread** de cada log, o que você não obtém nos logs do sistema de arquivos. Você pode ver campos adicionais exibindo a tabela de armazenamento do Azure diretamente.

12. Clique em **exibir todos os logs de aplicativo**.

    A tabela do log de rastreamento é exibida no visualizador da tabela de armazenamento do Azure.

    (Se você receber um erro "a sequência não contém elementos", abra o **Gerenciador de Servidores**, expanda o nó da conta de armazenamento sob o nó **Azure**, clique com o botão direito do mouse em **Tabelas** e clique em **Atualizar**.)

    ![Tabela de rastreamento no Gerenciador de Servidores][Tabela de rastreamento no Gerenciador de Servidores]

    ![Logs de armazenamento na exibição de tabela][Logs de armazenamento na exibição de tabela]

    Esta exibição mostra campos adicionais que você não vê em outros modos de exibição. Esse modo de exibição permite filtrar logs usando a interface do usuário especial do Construtor de Consultas para construir uma consulta. Para obter mais informações, consulte Trabalhando com recursos de tabela - Filtrando entidades em [Procurando recursos de armazenamento com o Gerenciador de Servidores (a página pode estar em inglês)][Procurando recursos de armazenamento com o Gerenciador de Servidores (a página pode estar em inglês)]

13. Para ver os detalhes de uma única linha, clique com o botão direito do mouse em uma das linhas e, em seguida, clique em **Editar**.

    ![Tabela de rastreamento no Gerenciador de Servidores][8]

## <a name="failedrequestlogs"></a><span class="short-header">Logs de solicitação com falha</span>Exibir logs de rastreamento de solicitação com falha

Os logs de rastreamento de solicitação com falha são úteis quando você precisa entender os detalhes de como o IIS trata uma solicitação HTTP, em cenários como problemas de autenticação ou regravação de URL.

Os sites do Azure usam a mesma funcionalidade de rastreamento de solicitação com falha disponível no IIS 7.0 e em versões posteriores. No entanto, você não tem acesso às configurações do IIS que configuram quais erros são registrados em log. Quando você habilita o rastreamento de solicitação com falha, todos os erros são capturados.

Você pode habilitar o rastreamento de solicitação com falha usando o Visual Studio, mas não pode exibi-lo no Visual Studio. Esses logs são arquivos XML. O serviço de log de streaming monitora apenas arquivos considerados legíveis em modo de texto sem formatação: arquivos *.txt*, *.html* e *.log*.

Você pode exibir os logs de rastreamento de solicitação com falha em um navegador diretamente por meio de FTP ou localmente depois de usar uma ferramenta FTP para baixá-los em seu computador local. Nesta seção você irá exibi-los diretamente em um navegador.

1.  Na guia **Configuração** da janela **Site do Azure** que você abriu no **Gerenciador de Servidores**, altere **Rastreamento de Solicitações com Falha** para **Ativado** e clique em **Salvar**.

    ![Habilitar o rastreamento de solicitações com falha][Habilitar o rastreamento de solicitações com falha]

2.  Na barra de endereços da janela do navegador que mostra o site, adicione um caractere extra à URL e clique em Enter para provocar um erro 404.

    Isso faz com que um log de rastreamento de solicitação com falha seja criado e as etapas seguintes mostram como exibir ou baixar o log.

3.  No Visual Studio, na guia **Configuração** da janela **Site do Azure**, clique em **Abrir no Portal de Gerenciamento**.

4.  No portal de gerenciamento, clique em **Painel**e, em seguida, clique em **Redefinir as credenciais de implantação** na seção **Visão Rápida**.

    ![Redefinir o link de credenciais FTP no Painel][Redefinir o link de credenciais FTP no Painel]

5.  Digite um novo nome de usuário e uma senha.

    ![Novo nome de usuário e senha FTP][Novo nome de usuário e senha FTP]

6.  Na guia **Painel** do portal de gerenciamento, pressione F5 para atualizar a página e, em seguida, role para baixo até onde você pode ver **Implantação/Usuário FTP**. Observe que o nome do usuário tem o nome do site como prefixo. **Quando você faz logon, você precisa usar esse nome de usuário completo com o nome do site como prefixo, conforme mostrado aqui.**

7.  Em uma nova janela do navegador, vá até a URL mostrada em **Nome do Host FTP** na guia **Painel** da página do portal de gerenciamento do site. O **Nome do Host FTP** está localizado próximo a **Implantação/Usuário FTP** na seção **Visão Rápida**.

8.  Faça logon usando as credenciais FTP que você criou anteriormente (incluindo o prefixo do nome do site do nome do usuário).

    O navegador mostra a pasta raiz do site.

9.  Abra a pasta *LogFiles*.

    ![Abrir a pasta LogFiles][Abrir a pasta LogFiles]

10. Abra a pasta chamada W3SVC mais um valor numérico.

    ![Abrir a pasta W3SVC][Abrir a pasta W3SVC]

    A pasta contém arquivos XML para todos os erros que foram registrados em log depois que você habilitou o rastreamento de solicitação com falha e um arquivo XSL que um navegador pode usar para formatar o XML.

    ![Pasta W3SVC][Pasta W3SVC]

11. Clique no arquivo XML da solicitação com falha da qual você deseja ver as informações de rastreamento.

    A ilustração a seguir mostra parte das informações de rastreamento de um erro de amostra.

    ![Rastreamento de solicitação com falha no navegador][Rastreamento de solicitação com falha no navegador]

## <a name="nextsteps"></a><span class="short-header">Próximas etapas</span>Próximas etapas

Você viu como o Visual Studio facilita a exibição de logs criados por um site do Azure. Convém saber mais sobre como solucionar problemas em sites do Azure, rastrear aplicativos em ASP.NET e analisar logs de servidor Web.

### Solução de problemas em Site do Azure

Para obter mais informações como solucionar problemas de WAWS (sites do Azure), consulte os seguintes recursos:

-   [Solucionando problemas no Azure][Solucionando problemas no Azure] <br />
     Um white paper introdutório básico que inclui uma breve seção sobre WAWS.
-   [Solucionando problemas de um site][Solucionando problemas de um site] <br />
     Uma introdução que trata de WAWS.
-   [Habilitar registro em log do diagnóstico para sites do Azure][Habilitar registro em log do diagnóstico para sites do Azure] <br />
     Cobre grande parte das mesmas informações fornecidas por este tutorial, mas se concentra em como obter logs de diagnóstico sem usar o Visual Studio.
-   [Como monitorar Sites][Como monitorar Sites] <br />
     A seção [Configurar diagnósticos e baixar logs][Configurar diagnósticos e baixar logs] tem informações valiosas não incluídas nos documentos de solução de problemas.
-   [Investigação de vazamentos de memória em sites do Azure Visual Studio 2013][Investigação de vazamentos de memória em sites do Azure Visual Studio 2013]. <br />
    Postagem no blog do Microsoft ALM sobre os recursos do Visual Studio para análise de problemas de memória gerenciados.
-   [Ferramentas online dos Sites do Windows Azure que você precisa conhecer][Ferramentas online dos Sites do Windows Azure que você precisa conhecer]. <br />Postagem de blog de Amit Apple.

Para obter ajuda com uma pergunta específica de solução de problemas, inicie um thread em um dos seguintes fóruns:

-   [O fórum do Azure no site do ASP.NET][O fórum do Azure no site do ASP.NET].
-   [O fórum do Azure no MSDN][O fórum do Azure no MSDN].
-   [StackOverflow.com][StackOverflow.com].

### Depuração no Visual Studio

Para obter mais informações sobre como usar o modo de depuração no Visual Studio, consulte o tópico do MSDN [Depurando no Visual Studio][Depurando no Visual Studio] e [Dicas de depuração no Visual Studio 2010][Dicas de depuração no Visual Studio 2010].

Para obter mais informações sobre depuração remota para sites do Azure, consulte os seguintes recursos:

-   [Introdução à depuração remota em Sites do Azure][Introdução à depuração remota em Sites do Azure].
-   [Introdução à depuração remota em Sites do Azure parte 2 - Por dentro da depuração remota][Introdução à depuração remota em Sites do Azure parte 2 - Por dentro da depuração remota]
-   [Introdução à depuração remota em Sites do Azure parte 3 - Ambiente de várias instâncias e GIT][Introdução à depuração remota em Sites do Azure parte 3 - Ambiente de várias instâncias e GIT]

Se o site usar uma API Web do Azure ou um back-end de Serviços Móveis e você precisar depurá-lo, consulte [Depurando Back-end do .NET no Visual Studio][Depurando Back-end do .NET no Visual Studio].

### Rastreando em aplicativos ASP.NET

Não existem introduções completas e atualizadas para rastreamento do ASP.NET disponíveis na Internet. O melhor a fazer é começar com materiais introdutórios antigos escritos para formulários da Web porque o MVC ainda não existia e complementar isso com postagens em blog mais recentes focalizadas em problemas específicas. Alguns bons locais para começar são os seguintes recursos:

-   [Monitoramento e telemetria (Compilando aplicativos de nuvem do mundo real com o Azure)][Monitoramento e telemetria (Compilando aplicativos de nuvem do mundo real com o Azure)].<br />
     Capítulo do livro eletrônico com recomendações para rastreamento em aplicativos de nuvem do Azure.
-   [Rastreamento do ASP.NET][Rastreamento do ASP.NET]<br />
     Antigo, mas ainda é um bom recurso para obter uma introdução básica sobre o assunto.
-   [Ouvintes de rastreamento][Ouvintes de rastreamento]<br />
     Informações sobre ouvintes de rastreamento, mas não menciona o [WebPageTraceListener][WebPageTraceListener].
-   [Passo a passo: Integrando o rastreamento do ASP.NET ao Rastreamento do System.Diagnostics][Passo a passo: Integrando o rastreamento do ASP.NET ao Rastreamento do System.Diagnostics]<br />
     Isso também é antigo, mas inclui algumas informações adicionais que o artigo introdutório não cobre.
-   [Rastreamento em exibições do Razor no ASP.NET MVC][Rastreamento em exibições do Razor no ASP.NET MVC]<br />
     Além do rastreamento em exibições do Razor, a postagem também explica como criar um filtro de erros para registrar em log todas as exceções não tratadas em um aplicativo MVC. Para obter informações sobre como registrar em log exceções não tratadas em um aplicativo de formulários da Web, consulte o exemplo do Global.asax em [Concluir exemplo para manipuladores de erro][Concluir exemplo para manipuladores de erro] no MSDN. No MVC ou em Formulários da Web, para registrar certas exceções em log, mas deixar o tratamento da estrutura padrão cuidar deles, você poderá capturar e relançar como no exemplo a seguir:

        try
        {
           // Your code that might cause an exception to be thrown.
        }
        catch (Exception ex)
        {
            Trace.TraceError("Exception: " + ex.ToString());
            throw;
        } 

-   [Log de rastreamento do diagnóstico de streaming na linha de comando do Azure (mais Glimpse!) (a página pode estar em inglês)][Log de rastreamento do diagnóstico de streaming na linha de comando do Azure (mais Glimpse!) (a página pode estar em inglês)]
    Como usar a linha de comando para fazer o que este tutorial mostra no Visual Studio. [Glimpse (a página pode estar em inglês)][Glimpse (a página pode estar em inglês)] é uma ferramenta para depuração de aplicativos ASP.NET.
-   [Usando o registro em log de site do Azure e diagnóstico com David Ebbo][Usando o registro em log de site do Azure e diagnóstico com David Ebbo] e [Logs de streaming de sites do Azure com David Ebbo][Logs de streaming de sites do Azure com David Ebbo]
     Vídeos de Scott Hanselman e David Ebbo.

Para o log de erros, uma alternativa para escrever seu próprio código de rastreamento é usar uma estrutura de software livre, como o [ELMAH (a página pode estar em inglês)][ELMAH (a página pode estar em inglês)] Para obter mais informações, consulte as [postagens no blog de Scott Hanselman sobre o ELMAH][postagens no blog de Scott Hanselman sobre o ELMAH]

Além disso, observe que você não precisa usar o rastreamento do ASP.NET ou do System.Diagnostics se quiser obter logs de streaming do Azure. O serviço de log de streaming de site do Azure transmitirá qualquer arquivo *.txt*, *.HTML* ou *.log* que encontrar na pasta *LogFiles*. Portanto, você pode criar seu próprio sistema de log que grava no sistema de arquivos do site, e o arquivo será automaticamente transmitido e baixado. Tudo o que você precisa fazer é escrever o código do aplicativo que cria arquivos na pasta *d:\\home\\logfiles*.

### Analisando logs de servidor Web

Para obter mais informações sobre como analisar logs de servidor web, consulte os seguintes recursos:

-   [LogParser][LogParser]<br />
     Uma ferramenta para exibir dados em logs de servidor web (arquivos *.log*).
-   [Solucionando problemas de desempenho do IIS ou erros de aplicativo usando o LogParser][Solucionando problemas de desempenho do IIS ou erros de aplicativo usando o LogParser]<br />
     Uma introdução à ferramenta LogParser que pode ser usada para analisar logs de servidor web.
-   [Postagens no blog por Robert McMurray sobre como usar o LogParser][Postagens no blog por Robert McMurray sobre como usar o LogParser]<br />
-   [O código de status HTTP no IIS 7.0, IIS 7.5 e IIS 8.0][O código de status HTTP no IIS 7.0, IIS 7.5 e IIS 8.0]

### Analisando logs de rastreamento de solicitação com falha

O site do Microsoft TechNet inclui uma seção [Usando rastreamento de solicitação com falha][Usando rastreamento de solicitação com falha] que pode ser útil para entender como usar esses logs. No entanto, essa documentação se concentra principalmente na configuração do rastreamento de solicitação com falha no IIS, o que não pode ser feito em sites do Azure.

### Depurando serviços de nuvem

Se você quiser depurar um serviço de nuvem do Azure, em vez de um site, consulte [Depurando serviços de nuvem][Depurando serviços de nuvem].

  [em execução no modo de depuração]: http://www.visualstudio.com/pt-br/get-started/debug-your-app-vs.aspx
  [IntelliTrace]: http://msdn.microsoft.com/library/vstudio/dd264915.aspx
  [Pré-requisitos]: #prerequisites
  [Gerenciamento e configuração de site]: #sitemanagement
  [Exibição remota]: #remoteview
  [Depuração remota]: #remotedebug
  [Visão geral dos logs de diagnóstico]: #logsoverview
  [Criar e exibir logs de rastreamento de aplicativos]: #apptracelogs
  [Exibir logs de servidor Web]: #webserverlogs
  [Exibir logs de mensagens de erro detalhadas]: #detailederrorlogs
  [Baixar logs do sistema de arquivos]: #downloadlogs
  [Exibir logs de armazenamento]: #storagelogs
  [Exibir logs de solicitações com falha]: #failedrequestlogs
  [Próximas etapas]: #nextsteps
  [Introdução ao Azure e ao ASP.NET]: /pt-br/develop/net/tutorials/get-started/
  [Gerenciar contas, assinaturas e funções administrativas]: http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert
  [Exibir Configurações no Gerenciador de Servidores]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png
  [Janela Site do Azure]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png
  [Sites do Azure: Como as Cadeias de caracteres do aplicativo e as Cadeias de conexão funcionam]: http://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx
  [Como configurar sites]: /pt-br/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig
  [Página de erro inútil]: ./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png
  [1]: ./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png
  [2]: ./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png
  [transformação de Web.config]: http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations
  [Arquivo e arquivos de log]: ./media/web-sites-dotnet-troubleshoot-visual-studio/fileandlogfiles.png
  [Abrir Web.config]: ./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png
  [Editar Web.config]: ./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png
  [Mensagem de erro detalhada]: ./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png
  [Publicar em modo de depuração]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png
  [Anexar depurador]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png
  [Configurar a depuração remota no portal de gerenciamento]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debuginportal.png
  [Exibir variável no modo de depuração em execução no Azure]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png
  [A página Sobre com o novo valor]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png
  [Preço do Azure]: /pt-br/pricing/calculator/
  [Restringir a entrada em Just My Code]: http://msdn.microsoft.com/pt-br/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code
  [WebJobs]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
  [Depurando no Visual Studio]: http://msdn.microsoft.com/pt-br/library/vstudio/sc65sadd.aspx
  [System.Diagnostics.Trace]: http://msdn.microsoft.com/pt-br/library/system.diagnostics.trace.aspx
  [Rastreamento na janela Depuração]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png
  [elemento trace]: http://msdn.microsoft.com/pt-br/library/vstudio/6915t83k(v=vs.100).aspx
  [trace.axd]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png
  [3]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png
  [Exibir Logs de Streaming no menu de contexto]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png
  [4]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png
  [Rastreamento de erro na janela Saída]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png
  [Log do aplicativo desativado]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png
  [Configurando o rastreamento como Detalhado]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png
  [Saída de rastreamento detalhado]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png
  [Botões da guia Logs]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png
  [Habilitar o log de servidor web]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png
  [Especificar quais logs do Azure monitorar]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png
  [Monitorar logs de servidor Web]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png
  [Logs de servidor Web na janela Saída]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png
  [Habilitar mensagens de erro detalhadas]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png
  [Monitorar todos os logs]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png
  [Log detalhado de erros na janela Saída]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png
  [Log detalhado de erros na janela do navegador]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png
  [5]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png
  [Arquivo baixado]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png
  [6]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png
  [Criar uma conta de armazenamento]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-createstorage.png
  [Digitar uma URL]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storageurl.png
  [7]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png
  [Clicar em Gerenciar Armazenamento da Tabela]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png
  [Gerenciar armazenamento da tabela]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png
  [Clicar em Atualizar]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png
  [Logs de armazenamento]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png
  [Tabela de rastreamento no Gerenciador de Servidores]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetableinse.png
  [Logs de armazenamento na exibição de tabela]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png
  [8]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png
  [Habilitar o rastreamento de solicitações com falha]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png
  [Redefinir o link de credenciais FTP no Painel]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-resetftpcredentials.png
  [Novo nome de usuário e senha FTP]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png
  [Abrir a pasta LogFiles]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png
  [Abrir a pasta W3SVC]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png
  [Pasta W3SVC]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png
  [Rastreamento de solicitação com falha no navegador]: ./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png
  [Solucionando problemas no Azure]: /pt-br/develop/net/best-practices/troubleshooting/
  [Solucionando problemas de um site]: /pt-br/develop/net/best-practices/troubleshooting-web-sites/
  [Habilitar registro em log do diagnóstico para sites do Azure]: /pt-br/develop/net/common-tasks/diagnostics-logging-and-instrumentation/
  [Como monitorar Sites]: /pt-br/manage/services/web-sites/how-to-monitor-websites/
  [Configurar diagnósticos e baixar logs]: /pt-br/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics
  [Investigação de vazamentos de memória em sites do Azure Visual Studio 2013]: http://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx
  [Ferramentas online dos Sites do Windows Azure que você precisa conhecer]: /blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/
  [O fórum do Azure no site do ASP.NET]: http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET
  [O fórum do Azure no MSDN]: http://social.msdn.microsoft.com/Forums/windowsazure/
  [StackOverflow.com]: http://www.stackoverflow.com
  [Dicas de depuração no Visual Studio 2010]: http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx
  [Introdução à depuração remota em Sites do Azure]: /blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/
  [Introdução à depuração remota em Sites do Azure parte 2 - Por dentro da depuração remota]: /blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/
  [Introdução à depuração remota em Sites do Azure parte 3 - Ambiente de várias instâncias e GIT]: /blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/
  [Depurando Back-end do .NET no Visual Studio]: http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx
  [Rastreamento do ASP.NET]: http://msdn.microsoft.com/pt-br/library/ms972204.aspx
  [Ouvintes de rastreamento]: http://msdn.microsoft.com/pt-br/library/4y5y10s7.aspx
  [WebPageTraceListener]: http://msdn.microsoft.com/pt-br/library/system.web.webpagetracelistener.aspx
  [Passo a passo: Integrando o rastreamento do ASP.NET ao Rastreamento do System.Diagnostics]: http://msdn.microsoft.com/pt-br/library/b0ectfxd.aspx
  [Rastreamento em exibições do Razor no ASP.NET MVC]: http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx
  [Concluir exemplo para manipuladores de erro]: http://msdn.microsoft.com/pt-br/library/bb397417.aspx
  [Usando o registro em log de site do Azure e diagnóstico com David Ebbo]: http://www.windowsazure.com/pt-br/documentation/videos/azure-web-site-logging-and-diagnostics/
  [Logs de streaming de sites do Azure com David Ebbo]: http://www.windowsazure.com/pt-br/documentation/videos/log-streaming-with-azure-web-sites/
  [postagens no blog de Scott Hanselman sobre o ELMAH]: http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx
  [LogParser]: http://www.microsoft.com/pt-br/download/details.aspx?id=24659
  [Solucionando problemas de desempenho do IIS ou erros de aplicativo usando o LogParser]: http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser
  [Postagens no blog por Robert McMurray sobre como usar o LogParser]: http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/
  [O código de status HTTP no IIS 7.0, IIS 7.5 e IIS 8.0]: http://support.microsoft.com/kb/943891
  [Usando rastreamento de solicitação com falha]: http://www.iis.net/learn/troubleshoot/using-failed-request-tracing
  [Depurando serviços de nuvem]: http://msdnstage.redmond.corp.microsoft.com/pt-br/library/windowsazure/ee405479.aspx
