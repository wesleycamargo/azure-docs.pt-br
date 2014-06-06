<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Comece a usar o Azure e o ASP.NET" authors=""  solutions="" writer="tdykstra" manager="wpickett" editor="mollybos"  />



# Comece a usar o Azure e o ASP.NET

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/develop/net/tutorials/get-started/" title="Visual Studio 2013">Visual Studio 2013</a><a href="/pt-br/develop/net/tutorials/get-started-vs2012/" title="Visual Studio 2012" class="current">Visual Studio 2012</a></div>

<div class="dev-callout"><strong>Observação</strong><p>Uma <a href="/pt-br/develop/net/tutorials/get-started/">versão mais recente deste tutorial</a> está disponível. Você ainda pode seguir esta versão se quiser usar o Visual Studio 2012, mas ela não mostra todos os recursos mais recentes do Azure SDK.</p></div>

Este tutorial mostra como implantar um aplicativo Web ASP.NET em um site do Azure usando o assistente Publicar Web no Visual Studio 2012 ou no Visual Studio 2012 for Web Express. Se preferir, você poderá seguir as etapas do tutorial usando o Visual Studio 2010 ou o Visual Web Developer Express 2010.

É possível abrir uma conta do Azure gratuitamente e, se você ainda não tiver o Visual Studio 2012, o SDK instalará automaticamente o Visual Studio 2012 for Web Express. Assim, você pode começar a desenvolver para o Azure de maneira totalmente gratuita.

Este tutorial pressupõe que você não tem nenhuma experiência anterior com o Azure. Ao concluir este tutorial, você terá um aplicativo Web simples em funcionamento na nuvem.
 
Você aprenderá:

* Como habilitar a máquina para o desenvolvimento do Azure instalando o SDK do Azure.
* Como criar um projeto do Visual Studio ASP.NET MVC 4 e publicá-lo em um Site do Azure.

A ilustração a seguir mostra o aplicativo concluído:

![Site de exemplo][DeployedWebSite]

<div class="dev-callout"><p><strong>Observação</strong> para concluir este tutorial, você precisará de uma conta do Azure. Se não tiver uma conta, você poderá <a href="/pt-br/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">ativar os benefícios de assinante MSDN</a> ou <a href="/pt-br/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">inscrever-se para uma avaliação gratuita</a>.</p></div>
 
### Segmentos do tutorial

1. [Definir o ambiente de desenvolvimento][]
2. [Criar um site no Azure][]
3. [Criar um aplicativo ASP.NET MVC 4][]
4. [Implantar o aplicativo no Azure][]
5. [Próximas etapas][]

[WACOM.INCLUDE [install-sdk-2012-only](../includes/install-sdk-2012-only.md)]

<h2><a name="setupwindowsazure"></a><span class="short-header">Criar site</span>Criar um site</h2>

A próxima etapa é criar o site do Azure.

1. No [Portal de Gerenciamento do Azure][PreviewPortal], clique em **Sites** e em **Novo**.

![Novo site][WebSiteNew]

2. Clique em **Criação Rápida**.
	
	![Criação Rápida][ClickQuickCreate]

3. Na etapa **Criar Site** do assistente, digite uma cadeia de caracteres na caixa **URL** para usar como URL exclusiva do seu aplicativo. A URL completa consistirá no que você digitar aqui mais o sufixo que você vê ao lado da caixa de texto. A ilustração mostra **example1**, mas se alguém já estiver usando essa cadeia para uma URL, será necessário digitar um valor diferente.

4. Na lista suspensa **Região**, escolha a região mais próxima de você.
Essa configuração especifica em qual data center seu site será executado. 

5. Clique na seta **Criar Site**.

	![Criar um novo site](./media/web-sites-dotnet-get-started-vs2012/CreateWebsite.png)

	O Portal de Gerenciamento retorna para a página Sites, e a coluna **Status** mostra que o site está sendo criado. Depois de um tempo (normalmente menos de um minuto), a coluna **Status** mostra que o site foi criado com êxito. Na barra de navegação à esquerda, o número de sites presentes na conta é exibido próximo ao ícone **Sites**.
	
	![Página Sites do Portal de Gerenciamento, site criado][WebSiteStatusRunning]

