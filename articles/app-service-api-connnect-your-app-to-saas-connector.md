<properties 
	pageTitle="Conectar seu aplicativo a um conector de SaaS existente" 
	description="Este artigo demonstra como se conectar a um conector de SaaS existente" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="VinayaReddy" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/20/2015" 
	ms.author="vinayr;tdykstra"/>

# Conectar seu aplicativo a um conector de SaaS existente

## Visão geral

Este tutorial mostra como utilizar um conector de SaaS no Azure Marketplace. 

Você executará as seguintes etapas:
- Provisionar o Conector do Dropbox.
- Configurar o Aplicativo de API do Dropbox.
- Configurar o gateway.
- Obter e armazenar o token no repositório de tokens. 
- Chamar as APIs do Dropbox e verificar se o acesso autenticado funciona.

## Provisionar o Conector do Dropbox

1. Vá para a home page do portal de visualização e clique em Marketplace.

	![](./media/app-service-api-connect-your-app-to-saas-connector/01-Marketplace.png)

2. Pesquise o Dropbox na galeria do Marketplace.

	![](./media/app-service-api-connect-your-app-to-saas-connector/02-Marketplace-search.png)
 
3. Clique no ícone do Dropbox para provisionar o Conector do Dropbox.  Clique no botão "Criar" para provisionar o Conector do Dropbox.  Certifique-se de preencher o nome e os valores desejados para todos os campos antes de clicar no botão "Criar". 

	![](./media/app-service-api-connect-your-app-to-saas-connector/03-Dropbox-Connector-Blade.png) 

