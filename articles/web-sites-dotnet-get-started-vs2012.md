<properties urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Get started with Azure Websites and ASP.NET" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra"></tags>

# Introdução aos Sites do Azure e ASP.NET

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/develop/net/tutorials/get-started/" title="Visual Studio 2013">Visual Studio 2013</a> <a href="/en-us/develop/net/tutorials/get-started-vs2012/" title="Visual Studio 2012" class="current">Visual Studio 2012</a></div>

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong><p>Uma <a href="/en-us/develop/net/tutorials/get-started/">vers&atilde;o mais recente deste tutorial</a> est&aacute; dispon&iacute;vel. Voc&ecirc; ainda pode seguir esta vers&atilde;o se quiser usar o Visual Studio 2012, mas ela n&atilde;o mostra todos os recursos mais recentes do Azure SDK.</p></div>

Este tutorial mostra como implantar um aplicativo Web ASP.NET em um site do Azure usando o assistente Publicar Web no Visual Studio 2012 ou no Visual Studio 2012 for Web Express. Se preferir, você poderá seguir as etapas do tutorial usando o Visual Studio 2010 ou o Visual Web Developer Express 2010.

É possível abrir uma conta do Azure gratuitamente e, se você ainda não tiver o Visual Studio 2012, o SDK instalará automaticamente o Visual Studio 2012 para o Web Express. Portanto, você pode começar a desenvolver para o Azure de maneira totalmente gratuita.

Este tutorial pressupõe que você não tem nenhuma experiência anterior com o Azure. Ao concluir este tutorial, você terá um aplicativo Web simples em funcionamento na nuvem.

Você aprenderá a:

-   Como habilitar seu computador para desenvolvimento do Azure ao instalar o SDK do Azure.
-   Como criar um projeto MVC 4 do Visual Studio ASP.NET e publicá-lo em um site do Azure.

A ilustração a seguir mostra o aplicativo concluído:

![Site de exemplo][]

<div class="dev-callout"><p><strong>Observa&ccedil;&atilde;o</strong> Para concluir este tutorial, voc&ecirc; precisar&aacute; de uma conta do Azure. Se n&atilde;o tiver uma conta, voc&ecirc; poder&aacute; <a href="/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">ativar os benef&iacute;cios de assinante MSDN</a> ou <a href="/en-us/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">inscrever-se para uma avalia&ccedil;&atilde;o gratuita</a>.</p></div>

### Segmentos do tutorial

1.  [Configurar o ambiente de desenvolvimento][]
2.  [Criar um site no Azure][]
3.  [Criar um aplicativo ASP.NET MVC 4][]
4.  [Implantar o aplicativo no Azure][]
5.  [Próximas etapas][]

[WACOM.INCLUDE [install-sdk-2012-only][]]

## <a name="setupwindowsazure"></a><span class="short-header"></span>Criar um site

A próxima etapa é criar o site do Azure.

1.  No [Portal de Gerenciamento do Azure][], clique em **Sites** e, em seguida, clique em **Novo**.

	![Novo site][]

1.  Clique em **Criação Rápida**.

    ![Criação Rápida][]

2.  Na etapa **Criar Site** do assistente, insira uma cadeia de caracteres na caixa **URL** a ser usada como a URL exclusiva de seu aplicativo. A URL completa consistirá no que você digitar aqui mais o sufixo que você vê ao lado da caixa de texto. A ilustração mostra **example1**, mas se alguém já estiver usando essa cadeia para uma URL, será necessário digitar um valor diferente.

3.  Na lista suspensa **Região**, escolha a região mais próxima de você.
    Esta configuração especifica de qual centro de dados seu site será executado.

4.  Clique no botão **Criar Site**.

    ![Criar um novo site][]

    O Portal de Gerenciamento do Azure retorna para a página Sites e a coluna **Status** mostra que o site está sendo criado. Depois de um tempo (normalmente menos de um minuto), a coluna **Status** mostra que o site foi criado com êxito. Na barra de navegação à esquerda, o número de sites presentes na conta é exibido próximo ao ícone **Sites**.

    ![Página Sites do Portal de Gerenciamento, site criado][]

## <a name="createmvc4app"></a><span class="short-header"></span>Criar um aplicativo ASP.NET MVC 4

Você criou um Site do Azure, mas ainda não há conteúdo nele. A próxima etapa é criar o projeto de aplicativo Web do Visual Studio que será publicado no Azure.

### Criar o projeto

1.  Inicie o Visual Studio 2012 ou o Visual Studio 2012 for Web Express.

