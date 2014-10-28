<properties linkid="develop-net-tutorials-get-started-vs2013" pageTitle="Get started with Azure Websites for ASP.NET" metaKeywords="" description="This tutorial shows you how to create an ASP.NET web project in Visual Studio 2013 and deploy it to an Azure Website. In less than 15 minutes you'll have an app up and running in the cloud." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Get started with Azure Websites and ASP.NET" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="tdykstra"></tags>

# Introdução aos Sites do Azure e ASP.NET

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/develop/net/tutorials/get-started/" title="Visual Studio 2013" class="current">Visual Studio 2013</a><a href="/en-us/develop/net/tutorials/get-started-vs2012/" title="Visual Studio 2012">Visual Studio 2012</a></div>

Este tutorial mostra como criar um aplicativo Web ASP.NET e implantá-lo em um site do Azure usando o Visual Studio 2013 ou o Visual Studio 2013 for Web Express. O tutorial pressupõe que você não tem nenhuma experiência anterior com o Azure ou o ASP.NET. Ao concluir o tutorial, você terá um aplicativo Web simples em funcionamento na nuvem.

É possível abrir uma conta do Azure gratuitamente e, se você ainda não tiver o Visual Studio 2013, o SDK instalará automaticamente o Visual Studio 2013 para o Web Express. Portanto, você pode começar a desenvolver para o Azure de maneira totalmente gratuita.

Você aprenderá a:

-   Como habilitar seu computador para desenvolvimento do Azure ao instalar o SDK do Azure.
-   Como criar um projeto Web Visual Studio ASP.NET e implantá-lo em um Site do Azure.
-   Como fazer uma alteração no projeto Web e a reimplantá-lo.
-   Como usar o Portal de Gerenciamento do Azure para monitorar e gerenciar seu site.

A ilustração a seguir mostra o aplicativo concluído:

![Home page do site][Home page do site]

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>Para concluir este tutorial, voc&ecirc; precisa de uma conta do Azure:</h5>
  <ul>
    <li>Voc&ecirc; pode <a href="/en-us/pricing/free-trial/?WT.mc_id=A261C142F">abrir uma conta do Azure gratuitamente</a> - Voc&ecirc; obt&eacute;m cr&eacute;ditos que podem ser usados para experimentar servi&ccedil;os pagos do Azure e, mesmo ap&oacute;s eles serem utilizados, voc&ecirc; pode manter a conta e utilizar os servi&ccedil;os gratuitos do Azure, como os Sites.</li>
    <li>Voc&ecirc; pode <a href="/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F">ativar benef&iacute;cios para assinantes do MSDN</a> - Todos os meses, sua assinatura do MSDN oferece cr&eacute;ditos que podem ser usados para servi&ccedil;os pagos do Azure.</li>
  <ul>
</div>

### Segmentos do tutorial

-   [Configurar o ambiente de desenvolvimento][Configurar o ambiente de desenvolvimento]
-   [Criar um aplicativo Web ASP.NET no Visual Studio][Criar um aplicativo Web ASP.NET no Visual Studio]
-   [Implantar o aplicativo no Azure][Implantar o aplicativo no Azure]
-   [Fazer uma alteração e reimplantar][Fazer uma alteração e reimplantar]
-   [Monitore e gerencie o site no Portal de Gerenciamento][Monitore e gerencie o site no Portal de Gerenciamento]
-   [Próximas etapas][Próximas etapas]

[WACOM.INCLUDE [install-sdk-2013-only][install-sdk-2013-only]]

## Criar um aplicativo Web ASP.NET

A primeira etapa é criar um projeto de aplicativo Web. O Visual Studio criará automaticamente o Site do Azure que implantará o seu projeto para mais tarde. O diagrama a seguir ilustra o que está acontecendo nessas duas etapas.

![Diagrama mostrando a criação do projeto e etapas de implantação][Diagrama mostrando a criação do projeto e etapas de implantação]

1.  Abra o Visual Studio 2013 ou o Visual Studio 2013 Express for Web.

2.  No menu **Arquivo**, clique em **Novo Projeto**.

3.  Na caixa de diálogo **Novo Projeto**, clique em **C\#** \> **Web** \> **Aplicativo Web ASP.NET**. Se preferir, você pode escolher **Visual Basic**.

4.  Verifique se o **.NET Framework 4.5** está selecionado como a estrutura de destino.

