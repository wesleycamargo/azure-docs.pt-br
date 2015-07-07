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
 
## Implantar o aplicativo de API 

Nesta seção, você verá as etapas necessárias para implantar um aplicativo de API em uma assinatura do Azure.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto (não na solução) e clique em **Publicar...**. 

	![Opção de menu de publicação do projeto](./media/app-service-dotnet-deploy-api-app/20-publish-gesture-v3.png)

2. Clique na guia **Perfil** e clique em **Aplicativos de API do Microsoft Azure (Visualização)**.

	![Caixa de diálogo Publicar na Web](./media/app-service-dotnet-deploy-api-app/21-select-api-apps-for-deployment-v2.png)

3. Clique em **Novo** para provisionar um novo Aplicativo de API em sua assinatura do Azure.

	![Selecione a caixa de diálogo Serviços de API Existentes](./media/app-service-dotnet-deploy-api-app/23-publish-to-apiapps-v3.png)

4. Na caixa de diálogo **Criar um Aplicativo de API**, digite o seguinte:

	- Em **Nome do Aplicativo de API**, digite ContactsList. 
	- Se você tiver várias assinaturas do Azure, selecione a que deseja usar.
	- Em **Plano de Serviço de Aplicativo**, selecione entre seus planos de Serviço de Aplicativo existentes ou selecione **Criar novo Plano de Serviço de Aplicativo** e digite o nome do novo plano. 
	- Em **Grupo de Recursos**, selecione entre os grupos de recursos existentes ou selecione **Criar novo grupo de recursos** e digite um nome. O nome deve ser exclusivo. Considere usar o nome do aplicativo como prefixo e acrescentar algumas informações pessoais, como sua ID da Microsoft (sem o sinal @).  
	- Em **Nível de Acesso**, selecione **Disponível para Qualquer Pessoa**. Esta opção tornará sua API totalmente pública, o que é adequado para este tutorial. Você pode restringir o acesso mais tarde no Portal de visualização do Azure.
	- Em **Região**, selecione uma região próxima de você.  

	![Configurar a caixa de diálogo de aplicativo Web do Microsoft Azure](./media/app-service-dotnet-deploy-api-app/24-new-api-app-dialog-v3.png)

5. Clique em **OK** para criar o Aplicativo de API em sua assinatura. Como esse processo pode levar alguns minutos, o Visual Studio exibe uma caixa de diálogo de confirmação.

	![Mensagem de confirmação de criação do serviço de API iniciado](./media/app-service-dotnet-deploy-api-app/25-api-provisioning-started-v3.png)

6. Clique em **OK** na caixa de diálogo de confirmação. O processo de provisionamento cria o grupo de recursos e o Aplicativo de API em sua assinatura do Azure. O Visual Studio mostra o andamento na janela **Atividade do Serviço de Aplicativo do Azure**.

	![Notificação de status pela janela de atividade do Serviço de Aplicativo do Azure](./media/app-service-dotnet-deploy-api-app/26-provisioning-success-v3.png)

7. Após o Aplicativo de API ser provisionado, clique com o botão direito do mouse no **Gerenciador de Soluções** e selecione **Publicar** para reabrir a caixa de diálogo de publicação. O perfil de publicação criado na etapa anterior deve estar pré-selecionado. Clique em **Publicar** para iniciar o processo de implantação.

	![Implantando o aplicativo de API](./media/app-service-dotnet-deploy-api-app/26-5-deployment-success-v3.png)

	A janela **Atividade do Serviço de Aplicativo do Azure** mostra o andamento da implantação.

	![Notificação de status na janela Atividade do Serviço de Aplicativo do Azure](./media/app-service-dotnet-deploy-api-app/26-5-deployment-success-v4.png)

	Durante esse processo de implantação, o Visual Studio reinicia automaticamente o *gateway*. O gateway é um aplicativo Web que lida com as funções administrativas para todos os aplicativos de API em um grupo de recursos, e precisa ser reiniciado para reconhecer as alterações de API em um aplicativo de API. Se você utiliza outro método para implantar um aplicativo de API e se sua implantação alterar a API, você talvez precise reiniciar o gateway manualmente. Para obter informações sobre como reiniciar o gateway no portal, consulte [Gerenciar um aplicativo API](app-service-api-manage-in-portal.md).

## Exibir o aplicativo no Portal de visualização do Azure

Nesta seção, você navegará até o portal para exibir as configurações básicas disponíveis para Aplicativos de API e fazer alterações iterativas em seu aplicativo de API. Com cada implantação, o portal refletirá as alterações que você fizer em seu aplicativo de API.

1. No navegador, vá para o [Portal de visualização do Azure](https://portal.azure.com). 

2. Clique no botão **Procurar** na barra lateral e selecione **Aplicativos de API**.

	![Procurar opções no portal do Azure](./media/app-service-dotnet-deploy-api-app/27-browse-in-portal-v3.png)

3. Selecione a API que você criou na lista de aplicativos de API de sua assinatura.

	![Lista de aplicativos de API](./media/app-service-dotnet-deploy-api-app/28-view-api-list-v3.png)

4. Clique em **Definição de API**. A folha **Definição de API** do aplicativo mostra a lista de operações de API que você definiu quando criou o aplicativo.

	![Definição de API](./media/app-service-dotnet-deploy-api-app/29-api-definition-v3.png)

5. Agora, volte para o projeto no Visual Studio e adicione o seguinte código ao arquivo **ContactsController.cs**. Este código adiciona um método **Post** que pode ser usado para publicar novas `Contact` instâncias da API.

		[HttpPost]
		public HttpResponseMessage Post([FromBody] Contact contact)
		{
			// todo: save the contact somewhere
			return Request.CreateResponse(HttpStatusCode.Created);
		}

	![Adicionando o método Post ao controlador](./media/app-service-dotnet-deploy-api-app/30-post-method-added-v3.png)

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
 

<!---HONumber=62-->