<h2><a name="createmvc4app"></a><span class="short-header">Criar o aplicativo</span>Criar um aplicativo ASP.NET MVC 4</h2>

Você criou um Site do Azure, mas ainda não há conteúdo nele. A próxima etapa é criar o projeto de aplicativo Web do Visual Studio que será publicado no Azure.

### Criar o projeto

1. Inicie o Visual Studio 2012 ou o Visual Studio 2012 for Web Express.

2. No menu **Arquivo**, clique em **Novo** e em **Projeto**.

![Novo projeto no menu Arquivo][NewVSProject]

3. Na caixa de diálogo **Novo Projeto**, expanda **C#**, selecione **Web** em **Modelos Instalados** e, em seguida, selecione **Aplicativo Web ASP.NET MVC 4**. 

3. Verifique se **.NET Framework 4.5** está selecionado como a estrutura de destino.

4. Nomeie o aplicativo **MyExample** e clique em **OK**.
	
![Caixa de diálogo Novo Projeto][NewMVC4WebApp]

5. Na caixa de diálogo **Novo Projeto ASP.NET MVC 4**, selecione o modelo **Aplicativo de Internet** e clique em **OK**.

![Caixa de diálogo Novo Projeto ASP.NET MVC 4][InternetAppTemplate]

### Executar o aplicativo localmente

1. Pressione **CTRL**+**F5** para executar o aplicativo. A home page do aplicativo é exibida no navegador padrão.

![Site em execução localmente][AppRunningLocally]

Isso é tudo o que é preciso fazer para criar um aplicativo simples que você poderá implantar no Azure.

<h2><a name="deploytowindowsazure"></a><span class="short-header">Implantar o aplicativo</span>Implantar o aplicativo no Azure</h2>

1. No Visual Studio, clique com o botão direito do mouse no projeto em **Gerenciador de Soluções** e selecione **Publicar** no menu de contexto.

	![Publicar no menu de contexto do projeto][PublishVSSolution]

O assistente de **Publicar Web** é aberto.

2. Na guia **Perfil** do assistente de **Publicar Web**, clique em **Importar**.

	![Importar configurações de publicação][ImportPublishSettings]

A caixa de diálogo **Importar Perfil de Publicação** é aberta.

3. Se você ainda não adicionou a assinatura do Azure no Visual Studio, execute as etapas a seguir. Nestas etapas você adiciona sua assinatura de forma que a lista suspensa em **Importar de um Site do Azure** incluirá o seu site.
    
	- Na caixa de diálogo **Importar Perfil de Publicação**, clique em **Importar de um Site do Azure** e clique em **Adicionar assinatura do Azure**. 
    
	![add Azure subscription](./media/web-sites-dotnet-get-started-vs2012/rzAddWAsub.png)
    
	- Na caixa de diálogo **Importar Assinaturas do Azure**, clique em **Baixar arquivo de assinatura**.
    
	![baixar arquivo de assinatura](./media/web-sites-dotnet-get-started-vs2012/rzDownLoadDownload.png)
    
	- Na janela do seu navegador, salve o arquivo *.publishsettings*.
    
	![baixar arquivo .publishsettings](./media/web-sites-dotnet-get-started-vs2012/rzDown2.png)

	- Na caixa de diálogo **Importar Assinaturas do Azure**, clique em **Procurar** e navegue até o arquivo *.publishsettings*.

	![baixar sub](./media/web-sites-dotnet-get-started-vs2012/rzDownLoadBrowse.png)

	- Clique em **Importar**.
    
	![import](./media/web-sites-dotnet-get-started-vs2012/rzImp.png)

	> [WACOM.NOTE]
	> O arquivo .publishsettings contém suas credenciais (sem codificação) que são usadas para administrar suas assinaturas e serviços do Azure. A melhor prática de segurança para este arquivo é armazená-lo temporariamente fora dos diretórios de origem (por exemplo, na pasta Libraries\Documents) e, em seguida, excluí-la após a conclusão da importação. Um usuário mal-intencionado que obtém acesso ao arquivo .publishsettings pode editar, criar e excluir seus serviços do Azure.

4. Na caixa de diálogo **Importar Perfil de Publicação**, selecione **Importar de um site do Azure**, selecione o seu site na lista suspensa e clique em **OK**.

	![Importar Perfil de Publicação][ImportPublishProfile]