5.  Nomeie o aplicativo **MyExample** e clique em **OK**.

    ![Caixa de diálogo Novo Projeto][Caixa de diálogo Novo Projeto]

6.  Na caixa de diálogo **Novo Projeto ASP.NET**, selecione o modelo **MVC**. Se prefere trabalhar com Web Forms ASP.NET, você pode selecionar o modelo **Web Forms**.

    [MVC e Web Forms][MVC e Web Forms] são estruturas do ASP.NET para criar sites. Para este tutorial, você pode escolher qualquer uma das duas opções; porém, se você escolher Web Forms, você precisará editar *Default.aspx* posteriormente nos pontos em que o tutorial instruir você a editar *Index.cshtml*.

7.  Clique em **Alterar Autenticação**.

    ![Caixa de diálogo Novo Projeto ASP .NET][Caixa de diálogo Novo Projeto ASP .NET]

8.  Na caixa de diálogo **Alterar Autenticação**, clique em **Sem Autenticação** e clique em **OK**.

    ![Sem Autenticação][Sem Autenticação]

    O aplicativo função Web que você está criando não permite que os usuários façam logon. A seção [Próximas etapas][Próximas etapas] vincula a um tutorial que implementa autenticação e autorização.

9.  Na caixa de diálogo **Novo Projeto ASP.NET**, deixe as configurações sob **Azure** inalteradas e, por último, clique em **OK**.

    ![Caixa de diálogo Novo Projeto ASP .NET][1]

    As configurações padrão especificam que o Visual Studio crie um Site do Azure para seu projeto Web. Na próxima seção do tutorial, você implantará o projeto Web para o site recém-criado.

    (A legenda da caixa de seleção deve ser **Host na nuvem** ou **Criar recursos remotos**. Em ambos os casos, o efeito é o mesmo.)

10. Se você ainda não tiver entrado no Azure, o Visual Studio solicitará a você que o faça. Clique em **Entrar**.

    ![Entrar no Azure][Entrar no Azure]

11. Na caixa de diálogo **Entrar no Azure**, insira a ID e a senha da conta que você usa para gerenciar sua assinatura do Azure.

    Quando estiver conectado, a caixa de diálogo **Configurar Definições de Site do Azure** perguntará quais recursos você deseja criar.

    ![Conectado no Azure][Conectado no Azure]

12. O Visual Studio fornece um **Nome de site** padrão, que o Azure usará como o prefixo de URL do aplicativo. Se preferir, digite um nome de site diferente.

    A URL completa consistirá no que você digitar aqui mais o domínio *.azurewebsites.net* (conforme exibido ao lado da caixa de texto **Nome do Site**). Por exemplo, se o nome do site for `MyExample6442`, a URL será `MyExample6442.azurewebsites.net`. A URL precisa ser exclusiva. Se outra pessoa já tiver usado o valor digitado por você, você verá um ponto de exclamação vermelho à direita em vez de uma marca de verificação verde, e você precisará inserir outro nome de site.

13. Na lista suspensa **Região**, escolha o local mais próximo de você.

    Essa configuração especifica em qual datacenter do Azure o seu site será executado. Para este tutorial, você pode selecionar qualquer região e isso não resultará em nenhuma diferença perceptível, mas para um site de produção, é melhor que seu servidor Web esteja tão próximo quanto possível dos navegadores acessando seu site, para reduzir a [latência][latência].

14. Deixe os campos de banco de dados inalterados.

    Para este tutorial, você não está usando um banco de dados. A seção [Próximas etapas][Próximas etapas] no final do tutorial vincula a um tutorial que mostra como usar um banco de dados.

15. Clique em **OK**.

    Em poucos segundos, o Visual Studio cria o projeto Web na pasta especificada e cria o site na região do Azure que você especificou.

    A janela **Gerenciador de Soluções** mostra os arquivos e as pastas no novo projeto.

    ![Gerenciador de Soluções][Gerenciador de Soluções]

    A **Atividade de Publicação da Web** mostra que o site foi criado.

    ![Site criado][Site criado]

    E você pode ver o site no Gerenciador de Servidores.

    ![Site criado][2]

## Implantar o aplicativo no Azure

