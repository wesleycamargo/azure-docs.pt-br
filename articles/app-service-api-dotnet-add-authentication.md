<properties 
	pageTitle="Proteger um aplicativo de API do Azure" 
	description="Saiba como proteger um aplicativo de API do Azure usando o Visual Studio." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tdykstra"/>

# Proteger um aplicativo de API:  Adicionar autenticação do Active Directory do Azure ou do provedor

## Visão geral

No tutorial [Implantar um aplicativo de API](app-service-dotnet-deploy-api-app.md), você implantou um aplicativo de API com nível de acesso **Disponível para qualquer pessoa**.  Este tutorial mostra como proteger um aplicativo de API para que somente usuários autenticados possam acessá-lo.

Você executará as seguintes etapas:

- Chamar o aplicativo de API para verificar se ele está funcionando.
- Aplica regras de autenticação ao aplicativo de API.
- Chamar o aplicativo de API novamente para verificar se ele rejeita as solicitações não autenticadas.
- Fazer logon no provedor configurado.
- Chamar o aplicativo de API novamente para verificar se o acesso autenticado funciona.

## Pré-requisitos

Este tutorial funciona com o aplicativo de API que você criou em [Criar um aplicativo de API](app-service-dotnet-create-api-app.md) e implantou em [Implantar um aplicativo de API](app-service-dotnet-deploy-api-app.md).

## Use o navegador para chamar o aplicativo de API 

A maneira mais simples de verificar se seu aplicativo de API é acessível publicamente é chamá-lo em um navegador.

1. Em seu navegador, vá para o [Portal Azure].

3. Na home page, clique em **Procurar > Aplicativos de API** e clique no nome do aplicativo de API que você deseja proteger.

	![Browse](./media/app-service-api-dotnet-add-authentication/browse.png)

	![Select API app](./media/app-service-api-dotnet-add-authentication/select.png)

3. Na lâmina **Aplicativo de API**, clique na **URL** para abrir uma janela do navegador que chama o Aplicativo de API.

	![API App blade](./media/app-service-api-dotnet-add-authentication/chooseapiappurl.png)

2. Adicione `api/contcts/get/` à URL na barra de endereço do navegador.

	Por exemplo, se a URL do seu aplicativo de API fosse:

    	https://microsoft-apiappeeb5bdsasd744e188be7fa26f239bd4b.azurewebsites.net/

	A URL completa seria:

    	https://microsoft-apiappeeb5bdsasd744e188be7fa26f239bd4b.azurewebsites.net/api/contacts/get/

	Navegadores diferentes controlam chamadas de API de maneiras diferentes.  A imagem mostra uma chamada bem-sucedida de um navegador Chrome.

	![Chrome Get response](./media/app-service-api-dotnet-add-authentication/chromeget.png)

2. Salve a URL usada. Você vai usá-la novamente mais tarde no tutorial.

## Proteger o aplicativo de API

Quando implantou o aplicativo de API, você o implantou em um grupo de recursos.  Você pode adicionar aplicativos Web e outros aplicativos de API ao mesmo grupo de recursos, e cada aplicativo de API no grupo de recursos pode ter uma das três configurações de acessibilidade:
<!-todo: diagrama mostrando diferentes configurações de acessibilidade-->

- **Público (anônimo)** - qualquer pessoa pode chamar o aplicativo de API de fora do grupo de recursos sem estar conectado.
- **Público (autenticado)** - somente usuários autenticados têm permissão para chamar o aplicativo de API de fora do grupo de recursos.
- **Interno** - somente outros aplicativos de API ou Web do mesmo grupo de recursos têm permissão para chamar o aplicativo de API.

Quando o Visual Studio criou o grupo de recursos para você, ele também criou um *gateway*.  Um gateway é um aplicativo Web especial que processa todas as solicitações destinadas a aplicativos de API no grupo de recursos.

Quando vai até a lâmina do grupo de recursos no [Portal Azure], você pode ver seu aplicativo de API e o gateway no diagrama.

![Resource group diagram](./media/app-service-api-dotnet-add-authentication/rgdiagram.png)

Para configurar seu aplicativo de API para aceitar somente solicitações autenticadas, você definirá sua acessibilidade como **Público (autenticado)** e configurará o gateway para exigir a autenticação de um provedor, como o Active Directory do Azure, Google ou Facebook.

1. Volte para a lâmina **Aplicativo de API** do aplicativo de API que você deseja proteger.