4.  Examine a lâmina do grupo de recursos no [portal de visualização](https://portal.azure.com/).  Você verá o gateway e o Conector do Dropbox.  Ao provisionar um Aplicativo de API, você o provisionou para fazer parte de um grupo de recursos.  Quando o portal criou o grupo de recursos para você, ele também criou um gateway.  Um gateway é um aplicativo Web especial que processa todas as solicitações destinadas a aplicativos de API no grupo de recursos.

	Você pode adicionar aplicativos Web e outros aplicativos de API ao mesmo grupo de recursos, e cada aplicativo de API no grupo de recursos pode ter uma das três configurações de acessibilidade:

	* Público (anônimo) - qualquer pessoa pode chamar o aplicativo de API de fora do grupo de recursos sem estar conectado.
	* Público (autenticado) - somente usuários autenticados têm permissão para chamar o aplicativo de API de fora do grupo de recursos.
	* Interno - somente outros aplicativos de API ou Web do mesmo grupo de recursos têm permissão para chamar o aplicativo de API.

## Configurar o Aplicativo de API do Dropbox

Primeiro, configure o Dropbox para aceitar somente solicitações autenticadas.  Você definirá sua acessibilidade como **Público (autenticado)** e configurará o gateway para exigir autenticação de um provedor, como o Active Directory do Azure, Google ou Facebook.

1.	No [portal de visualização](https://portal.azure.com/) do Azure, clique em **Procurar > Aplicativos de API** e clique no nome do aplicativo de API que você deseja proteger.

	![](./media/app-service-api-connect-your-app-to-saas-connector/04-Browse-API-Apps.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/05-Dropbox-Connector.png) 
 
2.	Na folha do aplicativo de API, clique em Configurações e em Configurações do Aplicativo.
 
	![](./media/app-service-api-connect-your-app-to-saas-connector/06-Dropbox-connector-properties.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/07-dropbox-settings-dialog.png) 

3.	Na lâmina **Configurações do Aplicativo**, altere o **Nível de Acesso** para **Público (autenticado)**. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/08-public-auth-setting-blade.png) 

	Você protegeu o Conector do Dropbox contra acesso não autenticado.  Agora é hora de configurar a autenticação do Dropbox.  Preencha os detalhes de *ID do Cliente* e *Segredo do Cliente* na interface do usuário mostrada abaixo (você pode conectar a *ID do Cliente* e o *Segredo do Cliente* da [conta do desenvolvedor do Dropbox](https://www.dropbox.com/developers/apps) ).

	![](./media/app-service-api-connect-your-app-to-saas-connector/09-Dropbox-authentication-settings.png) 

Em seguida, você precisa configurar o gateway para especificar o provedor de autenticação a ser usado.

## Configurar o gateway

1. Volte para a lâmina do Aplicativo de API do Dropbox e clique no link para o gateway.
 
	![](./media/app-service-api-connect-your-app-to-saas-connector/10-dropbox-connector-gateway.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/11-gateway-all-properties.png)

	![](./media/app-service-api-connect-your-app-to-saas-connector/12-gateway-property-settings-blade.png) 

2. Escolha o provedor de identidade que você deseja usar e siga as etapas no artigo correspondente para configurar seu aplicativo de API com o provedor.  Esses artigos foram escritos para aplicativos móveis, mas os procedimentos são os mesmos para os aplicativos de API.  Alguns dos procedimentos demandam o uso do [portal de gerenciamento](https://manage.windowsazure.com/) e do [portal de visualização](https://portal.azure.com/).
  
	- [Conta da Microsoft](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-microsoft-authentication-preview)
	- [Logon no Facebook](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-facebook-authentication-preview)
	- [Logon no Twitter](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-twitter-authentication-preview)
	- [Logon no Google](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-google-authentication-preview)
	- [Active Directory do Azure](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-active-directory-authentication-preview)

	Consulte o artigo "Proteger um aplicativo de API:  Adicionar Active Directory do Azure ou autenticação de provedor social" para ver instruções passo a passo de como fazer a configuração. 

## Obtenha e armazene o token OAuth no repositório de tokens.

1.	No navegador, vá para a URL de logon: 

	Você pode obter a URL da lâmina de configuração do Active Directory do gateway.
	![](./media/app-service-api-connect-your-app-to-saas-connector/14-01-gateway-login-URL-portal.png) 
	
	Sua URL estará no seguinte formato...
	http://[resourcegroupname]gateway.azurewebsites.net/login/[providername]

	Por exemplo, se você denominar seu grupo de recursos "MyResource" e configurar o gateway para a autenticação do Active Directory do Azure, a URL será a seguinte:

		http://Myresourcegateway.azurewebsites.net/login/aad
	
	Certifique-se de iniciar as ferramentas de desenvolvedor antes de inserir a URL no navegador e pressionar Enter.  A mensagem de logon completo deve aparecer. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/14-02-gateway-login-URL-Browser.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/14-03-gateway-login-confirmation.png) 

	Obtenha p valor e o token de autenticação do Zumo da guia de rede (Chrome).
	![](./media/app-service-api-connect-your-app-to-saas-connector/14-04-Acquire-Zumo-Auth-Token.png) 

	Exiba a extensão Postman no Chrome.  Insira a URL de consentimento do gateway e o token de autenticação do Zumo (cabeçalho) e faça a solicitação POST. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/15-login-to-the-connector.png) 
 
	Capture a URL de redirecionamento que é retornada da solicitação POST e verifique se ela funciona. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/16-redirect-url-validate.png)


	Se a URL de redirecionamento funcionar, o token é válido.  Todas as chamadas feitas para o conector de SaaS usarão o token que estabelecemos e verificamos.  Esse token deve ser incluído em todas as solicitações de fontes externas que passam pelo gateway.  Quando você acessa uma API com um navegador, o navegador normalmente armazena o token em um cookie e o envia com todas as chamadas posteriores para a API.

O mesmo fluxo é aplicável a todos os conectores de SaaS, como Safesforce, Facebook etc. 

## Chamar as APIs do Dropbox e verificar se o acesso autenticado funciona.

1. Volte à lista de Aplicativos da API e selecione o Conector do Dropbox. 

1. Anote a URL na parte superior.

2. Clique no Conector do Dropbox (conforme mostrado na figura) para ver todas as APIs com suporte.

	![](./media/app-service-api-connect-your-app-to-saas-connector/13-dropbox-api-app-operations.png) 

	Você pode ver quais operações têm suporte no aplicativo de API na lâmina de Definição de API.  Você pode baixar o Swagger, que pode usar no Visual Studio para gerar clientes fortemente tipados.  Além disso, você pode baixar o Swaddle, que pode ser usado no Sienna e no PowerApp Studio. 

2. Em uma janela do navegador, digite a URL que você copiou do portal e acrescente a qualquer uma das APIs com suporte para acessar os arquivos ou outros detalhes na sua conta do Dropbox. 

	Formato:  `<URL>`/Operação

	Por exemplo:

		https://dropboxconnector7b47555bd6784237ad3e7736da769ffc.azurewebsites.net/folder/photos
   
	Dado que já estabelecemos a autenticação adequada, a chamada acima é bem-sucedida e mostra os detalhes da pasta de fotos no navegador. 
	![](./media/app-service-api-connect-your-app-to-saas-connector/17-call-dropbox-method-from-browser.png) 

<!--todo<Copiar a imagem do navegador>-->

## Próximas etapas

Você viu como proteger um aplicativo de API e configurar o gateway para acessar o conector de SaaS usando o repositório de tokens.  Para saber mais, consulte [O que são Aplicativos de API?](app-service-api-apps-why-best-platform.md). 

<!--HONumber=49-->