5. Na guia **Conexão**, clique em **Validar Conexão** para garantir que as configurações estão corretas.

	![Validar a conexão][ValidateConnection]

6. Quando a conexão tiver sido validada, uma marca de seleção verde será mostrada ao lado do botão **Validar Conexão**. Clique em **Próximo**.

	![Conexão validada com êxito][ValidateConnectionSuccess]

7. Na guia **Configurações**, desmarque a opção **Usar esta cadeia de conexão no tempo de execução**, já que este aplicativo não está usando um banco de dados. Você pode aceitar as configurações padrão para os itens restantes nesta página.  Você está implantando uma configuração de compilação de versão e não precisa excluir arquivos no servidor de destino, pré-compilar o aplicativo ou excluir arquivos na pasta App_Data.
Clique em **Próximo**.

	![Guia Configurações][PublishWebSettingsTab]

8. Na guia **Visualização**, clique em **Iniciar Visualização**.

	![Botão StartPreview na guia Visualização][PublishWebStartPreview]

	A guia exibe uma lista dos arquivos que serão copiados no servidor. A exibição da visualização não é obrigatória para publicar o aplicativo, mas é uma função reconhecidamente útil. Nesse caso, você não precisa fazer nada com a lista de arquivos exibida. 

	![Saída do arquivo StartPreview][PublishWebStartPreviewOutput]

9. Clique em **Publicar**. O Visual Studio inicia o processo de cópia dos arquivos no servidor do Azure.

10. A janela **Saída** mostra quais ações de implantação foram executadas e os relatórios de conclusão bem-sucedida da implantação.

	![Relatório de implantação bem-sucedida na janela de saída][PublishOutput]

11. Após a implantação bem-sucedida, o navegador padrão abrirá automaticamente a URL do site implantado.
O aplicativo que você criou agora está em execução na nuvem.

	![Site em execução no Azure][DeployedWebSite]

<h2><a name="nextsteps"></a><span class="short-header">Próximas etapas</span>Próximas etapas</h2>

Neste tutorial, você viu como implantar um aplicativo Web simples em um site do Azure. Outros recursos estão disponíveis para mostrar a você como gerenciar, dimensionar e solucionar problemas do site, como adicionar a funcionalidade de autorização, autenticação de banco de dados e como decidir se seu aplicativo deve ser executado em um Serviço de Nuvem do Azure em vez de em um Site do Azure.

<h3>Como gerenciar um site</h3>
Depois que terminar com o site, você poderá excluí-lo. Também poderá deixá-lo offline temporariamente ou alterar suas configurações. Você pode fazer algumas destas funções diretamente do **Gerenciador de Servidores** no Visual Studio.

![Sites do Azure no Gerenciador de Servidores](./media/web-sites-dotnet-get-started-vs2012/ServerExplorerWSSettings.png)

![Configuração do site no Visual Studio](./media/web-sites-dotnet-get-started-vs2012/WSConfigurationInVS.png)

Para excluir seu site, será possível usar o Portal de Gerenciamento do Azure. A captura de tela a seguir mostra os botões **Parar**, **Reiniciar** e **Excluir** na guia **Painel** do portal de gerenciamento.

![Guia do Painel de Portal de Gerenciamento](./media/web-sites-dotnet-get-started-vs2012/MPStopStartDelete.png)

Você pode alterar as configurações do site na guia **Configurar**. Para obter mais informações, consulte [Como gerenciar sites (a página pode estar em inglês)](/pt-br/manage/services/web-sites/how-to-manage-websites/).

<h3>Como dimensionar um site</h3>
Quando seu site é público e começa a receber mais tráfego, os tempos de resposta podem se tornar lentos. Para corrigir isso, você pode adicionar facilmente recursos do servidor na guia **Dimensionar** do portal de gerenciamento.

![Guia da Escala do Portal de Gerenciamento](./media/web-sites-dotnet-get-started-vs2012/MPScale.png)

Para obter mais informações, consulte [Como dimensionar um site (a página pode estar em inglês)](/pt-br/manage/services/web-sites/how-to-scale-websites/). (A adição de recursos do servidor para dimensionar um site não é gratuita.)