2. Na folha do **Aplicativo de API**, clique em **Configurações** e em **Configurações do Aplicativo**.

	![Click Settings](./media/app-service-api-dotnet-add-authentication/clicksettings.png)

	![Click Application settings](./media/app-service-api-dotnet-add-authentication/clickbasicsettings.png)

3. Na lâmina **Configurações do Aplicativo**, altere o **Nível de Acesso** para **Público (autenticado)** e clique em **Salvar**.

	![Click Basic settings](./media/app-service-api-dotnet-add-authentication/setpublicauth.png)

	Você protegeu o aplicativo de API de acessos não autenticados.  Em seguida, você precisa configurar o gateway para especificar o provedor de autenticação a ser usado.

4. Role para a lâmina **Aplicativo de API** à esquerda e clique no link para o gateway.

	![Click gateway](./media/app-service-api-dotnet-add-authentication/gateway.png)

7. Na lâmina **Gateway**, clique em **Configurações** e em **Identidade**.

	![Click Settings](./media/app-service-api-dotnet-add-authentication/clicksettingsingateway.png)

	![Click Identity](./media/app-service-api-dotnet-add-authentication/clickidentity.png)

	Da lâmina **Identidade**, você pode navegar para diferentes lâminas para configurar a autenticação usando o Active Directory do Azure e vários outros provedores.

	![Identity blade](./media/app-service-api-dotnet-add-authentication/identityblade.png)
  
3. Escolha o provedor de identidade que você deseja usar e siga as etapas no artigo correspondente para configurar seu aplicativo de API com o provedor.  Esses artigos foram escritos para aplicativos móveis, mas os procedimentos são os mesmos para os aplicativos de API.  Alguns dos procedimentos requerem que você use o [portal antigo]. 

 - [Conta da Microsoft](app-service-mobile-how-to-configure-microsoft-authentication-preview.md)
 - [Logon no Facebook](app-service-mobile-how-to-configure-facebook-authentication-preview.md)
 - [Logon no Twitter](app-service-mobile-how-to-configure-twitter-authentication-preview.md)
 - [Logon no Google](app-service-mobile-how-to-configure-google-authentication-preview.md)
 - [Active Directory do Azure](app-service-mobile-how-to-configure-active-directory-authentication-preview.md)

Por exemplo, as capturas de tela a seguir mostram o que você verá nas páginas do [portal antigo] e nas lâminas do [Portal do Azure] depois de configurar a autenticação do Active Directory do Azure.

No Portal do Azure, a lâmina **Active Directory do Azure** tem uma **ID do Cliente** do aplicativo que você criou na guia do Active Directory do Azure do portal antigo, e **Locatários Permitidos** tem seu locatário do Active Directory do Azure (por exemplo, "contoso.onmicrosoft.com").

![Azure Active Directory blade](./media/app-service-api-dotnet-add-authentication/tdinaadblade.png)

No portal do antigo, a guia **Configurar** do aplicativo que você criou no **Active Directory do Azure** tem a **URL de Logon**, o **URI da ID do Aplicativo** e a **URL de Resposta** da lâmina **Active Directory do Azure** no Portal do Azure.

![Old portal AAD](./media/app-service-api-dotnet-add-authentication/oldportal1.png)

![Old portal AAD](./media/app-service-api-dotnet-add-authentication/oldportal2.png)

![Old portal AAD](./media/app-service-api-dotnet-add-authentication/oldportal3.png)

![Old portal AAD](./media/app-service-api-dotnet-add-authentication/oldportal4.png)

(A URL de resposta na imagem mostra a mesma URL duas vezes, uma vez com `http:` e uma com `https:`.)

## Verifique se a autenticação funciona 

1. Abra uma janela do navegador e, na barra de endereços, digite a URL que chama o método 'Get' do seu aplicativo de API, como fez anteriormente.

	Neste momento a tentativa de acessar o aplicativo de API resulta em uma mensagem de erro.

	![Chrome Get response fail](./media/app-service-api-dotnet-add-authentication/chromegetfail.png)

2. No navegador, vá para a URL de logon: 

    	http://[resourcegroupname]gateway.azurewebsites.net/login/[providername]

	Por exemplo, se você denominar seu grupo de recursos myfirstrg e configurar o gateway para a autenticação do Active Directory do Azure, a URL será a seguinte:

    	http://myfirstrggateway.azurewebsites.net/login/aad

	Observe que diferentemente da URL anterior, esta não inclui o nome de seu aplicativo de API:  o gateway está autenticando você, não o aplicativo de API.  O gateway controla a autenticação de todos os aplicativos de API do grupo de recursos.

	(Se você tiver problemas para entrar, tente abrir uma janela particular ou incógnito).