2.  No menu **Arquivo**, clique em **Novo** e em **Projeto**.

	![Novo projeto no menu Arquivo][]

1.  Na caixa de diálogo **Novo Projeto**, expanda **C\#**, selecione **Web** em **Modelos instalados** e, em seguida, selecione **Aplicativo Web ASP.NET MVC 4**.

2.  Verifique se **.NET Framework 4.5** está selecionado como a estrutura de destino.

3.  Nomeie o aplicativo **MyExample** e clique em **OK**.

	![Caixa de diálogo Novo Projeto][]

1.  Na caixa de diálogo **Novo Projeto ASP.NET MVC 4**, selecione o modelo **Aplicativo de Internet** e clique em **OK**.

	![Caixa de diálogo Novo Projeto ASP.NET MVC 4][]

### Executar o aplicativo localmente

1.  Pressione **CTRL**+**F5** para executar o aplicativo. A home page do aplicativo é exibida no navegador padrão.

	![Site em execução localmente][]

Isso é tudo o que é preciso fazer para criar um aplicativo simples que você poderá implantar no Azure.

## <a name="deploytowindowsazure"></a><span class="short-header"></span>Implantar o aplicativo no Azure

1.  No Visual Studio, clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Publicar** no menu de contexto.

    ![Publicar no menu de contexto do projeto][]

	O assistente de **Publicar Web** é aberto.

1.  Na guia **Perfil** do assistente de **Publicar Web**, clique em **Importar**.

    ![Importar configurações de publicação][]

	A caixa de diálogo **Importar Perfil de Publicação** é aberta.

1.  Se você ainda não adicionou a assinatura do Azure no Visual Studio, execute as etapas a seguir. Nestas etapas, adicione a sua assinatura para que a lista suspensa em **Importar de um site do Azure** inclua o seu site.

    -   Na caixa de diálogo **Importar o Perfil de Publicação**, clique em **Importar de um site do Azure** e selecione **Adicionar assinatura do Azure**.

    ![Adicionar Assinatura do Azure][]

    -   Na caixa de diálogo **Importar Assinaturas do Azure**, clique em **Baixar arquivo de assinatura**.

    ![baixar arquivo de assinatura][]

    -   Na janela do seu navegador, salve o arquivo *.publishsettings*.

    ![baixar arquivo .publishsettings][]

    -   Na caixa de diálogo **Importar Assinaturas do Azure**, clique em **Procurar** e navegue até o arquivo *.publishsettings*.

    ![baixar sub][]

    -   Clique em **Importar**.

    ![import][]

    > [WACOM.NOTE]
    > O arquivo .publishsettings contém suas credenciais (sem codificação) que são usadas para administrar suas assinaturas e serviços do Azure. A melhor prática de segurança para este arquivo é armazená-lo temporariamente fora dos diretórios de origem (por exemplo, na pasta Libraries\\Documents) e, em seguida, excluí-la após a conclusão da importação. Um usuário mal-intencionado que obtenha acesso ao arquivo .publishsettings pode editar, criar e excluir os serviços do Azure.

2.  Na caixa de diálogo **Importar o perfil de publicação**, selecione **Importar de um site do Azure**, selecione seu site na lista suspensa e, em seguida, clique em **OK**.

    ![Importar Perfil de Publicação][]

3.  Na guia **Conexão**, clique em **Validar Conexão** para verificar se as configurações estão corretas.

    ![Validar a conexão][]

4.  Quando a conexão tiver sido validada, uma marca de seleção verde será mostrada ao lado do botão **Validar Conexão**. Clique em **Próximo**.

    ![Conexão validada com êxito][]

5.  Na guia **Configurações**, desmarque a opção **Usar esta cadeia de conexão no tempo de execução**, já que este aplicativo não está usando um banco de dados. Você pode aceitar as configurações padrão para os itens restantes nesta página. Você está implantando uma configuração de compilação de versão e não precisa excluir arquivos no servidor de destino, pré-compilar o aplicativo ou excluir arquivos na pasta App\_Data.
    Clique em **Avançar**.

    ![Guia Configurações][]

6.  Na guia **visualização**, clique em **Iniciar Visualização**.

    ![Botão StartPreview na guia Visualização][]

    A guia exibe uma lista dos arquivos que serão copiados no servidor. A exibição da visualização não é obrigatória para publicar o aplicativo, mas é uma função reconhecidamente útil. Nesse caso, você não precisa fazer nada com a lista de arquivos exibida.

    ![Saída do arquivo StartPreview][]

7.  Clique em **Publicar**. O Visual Studio inicia o processo de cópia dos arquivos no servidor do Azure.

