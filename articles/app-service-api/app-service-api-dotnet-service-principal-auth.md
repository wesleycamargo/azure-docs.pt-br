<properties
	pageTitle="Autenticação de entidade de serviço para Aplicativos de API no Serviço de Aplicativo do Azure | Microsoft Azure"
	description="Saiba como proteger um aplicativo de API no Serviço de Aplicativo do Azure em cenários serviço a serviço."
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
	ms.topic="get-started-article"
	ms.date="11/28/2015"
	ms.author="tdykstra"/>

# Autenticação de entidade de serviço para Aplicativos de API no Serviço de Aplicativo do Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Visão geral

Este tutorial mostra como usar os recursos de autenticação e autorização do Serviço de Aplicativo do Azure para proteger um aplicativo de API e como consumir um aplicativo de API protegido usando uma conta de serviço. O provedor de autenticação mostrado no tutorial é o Active Directory do Azure e o cliente e a API são APIs Web ASP.NET em execução em aplicativos de API.

## Autenticação e autorização no Serviço de Aplicativo

Para obter uma introdução aos recursos de autenticação usados neste tutorial, consulte o tutorial anterior desta série, [autenticação e autorização para Aplicativos de API no Serviço de Aplicativo do Azure](app-service-api-authentication.md).

## Como acompanhar este tutorial

Esse tutorial se baseia em um aplicativo de exemplo para o qual você baixa e cria um aplicativo de API no [primeiro tutorial da série de introdução dos Aplicativos de API e ASP.NET](app-service-api-dotnet-get-started.md).

## O projeto de exemplo CompanyUsers.API

No [aplicativo de exemplo ContactsList](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list), o projeto CompanyUsers.API é um projeto API Web simples com um método Get que retorna uma lista de contatos codificada. Para demonstrar um cenário de serviço a serviço, o método Get em ContactsList.API chama o método Get em CompanyContacts.API, adiciona os contatos obtidos ao que quer que esteja armazenado no repositório de dados e retorna a lista combinada.

Eis o método Get no CompanyUsers.API.

		public async Task<IEnumerable<Contact>> Get()
		{
		    var contacts = new Contact[]{
		                new Contact { Id = 1, EmailAddress = "nancy@contoso.com", Name = "Nancy Davolio"},
		                new Contact { Id = 2, EmailAddress = "alexander@contoso.com", Name = "Alexander Carson"}
		            };
		
		    return contacts;
		}


E eis o método Get no ContactsList.API, mostrando como ele chama o CompanyContacts.API e adiciona os resultados ao que ele retorna. (Partes do código são omitidas aqui para que tudo fique mais claro).

		private async Task<IEnumerable<Contact>> GetContacts()
		{
		    var contacts = await _storage.Get(FILENAME);
		
		    var contactsList = contacts.ToList<Contact>();
		    using (var client = CompanyContactsAPIClientWithAuth())
		    {
		        var results = await client.Contacts.GetAsync();
		        foreach (Contact c in results)
		        {
		            contactsList.Add(c);
		        }
		    }
		
		    return contactsList;
		}

O objeto de cliente retornado por `CompanyContactsAPIClientWithAuth()` no código acima é baseado no código de cliente gerado, mas adiciona um token de autorização para solicitações HTTP.

		private static CompanyContactsAPI CompanyContactsAPIClientWithAuth()
		{
		    var client = new CompanyContactsAPI(new Uri(ConfigurationManager.AppSettings["CompanyContactsAPIUrl"]));
		    client.HttpClient.DefaultRequestHeaders.Authorization =
		        new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);
		    return client;
		}

## Criar um aplicativo de API no Azure e implantar o projeto CompanyContacts.API nele

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto CompanyContacts.API e clique em **Publicar**.

3.  Na etapa **Perfil** do assistente **Publicar Web**, clique em **Serviço de Aplicativo do Microsoft Azure**.

	![](./media/app-service-api-dotnet-service-principal-auth/selectappservice.png)

4. Entre em sua conta do Azure, se ainda não tiver feito isso, ou atualize suas credenciais se elas tiverem expirado.

4. Na caixa de diálogo **Serviço de Aplicativo**, escolha a **Assinatura** do Azure que você deseja usar e clique em **Novo**.

	![](./media/app-service-api-dotnet-service-principal-auth/clicknew.png)