3. Insira suas credenciais quando o navegador exibir uma página de logon. 
 
	Se você tiver configurado o logon do Active Directory do Azure, use um dos usuários listados na guia **Usuários** do aplicativo que você criou na guia do Active Directory do Azure do [portal antigo], como admin@contoso.onmicrosoft.com.

	![AAD users](./media/app-service-api-dotnet-add-authentication/aadusers.png)

	![Login page](./media/app-service-api-dotnet-add-authentication/ffsignin.png)

4. Quando a mensagem de "logon concluído" for exibida, insira a URL para o método Get do aplicativo de API novamente.

	Dessa vez, como você foi autenticado, a chamada será bem-sucedida.  O gateway reconhece que você é um usuário autenticado e passa a solicitação para seu aplicativo de API.

	![Login completed](./media/app-service-api-dotnet-add-authentication/logincomplete.png)

	![Chrome Get response](./media/app-service-api-dotnet-add-authentication/chromeget.png)
	<!--todo: substituir por imagem mostrando nomes fictícios - >

## Usar o Postman para enviar uma solicitação Post

Quando você faz logon no gateway, o gateway envia de volta um token de autenticação.  Esse token deve ser incluído em todas as solicitações de fontes externas que passam pelo gateway.  Quando você acessa uma API com um navegador, o navegador normalmente armazena o token em um cookie e o envia com todas as chamadas posteriores para a API.

Para ver o que está acontecendo na telad de fundo, nesta seção você usa uma ferramenta de navegação para criar e enviar uma solicitação Post e obtém o token de autorização do cookie e o inclui em um cabeçalho HTTP.

Estas instruções mostram como usar a ferramenta Postman no navegador Chrome, mas você pode fazer a mesma coisa com qualquer ferramenta de cliente REST e quaisquer ferramentas de desenvolvedor do navegador.

1. Em uma janela do navegador Chrome, percorra as etapas mostradas na seção anterior para autenticar e as ferramentas do desenvolvedor (F12).

	![Go to Resources tab](./media/app-service-api-dotnet-add-authentication/resources.png)

3. Na guia **Recursos** das ferramentas de desenvolvedor do Chrome, localize os cookies do seu gateway e clique três vezes no valor do cookie **zumo-x-auth** para selecionar tudo.

	**Observação:** certifique-se de obter todo o valor do cookie. Se clicar duas vezes, você obterá apenas a primeira parte dele.

5. Clique com o botão direito do mouse no **Valor** do cookie **zumo-x-auth** e clique em **Copiar**.

	![Copy auth token](./media/app-service-api-dotnet-add-authentication/copyzumotoken.png)

4. Instale a extensão do Postman no navegador Chrome se você ainda não tiver feito isso.

6. Abra a extensão Postman.

7. No campo URL de Solicitação, digite a URL para o método Get do aplicativo de API que você usou anteriormente, mas omita "get/" do final.
 
		http://[apiappurl]/api/contacts
    
8. Clique em **Cabeçalhos** e adicione um cabeçalho *zumo-x-auth*.  Cole o valor do token da área de transferência para campo **Valor**.

9. Adicione um cabeçalho *Content-Type* com o valor *application/json*.

10. Clique em **form-data** e adicione uma chave de *contato* com o seguinte valor:

		{   "Id": 0,   "Name": "Li Yan",   "EmailAddress": "yan@contoso.com" }

11. Clique em Enviar.

	A aplicativo de API retorna uma resposta *201 Criado*.

	![Add headers and body](./media/app-service-api-dotnet-add-authentication/addcontact.png)

12. Para verificar se a solicitação não funciona sem o token de autenticação, exclua o cabeçalho de autenticação e clique em Enviar novamente.

	Você obtém uma resposta *403 Proibido*.

	![403 Forbidden response](./media/app-service-api-dotnet-add-authentication/403forbidden.png)

## Próximas etapas

Você viu como proteger um aplicativo de API do Azure exigindo autenticação do Active Directory do Azure ou provedor social.  Para saber mais, consulte [O que são Aplicativos de API?](app-service-api-apps-why-best-platform.md). 

[portal antigo]: https://manage.windowsazure.com/
[Portal do Azure]: https://portal.azure.com/


<!--HONumber=49-->