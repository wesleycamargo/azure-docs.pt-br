<properties
	pageTitle="Autenticação de usuário para Aplicativos de API no Serviço de Aplicativo do Azure | Microsoft Azure"
	description="Saiba como proteger um aplicativo de API no Serviço de Aplicativo do Azure, permitindo acesso apenas a usuários autenticados."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="11/30/2015"
	ms.author="tdykstra"/>

# Autenticação de usuário para Aplicativos de API no Serviço de Aplicativo do Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Visão geral

Este tutorial mostra como usar os recursos de Autenticação e Autorização do Serviço de Aplicativo do Azure para proteger um aplicativo de API e como consumir o aplicativo de API em nome dos usuários finais. O provedor de autenticação exibido no tutorial é o Active Directory do Azure, e o exemplo de cliente é um aplicativo de página única AngularJS em execução em um navegador.

![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)
 
Este é o terceiro de uma série de tutoriais que mostram como trabalhar com aplicativos de API no Serviço de Aplicativo do Azure. Para saber mais sobre a série, confira [Introdução aos Aplicativos de API e ao ASP.NET no Serviço de Aplicativo do Azure](app-service-api-dotnet-get-started.md).

## Autenticação e autorização no Serviço de Aplicativo do Azure

O Serviço de Aplicativo do Azure oferece serviços internos para autenticação e autorização. Você é livre para lidar com a autenticação em seu próprio código, mas o Serviço de Aplicativo oferece uma solução completa para minimizar a quantidade de código que você precisa escrever e manter.

Use qualquer um dos cinco provedores de autenticação aos quais o Serviço de Aplicativo oferece suporte: Active Directory do Azure, Facebook, Google, Twitter e conta da Microsoft. E você pode proteger uma API escrita em qualquer linguagem compatível com o Serviço de Aplicativo: sem escrever qualquer código em sua API, você pode exigir o logon do usuário ou um token para acessá-la.

O Serviço de Aplicativo do Azure controla a autenticação e deixa de autorização ser tratada pelo seu código. Você pode configurar o Serviço de Aplicativo a fim de permitir que apenas usuários autenticados chamem a API, ou você pode permitir todos os chamadores. Em ambos os casos, o Serviço de Aplicativo passa as informações de autenticação ao seu aplicativo nos cabeçalhos HTTP, e seu código pode usar essas informações para tomar decisões de autorização.

* Em uma API do .NET, você pode usar o atributo `Authorize`, e para uma autorização mais refinada, é possível escrever facilmente um código com base em declarações. As informações das declarações são preenchidas em classes do .NET.

* Para APIs escritas em outras linguagens, o Serviço de Aplicativo passa o token JWT no cabeçalho de Autorização de uma solicitação HTTP. Além disso, o Azure define alguns cabeçalhos especiais (por exemplo, `x-ms-client-principal-id`) para fornecer acesso fácil às declarações mais importantes.

Para saber mais sobre os serviços de autenticação e autorização no Serviço de Aplicativo do Azure, confira [Expandindo a autenticação/autorização do Serviço de Aplicativo](/blog/announcing-app-service-authentication-authorization/) e [Aplicativos de API do Serviço de Aplicativo: o que mudou](app-service-api-whats-changed.md).

## O exemplo de projeto ContactsList.Angular.AAD

Neste tutorial, você usará os exemplos de projetos que baixou no [primeiro tutorial desta série](app-service-api-dotnet-get-started.md) e os recursos do Azure (aplicativo de API e aplicativo Web) que criou no primeiro tutorial.

O projeto ContactsList.Angular.AAD é um cliente AngularJS que inclui um código para trabalhar com o Active Directory do Azure. O código tem base em uma amostra de AAD que pode ser encontrada no repositório [Azure-Samples/active-directory-angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp).

