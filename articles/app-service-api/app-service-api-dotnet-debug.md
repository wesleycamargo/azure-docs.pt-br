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
	ms.date="10/08/2015" 
	ms.author="tdykstra"/>

# Depurar um aplicativo de API no Serviço de Aplicativo do Azure

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## Visão geral

Neste tutorial, você vai depurar o código de API Web ASP.NET que está configurado para ser executado em um [Aplicativo de API](app-service-api-apps-why-best-platform.md) no [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md). Você depura o código enquanto ele é executado localmente e enquanto é executado remotamente no Azure.

O tutorial funciona com o aplicativo de API que você [criar](app-service-dotnet-create-api-app.md) e [implantar](app-service-dotnet-deploy-api-app.md) nos tutoriais anteriores desta série.

## Depurar um aplicativo de API remotamente 

Para habilitar a depuração remota, você precisa implantar uma compilação de depuração no Azure.

1. No **Gerenciador de Soluções** do Visual Studio, clique com o botão direito do mouse no projeto que você implantou [no tutorial anterior](app-service-dotnet-deploy-api-app.md), e selecione **Publicar**.

2. Na caixa de diálogo **Publicar Web**, selecione a guia **Configurações** e selecione a compilação **Depurar**.

4. Clique em **Publicar**.

	![Publicar projeto](./media/app-service-api-dotnet-debug/rd-debug-publish.png)

	Uma janela do navegador é aberta para a URL base do seu aplicativo de API.

4. Na barra de endereços do navegador, adicione /swagger ao fim da URL e pressione Enter.

	Essa etapa pressupõe que você habilitou a interface do usuário do Swagger conforme indicado no tutorial [Criar](app-service-dotnet-create-api-app.md).

	![IU do Swagger](./media/app-service-api-dotnet-debug/rd-swagger-ui.png)

5. Retorne ao Visual Studio e, em seguida, clique em **Exibir > Gerenciador de servidores**.

6. No **Gerenciador de Servidores**, expanda o nó **Azure > Serviço de Aplicativo**.

7. Localize o grupo de recursos que você criou ou selecionou quando implantou o aplicativo de API.

8. No grupo de recursos, clique com o botão direito no nó do aplicativo de API e selecione **Anexar Depurador**.

	![Anexando o depurador](./media/app-service-api-dotnet-debug/rd-attach-debugger.png)

	O depurador remoto tentará se conectar. Em alguns casos, talvez seja necessário clicar novamente em **Anexar Depurador** para estabelecer uma conexão. Portanto, em caso de falha, tente novamente.

	![Anexando o depurador](./media/app-service-api-dotnet-debug/rd-attaching.png)

9. Depois que a conexão for estabelecida, abra o arquivo **ContactsController.cs** no projeto do Aplicativo de API e adicione pontos de interrupção no método `Get`.

	![Aplicando pontos de interrupção ao controlador](./media/app-service-api-dotnet-debug/rd-breakpoints.png)

10. Retorne para a sessão do navegador, clique no verbo **Obter** para exibir o esquema para o objeto *Contato* e, em seguida, clique em **Experimentar**. O Visual Studio para a execução do programa no ponto de interrupção, e você pode depurar a lógica do controlador.

	![Experimentar](./media/app-service-api-dotnet-debug/rd-try-it-out.png)

## Depurar um aplicativo de API localmente 

Pode haver ocasiões em que você deseja depurar seu aplicativo de API localmente; por exemplo, se viagens de ida e volta ao servidor do Azure tornam a depuração lenta. Esta seção ilustra como depurar seu aplicativo de API localmente usando a IU do Swagger como o cliente de teste.

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

1. No Visual Studio, abra o arquivo *web.config* do projeto de aplicativo de API.

9. Em **Configurações do aplicativo**, adicione cada uma das chaves e dos valores a seguir na seção **appSettings** do arquivo *web.config*.
	- **EMA\_MicroserviceId**
	- **EMA\_Secret**
	- **EMA\_RuntimeUrl**

	Quando terminar, a seção **appSettings** de seu *web.config* deve se parecer com a captura de tela a seguir.

	![Configurações de Aplicativo Host de Aplicativo de API para Depuração Local](./media/app-service-api-dotnet-debug/ld-debug-settings.png)

	**Observação:** os valores *EMA\_* adicionados por você ao seu arquivo *web.config* nesta seção contêm informações de autorização confidenciais. Portanto, é recomendável que você evite salvar esses valores em um repositório de controle de origem pública. Para obter mais informações, consulte [Práticas recomendadas para implantação de senhas e outros dados confidenciais no Serviço de Aplicativo do Azure e ASP.NET](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

10. Coloque um ponto de interrupção no código do controlador do seu aplicativo de API no método `Get`.

11. Pressione F5 para iniciar uma sessão de depuração do Visual Studio.
 
13.  Se o nível de acesso do aplicativo de API for definido como **Público (anônimo)**, você poderá usar a página da interface do usuário do Swagger para realizar testes.

	* Quando o navegador carregar a página, você verá uma página de erro HTTP 403. No final da URL na barra de endereços do navegador, adicione */swagger* e pressione Enter.

	* Após a IU do Swagger ter carregado, clique no verbo **Obter** para exibir o esquema para o objeto Contato e, em seguida, clique em **Experimentar**.

		O Visual Studio para a execução do programa no ponto de interrupção, e você pode depurar a lógica do controlador.

14.	Se o nível de acesso do aplicativo de API for definido como **Público (autenticado)**, será necessário autenticar e usar uma ferramenta de navegação seguindo os procedimentos mostrados em [Proteger um aplicativo de API](app-service-api-dotnet-add-authentication.md#use-postman-to-send-a-post-request) para uma solicitação Post, que são:

	* Vá para a URL de logon do gateway e insira as credenciais para fazer logon.
	* Obtenha o valor do token Zumo do cookie x-zumo-auth.
	* Adicione um cabeçalho x-zumo-auth à sua solicitação e defina o valor dele como o valor do cookie x-zumo-auth.
	* Envie a solicitação.

	**Observação:** em uma execução local, o Azure não pode controlar o acesso ao aplicativo de API para garantir que somente usuários autenticados possam executar seus métodos. Em uma execução no Azure, todo o tráfego destinado para o aplicativo de API é roteado por meio do gateway, que não aprova solicitações não autenticadas. Não há redirecionamento na execução local, o que significa que as solicitações não autenticadas não são impedidas de acessar o aplicativo de API. Conforme descrito acima, a importância da autenticação é que você pode executar código de autenticação com êxito no aplicativo de API, como o código que recupera informações sobre o usuário conectado. Para obter mais informações sobre como o gateway controla a autenticação para aplicativos de API, consulte [Autenticação para aplicativos da API e aplicativos móveis](../app-service/app-service-authentication-overview.md#azure-app-service-gateway).

## Próximas etapas

Neste tutorial, você viu como depurar aplicativos de API.

Para obter mais informações sobre solução de problemas, consulte [Solucionar problemas de um aplicativo Web no Serviço de Aplicativo do Azure usando o Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md). Como os aplicativos de API são aplicativos Web com recursos adicionais para hospedagem de serviços Web, você pode usar as mesmas ferramentas de depuração e solução de problemas para aplicativos de API que você usa em aplicativos Web.

<!---HONumber=AcomDC_1203_2015-->