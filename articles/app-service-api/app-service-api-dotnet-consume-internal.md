<properties 
	pageTitle="Consumir um aplicativo de API interna no Serviço de Aplicativo do Azure por meio de um cliente .NET" 
	description="Saiba como consumir um aplicativo de API de um aplicativo de API .NET no mesmo grupo de recursos usando o SDK do Serviço de Aplicativo." 
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
	ms.date="10/30/2015" 
	ms.author="bradyg"/>

# Consumir um aplicativo de API interna no Serviço de Aplicativo do Azure por meio de um cliente .NET 

## Visão geral

Este tutorial mostra como escrever código para um [aplicativo de API](app-service-api-apps-why-best-platform.md) ASP.NET que chama outro aplicativo de API configurado para nível de acesso **Interno**. Os dois aplicativos de API devem estar no mesmo grupo de recursos. O mesmo código pode ser usado para chamar um aplicativo de API interna de um [aplicativo móvel](../app-service-mobile/app-service-mobile-value-prop-preview.md).

Você criará uma API Web ContactNames. O método Get da API Web chamará um aplicativo de API ContactsList e retornará apenas os nomes obtidos nas informações de contato fornecidas pelo aplicativo de API ContactsList. Esta é a tela do Swagger da interface do usuário para uma chamada bem-sucedida ao método Get ContactNames.

![](./media/app-service-api-dotnet-consume-internal/tryitout.png)

Para obter informações sobre como chamar aplicativos de API configurados para níveis de acesso **Público (anônimo)** ou **Público (autenticado)**, confira [Consumir um aplicativo de API de um cliente .NET no Serviço de Aplicativo do Azure](app-service-api-dotnet-consume.md).

## Pré-requisitos

O tutorial presume que você esteja familiarizado com a criação de projetos e com adicionar código a eles no Visual Studio, e como [Gerenciar aplicativos de API no portal de visualização do Azure](../app-service-api-apps-manage-in-portal.md).

Os exemplos de projeto e o código neste artigo baseiam-se no projeto de aplicativo de API que você pode criar e implantar nesses artigos:

- [Criar um aplicativo de API](app-service-dotnet-create-api-app.md)
- [Implantar um aplicativo de API](app-service-dotnet-deploy-api-app.md)

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

Este tutorial requer a versão 2.6 ou posterior do SDK do Azure para .NET.

### Configurar o aplicativo de API de destino

1. Se você ainda não o fez, siga o tutorial [Implantar um aplicativo de API](app-service-dotnet-deploy-api-app.md) para implantar o projeto de exemplo ContactsList em um aplicativo de API na sua assinatura do Azure.

