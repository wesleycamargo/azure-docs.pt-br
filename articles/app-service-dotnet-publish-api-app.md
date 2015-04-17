<properties 
	pageTitle="Implantar um Aplicativo de API do Serviço de Aplicativo do Azure" 
	description="Saiba como implantar um projeto de Aplicativo de API em sua assinatura do Azure." 
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
	ms.date="02/19/2015" 
	ms.author="bradyg;tarcher"/>

# Implantar um Aplicativo de API do Serviço de Aplicativo do Azure

## Visão geral

Se você está desenvolvendo ativamente seu próprio aplicativo de API usando o Visual Studio e precisa testar sua API na nuvem, é possível criar um novo aplicativo de API em sua assinatura do Azure e implantar seu código usando os convenientes recursos de implantação de Serviço de Aplicativo do Visual Studio. 

Este é o segundo tutorial de uma série de três:

1. Em [Criar um Aplicativo de API](app-service-dotnet-create-api-app.md), você criou um projeto de Aplicativo de API. 
* Neste tutorial, você implantará o aplicativo de API em sua assinatura do Azure.
* Em [Depurar uma Aplicativo de API](app-service-dotnet-remotely-debug-api-app.md), você usará o Visual Studio para depurar remotamente o código enquanto ele é executado no Azure.

## Implantar o Aplicativo de API 

No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto (não na solução) e clique em **Publicar...**. 

![Project publish menu option](./media/app-service-dotnet-deploy-api-app/20-publish-gesture-v3.png)

Clique na guia **Perfil** e clique em **Aplicativos de API do Microsoft Azure (Visualização)**. 

![Publish Web dialog](./media/app-service-dotnet-deploy-api-app/21-select-api-apps-for-deployment-v2.png)

Clique em **Novo** para provisionar um novo Aplicativo de API em sua assinatura do Azure.

![Select Existing API Services dialog](./media/app-service-dotnet-deploy-api-app/23-publish-to-apiapps-v3.png)

Na caixa de diálogo **Criar um Aplicativo de API**, digite o seguinte:

- Em **Nome do Aplicativo de API**, forneça um nome para o aplicativo. 
- Se você tiver várias assinaturas do Azure, selecione aquela que deseja usar.
-Em Plano de Serviço de Aplicativo, selecione entre seus planos de Serviço de Aplicativo existentes ou selecione **Criar novo Plano de Serviço de Aplicativo** e digite o nome do novo plano. 
- Em **Grupo de Recursos**, selecione entre os grupos de recursos existentes ou selecione **Criar novo grupo de recursos** e digite um nome.  O nome deve ser exclusivo. Considere usar o nome do aplicativo como prefixo e acrescentar algumas informações pessoais, como sua ID da Microsoft (sem o sinal @).  
- Em **Nível de Acesso**, selecione **Disponível para Qualquer Pessoa**.  Esta opção tornará sua API totalmente pública, o que é adequado para este tutorial.  Você pode restringir o acesso mais tarde no Portal do Azure.
- Selecione uma região.  

![Configure Microsoft Azure Web App dialog](./media/app-service-dotnet-deploy-api-app/24-new-api-app-dialog-v3.png)

Clique em **OK** para criar o Aplicativo de API em sua assinatura.  O processo pode levar alguns minutos, então o Visual Studio mostra uma caixa de diálogo informando que o processo foi iniciado. 

![API Service Creation Started confirmation message](./media/app-service-dotnet-deploy-api-app/25-api-provisioning-started-v3.png)

O processo de provisionamento cria o grupo de recursos e o Aplicativo de API em sua assinatura do Azure.  O Visual Studio mostra o andamento na janela **Atividades do Serviço de Aplicativo do Azure**. 

![Status notification via the Azure App Service Activity window](./media/app-service-dotnet-deploy-api-app/26-provisioning-success-v3.png)

Após o Aplicativo de API ser provisionado, clique com o botão direito do mouse no Gerenciador de Soluções e selecione **Publicar** para reabrir a caixa de diálogo de publicação.  O perfil de publicação criado na etapa anterior deve estar pré-selecionado.  Clique em **Publicar** para iniciar o processo de implantação. 

![Deploying the API App](./media/app-service-dotnet-deploy-api-app/26-5-deployment-success-v3.png)

A janela **Atividades do Serviço de Aplicativo do Azure** mostra o andamento da implantação. 

![Status notification of the Azure App Service Activity window](./media/app-service-dotnet-deploy-api-app/26-5-deployment-success-v4.png)

## Exibir o aplicativo no Portal do Azure

Nesta seção, você navegará até o portal para exibir as configurações básicas disponíveis para Aplicativos de API e fazer alterações iterativas em seu aplicativo de API.  Com cada implantação, o portal refletirá as alterações que você fizer em seu aplicativo de API. 

No seu navegador, navegue até o [Portal Azure](https://portal.azure.com). 

Clique no botão **Procurar** na barra lateral e selecione **Aplicativos de API**.

![Browse options on Azure portal](./media/app-service-dotnet-deploy-api-app/27-browse-in-portal-v3.png)

Selecione a API que você criou na lista de aplicativos de API de sua assinatura.

![API Apps list](./media/app-service-dotnet-deploy-api-app/28-view-api-list-v3.png)

Clique em **Definição da API**.  A lâmina **Definição da API** do aplicativo mostra a lista de operações de API que você definiu quando criou o aplicativo. 

![API Definition](./media/app-service-dotnet-deploy-api-app/29-api-definition-v3.png)

Agora, volte para o projeto no Visual Studio.  Adicione o código a seguir ao arquivo **ContactsController.cs**.  

	[HttpPost]
	public HttpResponseMessage Post([FromBody] Contact contact)
	{
		// todo: save the contact somewhere
		return Request.CreateResponse(HttpStatusCode.Created);
	}

Este código adiciona um método **Post** que pode ser usado para lançar novas instâncias de `Contato` para a API. 

![Adding the Post method to the controller](./media/app-service-dotnet-deploy-api-app/30-post-method-added-v3.png)

No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Publicar**. 

![Project Publish context menu](./media/app-service-dotnet-deploy-api-app/31-publish-gesture-v3.png)

Selecione a configuração **Depurar** na lista suspensa **Configuração** e clique em **Publicar** para reimplantar o aplicativo de API. 

![Publish Web settings](./media/app-service-dotnet-deploy-api-app/36.5-select-debug-option-v3.png)

Na guia **Visualização** do assistente **Publicar Web**, clique em **Publicar**.  

![Publish Web dialog](./media/app-service-dotnet-deploy-api-app/39-re-publish-preview-step-v2.png)

Quando o processo de publicação for concluído, vá para o portal e feche e reabra a lâmina **Definição de API**.  Você verá o novo ponto de extremidade da API que acabou de criar e implantar diretamente na sua assinatura do Azure.

![API Definition](./media/app-service-dotnet-deploy-api-app/38-portal-with-post-method-v4.png)

## Próximas etapas

Você viu como os recursos de implantação direta do Visual Studio facilitam o processo de iterar e implantar rapidamente e testar se sua API funciona corretamente.  No [próximo tutorial](app-service-dotnet-remotely-debug-api-app.md), você verá como depurar seu aplicativo de API enquanto ele é executado no Azure.



<!--HONumber=49-->