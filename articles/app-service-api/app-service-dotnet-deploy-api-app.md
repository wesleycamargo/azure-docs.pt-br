<properties 
	pageTitle="Implantar um Aplicativo de API no Serviço de Aplicativo do Azure" 
	description="Saiba como implantar um projeto de aplicativo de API em sua assinatura do Azure." 
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
	ms.date="05/04/2015" 
	ms.author="bradyg;tarcher"/>

# Implantar um Aplicativo de API no Serviço de Aplicativo do Azure 

## Visão geral

Neste tutorial, você implantará o projeto de API Web que criou no [tutorial anterior](app-service-dotnet-create-api-app.md) para um novo [aplicativo de API](app-service-api-apps-why-best-platform.md). Você usará o Visual Studio para criar o recurso de aplicativo de API no [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md) e implantar seu código de API Web no aplicativo de API do Azure.

### Outras opções de implantação

Há muitas outras maneiras de implantar aplicativos de API. Um aplicativo de API é um [aplicativo Web](../app-service-web/app-service-web-overview.md) com recursos adicionais para hospedagem de serviços Web e todos os [métodos de implantação disponíveis para aplicativos Web](../app-service-web/web-sites-deploy.md) também podem ser usados com aplicativos de API. O aplicativo Web que hospeda uma API de aplicativo é chamado de host de aplicativo de API no portal de visualização do Azure, e você pode configurar a implantação usando a folha de portal do host de aplicativo de API. Para obter informações sobre a folha do host de aplicativo de API, consulte [Gerenciar um aplicativo de API](app-service-api-manage-in-portal.md).

O fato de que aplicativos de API sejam baseados em aplicativos Web também significa que você pode implantar o código escrito em plataformas diferentes do ASP.NET nos aplicativos de API. Para obter um exemplo que usa Git para implantar o código do Node.js em um aplicativo de API, consulte [Criar um aplicativo de API do Node.js no Serviço de Aplicativo do Azure](app-service-api-nodejs-api-app.md).
 
## <a id="provision"></a>Criar um aplicativo de API no Azure 

Nesta seção, use o assistente **Publicar Web** do Visual Studio para criar um novo aplicativo de API no Azure. Quando houver instruções para inserir um nome para o aplicativo de API, digite *ContactsList*.

[AZURE.INCLUDE [app-service-api-pub-web-create](../../includes/app-service-api-pub-web-create.md)]

## <a id="deploy"></a>Implantar seu código no novo aplicativo de API

Use o mesmo assistente **Publicar Web** para implantar o código do novo aplicativo de API.

[AZURE.INCLUDE [app-service-api-pub-web-deploy](../../includes/app-service-api-pub-web-deploy.md)]

## Exibir o aplicativo no Portal de visualização do Azure

Nesta seção, você exibirá as configurações básicas disponíveis para aplicativos de API no portal e fará alterações iterativas em seu aplicativo de API. Com cada implantação, o portal reflete as alterações que você faz em seu aplicativo de API.

1. No [Portal de Visualização do Azure](https://portal.azure.com), vá para a folha **Aplicativo de API** do aplicativo de API implantado.

4. Clique em **Definição de API**.
 
	A folha **Definição de API** do aplicativo mostra a lista de operações de API que você definiu quando criou o aplicativo.

	![Definição da API](./media/app-service-dotnet-deploy-api-app/29-api-definition-v3.png)

5. Agora, volte para o projeto no Visual Studio e adicione o seguinte código ao arquivo **ContactsController.cs**.

		[HttpPost]
		public HttpResponseMessage Post([FromBody] Contact contact)
		{
			// todo: save the contact somewhere
			return Request.CreateResponse(HttpStatusCode.Created);
		}

	![Adicionando o método Post ao controlador](./media/app-service-dotnet-deploy-api-app/30-post-method-added-v3.png)

	Este código adiciona um método **Post** que pode ser usado para publicar novas `Contact` instâncias da API.

6. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Publicar**.

	![Menu de contexto de publicação do projeto](./media/app-service-dotnet-deploy-api-app/31-publish-gesture-v3.png)

7. Clique na guia **Configurações**.

8. Ca lista suspensa **Configuração**, selecione **Depurar**.

	![Publicar configurações da Web](./media/app-service-dotnet-deploy-api-app/36.5-select-debug-option-v3.png)

9. Clique na guia **Visualizar**

10. Clique em **Iniciar Visualização** para ver as alterações que serão feitas.

	![Caixa de diálogo Publicar na Web](./media/app-service-dotnet-deploy-api-app/39-re-publish-preview-step-v2.png)

11. Clique em **Publicar**.

12. Quando o processo de publicação for concluído, vá para o portal e feche e reabra a folha **Definição de API**. Você verá o novo ponto de extremidade da API que acabou de criar e implantar diretamente na sua assinatura do Azure.

	![Definição de API](./media/app-service-dotnet-deploy-api-app/38-portal-with-post-method-v4.png)

## Próximas etapas

Você viu como os recursos de implantação direta do Visual Studio facilitam o processo de iteração e implantação rapidamente e testam se sua API funciona corretamente. No [próximo tutorial](../app-service-dotnet-remotely-debug-api-app.md), você verá como depurar seu aplicativo de API enquanto ele é executado no Azure.
 

<!---HONumber=July15_HO3-->