2. No [portal de visualização do Azure](https://portal.azure.com/), na folha **Aplicativo de API** do aplicativo de API ContactsList implantado anteriormente, clique em **Configurações > Configurações do Aplicativo**, defina **Nível de acesso** como **Interno** e clique em **Salvar**.

	![](./media/app-service-api-dotnet-consume-internal/setinternal.png)
 
## Criar um novo aplicativo de API que chamará o aplicativo de API existente

- No Visual Studio, crie um projeto de aplicativo de API chamado ContactNames usando o modelo de projeto de aplicativo de API do Azure.

	Esse é o mesmo processo que você seguiu em [Criar um aplicativo de API](app-service-dotnet-create-api-app.md), mas você adicionará código diferente ao projeto mais adiante neste tutorial.
 
## Adicionar código de cliente gerado pelo SDK do serviço de aplicativo

As etapas a seguir são explicadas em mais detalhes em [Consumir um aplicativo de API de um cliente .NET no Serviço de Aplicativo do Azure](app-service-api-dotnet-consume.md).

3. Em **Gerenciador de soluções**, clique com o botão direito no projeto (não na solução) e selecione **Adicionar > Cliente de aplicativo de API do Azure**. 

3. Na caixa de diálogo **Adicionar cliente de aplicativo de API do Azure**, clique em **Baixar a partir do aplicativo de API do Azure**.

5. Na lista suspensa, selecione o aplicativo de API que você deseja chamar. Para este tutorial, escolha o aplicativo de API ContactsList que você criou anteriormente.

7. Clique em **OK**.

## Habilitar interface do usuário do Swagger

Por padrão, os projetos de Aplicativo de API são habilitados com a geração de metadados automática do [Swagger](http://swagger.io/ "Informações oficiais do Swagger"), mas o modelo de novo projeto de Aplicativo de API do Azure desabilita a página de teste de API. Nesta seção, você habilitará a página de teste.

1. Abra o arquivo *App\_Start/SwaggerConfig.cs*, e pesquise por **EnableSwaggerUI**:

2. Remova os comentários das seguintes linhas de código:

	        })
	    .EnableSwaggerUi(c =>
	        {

## Criar um controlador

5. Clique com o botão direito do mouse na pasta **Controladores** e selecione **Adicionar > Controlador**. 

6. Na caixa de diálogo **Adicionar Scaffold**, selecione a opção **Controlador de API Web 2 - Vazio** e clique em **Adicionar**.

7. Nomeie o controlador **ContactNamesController** e clique em **Adicionar**.

## Adicione código para chamar o aplicativo de API

Para chamar um aplicativo de API que foi protegido definindo seu nível de acesso como **Interno**, você precisa adicionar cabeçalhos de autenticação interna a solicitações HTTP. Esses cabeçalhos informam ao aplicativo de API de destino que a origem da chamada é um aplicativo de API par chamando de dentro do mesmo grupo de recursos.

O SDK do Serviço de Aplicativo gera classes de clientes que simplificam o código que você escreve para chamar o aplicativo de API. Para chamar um aplicativo de API **Público (anônimo)**, basta criar um objeto de cliente e chamar métodos nele, como neste exemplo:

		var client = new ContactsList();
		var contacts = await client.Contacts.GetAsync();

No entanto, para adicionar cabeçalhos de autenticação, é necessário o acesso ao objeto `HttpRequestMessage`, e você não dispõe dele aqui. Para obter acesso à solicitação e adicionar os cabeçalhos, você precisa criar uma classe `DelegatingHandler` e passar uma instância dela ao construtor do cliente gerado.

1. Adicione ao projeto um arquivo de classe chamado *InternalCredentialHandler.cs* e substitua o código de modelo pelo código a seguir.

		using Microsoft.Azure.AppService.ApiApps.Service;
		using System.Net.Http;
		using System.Threading;
		using System.Threading.Tasks;
		
		namespace ContactNames
		{
		    public class InternalCredentialHandler : DelegatingHandler
		    {
		        protected override Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
		        {
		            Runtime.FromAppSettings(request).SignHttpRequest(request);
		            return base.SendAsync(request, cancellationToken);
		        }
		    }
		}

	Esse código chama `SignHttpRequest` para adicionar os cabeçalhos de autenticação a cada solicitação enviada pela classe de cliente gerada:

		Runtime.FromAppSettings(this.Request).SignHttpRequest

1. Em *ContactNamesController.cs*, substitua o código de modelo pelo código a seguir.

		using ContactNames.Models;
		using Microsoft.Azure.AppService.ApiApps.Service;
		using System;
		using System.Collections.Generic;
		using System.Net.Http;
		using System.Net.Http.Headers;
		using System.Threading.Tasks;
		using System.Web.Http;
		
		namespace ContactNames.Controllers
		{
		    public class ContactNamesController : ApiController
		    {
		        [HttpGet]
		        public async Task<IEnumerable<string>> Get()
		        {
		            var names = new List<string>();

		            var client = new ContactsList(new DelegatingHandler[] { new InternalCredentialHandler() });
		            var contacts = await client.Contacts.GetAsync();
		
		            foreach (Contact contact in contacts)
		            {
		                names.Add(contact.Name);
		            }		
		            return names;
		        }
		    }
		}

	Esse código passa o manipulador para o construtor da classe de cliente gerada:

		var client = new ContactsList(new DelegatingHandler[] { new InternalCredentialHandler() });

### Implantar

Você não pode testar executando localmente. Você deve implantar o código e executá-lo em um aplicativo de API do Azure. Caso contrário, você não poderá adicionar os cabeçalhos de autenticação corretos, e as chamadas serão rejeitadas.

As etapas de implantação a seguir são explicadas em mais detalhes em [Implantar um aplicativo de API](app-service-dotnet-deploy-api-app.md).

1. Crie um aplicativo de API ContactNames.

	* No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto (não na solução) e clique em **Publicar**. 

	* Clique na guia **Perfil** e clique em **Aplicativos de API do Microsoft Azure**.

	* Clique em **Novo** para provisionar um novo Aplicativo de API em sua assinatura do Azure.

	* Na caixa de diálogo **Criar um aplicativo de API**, digite ContactNames como o **Nome do aplicativo de API**.

	* Para os outros valores na caixa de diálogo **Criar um aplicativo de API**, insira os mesmos valores que você inseriu anteriormente para [Implantar um aplicativo de API](app-service-dotnet-deploy-api-app.md).

		O mais importante: crie o novo aplicativo de API no mesmo grupo de recursos que o aplicativo de API que você vai chamar.

	* Clique em **OK**.

2. Implante seu código no novo aplicativo de API.

	* Após o aplicativo de API ser provisionado, clique com o botão direito do mouse no projeto em **Gerenciador de Soluções** e clique em **Publicar** para reabrir a caixa de diálogo de publicação.

	* Na caixa de diálogo **Publicar Web**, clique em **Publicar** para iniciar o processo de implantação.

### Teste

Nesta seção, você pode usar a interface do usuário do Swagger para testar o novo aplicativo de API e verificar se ele pode chamar o aplicativo de API que você criou anteriormente.

1. Abra um navegador para a URL do novo aplicativo de API.

	Com as configurações de publicação padrão, quando o Visual Studio conclui o processo de publicação, ele abre automaticamente um navegador para a URL do aplicativo de API. Se isso não acontecer ou se você fechar essa janela do navegador, siga estas etapas para obter a mesma URL:

	* No portal de visualização do Azure, vá para a folha Aplicativo de API do novo aplicativo de API ContactsName.

	* Clique em **URL**.

		![](./media/app-service-api-dotnet-consume-internal/clickurl.png)
  
5. Na barra de endereço do navegador, adicione `/swagger` no fim da URL e pressione Enter.

	Por exemplo, a URL resultante terá esta aparência:

		https://microsoft-apiapp214f26e673e5449a214f26e673e5449a.azurewebsites.net/swagger

1. Na página da interface do usuário do Swagger, clique em **ContactNames > Obter > Experimente!**

	![](./media/app-service-api-dotnet-consume-internal/tryitout.png)
  
	A página exibe os nomes de contato na seção Corpo da Resposta, que verifica que o aplicativo de API ContactNames recuperou com sucesso dados do aplicativo de API ContactsList.

	Se você quiser verificar se a configuração **Interna** está protegendo o aplicativo de API ContactsList, comente a chamada `SignHttpRequest` em *ContactNamesController.cs*, reimplante, execute o recurso **Experimente agora** do Swagger novamente e você obterá uma mensagem de erro.


## Adicionar código para chamar o aplicativo de API usando HttpClient
 
O SDK do Serviço de Aplicativo depende de definições da API do Swagger para gerar classes de cliente. Se quiser chamar um aplicativo de API que não implementou as definições da API do Swagger, você poderá fazer isso usando `HttpClient`. Nesse caso, você ainda usa o método `SignHttpRequest`, mas o chama diretamente no objeto `HttpRequestMessage`.

1. Em *ContactNamesController.cs*, adicione o código a seguir antes da instrução `return names;` no método `Get`.

		var httpClient = new HttpClient();
		HttpRequestMessage httpRequest = new HttpRequestMessage();
		httpRequest.Method = HttpMethod.Get;
		httpRequest.RequestUri = new Uri("https://{yourapiappurl}/api/contacts");
		Runtime.FromAppSettings(this.Request).SignHttpRequest(httpRequest);
		var response = await httpClient.SendAsync(httpRequest); 
		var contacts2 = await response.Content.ReadAsAsync<List<Contact>>();
		foreach (Contact contact in contacts2)
		{
		    names.Add(contact.Name);
		}

	Esse código passa o objeto de solicitação de entrada ao método `SignHttpRequest` para assinar o objeto de solicitação de saída:

		Runtime.FromAppSettings(this.Request).SignHttpRequest(httpRequest);

2. No portal de visualização do Azure, vá para a folha de aplicativo de API do aplicativo de API ContactsList e copie a URL.

	![](./media/app-service-api-dotnet-consume-internal/clurl.png)
 
4. Substitua a cadeia de caracteres de espaço reservado "{yourapiappurl}" no código do controlador pela URL real. Quando você terminar, essa linha de código será semelhante ao exemplo a seguir.

		httpRequest.RequestUri = new Uri("https://microsoft-apiappd99e684d99e684d99e684d99e684.azurewebsites.net/api/contacts");

### Implantar e testar

1. Siga o mesmo procedimento que você realizou anteriormente para implantar o código do aplicativo de API.

	**Dica:** você pode ignorar a caixa de diálogo **Publicar Web** e reimplantar clicando em um único botão na barra de ferramentas. No Visual Studio, clique em **Exibir > Barras de ferramentas** e habilite a barra de ferramentas **Publicação Web com Um Clique**.
 
2. Siga o procedimento que você realizou anteriormente para usar a interface do usuário do Swagger.

	Como você manteve o código HttpClient, desta vez, a saída mostra um conjunto duplicado de nomes.

	![](./media/app-service-api-dotnet-consume-internal/dupnames.png)
  
## Próximas etapas

Este artigo mostrou como consumir um aplicativo de API interno de um cliente .NET. Para obter informações sobre como consumir aplicativos de API definidos com níveis de acesso **Público (autenticado)** e **Público (anônimo)**, confira [Consumir um aplicativo de API de um cliente .NET no Serviço de Aplicativo do Azure](app-service-api-dotnet-consume.md).

Para obter exemplos adicionais de um código que chame um aplicativo de API de clientes .NET, baixe o aplicativo de exemplo [Azure cartões](https://github.com/Azure-Samples/API-Apps-DotNet-AzureCards-Sample).

Para obter informações sobre a autenticação no Serviço de Aplicativo, consulte [Autenticação para aplicativos de API e aplicativos móveis](../app-service/app-service-authentication-overview.md).
 

<!---HONumber=Nov15_HO3-->