<properties title="Troubleshooting Azure Websites in Visual Studio" pageTitle="Solucionando problemas em Sites do Azure no Visual Studio" metaKeywords="solucionar problemas de depuração de log de rastreamento de Site do Azure" description="Learn how to troubleshoot an Azure Website by using remote debugging, tracing, and logging tools that are built in to Visual Studio 2013." metaCanonical="" services="web-sites" documentationCenter=".NET" authors="tdykstra" manager="wpickett" solutions="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/13/2014" ms.author="tdykstra" />

# Solucionando problemas de Sites do Azure no Visual Studio.

Este tutorial mostra como usar ferramentas do Visual Studio que ajudam a depurar um aplicativo enquanto ele é executado em um Site do Azure, seja executando o [modo de depuração](http://www.visualstudio.com/pt-br/get-started/debug-your-app-vs.aspx) remotamente ou exibindo logs do aplicativo e do servidor Web.

Você aprenderá a:

* Quais funções de gerenciamento de site do Azure estão disponíveis no Visual Studio.
* Como usar a exibição remota do Visual Studio para fazer alterações rápidas em um site remoto.
* Como executar o modo de depuração remotamente enquanto um projeto está em execução no Azure.
* Como criar logs de rastreamento de aplicativos e exibi-los enquanto o aplicativo os está criando.
* Como exibir logs do servidor Web, inclusive mensagens de erro detalhadas e rastreamento de solicitação com falha.
* Como enviar logs de diagnóstico para uma conta de armazenamento do Azure e exibi-los nela.

Se você tiver o Visual Studio Ultimate, também é possível utilizar o [IntelliTrace](http://msdn.microsoft.com/library/vstudio/dd264915.aspx) para depuração. O IntelliTrace não é abordado neste tutorial.

### Segmentos do tutorial

- [Pré-requisitos](#prerequisites)
- [Gerenciamento e configuração de site](#sitemanagement)
- [Acessar arquivos do site no Gerenciador de Servidores](#remoteview)
- [Depuração remota](#remotedebug)
	- Sites de depuração remota
	- Trabalhos Web de depuração remota
	- Observações sobre a depuração remota 
- [Visão geral dos logs de diagnóstico](#logsoverview)
- [Criar e exibir logs de rastreamento de aplicativos](#apptracelogs)
- [Exibir logs de servidor Web](#webserverlogs)
- [Exibir logs de mensagens de erro detalhadas](#detailederrorlogs)
- [Baixar logs do sistema de arquivos](#downloadlogs)
- [Exibir logs de armazenamento](#storagelogs)
- [Exibir logs de solicitações com falha](#failedrequestlogs)
- [Próximas etapas](#nextsteps)

<h2><a name="prerequisites"></a>Pré-requisitos</h2>

Este tutorial funciona com o ambiente de desenvolvimento, o projeto Web e o site do Azure que você configurou na [Introdução ao Azure e ao ASP.NET][GetStarted]. Para as seções de Trabalhos Web, será necessário o aplicativo que você criar em [Introdução ao SDK de Trabalhos Web do Azure][GetStartedWJ].

Os exemplos de código mostrados neste tutorial são para um aplicativo Web MVC em C#, mas os procedimentos para solução de problemas são os mesmos para aplicativos em Visual Basic e Web Forms.

Depuração remota requer o Visual Studio 2013 ou o Visual Studio 2012 com atualização 4. A depuração remota e os recursos do **Gerenciador de Servidores** para Trabalhos Web requerem o [Visual Studio 2013 atualização 4](http://go.microsoft.com/fwlink/?LinkID=510314) ou posterior. Outros recursos mostrados no tutorial também funcionam no Visual Studio 2013 Express para Web e no Visual Studio 2012 Express para Web. 

O recurso de logs de streaming funciona apenas para aplicativos que tenham como alvo o .NET Framework 4 ou posterior.

<h2><a name="sitemanagement"></a>Gerenciamento e configuração de site</h2>

O Visual Studio fornece acesso a um subconjunto das funções de gerenciamento e das definições de configuração disponíveis no Portal de gerenciamento. Nesta seção, você verá o que está disponível.

1. Se você ainda não entrou no Azure utilizando o Visual Studio, clique no botão **Conectar ao Azure** do **Gerenciador de Servidores**.

	Uma alternativa é instalar um certificado de gerenciamento que dê acesso à conta. O certificado de gerenciamento dá para o **Gerenciador de Servidores** acesso a serviços adicionais do Azure (banco de dados SQL e serviços móveis). Se você optar por instalar um certificado, clique com o botão direito do mouse no nó **Azure** do **Gerenciador de Servidores** e clique em **Gerenciar Assinaturas** no menu de contexto. Na caixa de diálogo **Gerenciar Assinaturas do Azure**, clique na guia **Certificados** e em **Importar**. Siga as instruções para baixar e importe um arquivo de assinatura (também chamado de arquivo *.publishsettings*) para a conta do Azure.

	> [WACOM.NOTE]
	>Se você baixar um arquivo de assinatura, salve-o em uma pasta fora dos diretórios de código-fonte (por exemplo, na pasta Downloads) e exclua-o após a conclusão da importação. Um usuário mal-intencionado que consiga acesso ao arquivo de assinatura pode editar, criar e excluir os serviços do Azure.

	Para obter mais informações sobre como se conectar aos recursos do Azure no Visual Studio, consulte [Gerenciar contas, assinaturas e funções administrativas](http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert).

2. No **Gerenciador de Servidores**, expanda **Azure** e **Sites**.

3. Clique com o botão direito do mouse no site que você criou em [Introdução ao Azure e no ASP.NET][GetStarted] e clique em **Exibir configurações**.

	![View Settings in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

	A guia **Site do Azure** aparece e é possível visualizar as tarefas de configuração e gerenciamento de sites que estão disponíveis no Visual Studio.

	![Azure Website window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

Neste tutorial, você utilizará os menus suspensos de log e rastreamento.	Você também utilizará a depuração remota, mas utilizará um método diferente para habilitá-la.
   
	Para obter informações sobre as caixas Configurações do aplicativo e Cadeias de conexão nessa janela, consulte [Sites do Azure: Como as Cadeias de caracteres do aplicativo e as Cadeias de conexão funcionam](http://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx).

	Se você desejar executar uma tarefa de gerenciamento de site que não possa ser executada nessa janela, é possível clicar em **Configurações completas de sites** para abrir uma janela do navegador para o Portal de gerenciamento. Para obter mais informações, consulte [Como configurar sites](/pt-br/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig).

<h2><a name="remoteview"></a>Acessar arquivos do site no Gerenciador de Servidores</h2>

Normalmente ao implantar um site com o sinalizador `customErrors` no arquivo Web.config definido como `On` ou `RemoteOnly`, o que significa que você não recebe uma mensagem de erro útil quando há algum erro. Para muitos erros, tudo o que você recebe é uma página como uma das páginas a seguir.

**Erro de servidor no aplicativo '/':**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Ocorreu um erro:**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**O site não pode exibir a página**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Normalmente, a maneira mais fácil de encontrar a causa do erro é habilitar as mensagens de erro detalhadas, que a primeira das capturas de tela anteriores explica como fazer. Isso exige uma alteração no arquivo Web.config implantado. É possível editar o arquivo *Web.config* no projeto e reimplantar o projeto ou criar uma [transformação de Web.config](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) e implantar uma compilação de depuração, mas há uma maneira mais rápida: no **Gerenciador de Soluções**, é possível exibir e editar diretamente arquivos no site remoto utilizando o recurso *remote view*.

1. No **Gerenciador de Servidores**, expanda **Azure**, **Sites** e o nó do site que você está implantando.

	Você verá nós que dão acesso aos arquivos de conteúdo e de log do site.

	![File and log files](./media/web-sites-dotnet-troubleshoot-visual-studio/fileandlogfiles.png)

2. Expanda o nó **Arquivos** e clique duas vezes no arquivo *Web.config*.

	![Open Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

	O Visual Studio abre o arquivo Web.config do site remoto e mostra [Remoto] próximo ao nome do arquivo na barra de título.

3. Adicione a seguinte linha ao elemento `system.web`:

	`<customErrors mode="off"></customErrors>`

	![Edit Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)

4. Atualize o navegador que está mostrando a mensagem de erro inútil, e agora você recebe uma mensagem de erro detalhada, como o seguinte exemplo:

	![Detailed error messaeg](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

	(O erro mostrado foi criado adicionando-se a linha mostrada em vermelho ao *Views\Home\Index.cshtml*.)

A edição do arquivo Web.config é apenas um exemplo dos cenários em que a capacidade de ler e editar arquivos no site do Azure facilita a solução de problemas.

<h2><a name="remotedebug"></a>Depuração remota</h2>

Se a mensagem de erro detalhada não oferecer informações suficientes e não for possível recriar o erro localmente, outra maneira de solucionar o problema é executar no modo de depuração remotamente. Você pode definir os pontos de interrupção, manipular diretamente a memória, percorrer o código e até mesmo alterar o caminho do código. 

A depuração remota não funciona em edições Express do Visual Studio.

### Sites de depuração remota

Esta seção mostra como depurar remotamente utilizando o projeto criado por você em [Introdução ao Azure e ao ASP.NET][GetStarted].

1. Abra o projeto Web que você criou em [Introdução ao Azure e ao ASP.NET][GetStarted].

1. Abra *Controllers\HomeController.cs*.

2. Exclua o método `About()` e insira o código a seguir no lugar.

        public ActionResult About()
        {
            string currentTime = DateTime.Now.ToLongTimeString();
            ViewBag.Message = "The current time is " + currentTime;
            return View();
        }

2. [Defina um ponto de interrupção](http://www.visualstudio.com/pt-br/get-started/debug-your-app-vs.aspx) na linha `ViewBag.Message`.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e clique em **Publicar**.

2. Na lista suspensa **Perfil**, selecione o mesmo perfil que você utilizou em [Introdução ao Azure e ao ASP.NET][GetStarted].

3. Clique na guia **Configurações** e altere **Configuração** para **Depuração** e, em seguida, clique em **Publicar**.

	![Publish in debug mode](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

4. Quando a implantação for concluída e o navegador abrir a URL do Azure do site, feche o navegador.

5. Para Visual Studio 2013: No **Gerenciador de Servidores**, expanda **Azure**, expanda **Sites**, clique com o botão direito do mouse no site e depois clique em **Anexar Depurador**. 

	![Attach debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

	O navegador é aberto automaticamente na home page em execução no Azure. Convém aguardar cerca de 20 segundos enquanto o Azure configura o servidor para depuração. Esse atraso ocorre apenas na primeira vez que você executa em modo de depuração em um site. Nas próximas vezes, durante as próximas 48 horas, quando você iniciar a depuração novamente não haverá atraso.

6. Para o Visual Studio 2012 com atualização 4:<a id="vs2012"></a>

	*  No Portal de gerenciamento do Azure, vá até a guia **Configurar** do site e role para baixo até a seção **Diagnóstico do site**.

	*  Defina **Depuração remota** como **Ativa** e defina a **Versão do Visual Studio para a Depuração remota** como **2012**.

	![Set remote debugging in management portal](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debuginportal.png)
   
	*  No menu **Depurar** do Visual Studio, clique em **Anexar ao processo**.

	*  Na caixa **Qualificador**, digite a URL de seu site, sem o prefixo `http://`. 

	* Selecione **Mostrar processos de todos os usuários**.

	* Quando você for solicitado para informar as credenciais, digite o nome de usuário e a senha que tem permissões para publicar o site. Para obter essas credenciais, vá para a guia Painel de seu site no portal de gerenciamento e clique em **Baixar o perfil de publicação**. Abra o arquivo em um editor de texto e você encontrará o nome do usuário e a senha após as primeiras ocorrências de **userName =** e **userPWD =**. 

	*  Quando os processos aparecerem na tabela **Processos disponíveis**, selecione **w3wp.exe** e, em seguida, clique em **Anexar**.

	* Abra um navegador para a URL do site.

	Convém aguardar cerca de 20 segundos enquanto o Azure configura o servidor para depuração. Esse atraso ocorre apenas na primeira vez que você executa em modo de depuração em um site. Nas próximas vezes, durante as próximas 48 horas, quando você iniciar a depuração novamente não haverá atraso.

6. Clique em **Sobre** no menu.

	O Visual Studio para no ponto de interrupção, e o código está em execução no Azure, e não no computador local.

7. Focalize sobre a variável `currentTime` para ver o valor da hora.

	![View variable in debug mode running in Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

	A hora que você vê é a hora do servidor do Azure, que pode estar em um fuso horário diferente do computador local.

8. Insira um novo valor para a variável `currentTime`, como "Agora em execução no Azure".

5. Pressione F5 para continuar a execução.

	A página Sobre em execução no Azure exibe o novo valor que você inseriu na variável currentTime.

	![About page with new value](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

### <a name="remotedebugwj"></a> Trabalhos Web de depuração remota

Esta seção mostra como depurar remotamente utilizando o projeto e o site que você criar na [Introdução ao SDK de Trabalhos Web do Azure](../websites-dotnet-webjobs-sdk). Os recursos mostrados nesta seção estão disponíveis apenas no Visual Studio 2013 com atualização 4.

1. Abra o projeto Web que você criou na [Introdução aos SDK de Trabalhos Web do Azure][GetStartedWJ].

1. No projeto ContosoAdsWebJob, abra *Functions.cs*.

2. [Defina um ponto de interrupção](http://www.visualstudio.com/pt-br/get-started/debug-your-app-vs.aspx) na primeira instrução no método 'GnerateThumbnail'.

	![Set breakpoint](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto Web ContosoAdsWeb (não no projeto Trabalho Web) e clique em **Publicar**.

2. Na lista suspensa **Perfil**, selecione o mesmo perfil que você utilizou em [Introdução ao SDK de Trabalhos Web do Azure](../websites-dotnet-webjobs-sdk).

3. Clique na guia **Configurações** e altere **Configuração** para **Depuração** e, em seguida, clique em **Publicar**.

	Visual Studio implanta os projetos de Trabalho Web e Web e abre o navegador na URL do Azure do seu site.

5. No **Gerenciador de Servidores** expanda **Azure** > **Sites** > seu site > **Trabalhos Web** > **Contínuo** e, em seguida, clique com o botão direito do mouse em **ContosoAdsWebJob**.

7. Clique em **Anexar o depurador**. 

	![Attach debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

	O navegador é aberto automaticamente na home page em execução no Azure. Convém aguardar cerca de 20 segundos enquanto o Azure configura o servidor para depuração. Esse atraso ocorre apenas na primeira vez que você executa em modo de depuração em um site. Na próxima vez, dentro das próximas 48 horas, quando você anexar o depurador não haverá um atraso.

6. No navegador da Web que abre na home page de Anúncios do Contoso, crie um novo anúncio. 

	Criar um anúncio faz com que uma mensagem da fila seja criada, ela será capturada pelo Trabalho Web e processada. Quando o SDK de Trabalhos Web chama a função para processar a mensagem da fila, o código atingirá o ponto de interrupção.

7. Quando o depurador for interrompido no ponto de interrupção, é possível examinar e alterar valores de variáveis enquanto o programa está em execução na nuvem. Na ilustração a seguir, o depurador mostra o conteúdo do objeto blobInfo que foi passado para o método GenerateThumbnail.

	![blobInfo object in debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)
 
5. Pressione F5 para continuar a execução.

	O método GenerateThumbnail termina de criar a miniatura.

6. No navegador, atualize a página de índice e você verá a miniatura.

6. No Visual Studio, pressione SHIFT+F5 para parar a depuração.

7. No **Gerenciador de Servidores**, clique com botão direito do mouse no nó ContosoAdsWebJob e clique em **Painel de exibição**.

8. Entre com suas credenciais do Azure e, em seguida, clique no nome do Trabalho Web para ir para a página de seu Trabalho Web. 

	![Click ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

	O painel que mostra a função GenerateThumbnail foi executada recentemente.

	(Na próxima vez que você clicar em **Painel de exibição**, não será necessário entrar e o navegador vai diretamente para a página do Trabalho Web.)

9. Clique no nome da função para ver detalhes sobre a execução da função.

	![Function details](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Se sua função [criou logs](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#logs), é possível clicar em **ToggleOutput** para vê-los.

### Observações sobre a depuração remota

*  A execução no modo de depuração em produção não é recomendada. Se seu site de produção não é dimensionado para várias instâncias do servidor, a depuração impedirá o servidor Web respondendo a outras solicitações. Se houver várias instâncias de servidor Web, ao anexar o depurador você obterá uma instância aleatória e não há como garantir que suas solicitações subsequentes do navegador irão para essa instância. Além disso, normalmente você não implanta uma compilação de depuração em produção e as otimizações do compilador para compilações de versão podem impossibilitar a exibição do que está acontecendo linha por linha em seu código-fonte. Para solucionar problemas de produção, os melhores recursos são o rastreamento do aplicativo e os logs do servidor Web.

*  Evite paradas longas em pontos de interrupção durante a depuração remota. O Azure trata um processo parado por mais de alguns minutos como um processo sem resposta e o desliga.

*  Enquanto você está depurando, o servidor está enviando dados para o Visual Studio, o que pode afetar os encargos de largura de banda. Para obter informações sobre as taxas de largura de banda, consulte [Preço do Azure](/pt-br/pricing/calculator/).

*  Verifique se o atributo `debug` do elemento `compilation` no arquivo *Web.config* está definido como true. Por padrão, ele é definido como true quando você publica uma configuração da compilação de depuração.

        <system.web>
          <compilation debug="true" targetFramework="4.5" />
          <httpRuntime targetFramework="4.5" />
        </system.web>

*  Se você descobrir que o depurador não entra no código que você deseja depurar, você precisará alterar a configuração de Just My Code.  Para obter mais informações, consulte [Restringir a entrada a Apenas Meu Código](http://msdn.microsoft.com/pt-br/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code).

*  Um timer é iniciado no servidor quando você habilita o recurso de depuração remota e, após 48 horas, o recurso é desativado automaticamente. Esse limite de 48 horas é definido por razões de segurança e desempenho. Você pode reativá-lo facilmente quantas vezes quiser. É recomendável deixá-lo desabilitado quando você não está depurando ativamente.

* É possível anexar manualmente o depurador a qualquer processo, não apenas ao processo de site (w3wp.exe). Para obter mais informações sobre como usar o modo de depuração no Visual Studio, consulte [Depurando no Visual Studio](http://msdn.microsoft.com/pt-br/library/vstudio/sc65sadd.aspx).

<h2><a name="logsoverview"></a>Visão geral dos logs de diagnóstico</h2>

Um aplicativo em ASP.NET executado em um site do Azure pode criar os seguintes tipos de logs:

* **Logs de rastreamento de aplicativos**<br/>
 O aplicativo cria esses logs chamando métodos da classe [System.Diagnostics.Trace](http://msdn.microsoft.com/pt-br/library/system.diagnostics.trace.aspx).
* **Logs de servidor Web**<br/>
  O servidor web cria uma entrada no log para cada solicitação HTTP ao site.
* **Logs de mensagens de erro detalhadas**<br/>
 O servidor Web cria uma página HTML com algumas informações adicionais para solicitações HTTP com falha (aquelas que resultam em um código de status 400 ou superior). 
* **Logs de rastreamento de solicitações com falha**<br/>
 O servidor Web cria um arquivo XML com informações de rastreamento detalhadas para solicitações HTTP com falha. O servidor Web também fornece um arquivo XSL para formatar o XML em um navegador.
  
Como o registro em log afeta o desempenho do site, o Azure oferece a possibilidade de habilitar ou desabilitar cada tipo de log conforme necessário. Para logs de aplicativo, você pode especificar que apenas os logs acima de um determinado nível de gravidade devem ser gravados. Por padrão, quando você cria um novo site, todo o registro em log está desabilitado.

Os logs são gravados em arquivos em uma pasta *LogFiles* no sistema de arquivos de seu site e são acessíveis via FTP. Os logs de servidor Web e de aplicativo também podem ser gravados em uma conta de armazenamento do Azure. Você pode manter um volume maior de logs em uma conta de armazenamento do que o que é possível no sistema de arquivos. Você está limitado a um máximo de 100 megabytes de logs ao usar o sistema de arquivos. (Os logs do sistema de arquivos são apenas para retenção de curto prazo. O Azure exclui arquivos de log antigos para abrir espaço para novos quando o limite é atingido.)  

<h2><a name="apptracelogs"></a>Criar e exibir logs de rastreamento de aplicativos</h2>

Nesta seção, você executará as seguintes tarefas:

* Adicione instruções de rastreamento ao projeto Web que você criou na [Introdução ao Azure e ao ASP.NET][GetStarted].
* Exiba os logs ao executar o projeto localmente.
* Exiba os logs conforme eles são gerados pelo aplicativo em execução no Azure. 

Para obter informações sobre como criar um logs de aplicativo em Trabalhos Web, consulte [Como trabalhar com o armazenamento de fila do Azure utilizando o SDK de Trabalhos Web - como gravar logs](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#logs). As instruções a seguir para exibir logs e controlar como eles são armazenados no Azure também se aplicam a logs de aplicativos criados por Trabalhos Web. 

### Adicionar instruções de rastreamento ao aplicativo

1. Abra *Controllers\HomeController.cs* e substitua o conteúdo do arquivo pelo código a seguir para adicionar instruções de `Trace` e uma instrução `using` para `System.diagnostics`:

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

3. Pressione F5 para executar o aplicativo em modo de depuração.

	O ouvinte de rastreamento padrão grava toda saída de rastreamento na janela **Saída**, junto com as outras saídas de depuração. A ilustração a seguir mostra a saída das instruções de rastreamento que você adicionou ao método `Index`.

	![Tracing in Debug window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

	As etapas a seguir mostram como exibir a saída do rastreamento em uma página da web, sem compilação em modo de depuração.

2. Abra o arquivo Web.config do aplicativo (o arquivo localizado na pasta do projeto) e adicione um elemento `<system.diagnostics>` no final do arquivo exatamente antes do elemento `</configuration>` de fechamento:

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

	O `WebPageTraceListener` permite que você exiba a saída do rastreamento navegando até `/trace.axd`.

3. Adicione um <a href="http://msdn.microsoft.com/pt-br/library/vstudio/6915t83k(v=vs.100).aspx">elemento de rastreamento</a> em `<system.web>` no arquivo Web.config, como no seguinte exemplo:

		<trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />

3. Pressione CTRL+F5 para executar o aplicativo.

4. Na barra de endereços da janela do navegador, adicione *trace.axd* à URL e pressione Enter (a URL será semelhante a http://localhost:53370/trace.axd).

5. Na página de **Rastreamento do aplicativo**, clique em **Exibir detalhes** na primeira linha (não a linha BrowserLink).

	![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

	A página **Solicitar Detalhes** será exibida e, na seção **Informações de rastreamento** você verá a saída das instruções de rastreamento adicionadas ao método `Index`.

	![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

	Por padrão, o `trace.axd` só está disponível localmente. Se desejar disponibilizá-lo em um site remoto, será possível adicionar `localOnly="false"` ao elemento `trace` no arquivo *Web.config*, como mostrado no exemplo a seguir:

		<trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

	No entanto, a habilitação de `trace.axd` em um site de produção não costuma ser recomendada por motivos de segurança e, nas seções a seguir, você verá uma maneira mais fácil de ler logs de rastreamento em um site do Azure.

### Exibir a saída de rastreamento no Azure

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto Web e clique em **Publicar**.

2. Na caixa de diálogo **Publicar Web**, clique em **Publicar**.

	Depois de publicar sua atualização, o Visual Studio abre uma janela do navegador em sua home page (supondo que você não limpou a **URL de destino** na guia **Conexão**).

3. No **Gerenciador de Servidores**, clique com o botão direito do mouse no site e selecione **Exibir logs de streaming**. 

	![View Streaming Logs in context menu](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

	A janela **Saída** mostra que você está conectado ao serviço de streaming de log e adiciona uma linha de notificação a cada minuto que passa sem um log para exibir.

	![View Streaming Logs in context menu](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)

4. Na janela do navegador que mostra a home page do aplicativo, clique em **Contato**.

Em alguns segundos, a saída do rastreamento em nível de erro adicionada ao método `Contact` aparece na janela **Saída**.

	![Error trace in Output window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

	O Visual Studio está mostrando apenas rastreamentos em nível de erro, porque essa é a configuração padrão quando você habilita o serviço de monitoramento de log. Ao criar um novo site do Azure, todo registro em log é desabilitado por padrão, como você viu quando abriu a página de configurações do site anteriormente:

	![Application Logging off](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)


	No entanto, ao selecionar **Exibir logs de streaming**, o Visual Studio alterou automaticamente **Log de aplicativos (sistema de arquivos)** para **Erro**, o que significa que os logs em nível de erro são relatados. Para ver todos os logs de rastreamento, você pode alterar essa configuração para **Detalhado**. Quando você seleciona um nível de severidade inferior a erro, todos os logs para níveis de severidade mais altos são relatados. Portanto, quando selecionar detalhado, você também verá logs de informações, avisos e erros.  

4. No **Gerenciador de Servidores**, clique com o botão direito do mouse no site e, em seguida, clique em **Exibir configurações** como feito anteriormente.

5. Altere **Log de aplicativos (sistema de arquivos)** para **Detalhado** e clique em **Salvar**.
 
	![Setting trace level to Verbose](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)

6. Na janela do navegador que agora está mostrando a página **Contato**, clique em **Início**, em **Sobre** e, em seguida, em **Contato**.

	Em poucos segundos, a janela **Saída** mostra toda a saída de rastreamento.

	![Verbose trace output](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

	Nesta seção, você habilitou e desabilitou o registro em log utilizando as configurações de site do Azure. Você também pode habilitar e desabilitar os ouvintes de rastreamento modificando o arquivo Web.config. No entanto, a modificação do arquivo Web.config faz com que o domínio de aplicativo seja reciclado, enquanto a habilitação por meio do site não faz isso. Se o problema levar muito tempo para ser reproduzido, ou for intermitente, a reciclagem do domínio de aplicativo pode "corrigir" isso e forçar você a esperar até que o problema ocorra novamente. Como a habilitação do diagnóstico no Azure não faz isso, você não pode começar a capturar informações de erro imediatamente.

### Recursos da janela Saída

A guia **Logs do Azure** da janela **Saída** possui vários botões e uma caixa de texto:

![Logs tab buttons](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Desempenham as seguintes funções:

* Limpe a janela **Saída**.
* Habilite ou desabilite a quebra automática de linha.
* Inicie ou pare logs de monitoramento.
* Especifique quais logs monitorar.
* Baixe logs.
* Filtre logs com base em uma cadeia de caracteres de pesquisa ou uma expressão regular.
* Feche a janela **Saída**.

Se você inserir uma cadeia de caracteres de pesquisa ou uma expressão regular, o Visual Studio filtrará as informações de log no cliente. Isso significa que é possível inserir os critérios depois que os logs são exibidos na janela **Saída** e alterar os critérios de filtragem sem precisar gerar os logs novamente.

<h2><a name="webserverlogs"></a>Exibir logs de servidor Web</h2>

Os logs do servidor Web registram toda a atividade HTTP do site. Para vê-los na janela **Saída**, você precisa habilitá-los no site e informar ao Visual Studio que deseja monitorá-los. 

1. Na guia **Configuração de site do Azure** que você abriu no **Gerenciador de Servidores**, altere Registro em log do servidor Web para **Ativado** e clique em **Salvar**.

	![Enable web server logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)

2. Na janela **Saída**, clique no botão **Especificar quais logs do Azure monitorar**.
	
	![Specify which Azure logs to monitor](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)

3. Na caixa de diálogo **Opções de registro em log do Azure**, selecione **Logs do servidor Web** e clique em **OK**.

	![Monitor web server logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)

4. Na janela do navegador que mostra o site, clique em **Início**, em **Sobre** e, em seguida, clique em **Contato**.

Os logs de aplicativo geralmente aparecem primeiro, seguidos dos logs de servidor Web. Talvez seja necessário esperar algum tempo para que os logs sejam exibidos. 

	![Web server logs in Output window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)


Por padrão, quando você habilita os logs do servidor Web pela primeira vez utilizando o Visual Studio, o Azure grava os logs no sistema de arquivos. Como alternativa, você pode usar o portal de gerenciamento para especificar que os logs de servidor Web devem ser gravados em um contêiner de blob em uma conta de armazenamento. Para obter mais informações, consulte a seção **diagnóstico de site** em [Como configurar sites](/pt-br/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig). 

If you use the management portal to enable web server logging to an Azure storage account, and then disable logging in Visual Studio, when you re-enable logging in Visual Studio your storage account settings are restored. 

<h2><a name="detailederrorlogs"></a>View detailed error message logs</h2>

Detailed error logs provide some additional information about HTTP requests that result in error response codes (400 or above). In order to see them in the **Output** window, you have to enable them on the site and tell Visual Studio that you want to monitor them.

1. In the **Azure Website Configuration** tab that you opened from **Server Explorer**, change **Detailed Error Messages** to **On**, and then click **Save**.

	![Enable detailed error messages](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. In the **Output** Window, click the **Specify which Azure logs to monitor** button.

3. In the **Azure Logging Options** dialog box, click **All logs**, and then click **OK**.

	![Monitor all logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. In the address bar of the browser window, add an extra character to the URL to cause a 404 error (for example, `http://localhost:53370/Home/Contactx`), and press Enter.

	After several seconds the detailed error log appears in the Visual Studio **Output** window.

	![Detailed error log in Output window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

	Control+click the link to see the log output formatted in a browser:

	![Detailed error log in browser window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

<h2><a name="downloadlogs"></a>Download file system logs</h2>

Any logs that you can monitor in the **Output** window can also be downloaded as a *.zip* file. 

1. In the **Output** window, click **Download Streaming Logs**.

	![Logs tab buttons](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

	File Explorer opens to your *Downloads* folder with the downloaded file selected.

	![Downloaded file](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)

2. Extract the *.zip* file, and you see the following folder structure:

	![Downloaded file](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

	* Application tracing logs are in *.txt* files in the *LogFiles\Application* folder.
	* Web server logs are in *.log* files in the *LogFiles\http\RawLogs* folder. You can use a tool such as [Log Parser](http://www.microsoft.com/pt-br/download/details.aspx?displaylang=en&id=24659) to view and manipulate these files.
	* Detailed error message logs are in *.html* files in the *LogFiles\DetailedErrors* folder.

	(The *deployments* folder is for files created by source control publishing; it doesn't have anything related to Visual Studio publishing. The *Git* folder is for traces related to source control publishing and the log file streaming service.)  

<h2><a name="storagelogs"></a>View storage logs</h2>

Application tracing logs can also be sent to an Azure storage account, and you can view them in Visual Studio. To do that you'll create a storage account, enable storage logs in the management portal, and view them in the **Logs** tab of the **Azure Website** window.

You can send logs to any or all of three destinations:

* The file system.
* Storage account tables.
* Storage account blobs.

You can specify a different severity level for each destination. 

Tables make it easy to view details of logs online, and they support streaming; you can query logs in tables and see new logs as they are being created. Blobs make it easy to download logs in files and to analyze them using HDInsight, because HDInsight knows how to work with blob storage. For more information, see **Hadoop and MapReduce** in [Data Storage Options (Building Real-World Cloud Apps with Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

You currently have file system logs set to verbose level; the following steps walk you through setting up information level logs to go to storage account tables. Information level means all logs created by calling `Trace.TraceInformation`, `Trace.TraceWarning`, and `Trace.TraceError` will be displayed, but not logs created by calling `Trace.WriteLine`.

Storage accounts offer more storage and longer-lasting retention for logs compared to the file system. Another advantage of sending application tracing logs to storage is that you get some additional information with each log that you don't get from file system logs.

5. Right-click **Storage** under the Azure node, and then click **Create Storage Account**.

![Create Storage Account](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

3. In the **Create Storage Account** dialog, enter a name for the storage account. 

	The name must be must be unique (no other Azure storage account can have the same name). If the name you enter is already in use you'll get a chance to change it.

	The URL to access your storage account will be *{name}*.core.windows.net. 

5. Set the **Region or Affinity Group** drop-down list to the region closest to you.

	This setting specifies which Azure datacenter will host your storage account. For this tutorial your choice won't make a noticeable difference, but for a production site you want your web server and your storage account to be in the same region to minimize latency and data egress charges. The website (which you'll create later) should be as close as possible to the browsers accessing your site in order to minimize latency.

6. Set the **Replication** drop-down list to **Locally redundant**. 

	When geo-replication is enabled for a storage account, the stored content is replicated to a secondary datacenter to enable failover to that location in case of a major disaster in the primary location. Geo-replication can incur additional costs. For test and development accounts, you generally don't want to pay for geo-replication. For more information, see [How To Manage Storage Accounts](/pt-br/documentation/articles/storage-manage-storage-account/).

5. Clique em **Criar**. 

	![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)	

1. Na janela **Site do Azure** do Visual Studio, clique na guia **Logs** e, em seguida, clique em **Configurar registro em log no Portal de gerenciamento**.

	![Configure logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

	Isso abre a guia **Configurar** no Portal de gerenciamento do seu site. Outra maneira de fazer isso é clicar na guia **Sites**, clicar em seu site e, em seguida, na guia **Configurar**.

2. Na guia **Configurar** do Portal de gerenciamento, role até a seção de diagnóstico de aplicativos e altere **Registro em log do aplicativo (armazenamento de tabela)** para **Ativado**.

3. Altere **Nível de registro em log** para **Informações**.

4. Clique em **Gerenciar armazenamento de tabela**.

	![Click Manage TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

	Na caixa de diálogo **Gerenciar armazenamento de tabela para diagnóstico de aplicativos**, você poderá escolher a conta de armazenamento, se tiver mais de uma. É possível criar uma nova tabela ou utilizar uma existente.

	![Manage table storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)

6. Na caixa **Gerenciar armazenamento de tabela para diagnóstico de aplicativos**, clique na marca de seleção para fechar a caixa.

6. Na guia **Configurar** do Portal de gerenciamento, clique em **Salvar**.

7. Na janela do navegador que mostra o site do aplicativo, clique em **Início**, em **Sobre** e, em seguida, clique em **Contato**.

	As informações de log produzidas pela navegação nessas páginas da Web serão gravadas na conta de armazenamento.

8. Na guia **Logs** da janela **Site do Azure** no Visual Studio, clique em **Atualizar** em **Resumo do diagnóstico**.

	![Click Refresh](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

	Por padrão, a seção **Resumo do diagnóstico** mostra logs dos últimos 15 minutos. Você pode alterar o período para ver mais logs. 

	(Se você receber um erro "tabela não encontrada", verifique se navegou até as páginas que fazem o rastreamento depois de habilitar **Registro em log do aplicativo (armazenamento)** e clicar em **Salvar**.)

	![Storage logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

	Observe que nessa exibição você vê a **ID do processo** e a **ID do thread** de cada log, o que você não obtém nos logs do sistema de arquivos. É possível visualizar campos adicionais exibindo a tabela de armazenamento do Azure diretamente.

8. Clique em **Exibir todos os logs de aplicativo**.

	A tabela do log de rastreamento é exibida no visualizador da tabela de armazenamento do Azure.
   
	(Se você receber um erro "a sequência não contém elementos", abra o **Gerenciador de Servidores**, expanda o nó de sua conta de armazenamento sob o nó **Azure** e, em seguida, clique com o botão direito do mouse em **Tabelas** e clique em **Atualizar**.)

	![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetableinse.png)

	![Storage logs in table view](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

	Esta exibição mostra campos adicionais que você não vê em outros modos de exibição. Esse modo de exibição permite filtrar logs usando a interface do usuário especial do Construtor de Consultas para construir uma consulta. Para obter mais informações, consulte Trabalhando com recursos de tabela - filtrando entidades em [Procurando recursos de armazenamento com o Gerenciador de Servidores](http://msdn.microsoft.com/pt-br/library/windowsazure/ff683677.aspx).

7. Para visualizar os detalhes de uma única linha, clique duas vezes em uma das linhas.

	![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)

<h2><a name="failedrequestlogs"></a>Exibir logs de rastreamento de solicitações com falha</h2>

Os logs de rastreamento de solicitação com falha são úteis quando você precisa entender os detalhes de como o IIS trata uma solicitação HTTP, em cenários como problemas de autenticação ou regravação de URL. 

Os sites do Azure utilizam a mesma funcionalidade de rastreamento de solicitação com falha disponível no IIS 7.0 e em versões posteriores. No entanto, você não tem acesso às configurações do IIS que configuram quais erros são registrados em log. Quando você habilita o rastreamento de solicitação com falha, todos os erros são capturados. 

É possível habilitar o rastreamento de solicitação com falha utilizando o Visual Studio, mas não é possível exibi-lo no Visual Studio. Esses logs são arquivos XML. O serviço de log de streaming monitora apenas arquivos considerados legíveis em modo de texto sem formatação:  arquivos *.txt*, *.html*, e *.log*.

É possível exibir os logs de rastreamento de solicitação com falha em um navegador diretamente por meio de FTP ou localmente depois de utilizar uma ferramenta FTP para baixá-los em seu computador local. Nesta seção você vai exibi-los diretamente em um navegador.

1. Na guia **Configuração** da janela **Site do Azure** que você abriu no **Gerenciador de Servidores**, altere **Rastreamento de solicitação com falha** para **Ativado** e clique em **Salvar**.

	![Enable failed request tracing](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)

4. Na barra de endereços da janela do navegador que mostra o site, adicione um caractere extra à URL e clique em Enter para provocar um erro 404.

	Isso faz com que um log de rastreamento de solicitação com falha seja criado e as etapas seguintes mostram como exibir ou baixar o log.

2. No Visual Studio, na guia **Configuração** da janela **Site do Azure**, clique em **Abrir no Portal de gerenciamento**.

3. No Portal de gerenciamento, clique em **Painel** e em **Redefinir as credenciais de implantação** na seção **Visão Rápida**.

	![Reset FTP credentials link in Dashboard](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-resetftpcredentials.png)

4. Digite um novo nome de usuário e uma senha.

	![New FTP user name and password](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

5. Na guia **Painel** do Portal de Gerenciamento, pressione F5 para atualizar a página e role para baixo até onde você possa ver **Implantação/Usuário FTP**. Observe que o nome do usuário tem o nome do site como prefixo. **Quando você faz logon, você precisa usar esse nome de usuário completo com o nome do site como prefixo, conforme mostrado aqui.**

5. Em uma nova janela do navegador, vá até a URL mostrada em **Nome do host FTP** na guia **Painel** da página do Portal de gerenciamento do site. O **nome do host FTP** está localizado próximo a **Implantação/Usuário FTP** na seção **Visão Rápida**.

6. Faça logon usando as credenciais FTP que você criou anteriormente (incluindo o prefixo do nome do site do nome do usuário).

	O navegador mostra a pasta raiz do site.

6. Abra a pasta *LogFiles*.

	![Open LogFiles folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

7. Abra a pasta chamada W3SVC mais um valor numérico.

	![Open W3SVC folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

	A pasta contém arquivos XML para todos os erros que foram registrados em log depois que você habilitou o rastreamento de solicitação com falha e um arquivo XSL que um navegador pode usar para formatar o XML.

	![W3SVC folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

8. Clique no arquivo XML da solicitação com falha da qual você deseja ver as informações de rastreamento.

	A ilustração a seguir mostra parte das informações de rastreamento de um erro de amostra.

	![Failed request tracing in browser](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)


<h2><a name="nextsteps"></a>Próximas etapas</h2>

Você viu como o Visual Studio facilita a exibição de logs criados por um site do Azure. As seções a seguir fornecem links para mais recursos sobre tópicos relacionados:

* Solução de problemas em Site do Azure
* Depuração no Visual Studio 
* Depuração remota no Azure
* Rastreando em aplicativos ASP.NET
* Analisando logs de servidor Web
* Analisando logs de rastreamento de solicitação com falha
* Depurando serviços de nuvem

### Solução de problemas em Site do Azure

Para obter mais informações como solucionar problemas de WAWS (sites do Azure), consulte os seguintes recursos:

* [Como monitorar Sites](/pt-br/manage/services/web-sites/how-to-monitor-websites/)
* [Investigação de vazamentos de memória em sites do Azure Visual Studio 2013](http://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Postagem no blog do Microsoft ALM sobre os recursos do Visual Studio para análise de problemas de memória gerenciada.
* [Ferramentas online dos Sites do Windows Azure que você precisa conhecer](/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Postagem do blog por Amit Apple.

Para obter ajuda com uma pergunta específica de solução de problemas, inicie um thread em um dos seguintes fóruns:

* [O fórum do Azure no site do ASP.NET](http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [O fórum do Azure no MSDN](http://social.msdn.microsoft.com/Forums/windowsazure/).
* [StackOverflow.com](http://www.stackoverflow.com).

### Depuração no Visual Studio 

Para obter mais informações sobre como utilizar o modo de depuração no Visual Studio, consulte o tópico do MSDN [Depurando no Visual Studio](http://msdn.microsoft.com/pt-br/library/vstudio/sc65sadd.aspx) e [Dicas de depuração no Visual Studio 2010](http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### Depuração remota no Azure

Para obter mais informações sobre depuração remota para Sites do Azure, consulte os seguintes recursos:

* [Introdução à depuração remota em Sites do Azure](/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Introdução à depuração remota em Sites do Azure parte 2 - Por dentro da depuração remota](/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Introdução à depuração remota em Sites do Azure parte 3 - Ambiente de várias instâncias e GIT](/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [Depuração de Trabalhos Web (vídeo)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Se o site utilizar uma API Web do Azure ou um back-end de Serviços Móveis e você precisar depurá-lo, consulte [Depurando Back-end do .NET no Visual Studio](http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### Rastreando em aplicativos ASP.NET

Não existem introduções completas e atualizadas para rastreamento do ASP.NET disponíveis na Internet. O melhor a fazer é começar com materiais introdutórios antigos escritos para formulários da Web porque o MVC ainda não existia e complementar isso com postagens em blog mais recentes focalizadas em problemas específicas. Alguns bons locais para começar são os seguintes recursos:

* [Monitoramento e telemetria (Compilando aplicativos de nuvem do mundo real com o Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br> 
  Capítulo do livro eletrônico com recomendações para rastreamento em aplicativos de nuvem do Azure.
* [Rastreamento do ASP.NET](http://msdn.microsoft.com/pt-br/library/ms972204.aspx)<br/>
  Antigo, mas ainda é um bom recurso para obter uma introdução básica sobre o assunto.
* [Ouvintes de rastreamento](http://msdn.microsoft.com/pt-br/library/4y5y10s7.aspx)<br/>
 Informações sobre ouvintes de rastreamento, mas não menciona o [WebPageTraceListener](http://msdn.microsoft.com/pt-br/library/system.web.webpagetracelistener.aspx).
* [Passo a passo: Integrando o rastreamento do ASP.NET com o rastreamento do System.Diagnostics](http://msdn.microsoft.com/pt-br/library/b0ectfxd.aspx)<br/>
  Isso também é antigo, mas inclui algumas informações adicionais que o artigo introdutório não cobre.
* [Rastreando em exibições do Razor do ASP.NET MVC (a página pode estar em inglês)](http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
 Além do rastreamento em exibições do Razor, a postagem também explica como criar um filtro de erros para registrar em log todas as exceções não tratadas em um aplicativo MVC. Para obter informações sobre como registrar em log exceções não tratadas em um aplicativo de formulários da Web, consulte o exemplo do Global.asax em [Concluir exemplo para manipuladores de erro](http://msdn.microsoft.com/pt-br/library/bb397417.aspx) no MSDN. No MVC ou em Formulários da Web, se você deseja registrar certas exceções em log, mas deixe o tratamento da estrutura padrão cuidar deles, será possível capturar e relançar como no exemplo a seguir:

        try
        {
           // O código que pode fazer com que uma exceção seja lançada.
        }
        catch (Exception ex)
        {
            Trace.TraceError("Exception: " + ex.ToString());
            Throw;
        } 

* [Log de rastreamento do diagnóstico de streaming na linha de comando do Azure (mais Glimpse!) (a página pode estar em inglês)](http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
Como usar a linha de comando para fazer o que este tutorial mostra no Visual Studio. [Glimpse (a página pode estar em inglês)](http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) é uma ferramenta para depuração de aplicativos ASP.NET. 
* [Utilizando o diagnóstico e registro em log de site do Azure - com David Ebbo](http://www.windowsazure.com/pt-br/documentation/videos/azure-web-site-logging-and-diagnostics/) e [Logs de streaming de Sites do Azure - com David Ebbo](http://www.windowsazure.com/pt-br/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Vídeos de Scott Hanselman e David Ebbo.

Para o log de erros, uma alternativa para escrever seu próprio código de rastreamento é utilizar uma estrutura de software livre, como o [ELMAH](http://nuget.org/packages/elmah/). Para obter mais informações, consulte as [postagens no blog de Scott Hanselman sobre o ELMAH](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Além disso, observe que não é necessário utilizar o rastreamento do ASP.NET ou do System.Diagnostics se quiser obter logs de streaming do Azure. O serviço de log de streaming de site do Azure transmitirá qualquer arquivo *.txt*, *.html* ou *.log* que encontrar na pasta *LogFiles*. Portanto, você pode criar seu próprio sistema de log que grava no sistema de arquivos do site, e o arquivo será automaticamente transmitido e baixado. Tudo o que você precisa fazer é escrever o código do aplicativo que cria arquivos na pasta *d:\home\logfiles*. 

### Analisando logs de servidor Web

Para obter mais informações sobre como analisar logs de servidor web, consulte os seguintes recursos:

* [LogParser](http://www.microsoft.com/pt-br/download/details.aspx?id=24659)<br/>
  Uma ferramenta para exibir dados em logs de servidor Web (arquivos *.log*).
* [Solucionando problemas de desempenho do IIS ou erros de aplicativo usando o LogParser ](http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Uma introdução à ferramenta Log Parser que pode ser usada para analisar logs de servidor web.
* [Postagens no blog por Robert McMurray sobre como usar o LogParser](http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [O código de status HTTP no IIS 7.0, IIS 7.5 e IIS 8.0](http://support.microsoft.com/kb/943891)

### Analisando logs de rastreamento de solicitação com falha

O site do Microsoft TechNet inclui uma seção [Utilizando rastreamento de solicitação com falha](http://www.iis.net/learn/troubleshoot/using-failed-request-tracing) que pode ser útil para entender como utilizar esses logs. No entanto, essa documentação se concentra principalmente na configuração do rastreamento de solicitação com falha no IIS, o que não pode ser feito em Sites do Azure.

### Depurando serviços de nuvem

Se você quiser depurar um serviço de nuvem do Azure, em vez de um site, consulte [Depurando serviços de nuvem](http://msdn.microsoft.com/pt-br/library/windowsazure/ee405479.aspx).




[Introdução]: ../web-sites-dotnet-get-started/
[Introdução aos Trabalhos Web]: ../websites-dotnet-webjobs-sdk/