1.  Na janela **Atividade de Publicação da Web**, clique em **Publicar MyExample nesse site agora**.

    ![Site criado][3]

    Em poucos segundos, o assistente de **Publicar Web** é exibido.

    As configurações das quais do Visual Studio precisa para implantar seu projeto no Azure foram salvas em um *perfil de publicação*. O assistente permite que você revise e altere essas mudanças.

2.  Na guia **Conexão** do assistente **Publicar na Web**, clique em **Validar Conexão** para certificar-se de que o Visual Studio pode se conectar ao Azure para implantar o projeto Web.

    ![Validar a conexão][Validar a conexão]

    Quando a conexão tiver sido validada, uma marca de seleção verde será mostrada ao lado do botão **Validar Conexão**.

3.  Clique em **Próximo**.

    ![Conexão validada com êxito][Conexão validada com êxito]

4.  Na guia **Configurações**, clique em **Avançar**.

    ![Guia Configurações][Guia Configurações]

    Você pode aceitar os valores padrão para **Configuração** e **Opções de Publicação de Arquivos**.

    O menu suspenso **Configuração** permite que você implante uma compilação de Depuração para depuração remota. A seção [Próximas etapas][Próximas etapas] vincula a um tutorial que mostra como executar o Visual Studio no modo de depuração remotamente.

    Se você expandir **Opções de Publicação de Arquivos**, verá diversas configurações que permitem a você gererenciar cenários que não se aplicam a este tutorial:

    -   Remova os arquivos adicionais presentes no destino.

        Exclua quaisquer arquivos do servidor que não estejam em seu projeto. Você pode precisar disso se estiver implantando um projeto em um site para o qual você já implantou um projeto diferente, anteriormente.

    -   Faça a pré-compilação durante a publicação.

        Isso pode reduzir os tempos de aquecimento para primeira solicitação em sites grandes.

    -   Exclua os arquivos da pasta App\_Data.

        Para teste, algumas vezes você tem um arquivo de banco de dados de SQL Server na pasta App\_Data que você não deseja implantar na produção.

5.  Na guia **visualização**, clique em **Iniciar Visualização**.

    ![Botão StartPreview na guia Visualização][Botão StartPreview na guia Visualização]

    A guia exibe uma lista dos arquivos que serão copiados no servidor. A exibição da visualização não é obrigatória para publicar o aplicativo, mas é uma função reconhecidamente útil.

6.  Clique em **Publicar**.

    ![Saída do arquivo StartPreview][Saída do arquivo StartPreview]

    O Visual Studio inicia o processo de cópia dos arquivos no servidor do Azure.

    As janelas **Saída** e **Atividade de Publicação da Web** mostram as ações de implantação que foram executadas e relata a conclusão bem-sucedida da implantação.

    ![Relatório de implantação bem-sucedida na janela de saída][Relatório de implantação bem-sucedida na janela de saída]

    Após a implantação bem-sucedida, o navegador padrão abre automaticamente a URL do site implantado e
    o aplicativo que você criou agora está em execução na nuvem. A URL na barra de endereços do navegador mostra que o site está sendo carregado da Internet.

    ![Site em execução no Azure][Site em execução no Azure]

7.  Feche o navegador.

## Fazer uma alteração e reimplantar

Nesta seção do tutorial, você altera o título **h1** da home page, executa o projeto localmente no seu computador de desenvolvimento para verificar a alteração e, em seguida, implanta a alteração no Azure.

1.  Abra os arquivos *Views/Home/Index.cshtml* ou *.vbhtml* no **Gerenciador de Soluções**, altere o título **h1** de "ASP.NET" para "ASP.NET e Azure" e salve o arquivo.

    ![index.cshtml de MVC][index.cshtml de MVC]

    ![Alteração de h1 no MVC][Alteração de h1 no MVC]

2.  Pressione CTRL + F5 para ver o título atualizado executando o site no seu computador local.

    ![Site em execução localmente][Site em execução localmente]

    A URL `http://localhost` mostra que ele está sendo executado no computador local. Por padrão, ele é executado no IIS Express, que é uma versão simples do IIS desenvolvida para uso durante o desenvolvimento de aplicativos Web.

3.  Feche o navegador.

4.  No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e escolha **Publicar**.

    ![Selecionar Publicar][Selecionar Publicar]

    A guia Visualizar do assistente **Publicar Web** é exibida. Se for necessário alterar quaisquer configurações de publicação, você poderá escolher uma guia diferente, mas agora tudo o que você deseja fazer é reimplantar com as mesmas configurações.