3. Na guia **Hospedagem** da caixa de diálogo **Criar Serviço de Aplicativo**, clique em **Alterar Tipo** e em **Aplicativo de API**.

4. Insira um **Nome de Aplicativo de API** que seja exclusivo no domínio *azurewebsites.net*.

6. Na lista suspensa **Grupo de Recursos**, selecione o grupo de recursos que você tem usado nos tutoriais.

4. Na lista suspensa **Plano de Serviço de Aplicativo**, selecione o plano que você tem usado nos tutoriais.

7. Clique em **Criar**.

	![](./media/app-service-api-dotnet-service-principal-auth/createappservice.png)

	O Visual Studio cria o aplicativo de API e um perfil de publicação que tem todas as configurações necessárias para o novo aplicativo de API.

8. Na guia **Conexão** do assistente **Publicar Web**, clique em **Publicar**.

	![](./media/app-service-api-dotnet-service-principal-auth/conntab.png)

	O Visual Studio implanta o projeto no novo aplicativo de API e abre um navegador com a URL do aplicativo de API. Uma página "criado com êxito" será exibida no navegador.

9. Feche o navegador.

## Atualizar o código de cliente gerado no projeto ContactsList.API

O projeto ContactsList.API já tem o código de cliente gerado, mas você o excluirá e o regenerará de seu próprio aplicativo de API.

1. No **Gerenciador de Soluções** do Visual Studio, no projeto ContactsList.API, exclua a pasta *CompanyContactsAPI*.

2. Clique com o botão direito do mouse no projeto ContactsList.API e clique em **Adicionar > Cliente da API REST**.

3. Na caixa de diálogo **Adicionar Cliente da API REST**, clique em **Baixar do Aplicativo de API do Microsoft Azure** e em **Procurar**.

8. Na caixa de diálogo **Serviço de Aplicativo**, expanda o grupo de recursos que você está usando neste tutorial e escolha o aplicativo de API que acabou de criar

	Se você não encontrar o aplicativo de API na lista, é possível que, ao criar o aplicativo de API, você tenha omitido acidentalmente a etapa que alterava o tipo de aplicativo Web para aplicativo de API. Nesse caso, você pode criar um novo aplicativo de API repetindo as etapas que realizou anteriormente. Você precisará escolher um nome diferente para o aplicativo de API, a menos que acesse o portal e exclua o aplicativo Web primeiro.

10. Clique em **OK**.

9. Na caixa de diálogo **Adicionar Cliente da API REST**, clique em **OK**.

	O Visual Studio cria uma pasta com o nome do aplicativo de API e gera classes de cliente.

## Atualizar o código em ContactsList.API e implantar o projeto

O código em ContactsList.API que chama CompanyContacts.API estava comentado nos tutoriais anteriores. Nesta seção, você removerá o comentário do código e implantará o aplicativo.

1. No projeto ContactsList.API, abra *Controllers/ContactsController.cs*.

2. Na parte superior da classe `ContactsController`, no código que usa a classe de cliente gerada para adicionar um token de autorização, substitua o nome da classe `CompanyContactsAPI` pelo nome da classe gerada usando o aplicativo de API.

	Por exemplo, se seu aplicativo de API se chamar CompanyContactsAPI3, o código será o seguinte:

		 private static CompanyContactsAPI3 CompanyContactsAPIClientWithAuth()
		 {
		     var client = new CompanyContactsAPI3(new Uri(ConfigurationManager.AppSettings["CompanyContactsAPIUrl"]));
		     client.HttpClient.DefaultRequestHeaders.Authorization =
		         new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);
		     return client;
		 }
 
4. No método `Get`, remova o bloco de código que chama CompanyContacts.API.

		using (var client = CompanyContactsAPIClientWithAuth())
		{
		    var results = await client.Contacts.GetAsync();
		    foreach (Contact c in results)
		    {
		        contactsList.Add(c);
		    }
		}

2. Clique com o botão direito do mouse no projeto ContactsList.API e em **Publicar**.

	O assistente **Publicar Web** é aberto no perfil de publicação que você usou anteriormente.

3. No assistente **Publicar na Web**, clique em **Publicar**.

	O Visual Studio implanta o projeto e abre uma janela do navegador para a URL base do aplicativo de API. Feche essa janela do navegador.

