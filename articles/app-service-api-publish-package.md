<properties 
	pageTitle="Criar um pacote de aplicativos de API" 
	description="Saiba como publicar um pacote de aplicativos de API no Azure Marketplace" 
	services="app-service\api" 
	documentationCenter=".net"
	authors="guangyang"
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="guayan"/>

# Publicar um pacote de aplicativos de API no Azure Marketplace

## Visão geral

Este artigo mostra como publicar um pacote de aplicativos de API no [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/).

- Para saber como criar um aplicativo de API, consulte [Criar um aplicativo de API usando o Visual Studio](app-service-dotnet-create-api-app.md).
- Para saber como criar um pacote de aplicativos de API, consulte [Criar um pacote de aplicativos de API](app-service-api-create-package).

## Fluxo geral de publicação

A seguir está o fluxo geral de publicação

1. Crie um pacote do Nuget para seu aplicativo de API, seguindo as instruções no tutorial [Criar um pacote de aplicativos de API](app-service-api-create-package).
2. Publicá-lo em uma galeria com suporte pelo Nuget em https://apiapps.nuget.org.
3. Em seguida, ele será sincronizado com o [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/) automaticamente.
4. Vá até o [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/) e o [portal de visualização do Azure](https://portal.azure.com) para verificar a experiência de ponta a ponta.

## Publicar na galeria com suporte pelo Nuget

1. Vá para https://apiapps.nuget.org.

    ![Home page da galeria com suporte pelo Nuget](./media/app-service-api-publish-package/nuget-homepage.png)

2. Clique em **Entrar** e faça logon usando sua conta do Azure.
3. Na guia **Aplicativos de API** clique em **Carregar aplicativo de API** e depois carregue seu pacote de aplicativos de API.

    ![Página de carregamento de pacote da galeria com suporte pelo Nuget](./media/app-service-api-publish-package/nuget-upload-package-page.png)

4. Para **Namespace**, a lista suspensa mostrará todos os domínios verificados dos locatários do Azure Active Directory da sua conta do Azure. Certifique-se de escolher um que corresponda à propriedade de namespace no arquivo apiapp.json do seu pacote de aplicativos de API. Isso é verificado para assegurar que editores declarem namespaces válidos para seus pacotes de aplicativos de API.
5. Certifique-se que a opção **Remover este aplicativo API da lista** está desmarcada.
6. Clique em **Publicar**.
7. Se houver qualquer erro de validação, corrija-os e carregue novamente.

## Visualizar seu pacote de aplicativos de API no Azure Marketplace

Depois de alguns minutos, o pacote de aplicativos de API será sincronizado com o Azure Marketplace. Você pode acessar [aqui](http://azure.microsoft.com/marketplace/api-apps/) para verificar o título, a descrição, o ícone, etc. Se houver qualquer coisa que você precise alterar, basta alterar tal propriedade em seu pacote de aplicativos de API e publicar novamente.

![Página de aplicativo de API do Azure Marketplace](./media/app-service-api-publish-package/acom-marketplace-apiapp-page.png)

## Implantar o pacote de aplicativos de API no portal de visualização do Azure

Você também pode entrar em [Portal de visualização do Azure](https://portal.azure.com) usando sua conta do Azure (pode ser uma conta diferente daquela que você usa para publicar o pacote de aplicativos de API). Aqui você pode verificar a experiência de criação de seu pacote de aplicativos de API oferecida pelo Portal de Visualização do Azure. Se houver qualquer coisa que você precise alterar, basta alterar tal propriedade em seu pacote de aplicativos de API e publicar novamente.

Para obter detalhes sobre como implantar um pacote de aplicativos da API no portal do Azure, consulte um exemplo de como implantar o DropboxConnector [aqui](app-service-api-connnect-your-app-to-saas-connector.md).

<!--HONumber=52-->