8.  A janela **Saída** mostra quais ações de implantação foram executadas e os relatórios da conclusão com êxito da implantação.

    ![Relatório de implantação bem-sucedida na janela de saída][]

9.  Após a implantação bem-sucedida, o navegador padrão abre automaticamente a URL do site implantado.
    O aplicativo que você criou agora está em execução na nuvem.

    ![Site em execução no Azure][Site de exemplo]

## <a name="nextsteps"></a><span class="short-header"></span>Próximas etapas

Neste tutorial, você viu como implantar um aplicativo Web simples em um site do Azure. Outros recursos estão disponíveis para mostrar a você como gerenciar, dimensionar e solucionar problemas do site, como adicionar a funcionalidade de autorização, autenticação de banco de dados e como decidir se seu aplicativo deve ser executado em um Serviço de Nuvem do Azure em vez de em um Site do Azure.

### Como gerenciar um site

Depois que terminar com o site, você poderá excluí-lo. Também poderá deixá-lo offline temporariamente ou alterar suas configurações. Você pode fazer algumas destas funções diretamente do **Gerenciador de Servidores** no Visual Studio.

![Sites do Azure no Gerenciador de Servidores][]

![Configuração do site no Visual Studio][]

Para excluir seu site, será possível usar o Portal de Gerenciamento do Azure. A captura de tela a seguir mostra os botões **Parar**, **Reiniciar** e **Excluir** na guia **Painel** do portal de gerenciamento.

![Guia do Painel de Portal de Gerenciamento][]

Você pode alterar as configurações do site na guia **Configurar**. Para obter mais informações, consulte [Como gerenciar sites (a página pode estar em inglês)][].

### Como dimensionar um site

Quando seu site é público e começa a receber mais tráfego, os tempos de resposta podem se tornar lentos. Para corrigir isso, você pode adicionar facilmente recursos do servidor na guia **Dimensionar** do portal de gerenciamento.

![Guia da Escala do Portal de Gerenciamento][]

Para obter mais informações, consulte [Como dimensionar um site (a página pode estar em inglês)][]. (A adição de recursos do servidor para dimensionar um site não é gratuita.)

### Como solucionar problemas de um site

Convém examinar a saída de rastreamento ou o log para obter ajuda na solução de problemas. O Visual Studio fornece ferramentas internas para facilitar a exibição dos logs do Azure à medida que são gerados em tempo real.

![Logs no Visual Studio][]

Para obter mais informações, consulte [Solucionando problemas de Sites do Azure no Visual Studio (a página pode estar em inglês)][].

### Como adicionar funcionalidade de banco de dados e autorização

A maioria dos sites de produção usam um banco de dados e restringem algumas funções de site a usuários autorizados determinados. Para um tutorial que mostra como começar com o acesso ao banco de dados, autenticação e autorização, consulte [Implantar um aplicativo ASP.NET MVC seguro com associação, OAuth e Banco de Dados SQL a um Site do Azure (a página pode estar em inglês)][].

### Como decidir se seu aplicativo deve ser executado em um Serviço de Nuvem