5.  No assistente **Publicar na Web**, clique em **Publicar**.

    ![Clicar em Publicar][Clicar em Publicar]

    O Visual Studio implanta o projeto no Azure e abre o site no navegador padrão.

    ![Site implantado alterado][Home page do site]

**Dica:** Você pode habilitar a barra de ferramentas **Publicação Web Com Um Clique** para implantação mais rápida. Clique em **Visualizar** \> **Barras de ferramentas** e, em seguida, selecione **Publicação Web Com Um Clique**. A barra de ferramentas permite a você selecionar um perfil, clicar em um botão para publicar ou clicar em um botão para abrir o assistente **Publicar Web**.

![Barra de ferramentas Publicação Web com Um Clique][Barra de ferramentas Publicação Web com Um Clique]

## Monitore e gerencie o site no Portal de Gerenciamento

O [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure] é uma interface da Web que permite a você gerenciar e monitorar seus serviços do Azure, como o site que você acabou de criar. Nesta seção do tutorial, você descobre um pouco do que pode fazer no portal.

1.  Em seu navegador, vá até [][]<http://manage.windowsazure.com></a> e entre com suas credenciais do Azure.

    O portal exibe uma lista de seus serviços do Azure.

2.  Clique no nome do seu site.

    ![Portal home page com novo site web chamado][Portal home page com novo site web chamado]

3.  Clique na guia **Painel**.

    A guia **Painel** exibe uma visão geral das estatísticas de utilização e link para uma série de funções de gerenciamento de site comumente utilizadas. Em **Visão Rápida**, você também encontrará um link para a home page de seu aplicativo.

    ![Guia de painel do site do portal][Guia de painel do site do portal]

    Nesse período, seu site não teve muito tráfego e talvez não exiba nada no gráfico. Se você navegar até seu aplicativo, atualize a página algumas vezes, então atualize o portal da página **Painel**; você verá algumas estatísticas serem exibidas. Você pode clicar na guia **Monitorar** para obter mais detalhes.

4.  Clique na guia **Configurar**.

    A guia [Configurar][Configurar] permite que você controle a versão do .NET utilizada no site, habilita recursos como [WebSockets][WebSockets] e [registro em log de diagnóstico][registro em log de diagnóstico], definir [valores de cadeia de conexão][valores de cadeia de conexão] e mais.

    ![Guia Configurar site do portal][Guia Configurar site do portal]

5.  Clique na guia **Escala**.

    Para as camadas pagas do serviço de Sites, a guia [Escala][Escala] permite que você controle o tamanho e número das máquinas que atendem seu aplicativo Web para gerenciar variações no tráfego.

    Você pode dimensionar manualmente, ou então configurar critérios ou agendamentos para dimensionamento automático.

    ![Guia Escala de site do portal][Guia Escala de site do portal]

Esses são apenas alguns dos recursos do Portal de Gerenciamento. Você também pode criar novos sites, excluir sites existentes, parar e reiniciar sites e gerenciar outros tipos de serviços do Azure, como bancos de dados e máquinas virtuais.

**Dica:** Há um novo Portal de Gerenciamento na visualização que eventualmente substituirá aquele que você esteve utilizando. Para obter mais informações, consulte [Portal de Visualização do Azure][Portal de Visualização do Azure].

## Próximas etapas

Neste tutorial, você viu como criar um aplicativo Web simples e implantá-lo em um Site do Azure. Estes são alguns tópicos e recursos relacionados para saber mais sobre eles.

-   Outras maneiras de implantar um projeto Web

    Neste tutorial, você viu a maneira mais rápida de criar um site e implantá-lo em uma única operação. Para uma visão geral de outras maneiras de implantar, usando o Visual Studio ou por [implantação automática][implantação automática] de um [sistema de controle do código-fonte][sistema de controle do código-fonte], consulte [Como implantar um Site do Azure][Como implantar um Site do Azure].

    O Visual Studio também pode gerar scripts do Windows PowerShell, que permitem a você automatizar a implantação. Para obter mais informações, consulte [Automatizar tudo (Compilando aplicativos de nuvem do mundo real com o Azure)][Automatizar tudo (Compilando aplicativos de nuvem do mundo real com o Azure)].