## Configurar autenticação e autorização no Azure para o novo aplicativo de API

1. No [portal do Azure](https://portal.azure.com/), navegue até a folha de Aplicativo de API do aplicativo de API que você criou neste tutorial para o projeto CompanyContacts.API e clique em **Configurações**.

2. Localize a seção **Recursos** e clique em **Autenticação/Autorização**.

3. Na folha **Autenticação/Autorização**, clique em **Ativada**.

4. Na lista suspensa **Ação a realizar quando a solicitação não está autenticada**, escolha **Fazer logon com o Active Directory do Azure**.

5. Em **Provedores de Autenticação**, clique em **Active Directory do Azure**.

6. Na folha **Configurações do Active Directory do Azure**, clique em **Express**

	O Azure criará automaticamente um aplicativo AAD no locatário AAD. Anote o nome do novo aplicativo AAD, já que você o selecionará posteriormente quando acessar o portal clássico do Azure para obter sua ID do cliente.

7. Clique em **OK**.

10. Na folha **Autenticação/Autorização**, clique em **Salvar**.
 
11. No [portal clássico do Azure](https://manage.windowsazure.com/), vá para **Active Directory do Azure**.

12. Na guia **Diretório**, clique no locatário do AAD.

14. Clique em **Aplicativos > Aplicativo que minha empresa possui** e clique na marca de seleção.

15. Na lista de aplicativos, clique no nome do aplicativo que o Azure criou quando você habilitou a autenticação para o aplicativo de API.

16. Clique em **Configurar**.

17. Mantenha essa página aberta para que você possa copiar e colar valores dela e atualizar os valores na página em etapas posteriores do tutorial.

## Atualizar configurações no aplicativo de API que executa o código do projeto ContactsList.API

3. Em outra janela do navegador, vá para o [portal clássico do Azure](https://manage.windowsazure.com/) e, em seguida, para a guia **Configurar** do aplicativo do AAD que você criou para o aplicativo de API ContactsList.API.

5. Em **Chaves**, selecione **1 ano** na lista suspensa **Selecionar duração**.

6. Clique em **Salvar**.

	![](./media/app-service-api-dotnet-service-principal-auth/genkey.png)

7. Copie o valor da chave.

	![](./media/app-service-api-dotnet-service-principal-auth/genkeycopy.png)

1. Em outra janela do navegador, vá para o [portal do Azure](https://portal.azure.com/) e navegue até a folha de aplicativo de API do aplicativo de API no qual você implantou o projeto ContactsList.API. (Esse é o aplicativo de API que está chamando, não aquele que está sendo chamado: ContactsList.API, não CompanyContacts.API.)

2. Clique em **Configurações > Configurações do aplicativo**.

3. Na seção **Configurações do aplicativo**, adicione uma chave chamada "ida:ClientSecret" e, no campo de valor, cole a chave que você acabou de criar.

3. Adicione uma chave chamada "ida:ClientId" e, no campo de valor, cole a ID do cliente da mesma página **Configurar** do AAD.

4. Adicione uma chave chamada "ida:Authority" e, no campo de valor, digite https://login.windows.net/{seu locatario}. Por exemplo, "https://login.windows.net/contoso.onmicrosoft.com".

3. No portal clássico do Azure, vá para a guia **Configurar** do aplicativo do AAD que você criou para o aplicativo de API CompanyContacts.API.

4. Copie a ID do cliente.

3. Na folha **Configurações do aplicativo** do portal do Azure, na seção **Configurações do aplicativo**, adicione uma chave chamada ida:Resource e, no campo de valor, cole a ID de Cliente que você acabou de copiar.

4. Adicione uma chave chamada "CompanyContactsAPIUrl" e, no campo de valor, digite https://{nome do aplicativo de api}.azurewebsites.net. Por exemplo: "https://companycontactsapi.azurewebsites.net."

6. Clique em Salvar.

	![](./media/app-service-api-dotnet-service-principal-auth/appsettings.png)

## Testar no Azure

1. Vá para a URL do aplicativo Web no qual você implantou o projeto ContactsList.Angular.AAD.

2. Clique na guia **Contatos** e faça logon.

	Você verá a página Contatos com os contatos adicionais que foram recuperados do aplicativo de API CompanyContacts.API.

	![](./media/app-service-api-dotnet-service-principal-auth/contactspagewithdavolio.png)

Assim como no tutorial anterior, você também pode configurar os projetos do Visual Studio com URLs de SSL de localhost e executar o aplicativo localmente. Nesse caso, você pode armazenar no arquivo Web.config as configurações armazenadas no Azure para execução no Azure (ID do cliente, segredo do cliente, etc.). No entanto, tenha cuidado para não fazer check-in para o controle de origem de um arquivo Web.config que contenha informações confidenciais, como o segredo do cliente. Para obter mais informações, consulte [Práticas recomendadas para implantação de senhas e outros dados confidenciais no Serviço de Aplicativo do Azure e ASP.NET](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

## Proteger o aplicativo de API contra acesso do navegador

Para este tutorial, você usou a opção Express no portal do Azure para configurar a autenticação do AAD para o aplicativo de API que você deseja acessar usando a autenticação de entidade de serviço. Por padrão, o serviço de aplicativo configura o novo aplicativo do AAD de maneira a habilitar um usuário a ir para a URL do aplicativo de API em um navegador e fazer logon. Isso significa que é possível que um usuário final, e não apenas uma entidade de serviço, tenha acesso à API. Se quiser que apenas uma entidade de serviço tenha acesso à API, você poderá impedir o acesso do navegador alterando a **URL de Resposta** no aplicativo do AAD para que ela seja diferente da URL de base do aplicativo de API.

### Verificar se o acesso do navegador funciona

1. Em uma nova janela do navegador, vá para a URL HTTPS do aplicativo de API que você criou para o projeto CompanyContacts.API.

	O navegador vai para uma tela de logon.
	
2. Faça logon com credenciais de um usuário no locatário do AAD.

3. O navegador exibe a tela de "criado com êxito" do aplicativo de API.

	Se a interface do usuário do Swagger estivesse habilitada, você poderia ir para a URL do `/swagger` e chamar a API. Você pode chamar a API por meio do navegador adicionando `/api/contacts/get` à URL.

### Desabilitar o acesso do navegador

1. Na guia **Configurar** do portal clássico do aplicativo do AAD criado para o projeto CompanyContacts.API, altere o valor do campo **URL de Resposta** para que seja uma URL válida, mas não a URL do aplicativo de API.
 
2. Clique em **Salvar**.

### Verificar se o acesso de navegador não funciona mais

1. Em uma nova janela do navegador, vá para a URL do aplicativo de API novamente.

2. Faça logon quando for solicitado a fazer isso.

3. O logon é bem-sucedido, mas leva a uma página de erro.

	Você ainda pode acessar o aplicativo de API usando um token de entidade de serviço, mas os usuários no locatário do AAD não podem fazer logon e acessar a API por meio de um navegador.

## Próximas etapas

Este é o último tutorial da série de Introdução aos aplicativos de API. Esta seção oferece sugestões adicionais para aprender mais sobre como trabalhar com aplicativos de API.

* Outras maneiras de implantar um aplicativo do Serviço de Aplicativo

	Para saber mais sobre outras maneiras de implantar projetos Web em aplicativos Web, usando o Visual Studio ou [automatizando a implantação](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) de um [sistema de controle do código-fonte](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control), confira [Como implantar um aplicativo Web do Azure](web-sites-deploy.md).

	O Visual Studio também pode gerar scripts do Windows PowerShell, que permitem a você automatizar a implantação. Para obter mais informações, consulte [Automatizar tudo (Compilando aplicativos de nuvem do mundo real com o Azure) ](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

* Como solucionar problemas de um aplicativo do Serviço de Aplicativo

	O Visual Studio fornece ferramentas que facilitam a exibição dos logs do Azure à medida que são gerados em tempo real. Você também pode executar em modo de depuração remotamente no Azure. Para obter mais informações, consulte [Solucionando problemas de aplicativos Web do Azure no Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

* Como adicionar um nome de domínio personalizado e SSL

	Para obter informações sobre como utilizar SSL e seu próprio domínio (por exemplo www.contoso.com, em vez de contoso.azurewebsites.net), consulte os recursos a seguir:

	* [Configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure](web-sites-custom-domain-name.md)
	* [Habilitar HTTPS para um site do Azure](web-sites-configure-ssl-certificate.md)

<!---HONumber=AcomDC_1217_2015-->