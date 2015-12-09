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

Este tutorial mostra como usar os recursos Autenticação e Autorização do Serviço de Aplicativo do Azure para proteger um aplicativo de API e como consumir um aplicativo de API protegido usando uma conta de serviço. Uma conta de serviço também pode ser chamada de *entidade de serviço*, e a autenticação que usa essa conta também é conhecida como um cenário de *serviço a serviço*. Neste tutorial, você aprende a proteger um aplicativo de API em um cenário de serviço a serviço usando o Active Directory do Azure para autenticar e consumir a API de um cliente .NET.

O tutorial usa a API Web ASP.NET tanto para o cliente que chama quanto para a API chamada, mas as técnicas mostradas também se aplicam a outros idiomas e estruturas com suporte do Serviço de Aplicativo do Azure. O código de cliente mostrado aqui é o código padrão do Active Directory do Azure para obter e passar um token de portador a uma conta de serviço. Nenhum código especial exclusivo do Azure será exigido, como era o costume no tratamento do token Zumo dos Serviços Móveis.

Este é o quarto tutorial de uma série que mostra como trabalhar com aplicativos de API no Serviço de Aplicativo do Azure. Para saber mais sobre a série, veja o primeiro tutorial, [Introdução aos aplicativos de API e ao ASP.NET no Serviço de Aplicativo do Azure](app-service-api-dotnet-get-started.md). Para saber mais sobre a autenticação e a autorização no Serviço de Aplicativo do Azure, veja o tutorial anterior da série, [Autenticação de usuário para aplicativos de API no Serviço de Aplicativo do Azure](app-service-api-dotnet-user-principal-auth.md).

## Outras opções de autenticação serviço a serviço.

Se você quiser tratar de um cenário de serviço a serviço sem usar autorização e autenticação do Serviço de Aplicativo, por exemplo, usando certificados de cliente, confira a seção [Próximas etapas](#next-steps).

## O projeto de exemplo CompanyUsers.API

Neste tutorial, você usará os projetos de exemplo que baixou no [primeiro tutorial desta série](app-service-api-dotnet-get-started.md) e os recursos do Azure (aplicativo de API e aplicativo Web) que criou nos tutoriais anteriores.

O projeto CompanyUsers.API é um projeto API Web simples com um método Get que retorna uma lista de contatos embutida em código. Para demonstrar um cenário de serviço a serviço, o método Get no ContactsList.API chama o método Get CompanyContacts.API, adiciona os contatos obtidos ao que quer que esteja guardado em seu armazenamento de dados e retorna a lista combinada.

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

O objeto de cliente do CompanyContacts.API é uma modificação do código cliente do aplicativo de API gerado que adiciona um token à solicitação HTTP.

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

4. Na caixa de diálogo Serviço de Aplicativo, escolha a **Assinatura** do Azure que você deseja usar e clique em **Novo**.

	![](./media/app-service-api-dotnet-service-principal-auth/clicknew.png)

3. Na guia **Hospedagem** da caixa de diálogo **Criar Serviço de Aplicativo**, clique em **Alterar Tipo** e em **Aplicativo de API**.

4. Insira um **nome de aplicativo de API** que seja exclusivo em todo o domínio *azurewebsites.net*.

6. Na lista suspensa **Grupo de Recursos**, selecione o grupo de recursos que tem usado nestes tutoriais.

4. Na lista suspensa **Plano de Serviço de Aplicativo**, selecione o plano que você tem usado nestes tutoriais.

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

2. Clique com botão direito do mouse no projeto ContactsList.API e clique em **Adicionar > Cliente da API REST**.

3. Na caixa de diálogo **Adicionar Cliente da API REST**, clique em **Baixar do Aplicativo de API do Microsoft Azure** e em **Procurar**.

8. Na caixa de diálogo **Serviço de Aplicativo**, expanda o grupo de recursos que você está usando neste tutorial e selecione o aplicativo de API que acabou de criar

10. Clique em **OK**.

9. Na caixa de diálogo **Adicionar Cliente da API REST**, clique em **OK**.

	O Visual Studio cria uma pasta com o nome do aplicativo de API e gera classes de cliente.

## Atualizar o código em ContactsList.API e implantar o projeto

O código em ContactsList.API que chama CompanyContacts.API estava comentado nos tutoriais anteriores. Nesta seção, você removerá o comentário do código e implantará o aplicativo.

1. No projeto ContactsList.API, abra *Controllers/ContactsController.cs*.

2. Na parte superior da classe `ContactsController`, no código que usa a classe de cliente gerada para adicionar um token de autorização, substitua o nome da classe `CompanyContactsAPI` pelo nome da classe gerada usando seu aplicativo de API.

	Por exemplo, se seu aplicativo de API se chamar CompanyContactsAPI3, o código será o seguinte:

		 private static CompanyContactsAPI3 CompanyContactsAPIClientWithAuth()
		 {
		     var client = new CompanyContactsAPI3(new Uri(ConfigurationManager.AppSettings["CompanyContactsAPIUrl"]));
		     client.HttpClient.DefaultRequestHeaders.Authorization =
		         new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);
		     return client;
		 }
 
