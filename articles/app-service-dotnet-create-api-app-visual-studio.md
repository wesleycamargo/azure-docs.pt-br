<properties 
	pageTitle="Converter uma API existente em um aplicativo de API" 
	description="Saiba como configurar um projeto de API Web no Visual Studio para implantação como um aplicativo de API no Serviço de Aplicativo do Azure." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tdykstra"/>

# Converter uma API existente em um aplicativo de API

## Visão geral

Neste tutorial, você cria um projeto de API Web ASP.NET no Visual Studio e adiciona pacotes NuGet e metadados de projeto que lhe permitem publicar e implantar o projeto como um aplicativo de API no Serviço de Aplicativo do Azure.  O tutorial também explica como personalizar os metadados do aplicativo de API.

[AZURE.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## Criar um projeto de API Web

1.  Abra a caixa de diálogo **Novo Projeto** no Visual Studio.

2.  Selecione o nó **Nuvem** no painel **Modelos instalados** e selecione o modelo **Aplicativo Web ASP.NET**.

3.  Nomeie o projeto *ContactsList* e clique em **OK**.

	![New Project dialog](./media/app-service-dotnet-create-api-app-visual-studio/vstemplate.png)

4. Na caixa de diálogo **Novo Projeto ASP.NET**, selecione o modelo **Vazio**, clique na caixa de diálogo **API Web**, desmarque a caixa de seleção **Host na nuvem** e clique em **OK**.

	![Azure API App template](./media/app-service-dotnet-create-api-app-visual-studio/vstemplate2.png)

	O Visual Studio cria arquivos de projeto para um projeto de API Web vazio.

	![Web API files in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/sewebapi.png)

3. Em **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta **Modelos** e, no menu de contexto, clique em **Adicionar > Classe**. 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-class-v2.png) 

4. Nomeie o novo arquivo *Contact.cs* e clique em **Adicionar**.

5. Substitua o conteúdo do novo arquivo pelo código a seguir. 

		namespace ContactsList.Models
		{
			public class Contact
			{
				public int Id { get; set; }
				public string Name { get; set; }
				public string EmailAddress { get; set; }
			}
		}

5. Clique com o botão direito do mouse na pasta **Controladores** e, no menu de contexto, clique em **Adicionar > Controlador**. 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-controller.png)

6. Na caixa de diálogo **Adicionar Scaffold**, selecione a opção **Controlador de API Web 2 - Vazio** e clique em **Adicionar**. 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-controller-dialog.png)

7. Nomeie o controlador **ContactsController** e clique em **Adicionar**. 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-controller-name.png)

8. Substitua o código no novo arquivo de controlador pelo código a seguir. 

		using ContactsList.Models;
		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Threading.Tasks;
		using System.Web.Http;
		
		namespace ContactsList.Controllers
		{
		    public class ContactsController : ApiController
		    {
		        [HttpGet]
		        public IEnumerable<Contact> Get()
		        {
		            return new Contact[]{
		                new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
		                new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
		                new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"},
		            };
		        }
		    }
		}


Agora, você tem um projeto de API Web em funcionamento.  A maneira mais simples de verificar se ela funciona é chamar o método Get em um navegador.

6. Pressione CTRL + F5 para executar o projeto.

	O navegador exibe um erro de HTTP 403 porque ele é aberto sem uma URL completa que aponta para o método Get.

7. Na barra de endereço do navegador, adicione "api/contacts/get/" à URL e pressione Enter.  A URL final será parecida com o exemplo a seguir:

		http://localhost:25735/api/contacts/get/

	Diferentes navegadores respondem de forma diferente a uma chamada à API. Se estiver usando o Internet Explorer, você verá uma mensagem de download na parte inferior da janela do navegador:

	![](./media/app-service-dotnet-create-api-app-visual-studio/get-response.png)

## Configurar o aplicativo para implantação como um aplicativo de API

1. Em **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto (não na solução) e clique em **Adicionar > SDK do Aplicativo de API do Azure**.

	![Add API app metadata](./media/app-service-dotnet-create-api-app-visual-studio/addapiappsdk.png)

2. Na caixa de diálogo **Escolher Origem de Metadados do Aplicativo de API**, clique em **Geração Automática de Metadados**.

	![Choose automatic metadata](./media/app-service-dotnet-create-api-app-visual-studio/chooseswagger.png)

	Essa opção habilita a interface de usuário dinâmica do Swagger, que você verá mais adiante no tutorial.  Você também pode fornecer um arquivo de definição estático de API do Swagger.  Para fazer isso, você selecionaria **Especificar um arquivo JSON estático que contém metadados do Swagger**, e o Visual Studio solicitará que você carregue um arquivo.  O arquivo que você carregar será salvo como *apiDefinition.Swagger.json* na pasta *Metadata*.