-   Como gerenciar um site no Visual Studio

    Para obter informações sobre o que você pode fazer no **Gerenciador de Servidores**, consulte [Solucionando problemas de Sites do Azure no Visual Studio][Solucionando problemas de Sites do Azure no Visual Studio].

-   Como solucionar problemas de um site

    O Visual Studio fornece ferramentas que facilitam a exibição dos logs do Azure à medida que são gerados em tempo real. Você também pode executar em modo de depuração remotamente no Azure. Para obter mais informações, consulte [Solucionando problemas de Sites do Azure no Visual Studio][Solucionando problemas de Sites do Azure no Visual Studio].

-   Como adicionar funcionalidade de banco de dados e autorização

    Para um tutorial que mostra como acessar um banco de dados e restringe algumas funções do site para usuários autorizados, consulte [implantar um aplicativo ASP.NET MVC seguro com associação, OAuth e banco de dados SQL em um site do Azure][implantar um aplicativo ASP.NET MVC seguro com associação, OAuth e banco de dados SQL em um site do Azure].

-   Como adicionar um nome de domínio personalizado e SSL

    Para obter informações sobre como utilizar SSL e seu próprio domínio (por exemplo www.contoso.com, em vez de contoso.azurewebsites.net), consulte os recursos a seguir:

    -   [Configurando um nome de domínio personalizado para um Site do Azure][Configurando um nome de domínio personalizado para um Site do Azure].
    -   [Habilitar HTTPS para um site do Azure][Habilitar HTTPS para um site do Azure]
-   Como evitar tempo de espera para voltar ao funcionamento após intervalos em ociosidade

    Por padrão, os sites serão descarregados se estiverem ociosos durante um determinado período de tempo. A primeira solicitação após isso acontecer precisa aguardar que o site seja carregado novamente. Para evitar esse tempo de espera, você pode habilitar o recurso AlwaysOn. Para obter mais informações, consulte as opções de configuração em [Como configurar sites][Como configurar sites].

-   Como adicionar recursos em tempo real como chat

    Se o seu site for incluir recursos em tempo real (como um serviço de chat, um jogo, um indicador de ações, entre outros), você poderá obter o melhor desempenho utilizando o [ASP.NET SignalR][ASP.NET SignalR] com o método de transporte [WebSockets][WebSockets]. Para obter mais informações, consulte [Usando SignalR com sites do Windows Azure][Usando SignalR com sites do Windows Azure].