O código no projeto ContactsList.Angular.AAD é estruturado de forma diferente do projeto mais simples, ContactsLists.Angular. O código que chama a API está no arquivo *app/scripts/contactsSvc.js* no projeto ContactsList.Angular.AAD.

		angular.module('contactsListApp')
		.factory('contactsSvc', ['$http', function ($http) {
		    //var apiEndpoint = "https://{your api app name}.azurewebsites.net";
		    var apiEndpoint = "https://localhost:44300";
		
		    $http.defaults.useXDomain = true;
		    delete $http.defaults.headers.common['X-Requested-With']; 
		
		    return {
		        getItems: function () {
		            return $http.get(apiEndpoint + '/api/contacts');
		        },
		        getItem : function(id){
		            return $http.get(apiEndpoint + '/api/contacts/' + id);
		        },
		        postItem : function(item){
		            return $http.post(apiEndpoint + '/api/contacts', item);
		        },
		        putItem : function(item){
		            return $http.put(apiEndpoint + '/api/contacts/', item);
		        },
		        deleteItem : function(id){
		            return $http({
		                method: 'DELETE',
		                url: apiEndpoint + '/api/contacts/' + id
		            });
		        }
		    };
		}]);

Aqui, o método `Get` é rotulado como `getItems`, e o controlador (*app/scripts/contactsCtrl.js*) `getItems` é conectado até `$scope.populate`.

		$scope.populate = function () {
		    contactsSvc.getItems().then(function (results) {
		        $scope.contactsList = results.data;
		        $scope.loadingMessage = "";
		    }, function (err) {
		        $scope.loadingMessage = "";
		        $scope.error = "Error: " + err;
		    });
		};

No modo de exibição (*app/views/Contacts.html*), $scope.populate é chamado na inicialização.

		<div ng-init="populate()">

## Configurar a autenticação e a autorização no Azure