<h3>Como solucionar problemas de um site</h3>
Convém examinar a saída de rastreamento ou o log para obter ajuda na solução de problemas. O Visual Studio fornece ferramentas internas para facilitar a exibição dos logs do Azure à medida que são gerados em tempo real.

![Logs no Visual Studio](./media/web-sites-dotnet-get-started-vs2012/LogsInVS.png)

Para obter mais informações, consulte [Solucionando problemas de Sites do Azure no Visual Studio (a página pode estar em inglês)](/pt-br/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

<h3>Como adicionar funcionalidade de banco de dados e autorização</h3>
A maioria dos sites de produção usam um banco de dados e restringem algumas funções de site a usuários autorizados determinados. Para obter um tutorial que mostra como começar com acesso ao banco de dados, autenticação e autorização, consulte [Implantar um aplicativo ASP.NET MVC seguro com associação, OAuth e Banco de Dados SQL a um Site do Azure (a página pode estar em inglês)][WebWithSQL].

<h3>Como decidir se seu aplicativo deve ser executado em um Serviço de Nuvem</h3>
Em algumas situações, convém executar o aplicativo em um Serviço de Nuvem do Azure em vez de em um Site do Azure. Para obter mais informações, consulte [Modelos de execução do Azure (a página pode estar em inglês)](/pt-br/develop/net/fundamentals/compute/) e [Sites, Serviços de Nuvem e VMs do Azure: quando usar o quê?](http://msdn.microsoft.com/pt-br/library/windowsazure/jj218759.aspx). Para obter uma série de tutoriais que mostram como criar um aplicativo Web ASP.NET de várias camadas e implantá-lo em um Serviço de Nuvem, consulte [Aplicativo .NET de várias camadas usando tabelas de armazenamento, filas e blobs (a página pode estar em inglês)](/pt-br/develop/net/tutorials/multi-tier-web-site/1-overview/).

[Definir o ambiente de desenvolvimento]: #setupdevenv
[Criar um site no Azure]: #setupwindowsazure
[Criar um aplicativo ASP.NET MVC 4]: #createmvc4app
[Implantar o aplicativo no Azure]: #deploytowindowsazure
[Próximas etapas]: #nextsteps
[PreviewPortal]: http://manage.windowsazure.com



[WebWithSQL]: /pt-br/develop/net/tutorials/web-site-with-sql-database/

[AppRunningLocally]: ./media/web-sites-dotnet-get-started-vs2012/AppRunningLocally.png
[ClickQuickCreate]: ./media/web-sites-dotnet-get-started-vs2012/ClickQuickCreate.png
[DeployedWebSite]: ./media/web-sites-dotnet-get-started-vs2012/DeployedWebSite.png

[ImportPublishSettings]: ./media/web-sites-dotnet-get-started-vs2012/ImportPublishSettings.png
[ImportPublishProfile]: ./media/web-sites-dotnet-get-started-vs2012/ImportPublishProfile.png
[InternetAppTemplate]: ./media/web-sites-dotnet-get-started-vs2012/InternetAppTemplate.png
[NewMVC4WebApp]: ./media/web-sites-dotnet-get-started-vs2012/NewMVC4WebApp.png
[NewVSProject]: ./media/web-sites-dotnet-get-started-vs2012/NewVSProject.png
[PublishOutput]: ./media/web-sites-dotnet-get-started-vs2012/PublishOutput.png
[PublishVSSolution]: ./media/web-sites-dotnet-get-started-vs2012/PublishVSSolution.png
[PublishWebSettingsTab]: ./media/web-sites-dotnet-get-started-vs2012/PublishWebSettingsTab.png
[PublishWebStartPreview]: ./media/web-sites-dotnet-get-started-vs2012/PublishWebStartPreview.png
[PublishWebStartPreviewOutput]: ./media/web-sites-dotnet-get-started-vs2012/PublishWebStartPreviewOutput.png

[ValidateConnection]: ./media/web-sites-dotnet-get-started-vs2012/ValidateConnection.png
[ValidateConnectionSuccess]: ./media/web-sites-dotnet-get-started-vs2012/ValidateConnectionSuccess.png

[WebSiteNew]: ./media/web-sites-dotnet-get-started-vs2012/WebSiteNew.png
[WebSiteStatusRunning]: ./media/web-sites-dotnet-get-started-vs2012/WebSiteStatusRunning.png