4. No método Get, remova o comentário do bloco de código que usa o objeto de cliente retornado por `CompanyContactsAPIClientWithAuth`.

		using (var client = CompanyContactsAPIClientWithAuth())
		{
		    var results = await client.Contacts.GetAsync();
		    foreach (Contact c in results)
		    {
		        contactsList.Add(c);
		    }
		}

2. Clique no projeto ContactsList.API e em **Publicar**.

	O assistente **Publicar Web** abre no perfil de publicação que você usou anteriormente.

3. No assistente **Publicar na Web**, clique em **Publicar**.

## Configurar autenticação e autorização no Azure para o novo aplicativo de API

1. No [portal do Azure](https://portal.azure.com/), navegue até a folha de aplicativo de API do aplicativo de API que você criou neste tutorial para o projeto CompanyContacts.API e clique em **Configurações**.

2. Encontre a seção **Recursos** e clique em **Autenticação/Autorização**.

3. Na folha **Autenticação/Autorização**, clique em **Ativada**.

4. Na lista suspensa **Ação a realizar quando a solicitação não está autenticada**, selecione **Fazer logon com o Active Directory do Azure**.

5. Em **Provedores de Autenticação**, clique em **Active Directory do Azure**.

6. Na folha **Configurações do Active Directory do Azure**, clique em **Express**.

	O Azure criará automaticamente um aplicativo AAD no locatário AAD. Anote o nome do novo aplicativo AAD, já que você o selecionará posteriormente quando acessar o portal clássico do Azure para obter sua ID do cliente.

7. Clique em **OK**.

10. Na folha **Autenticação/Autorização**, clique em **Salvar**.
 
11. No [portal clássico do Azure](https://manage.windowsazure.com/), vá para **Active Directory do Azure**.

12. Na guia **Diretório**, clique no locatário AAD.

14. Clique em **Aplicativos > Aplicativo que minha empresa possui** e clique na marca de seleção.

15. Na lista de aplicativos, clique no nome do aplicativo que o Azure criou quando você habilitou a autenticação para o aplicativo de API.

16. Clique em **Configurar**.

15. Na parte inferior da página, clique em **Gerenciar manifesto > Baixar manifesto** e salve o arquivo em um local onde você possa editá-lo.

16. No arquivo de manifesto baixado, procure a propriedade `oauth2AllowImplicitFlow`. Altere o valor dessa propriedade de `false` para `true` e salve o arquivo.

16. Clique em **Gerenciar manifesto > Carregar manifesto** e carregue o arquivo atualizado na etapa anterior.

17. Mantenha essa página aberta para que você possa copiar e colar valores dela e atualizar os valores na página em etapas posteriores do tutorial.

## Atualizar configurações no aplicativo de API que executa o código do projeto ContactsList.API

1. No [portal do Azure](https://portal.azure.com/), navegue até a folha de aplicativo de API do aplicativo de API no qual você implantou o projeto ContactsList.API. Esse é o aplicativo de API de chamada, não o que está sendo chamado, que você acabou de criar neste tutorial.

2. Clique em **Configurações > Configurações do aplicativo**.

	Você adicionará algumas configurações aqui, mas precisará obtê-las em outra página no portal clássico do Azure.

3. No [portal clássico do Azure](https://manage.windowsazure.com/), vá para a guia **Configurar** do aplicativo AAD que você criou para o aplicativo de API ContactsList.API.

5. Em **Chaves**, selecione **1 ano** na lista suspensa **Selecionar duração**.

6. Clique em **Salvar**.

	![](./media/app-service-api-dotnet-service-principal-auth/genkey.png)


7. Copie o valor da chave.

	![](./media/app-service-api-dotnet-service-principal-auth/genkeycopy.png)

3. Na folha **Configurações do aplicativo** do portal do Azure, seção **Configurações do aplicativo**, adicione uma chave chamada ida: ClientSecret e, no campo valor, cole a chave que você acabou de criar.

3. No portal do Azure clássico, vá para a guia **Configurar** do aplicativo AAD que você criou para o aplicativo de API CompanyContacts.API.

4. Copie a ID do cliente.

3. Na folha **Configurações do aplicativo** do portal do Azure, seção **Configurações do aplicativo**, adicione uma chave chamada ida:Resource e, no campo valor, cole a ID de cliente que você acabou de copiar.

4. Adicione uma chave chamada CompanyContactsAPIUrl e, no campo valor, digite https://{your nome do aplicativo de API}.azurewebsites.net, por exemplo: https://companycontactsapi.azurewebsites.net.

6. Clique em Salvar.

	![](./media/app-service-api-dotnet-service-principal-auth/appsettings.png)

## Testar no Azure

1. Vá para a URL do aplicativo Web no qual você implantou o projeto ContactsList.Angular.AAD.

2. Clique na guia **Contatos** e faça logon.

	Você verá a página Contatos com os contatos adicionais que foram recuperados do aplicativo de API CompanyContacts.API.

	![](./media/app-service-api-dotnet-service-principal-auth/contactspagewithdavolio.png)

## Próximas etapas

Este é o último tutorial da série de Introdução aos aplicativos de API. Esta seção oferece sugestões adicionais para aprender mais sobre como usar aplicativos de API.

* Outras maneiras de consumir um aplicativo de API protegido por autenticação e autorização do Serviço de Aplicativo do Azure.

	Este artigo mostrou como proteger um aplicativo de API e chamá-lo de um código em execução em outro aplicativo de API. Para obter informações sobre como chamar um aplicativo de API protegido usando um aplicativo lógico, confira [Usando a API personalizada hospedada no Serviço de Aplicativo com Aplicativos Lógicos](../app-service-logic/app-service-logic-custom-hosted-api.md).

* Outras maneiras de proteger um aplicativo de API em cenários serviço a serviço

	Como uma alternativa à autenticação e à autorização do Serviço de Aplicativo, você pode proteger um aplicativo de API usando certificados de cliente ou autenticação básica. Para obter informações sobre certificados de cliente no Azure, confira [Como configurar a autenticação mútua TLS para aplicativos Web](../app-service-web/app-service-web-configure-tls-mutual-auth.md).

* Outras maneiras de implantar um aplicativo do Serviço de Aplicativo

	Para saber mais sobre outras maneiras de implantar projetos em aplicativos Web usando o Visual Studio ou [automatizando a implantação](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) de um [sistema de controle de código-fonte](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control), confira [Como implantar um aplicativo Web do Azure](web-sites-deploy.md).

	O Visual Studio também pode gerar scripts do Windows PowerShell, que permitem a você automatizar a implantação. Para obter mais informações, consulte [Automatizar tudo (Compilando aplicativos de nuvem do mundo real com o Azure) ](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

* Como solucionar problemas de um aplicativo do Serviço de Aplicativo

	O Visual Studio fornece ferramentas que facilitam a exibição dos logs do Azure à medida que são gerados em tempo real. Você também pode executar em modo de depuração remotamente no Azure. Para obter mais informações, consulte [Solucionando problemas de aplicativos Web do Azure no Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

* Como adicionar um nome de domínio personalizado e SSL

	Para obter informações sobre como utilizar SSL e seu próprio domínio (por exemplo www.contoso.com, em vez de contoso.azurewebsites.net), consulte os recursos a seguir:

	* [Configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure](web-sites-custom-domain-name.md)
	* [Habilitar HTTPS para um site do Azure](web-sites-configure-ssl-certificate.md)

<!---HONumber=AcomDC_1203_2015-->