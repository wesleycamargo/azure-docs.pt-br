<properties 
	pageTitle="Depurar um aplicativo de API no Serviço de Aplicativo do Azure" 
	description="Saiba como depurar um aplicativo de API enquanto ele é executado no Serviço de Aplicativo do Azure, usando o Visual Studio." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2015" 
	ms.author="bradyg;tarcher"/>

# Depurar um aplicativo de API no Serviço de Aplicativo do Azure

## Visão geral

Neste tutorial, você aprenderá como depurar o código de API Web ASP.NET que está configurado para ser executado em um [Aplicativo de API](app-service-api-apps-why-best-platform.md) no [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md). Você vai depurar ambos localmente e remotamente (enquanto ele é executado no Azure). O tutorial funciona com o aplicativo de API que você [criar](app-service-dotnet-create-api-app.md) e [implantar](app-service-dotnet-deploy-api-app.md) nos tutoriais anteriores desta série.

## Depurar um aplicativo de API remotamente 

As etapas a seguir permitem depurar seu aplicativo de API enquanto ele é executado na nuvem, usando a IU do Swagger como o cliente de teste.

1. No **Gerenciador de Soluções** do Visual Studio, clique com o botão direito do mouse no projeto que você [implantou no tutorial anterior](app-service-dotnet-deploy-api-app.md), e selecione **Publicar**.

	![Publicar projeto](./media/app-service-api-dotnet-debug/rd-publish.png)

2. Na caixa de diálogo **Publicar Web**, selecione a guia Configurações e verifique se a configuração da compilação **Depurar** está selecionada. Quando terminar, clique em **Publicar** para enviar quaisquer eventuais alterações por push à sua assinatura do Azure.

	![Publicar projeto](./media/app-service-api-dotnet-debug/rd-debug-publish.png)

3. Uma janela do navegador deve ser aberta e exibir uma mensagem confirmando que o aplicativo API foi criado com êxito.

4. Na barra de endereços do navegador, adicione "/swagger" ao fim da URL e pressione &lt;Enter>. Isso exibirá o cliente de IU do Swagger.

	![IU do Swagger](./media/app-service-api-dotnet-debug/rd-swagger-ui.png)

5. No Visual Studio, no Menu **Exibir**, selecione **Gerenciador de Servidores**.

6. No **Gerenciador de Servidores**, expanda o nó **Azure > Serviço de Aplicativo**.

7. Localize o grupo de recursos que você criou quando implantou o aplicativo de API.

8. No grupo de recursos, clique com o botão direito no nó do aplicativo de API e selecione **Anexar Depurador**.

	![Anexando o depurador](./media/app-service-api-dotnet-debug/rd-attach-debugger.png)

	O depurador remoto tentará se conectar. Em alguns casos, talvez seja necessário clicar novamente em **Anexar Depurador** para estabelecer uma conexão. Portanto, em caso de falha, tente novamente.

	![Anexando o depurador](./media/app-service-api-dotnet-debug/rd-attaching.png)

9. Depois que a conexão for estabelecida, abra o arquivo **ContactsController.cs** no aplicativo de API e adicione pontos de interrupção em `Get` e `Post` métodos. A princípio, podem não aparecer como ativos, mas se o depurador remoto estiver anexado, você estará pronto para depurar.

	![Aplicando pontos de interrupção ao controlador](./media/app-service-api-dotnet-debug/rd-breakpoints.png)

10. Retorne para a sessão do navegador, clique no verbo **Obter** para exibir o esquema para o objeto *Contato* e, em seguida, clique em **Experimentar**. Se você definir um ponto de interrupção no método **Get** do controlador, o Visual Studio interromperá a execução do programa e você poderá depurar a lógica do controlador.

	![Experimentar](./media/app-service-api-dotnet-debug/rd-try-it-out.png)

## Depurar um aplicativo de API localmente 

Pode haver ocasiões em que você deseja depurar seu aplicativo API localmente; por exemplo, para evitar idas e voltas potencialmente lentas durante seu ciclo de testes/depuração. As etapas a seguir ilustram como depurar seu aplicativo de API localmente usando a IU do Swagger como o cliente de teste.

1. No Visual Studio, abra o arquivo *web.config* do projeto de aplicativo de API. 
 