1. No [Portal do Azure](https://portal.azure.com/), navegue até a folha **Aplicativo de API** do aplicativo de API que você criou no primeiro tutorial e clique em **Configurações**

2. Encontre a seção **Recursos** e clique em **Autenticação/Autorização**.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. Na folha **Autenticação/Autorização**, clique em **Ativada**.

4. Na lista suspensa **Ação a realizar quando a solicitação não está autenticada**, selecione **Fazer logon com o Active Directory do Azure**.

5. Em **Provedores de Autenticação**, clique em **Active Directory do Azure**.

	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. Na folha **Configurações do Active Directory do Azure**, clique em **Express**

	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

	O Azure criará automaticamente um aplicativo AAD no locatário AAD. Anote o nome do novo aplicativo AAD, pois você o selecionará posteriormente quando acessar o Portal clássico do Azure para obter a ID de cliente do novo aplicativo AAD.

7. Clique em **OK**.

10. Na folha **Autenticação/Autorização**, clique em **Salvar**.

8. Para verificar se o aplicativo de API está protegido, acesse a URL do aplicativo de API + `/swagger`, como você fez no primeiro tutorial para usar a interface de usuário do Swagger.

	Desta vez, você será redirecionado para uma página de logon.

	![](./media/app-service-api-dotnet-user-principal-auth/loginpage.png)

11. No [portal clássico do Azure](https://manage.windowsazure.com/), vá para **Active Directory do Azure**.

12. Na guia Diretório, clique em seu locatário AAD.

	![](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. Clique em **Aplicativos > Aplicativo que minha empresa possui** e clique na marca de seleção.

	Talvez também seja necessário atualizar a página para ver o novo aplicativo.

15. Na lista de aplicativos, clique no nome do aplicativo que o Azure criou quando você habilitou a autenticação para o aplicativo de API.

	![](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. Clique em **Configurar**.

	![](./media/app-service-api-dotnet-user-principal-auth/configure.png)

15. Na parte inferior da página, clique em **Gerenciar manifesto > Baixar manifesto** e salve o arquivo em um local onde você possa editá-lo.

16. No arquivo de manifesto baixado, procure a propriedade `oauth2AllowImplicitFlow`. Altere o valor dessa propriedade de `false` para `true` e salve o arquivo.

16. Clique em **Gerenciar manifesto > Carregar manifesto** e carregue o arquivo atualizado na etapa anterior.

17. Mantenha essa página aberta para que você possa copiar e colar valores dela e atualizar os valores na página em etapas posteriores do tutorial.

## Configurar projetos do Visual Studio para https

1. No **Gerenciador de Soluções**, clique no projeto ContactList.API e, em seguida, na janela **Propriedades**, altere **Habilitado para SSL** para **Verdadeiro**.

2. Copie a URL do SSL.

	![](./media/app-service-api-dotnet-user-principal-auth/enablessl.png)

3. Clique com o botão direito no projeto ContactsList.API e clique em **Propriedades**.

5. Clique na guia **Web**, cole a URL do SSL no campo **Url do Projeto** e salve as alterações.

	![](./media/app-service-api-dotnet-user-principal-auth/setprojecturl.png)

1. Siga o mesmo procedimento para habilitar SSL para o projeto ContactsList.Angular.AAD.

2. Defina o projeto ContactsList.API e os projetos ContactsList.Angular.AAD como projetos de inicialização e configure o projeto ContactsList.API para iniciar primeiro.

## Atualizar a URL do ponto de extremidade e as configurações de AAD no projeto ContactsList.Angular.AAD

7. No projeto ContactsList.Angular.AAD, abra o arquivo *app/scripts/app.js*.

8. Certifique-se de que o código que define a variável `endpoints` tenha a URL de SSL correta para o projeto ContactsList.API, e substitua as duas instâncias de "yourclientid" pelo valor real da ID de cliente do aplicativo AAD a partir da guia **Configurar** do portal clássico para o aplicativo AAD. Verifique se o URL do ponto de extremidade termina com uma barra.

	O código será semelhante ao exemplo a seguir:

		var endpoints = {
		    //"https://{your api app name}.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		    "https://localhost:44300/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

9. Além disso, em *app.js*, no código `adalProvider.init`, substitua "{url do locatário}" e "{id do cliente}" pelos valores reais.

	O código será semelhante ao exemplo a seguir:

		adalProvider.init(
		    {
		        instance: 'https://login.microsoftonline.com/', 
		        tenant: 'contoso.onmicrosoft.com',
		        clientId: '1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3',
		        extraQueryParameter: 'nux=1',
		        endpoints: endpoints
		    },
		    $httpProvider
		    );

10. Em *app/scripts/contactsSvc.js*, defina apiEndpoint como a URL de SSL correta para o projeto ContactsList.API.

		//var apiEndpoint = "https://{your api app name}.azurewebsites.net";
		var apiEndpoint = "https://localhost:44300";

## Atualizar as configurações de AAD no projeto ContactsList.API

1. Abra o arquivo *Web.config* do aplicativo no projeto ContactsList.API.

2. No elemento appSettings, defina ida:Authority como o valor "https://login.windows.net/{url de seu locatário}" e defina ida:ClientId como a ID de cliente do aplicativo AAD, conforme exibido no exemplo a seguir.

		<appSettings>
		  <add key="ida:Authority" value="https://login.windows.net/contoso.onmicrosoft.com" />
		  <add key="ida:ClientId" value="1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3" />
		</appSettings>

## Configurar o aplicativo AAD para localhost 

1. Na guia **Configurar** do aplicativo AAD no portal clássico, no campo **URL de Entrada**, cole a URL de SSL do projeto ContactsList.Angular.AAD e remova a barra à direita.

	![](./media/app-service-api-dotnet-user-principal-auth/signonurl.png)

3. Próximo à parte inferior da guia **Configurar**, no campo **URL de Resposta**, cole a URL de SSL do projeto ContactsList.Angular.AAD, substituindo o valor existente e deixando a barra à direita no lugar.

	![](./media/app-service-api-dotnet-user-principal-auth/replyurl.png)

4. Clique em **Salvar**.

## Executar a API e os projetos cliente localmente

Ao executar HTTPS localmente, você receberá mensagens de aviso sobre o certificado SSL do IIS Express para localhost. Para executar localmente, ignore estas mensagens. Se preferir, ignore esta seção e vá diretamente para a próxima seção, na qual você se prepara para executar o aplicativo e a API no Azure.

Se você tiver problemas para fazer logon, tente usar outro navegador ou abrir uma janela InPrivate ou anônima para a URL do projeto AngularJS, por exemplo, `https://localhost:44301`.

5. No Visual Studio, pressione F5 para executar localmente a API e os projetos AngularJS.

	A guia de início do aplicativo AngularJS é exibida.

10. Clique na guia **Login**.

	Você receberá uma solicitação para fazer logon.

7. Faça logon com credenciais de um usuário em seu locatário do AAD.

10. Clique na guia **Contatos**.

	A página **Contatos** é exibida.

	![](./media/app-service-api-dotnet-user-principal-auth/contactspagelocal.png)

11. Feche as janelas do navegador.

O código do projeto ContactsList.Angular.AAD exige que você esteja conectado antes de chamar a API e exibir os contatos. No entanto, não há nada que impeça os chamadores não autenticados de chamar a API. Você pode verificar isso executando a interface de usuário do Swagger na janela do navegador aberta para a URL de SSL do projeto ContactsList.API. A API será protegida contra chamadores não autenticados apenas quando for executada no Serviço de Aplicativo do Azure.

Nas seções a seguir, você configurará os projetos e o AAD para executar o cliente e a API no Serviço de Aplicativo do Azure, implantar e testar os projetos no Azure.

## Configurar o projeto ContactsList.Angular.AAD para chamar o aplicativo de API do Azure

1. No projeto ContactsList.Angular.AAD, abra o arquivo *app/scripts/app.js*.

2. Comente o ponto de extremidade do localhost, remova a marca de comentário do ponto de extremidade do Azure e substitua "{nome do aplicativo de api}" pelo nome de seu aplicativo de API.

	Agora, o código é semelhante ao exemplo a seguir.

		var endpoints = {
		    "https://contactslistapi.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		    //"https://localhost:44300/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

1. Faça a mesmo alteração de URL do ponto de extremidade no arquivo *app/scripts/contactsSvc.js*.

	Agora, o código é semelhante ao exemplo a seguir.

		var apiEndpoint = "https://contactslistapi.azurewebsites.net";
		//var apiEndpoint = "https://localhost:44300";


## Configurar o aplicativo AAD para o aplicativo Web do Azure

1. Na guia **Configurar** do aplicativo AAD no portal clássico, no campo **URL de Entrada**, exclua a URL de SSL do projeto ContactsList.Angular.AAD e substitua-a pela URL base do aplicativo Web sem a barra à direita. (Observe que essa é a URL do aplicativo Web, não a URL do aplicativo de API).

	![](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

3. No campo **URL de Resposta**, substitua a URL de SSL do projeto ContactsList.Angular.AAD pela URL base do aplicativo Web, mantendo a barra à direita no lugar.

	![](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

4. Clique em **Salvar**.

## Implantar o projeto ContactsList.API no Azure

8. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto ContactsList.API e clique em **Publicar**.

	O assistente **Publicar Web** abre no último perfil de publicação usado para este projeto, que serve para a implantação no aplicativo de API criado anteriormente.

7. Clique em **Publicar**.

8. Feche a janela do navegador que abre automaticamente.

## Implantar o projeto ContactsList.Angular.AAD no Azure

8. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto ContactsList.Angular.API e clique em **Publicar**.

9. Clique em **Serviço de Aplicativo do Microsoft Azure**

10. Na caixa de diálogo **Serviço de Aplicativo**, na lista suspensa **Assinatura**, escolha sua assinatura.

11. Expanda o grupo de recursos que você criou neste tutorial e selecione o aplicativo Web que você criou no segundo tutorial.

	![](./media/app-service-api-dotnet-user-principal-auth/deploytowebapp.png)

12. Clique em **OK**.

12. No assistente **Publicar Web**, clique na guia **Conexão** e, na caixa **URL de Destino**, mude `http://` para `https://`

	![](./media/app-service-api-dotnet-user-principal-auth/httpsinconntab.png)

12. No assistente **Publicar Web**, clique na guia **Configurações**, expanda **Opções de Publicação do Arquivo** e marque a caixa de seleção **Remover arquivos adicionais no destino**.

7. Clique em **Publicar**.

	O Visual Studio implanta o projeto e abre a página inicial do aplicativo em um navegador.

## Testar o aplicativo Web AngularJS no Azure

8. Clique na guia **Contatos**.

	Você receberá uma solicitação para fazer logon.

9. Faça logon com as credenciais de um usuário em seu locatário do AAD.

10. A página **Contatos** é exibida.

	![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)

11. Para verificar se o aplicativo de API está protegido, acesse a URL de interface do usuário do Swagger em uma janela InPrivate ou anônima do navegador.

	Você será redirecionado a uma página de logon.

	Agora, o front-end pode chamar a API em nome de um usuário autenticado, mas usuários não autenticados não podem chamar a API.

## Próximas etapas

Neste tutorial, você usou a Autenticação/Autorização do Serviço de Aplicativo para restringir o acesso a um aplicativo de API de modo que somente usuários autenticados possam chamá-lo. No próximo tutorial da série, você aprenderá a [restringir o acesso ao aplicativo de API para cenários entre serviços](app-service-api-dotnet-service-principal-auth.md).

<!---HONumber=AcomDC_1203_2015-->