-   Como escolher entre Sites, Serviços de nuvem e VMs do Azure para aplicativos Web

    No Azure você pode executar aplicativos Web nos Sites, conforme mostrado neste tutorial ou em serviços de nuvem ou máquinas virtuais. Para obter mais informações, consulte [Modelos de execução do Azure][Modelos de execução do Azure] e [Sites, Serviços de Nuvem e VMs do Azure: Quando usar qual opção?][Sites, Serviços de Nuvem e VMs do Azure: Quando usar qual opção?].

  [Visual Studio 2013]: /en-us/develop/net/tutorials/get-started/ "Visual Studio 2013"
  [Visual Studio 2012]: /en-us/develop/net/tutorials/get-started-vs2012/ "Visual Studio 2012"
  [Home page do site]: ./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png
  [abrir uma conta do Azure gratuitamente]: /en-us/pricing/free-trial/?WT.mc_id=A261C142F
  [ativar benefícios para assinantes do MSDN]: /en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
  [Configurar o ambiente de desenvolvimento]: #set-up-the-development-environment
  [Criar um aplicativo Web ASP.NET no Visual Studio]: #create-an-aspnet-web-application
  [Implantar o aplicativo no Azure]: #deploy-the-application-to-azure
  [Fazer uma alteração e reimplantar]: #make-a-change-and-redeploy
  [Monitore e gerencie o site no Portal de Gerenciamento]: #monitor-and-manage-the-site-in-the-management-portal
  [Próximas etapas]: #next-steps
  [install-sdk-2013-only]: ../includes/install-sdk-2013-only.md
  [Diagrama mostrando a criação do projeto e etapas de implantação]: ./media/web-sites-dotnet-get-started-vs2013/createdeploydiagram.png
  [Caixa de diálogo Novo Projeto]: ./media/web-sites-dotnet-get-started-vs2013/GS13newprojdb.png
  [MVC e Web Forms]: http://www.asp.net/get-started/websites
  [Caixa de diálogo Novo Projeto ASP .NET]: ./media/web-sites-dotnet-get-started-vs2013/GS13changeauth.png
  [Sem Autenticação]: ./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png
  [1]: ./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png
  [Entrar no Azure]: ./media/web-sites-dotnet-get-started-vs2013/signin.png
  [Conectado no Azure]: ./media/web-sites-dotnet-get-started-vs2013/configuresitesettings.png
  [latência]: http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090
  [Gerenciador de Soluções]: ./media/web-sites-dotnet-get-started-vs2013/solutionexplorer.png
  [Site criado]: ./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated1.png
  [2]: ./media/web-sites-dotnet-get-started-vs2013/siteinse.png
  [3]: ./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated.png
  [Validar a conexão]: ./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnection.png
  [Conexão validada com êxito]: ./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnectionSuccess.png
  [Guia Configurações]: ./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png
  [Botão StartPreview na guia Visualização]: ./media/web-sites-dotnet-get-started-vs2013/GS13Preview.png
  [Saída do arquivo StartPreview]: ./media/web-sites-dotnet-get-started-vs2013/GS13previewoutput.png
  [Relatório de implantação bem-sucedida na janela de saída]: ./media/web-sites-dotnet-get-started-vs2013/PublishOutput.png
  [Site em execução no Azure]: ./media/web-sites-dotnet-get-started-vs2013/GS13deployedsite.png
  [index.cshtml de MVC]: ./media/web-sites-dotnet-get-started-vs2013/index.png
  [Alteração de h1 no MVC]: ./media/web-sites-dotnet-get-started-vs2013/mvcandazure.png
  [Site em execução localmente]: ./media/web-sites-dotnet-get-started-vs2013/localandazure.png
  [Selecionar Publicar]: ./media/web-sites-dotnet-get-started-vs2013/choosepublish.png
  [Clicar em Publicar]: ./media/web-sites-dotnet-get-started-vs2013/clickpublish.png
  [Barra de ferramentas Publicação Web com Um Clique]: ./media/web-sites-dotnet-get-started-vs2013/weboneclickpublish.png
  [Portal de Gerenciamento do Azure]: /en-us/services/management-portal/
  [Portal home page com novo site web chamado]: ./media/web-sites-dotnet-get-started-vs2013/portalhome.png
  [Guia de painel do site do portal]: ./media/web-sites-dotnet-get-started-vs2013/portaldashboard.png
  [Configurar]: /pt-br/documentation/articles/web-sites-configure//
  [WebSockets]: /blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/
  [registro em log de diagnóstico]: /pt-br/documentation/articles/web-sites-enable-diagnostic-log/
  [valores de cadeia de conexão]: /blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/
  [Guia Configurar site do portal]: ./media/web-sites-dotnet-get-started-vs2013/portalconfigure.png
  [Escala]: /pt-br/documentation/articles/web-sites-scale/
  [Guia Escala de site do portal]: ./media/web-sites-dotnet-get-started-vs2013/portalscale.png
  [Portal de Visualização do Azure]: /en-us/overview/preview-portal/
  [implantação automática]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery
  [sistema de controle do código-fonte]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control
  [Como implantar um Site do Azure]: /pt-br/documentation/articles/web-sites-deploy/"
  [Automatizar tudo (Compilando aplicativos de nuvem do mundo real com o Azure)]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything
  [Solucionando problemas de Sites do Azure no Visual Studio]: /en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
  [implantar um aplicativo ASP.NET MVC seguro com associação, OAuth e banco de dados SQL em um site do Azure]: /en-us/develop/net/tutorials/web-site-with-sql-database/
  [Configurando um nome de domínio personalizado para um Site do Azure]: /pt-br/documentation/articles/web-sites-custom-domain-name/
  [Habilitar HTTPS para um site do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-configure-ssl-certificate/
  [Como configurar sites]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-configure/
  [ASP.NET SignalR]: http://www.asp.net/signalr
  [Usando SignalR com sites do Windows Azure]: http://www.asp.net/signalr/overview/signalr-20/getting-started-with-signalr-20/using-signalr-with-windows-azure-web-sites
  [Modelos de execução do Azure]: /en-us/develop/net/fundamentals/compute/
  [Sites, Serviços de Nuvem e VMs do Azure: Quando usar qual opção?]: /en-us/manage/services/web-sites/choose-web-app-service/