2. No navegador, vá para o [Portal de visualização do Azure](https://portal.azure.com).

3. Clique no botão **Procurar** na barra lateral e selecione **Aplicativos de API**.

	![Procurar opções no portal do Azure](./media/app-service-api-dotnet-debug/ld-browse.png)

4. Selecione, na lista de aplicativos de API de sua assinatura, o aplicativo de API que você criou.

	![Lista de Aplicativos de API](./media/app-service-api-dotnet-debug/ld-api-app-list.png)

5. Na folha do aplicativo de API, clique no **Host de aplicativo de API**.

	![Host de Aplicativo de API](./media/app-service-api-dotnet-debug/ld-api-app-blade-api-app-host.png)

6. Na folha do host de aplicativo de API, clique em **Todas as Configurações**.

	![Todas as Configurações de Host de Aplicativo de API](./media/app-service-api-dotnet-debug/ld-api-app-host-all-settings.png)

7. Na folha **Configurações**, clique em **Configurações do aplicativo**.

	![Configurações de Aplicativo Host de Aplicativo de API](./media/app-service-api-dotnet-debug/ld-application-settings.png)

8. Na folha **Configurações de aplicativo da Web**, role para baixo até a seção **Configurações do aplicativo**.

	![Configurações de Aplicativo Host de Aplicativo de API para Depuração Local](./media/app-service-api-dotnet-debug/ld-app-settings-for-local-debugging.png)

9. Em **Configurações do aplicativo**, localize cada um dos valores a seguir e adicione-os, no arquivo *web.config*, à seção **appSettings**.
	- **EMA\_MicroserviceId**
	- **EMA\_Secret**
	- **EMA\_RuntimeUrl**

	Quando terminar, a seção **appSettings** de seu *web.config* deve se parecer com a captura de tela a seguir.

	![Configurações de Aplicativo Host de Aplicativo de API para Depuração Local](./media/app-service-api-dotnet-debug/ld-debug-settings.png)

	**Observação:** os valores *EMA\_* adicionados por você ao seu arquivo *web.config* nesta seção contêm informações de autorização confidenciais. Portanto, é recomendável que você tenha cuidado ao alocar esse arquivo em um meio de controle do código-fonte público (como *github*), já que esses segredos ficarão então visíveis para outras pessoas. Para obter mais informações, consulte o artigo [Práticas recomendadas para implantação de senhas e outros dados confidenciais no Serviço de Aplicativo do Azure e ASP.NET](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

10. Coloque um ou mais pontos de interrupção no código do controlador do seu aplicativo de API (nos métodos `Get` e `Post`).

	![Definição de pontos de interrupção](./media/app-service-api-dotnet-debug/ld-breakpoints.png)

11. Pressione F5 para iniciar uma sessão de depuração do Visual Studio.
 
13.  Se o nível de acesso do aplicativo de API for definido como**Público (anônimo)**, você poderá usar a página da interface do usuário do Swagger para realizar testes.

	* Quando o navegador carregar a página, você verá uma mensagem de erro. No final da URL na barra de endereços do navegador, adicione */swagger* e pressione Enter.

	* Após a IU do Swagger ter carregado, clique no verbo **Obter** para exibir o esquema para o objeto Contato e, em seguida, clique em **Experimentar**.

		O Visual Studio agora interromperá a execução do programa nos pontos de interrupção que você definiu anteriormente, e você pode depurar a lógica do controlador.

		![Experimentar](./media/app-service-api-dotnet-debug/ld-try-it-out.png)

14.	Se o nível de acesso do aplicativo de API for definido como **Público (autenticado)**, será necessário autenticar e usar uma ferramenta de navegação seguindo os procedimentos mostrados em [Proteger um aplicativo de API](app-service-api-dotnet-add-authentication.md#use-postman-to-send-a-post-request) para uma solicitação Post, que são:

	* Vá para a URL de logon do gateway e insira as credenciais para fazer logon.
	* Obtenha o valor do token Zumo do cookie x-zumo-auth.
	* Adicione um cabeçalho x-zumo-auth à sua solicitação e defina o valor dele como o valor do cookie x-zumo-auth.
	* Envie a solicitação.

	**Observação:** em uma execução local, o Azure não pode controlar o acesso ao aplicativo de API para garantir que somente usuários autenticados possam executar seus métodos. Em uma execução no Azure, todo o tráfego destinado para o aplicativo de API é roteado por meio do gateway, que não aprova solicitações não autenticadas. Não há redirecionamento na execução local, o que significa que as solicitações não autenticadas não são impedidas de acessar o aplicativo de API. Conforme descrito acima, a importância da autenticação é que você pode executar código de autenticação com êxito no aplicativo de API, como o código que recupera informações sobre o usuário conectado. Para obter mais informações sobre como o gateway controla a autenticação para aplicativos de API, consulte [Autenticação para aplicativos da API e aplicativos móveis](../app-service/app-service-authentication-overview.md#azure-app-service-gateway).

## Próximas etapas

Depuração remota para aplicativos de API torna mais fácil ver como o código está em execução no Serviço de Aplicativo do Azure. Diagnóstico avançado e depuração de dados estão disponíveis no IDE do Visual Studio para aplicativos de API do Azure.

Aplicativos de API do Serviço de Aplicativo são aplicativos Web do Serviço de Aplicativo com recursos adicionais para hospedagem de serviços Web, para que você possa usar as mesmas ferramentas de depuração e solução de problemas para aplicativos de API que você usa em aplicativos Web. Para obter mais informações, consulte [Solucionar problemas de um aplicativo Web no Serviço de Aplicativo do Azure usando o Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).

O aplicativo de API que você criou nesta série está disponível publicamente para qualquer pessoa que chamar. Para obter informações sobre como proteger aplicativos de API de modo que somente usuários autenticados possam chamá-los, consulte [Autenticação para aplicativos de API e aplicativos móveis no Serviço de Aplicativo do Azure](../app-service/app-service-authentication-overview.md).
 

<!---HONumber=August15_HO6-->