3. Clique em **OK**.

	O Visual Studio adiciona um arquivo *apiapp.json* e uma pasta *Metadata*.

	![API App files in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/metadatainse.png)

	O Visual Studio também adiciona pacotes NuGet do Swashbuckle e, nas etapas a seguir, você experimenta a interface de usuário da definição de API dinâmica que o Swagger oferece. 

6. Pressione CTRL + F5 para executar o projeto.

	Como antes, o navegador exibirá um erro de HTTP 403.

7. Na barra de endereço do navegador, adicione "swagger/" à URL e pressione Enter.  A URL final será parecida com o exemplo a seguir:

		http://localhost:25735/swagger/

8. Na página do Swagger, clique em **Contatos** para ver os métodos disponíveis.
 
	Apenas `Get` é mostrado porque esse é o único método que você criou no controlador de Contatos.  A página mostra um exemplo de JSON de resposta.

9. Clique no método Get para ver um exemplo de JSON de resposta e um botão **Experimentar**.

 	![Swagger - expand Contacts](./media/app-service-dotnet-create-api-app-visual-studio/swagger1.png)

9. Clique em **Experimentar**.

	A resposta que você codificou em ContactsController será retornada.

 	![Swagger - Try it out](./media/app-service-dotnet-create-api-app-visual-studio/swagger2.png)

Seu projeto de API Web está pronto para ser implantado como um aplicativo de API no Serviço de Aplicativo do Azure.  As seguintes seções deste tutorial fornecem informações sobre os metadados que você pode alterar para personalizar seu aplicativo de API. 

## Revise o apiapp.json

As configurações no arquivo *apiapp.json* determinam como o Aplicativo de API é identificado e como ele é apresentado no Azure Marketplace.  Nesta versão de visualização, o Visual Studio não inclui a capacidade de publicar aplicativos de API no Marketplace. Por isso, muitas dessas configurações não têm efeito.

![apiapp.json in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/apiappjsoninse.png)

O conteúdo inicial do arquivo que é criado quando você escolhe o **SDK de Aplicativo de API do Azure** no menu se parece com o seguinte exemplo:

		{
		  "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		  "id": "ContactsList",
		  "namespace": "",
		  "gateway": "2015-01-14",
		  "version": "1.0.0",
		  "title": "ContactsList",
		  "summary": "",
		  "author": "",
		  "endpoints": {
		    "apiDefinition": "/swagger/docs/v1",
		    "status": null
		  }
		}

A tabela a seguir explica o formato e o uso dos campos incluídos no arquivo e os campos opcionais que você pode adicionar a ele. 

**Observação:** conforme mencionado acima, para esta versão de visualização muitos desses campos não têm nenhum efeito porque eles determinam como o aplicativo de API será apresentado no Azure Marketplace, mas o Visual Studio ainda não inclui o recurso de publicar aplicativos de API no Marketplace.