Em algumas situações, convém executar o aplicativo em um Serviço de Nuvem do Azure em vez de em um Site do Azure. Para obter mais informações, consulte [Modelos de execução do Azure][] e [Comparação de Máquinas Virtuais do Azure][]. Para obter uma série de tutoriais que mostram como criar um aplicativo Web ASP.NET de várias camadas e implantá-lo em um Serviço de Nuvem, consulte [Aplicativo .NET de várias camadas usando tabelas de armazenamento, filas e blobs (a página pode estar em inglês)][].

  [Visual Studio 2013]: /en-us/develop/net/tutorials/get-started/ "Visual Studio 2013"
  [Visual Studio 2012]: /en-us/develop/net/tutorials/get-started-vs2012/ "Visual Studio 2012"
  [versão mais recente deste tutorial]: /en-us/develop/net/tutorials/get-started/
  [Site de exemplo]: ./media/web-sites-dotnet-get-started-vs2012/DeployedWebSite.png
  [ativar os benefícios de assinante MSDN]: /en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
  [inscrever-se para uma avaliação gratuita]: /en-us/pricing/free-trial/?WT.mc_id=A261C142F
  [Configurar o ambiente de desenvolvimento]: #setupdevenv
  [Criar um site no Azure]: #setupwindowsazure
  [Criar um aplicativo ASP.NET MVC 4]: #createmvc4app
  [Implantar o aplicativo no Azure]: #deploytowindowsazure
  [Próximas etapas]: #nextsteps
  [install-sdk-2012-only]: ../includes/install-sdk-2012-only.md
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Novo site]: ./media/web-sites-dotnet-get-started-vs2012/WebSiteNew.png
  [Criação Rápida]: ./media/web-sites-dotnet-get-started-vs2012/ClickQuickCreate.png
  [Criar um novo site]: ./media/web-sites-dotnet-get-started-vs2012/CreateWebsite.png
  [Página Sites do Portal de Gerenciamento, site criado]: ./media/web-sites-dotnet-get-started-vs2012/WebSiteStatusRunning.png
  [Novo projeto no menu Arquivo]: ./media/web-sites-dotnet-get-started-vs2012/NewVSProject.png
  [Caixa de diálogo Novo Projeto]: ./media/web-sites-dotnet-get-started-vs2012/NewMVC4WebApp.png
  [Caixa de diálogo Novo Projeto ASP.NET MVC 4]: ./media/web-sites-dotnet-get-started-vs2012/InternetAppTemplate.png
  [Site em execução localmente]: ./media/web-sites-dotnet-get-started-vs2012/AppRunningLocally.png
  [Publicar no menu de contexto do projeto]: ./media/web-sites-dotnet-get-started-vs2012/PublishVSSolution.png
  [Importar configurações de publicação]: ./media/web-sites-dotnet-get-started-vs2012/ImportPublishSettings.png
  [Adicionar Assinatura do Azure]: ./media/web-sites-dotnet-get-started-vs2012/rzAddWAsub.png
  [baixar arquivo de assinatura]: ./media/web-sites-dotnet-get-started-vs2012/rzDownLoadDownload.png
  [baixar arquivo .publishsettings]: ./media/web-sites-dotnet-get-started-vs2012/rzDown2.png
  [baixar sub]: ./media/web-sites-dotnet-get-started-vs2012/rzDownLoadBrowse.png
  [import]: ./media/web-sites-dotnet-get-started-vs2012/rzImp.png
  [Importar Perfil de Publicação]: ./media/web-sites-dotnet-get-started-vs2012/ImportPublishProfile.png
  [Validar a conexão]: ./media/web-sites-dotnet-get-started-vs2012/ValidateConnection.png
  [Conexão validada com êxito]: ./media/web-sites-dotnet-get-started-vs2012/ValidateConnectionSuccess.png
  [Guia Configurações]: ./media/web-sites-dotnet-get-started-vs2012/PublishWebSettingsTab.png
  [Botão StartPreview na guia Visualização]: ./media/web-sites-dotnet-get-started-vs2012/PublishWebStartPreview.png
  [Saída do arquivo StartPreview]: ./media/web-sites-dotnet-get-started-vs2012/PublishWebStartPreviewOutput.png
  [Relatório de implantação bem-sucedida na janela de saída]: ./media/web-sites-dotnet-get-started-vs2012/PublishOutput.png
  [Sites do Azure no Gerenciador de Servidores]: ./media/web-sites-dotnet-get-started-vs2012/ServerExplorerWSSettings.png
  [Configuração do site no Visual Studio]: ./media/web-sites-dotnet-get-started-vs2012/WSConfigurationInVS.png
  [Guia do Painel de Portal de Gerenciamento]: ./media/web-sites-dotnet-get-started-vs2012/MPStopStartDelete.png
  [Como gerenciar sites (a página pode estar em inglês)]: /en-us/manage/services/web-sites/how-to-manage-websites/
  [Guia da Escala do Portal de Gerenciamento]: ./media/web-sites-dotnet-get-started-vs2012/MPScale.png
  [Como dimensionar um site (a página pode estar em inglês)]: /en-us/manage/services/web-sites/how-to-scale-websites/
  [Logs no Visual Studio]: ./media/web-sites-dotnet-get-started-vs2012/LogsInVS.png
  [Solucionando problemas de Sites do Azure no Visual Studio (a página pode estar em inglês)]: /en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
  [Implantar um aplicativo ASP.NET MVC seguro com associação, OAuth e Banco de Dados SQL a um Site do Azure (a página pode estar em inglês)]: /en-us/develop/net/tutorials/web-site-with-sql-database/
  [Modelos de execução do Azure]: /en-us/develop/net/fundamentals/compute/
  [Comparação de Máquinas Virtuais do Azure]: http://azure.microsoft.com/en-us/documentation/articles/choose-web-site-cloud-service-vm/
  [Aplicativo .NET de várias camadas usando tabelas de armazenamento, filas e blobs (a página pode estar em inglês)]: /en-us/develop/net/tutorials/multi-tier-web-site/1-overview/