| Nome (negrito = obrigatório) | Tipo | Comentários |
|:-----------|:------------|:------------|
|**id**           |cadeia de caracteres|A ID do pacote.  Deve ser exclusivo dentro de um namespace e não pode pontos ou os caracteres / e @.  Quando você implanta o projeto, o Visual Studio valida se a ID é exclusiva e lhe dá a oportunidade de alterá-la. 
|**namespace**    |cadeia de caracteres|O namespace que, junto com a propriedade **id**, identifica exclusivamente o aplicativo de API.  A propriedade é obrigatória, mas o valor pode ser uma cadeia de caracteres vazia. <br/><br/>Essa propriedade permite que você especifique um domínio, como contoso.com.  Se a ID do pacote que você deseja usar já estiver em uso, você pode adicionar um domínio para poder usar essa ID.  Por exemplo, se ContactsList já existir na Galeria de Aplicativos de API sem um namespace, você pode adicionar um pacote ContactsList com o namespace contoso.com. <br/><br/>Outro motivo para especificar um domínio é adicionar um pacote à Galeria de Aplicativos de API que apenas membros da sua organização podem acessar. <br/><br/>O namespace será usado como o nome do editor no Marketplace, após converter pontos finais para hifens e hifens para dois hifens (--).<br/><br/>O namespace é "microsoft.com" para aplicativos de API fornecidos pela Microsoft.  
|**versão**      |cadeia de caracteres|Formato [Semver](http://docs.nuget.org/Create/Versioning), por exemplo, 1.0.1, 1.1.0-alpha.
|**gateway**      |cadeia de caracteres|Versão do gateway, expressa como uma data, por exemplo:  2015-01-14.  Um *gateway* é um aplicativo Web especial por meio do qual todas as solicitações de aplicativos de API de um grupo de recursos são encaminhadas.  Uma das suas principais funções é manipular a autenticação.  No momento, a única versão de gateway é 2015-01-14.  No futuro, quando novas versões de gateway forem lançadas, essa propriedade lhe dará a oportunidade de evitar alterações significativas e continuar usando a API de gateway anterior. 
|**título**        |cadeia de caracteres|O nome de exibição do aplicativo de API.
|**resumo**      |cadeia de caracteres|Um breve resumo do aplicativo de API, com no máximo 100 caracteres.
|descrição      |cadeia de caracteres|A descrição completa do aplicativo de API.  Pode conter HTML, máximo de 1500 caracteres.
|**autor**       |cadeia de caracteres|Autor(es) do aplicativo de API, máximo de 256 caracteres.
|home page         |URI|Home page do aplicativo de API.
|pontos de extremidade        |objeto[]|Uma matriz de um elemento que pode conter um ponto de extremidade de definição da API. 
|>>endpoints.apiDefinition|cadeia de caracteres|URI relativa da interface de usuário de definição dinâmica da API do Swagger (por exemplo, "/swagger/docs/v1") ou de um arquivo de definição estático da API do Swagger.  Para a API Web ASP.NET, a interface de usuário do Swagger gerada dinamicamente normalmente é a melhor opção, mas se isso não funcionar com sua API ou se você não estiver usando a API Web ASP.NET, você pode fornecer um arquivo de definição estático.  Para fornecer um arquivo de definição estático, você pode especificar o URI relativo que aponta para ele aqui, ou pode deixar essa propriedade em branco e incluir o arquivo de definição estático da API na pasta de metadados, como [apiDefinition.swagger.json](#apidef). 
|>>endpoints.status|URI|Reservado para uso futuro.
|categorias       |cadeia de caracteres[]|Determina onde o pacote será exibido no Azure Marketplace.  Os valores válidos são: social, empresarial, integração, protocolo e app-datasvc, outros.  Padrão: outros.
|licença          |objeto|A licença do aplicativo de API.
|>>**license.type**|cadeia de caracteres|O identificador de licença SPDX, como MIT.
|>>license.url    |url|A URL absoluta que aponta para o texto da licença completo.
|>>license.requireAcceptance|bool|Se uma licença precisa ser aprovada antes da instalação.  Padrão: falso.
|links            |objeto[]|Uma matriz de links para adicionar à página do Marketplace.
|>>**links.text** |cadeia de caracteres|O texto do link.
|>>**. URL**  |url|A URL do link.
|autenticação|objeto[]|Uma matriz que indica o tipo de autenticação de que o aplicativo de API precisa para fazer chamadas de API.  Por exemplo, um conector do DropBox precisa autenticar para o DropBox e um conector do Salesforce precisa autenticar para o Salesforce.
|>>authentication.type|cadeia de caracteres|Os valores com suporte são os seguintes (há diferenciação entre maiúsculas e minúsculas):  Box, DropBox, Facebook, GitHub, Google, Instagram, Marketo, Office365, OneDrive, Quickbooks, Salesforce, SharePointOnline, SugarCRM, Twitter, Yammer.  Com base nesse valor, o portal sabe quais valores de configuração, como ID do cliente e segredo do cliente, são necessários. 
|>>authentication.scopes|cadeia de caracteres[]|Uma matriz de escopos que são específicos ao tipo de autenticação.
|direitos autorais        |cadeia de caracteres|A notificação de direitos autorais do aplicativo de API.
|brandColor       |cadeia de caracteres|Uma cor de marca opcional para orientar a experiência da interface do usuário, em qualquer formato compatível com CSS, como #abc, vermelho.
|marcas             |cadeia de caracteres[]|Uma lista de marcas relacionadas ao pacote.

<!--todo: adicionar quando estiver pronto dependências do documento
|dependências    |object[]|Uma matriz de dependências do pacote.
|>>dependencies.id|string|A ID do pacote de dependências.
|>>dependencies.domain|string|O domínio do pacote de dependências.
|>>dependencies.version|string|A versão do pacote de dependências.
-->

<!--todo: adicionar quando estiver pronto para URI de status do documento
Um URI para um método Get de serviço Web que retorna um valor que indica o status atual do aplicativo de API.  Se você fornecer esse URI, o portal mostrará atual status operacional da API do aplicativo, juntamente com outras informações sobre o aplicativo de API, como:  em execução, se aproximando da cota de expiração de certificado SSL etc. O formato do JSON que o portal espera receber é mostrado abaixo, após o final desta tabela.  Se você não fornecer um URI endpoints.status, o portal mostra o status da plataforma do Azure como status do aplicativo de API.
Este é um exemplo que mostra o formato esperado da resposta JSON do método Get para o qual `endpoints.status` aponta:
		{
		  "status":[{ 
		    "name":"Nearing Quota", 
		    "message":"One or more quotas is nearing their limit", 
		    "level":"Warning" 
		  }, { 
		    "name":"Expiring Certificate", 
		    "message":"The SSL Certificate associated with this website is about to expire", 
		    "level":"Error"
		  }, {
		    "name":"Running", 
		    "message":"The website is handling traffic", 
		    "level":"Info"
		  }]
		}
A propriedade `name` é uma breve descrição do status, `message` é uma descrição mais detalhada e `level` pode ser "Error", "Warning" ou "Info" para um status normal.
-->

## Examinar a pasta de metadados

A pasta de metadados pode conter ícones e capturas de tela da Galeria de Aplicativos de API, um arquivo do Swagger documentando a API e a configuração de interface do usuário do Portal do Azure.  Todas essas informações são opcionais.

<!-- todo: add later
an Azure Resource Manager template that specifies resources required, 
-->

![Metadata folder in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/metadatafolderinse.png)

**Observação:** conforme mencionado acima para o *apiapps.json*, metadados relativos à apresentação do aplicativo de API no Azure Marketplace não têm nenhum efeito para esta versão de visualização, mas o Visual Studio ainda não inclui o recurso de publicar aplicativos de API no Marketplace.

### A pasta de metadados/ícones

Você pode fornecer ícones a serem exibidos na Galeria. 
Se você não fornecer ícones personalizados, ícones de aplicativos de API genéricos serão usados.  Arquivos de ícone devem estar no formato PNG e seguir essas convenções de nome e tamanho:

<!--todo: also used in the workflow designer--> 

|Nome do Arquivo|Tamanho
|:-----|:-----:
|small.png|40 X 40
|medium.png|90 X 90
|large.png|115X115
|hero.png|815 X 290
|wide.png|255 X 115

### A pasta de metadados/capturas de tela

Você pode fornecer até 5 capturas de tela a serem exibidas na Galeria.  Os arquivos de imagem devem estar no formato PNG, 533 x 324.

### <a id="apidef"></a>O arquivo de metadados/apiDefinition.swagger.json

Você pode usar um formato de arquivo do [Swagger 2.0](https://github.com/swagger-api/swagger-spec/blob/master/versions/2.0.md) para descrever a definição da API do aplicativo de API.  Isso permite que você exponha as definições da API estaticamente no pacote. 

<!--todo Explain why this is useful. Original text:
Static API definitions are required for workflow designers to understand the API App triggers and actions before provisioning.-->

<!-- todo: descobrir se há algo para substituir - poderia ser a subpasta deploymentTemplates.
### O arquivo resourceTemplate.delta.json
Você pode especificar um modelo personalizado do Gerenciador de Recursos do Azure que é executado durante a implantação do Aplicativo de API.  Os recursos especificados no arquivo delta são adicionados aos recursos que são criados por padrão para um aplicativo de API.  Por exemplo, se seu aplicativo de API exigir uma instância do Banco de Dados SQL você pode usar o arquivo para fazer com que o banco de dados seja provisionado automaticamente  e a cadeia de conexão seja definida nos parâmetros de configuração quando o Aplicativo de API for implantado.
-->  

### O arquivo de metadados/UIDefinition.json

Você pode fornecer informações de interface do usuário, como dicas e validação no [formado do Azure
Marketplace](https://auxdocs.azurewebsites.net/pt-br/documentation/articles/gallery-items).

### A pasta de metadados/deploymentTemplates

O Visual Studio cria essa pasta quando você escolhe a entrada **SDK do Aplicativo de API do Azure** no menu, mas para esta versão de visualização ela não é usada.

## Próximas etapas

Agora, seu projeto de API Web está pronto para ser implantado como um aplicativo de API, e você pode seguir o tutorial [Implantar um aplicativo de API](app-service-dotnet-deploy-api-app.md) para fazer isso.

Para saber mais, consulte [O que são Aplicativos de API?](app-service-api-apps-why-best-platform.md)

<!--HONumber=49-->