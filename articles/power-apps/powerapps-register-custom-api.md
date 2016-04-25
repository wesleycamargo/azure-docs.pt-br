<properties
	pageTitle="Como usar APIs Personalizadas em Fluxos Lógicos e no PowerApps | Microsoft Azure"
	description="Definição de APIs personalizadas, uso de provedores do OAuth e uso do Swagger para adicionar APIs personalizadas a PowerApps e Fluxos Lógicos"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="sunaysv"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="04/12/2016"
   ms.author="mandia"/>

# O que são APIs Personalizadas

APIs Personalizadas são quaisquer APIs RESTful que podem ser colocadas e usadas com o PowerApps e os Fluxos Lógicos. Essas APIs podem ser hospedadas em qualquer lugar, desde que uma especificação bem documentada em conformidade com o padrão [OpenAPI][1] esteja disponível.

## Para começar, você precisa do seguinte:

- As APIs Personalizadas podem ser usadas por qualquer pessoa que esteja usando o PowerApps. Não é necessário ter o PowerApps Enterprise. 
- É necessário ter um arquivo do Swagger (.json) e um ícone para a API personalizada. Este tópico fornece várias opções para a criação do arquivo do Swagger. Como ícone, é possível usar qualquer imagem que desejar.


## Autenticação

É possível usar um dos seguintes mecanismos de autenticação:

- Autenticação básica 
- OAuth 2.0: veja a seguir uma lista de todos os provedores do OAuth 2.0 com suporte que podem ser usados com a API personalizada (suporte para mais provedores em breve):	

	- Active Directory do Azure
	- Box
	- Dropbox
	- Facebook
	- Google
	- Instagram
	- OneDrive
	- SalesForce
	- Margem de atraso
	- Yammer

> [AZURE.NOTE] O suporte para a autenticação de chave de API estará disponível em breve.


É possível saber mais sobre como especificar o tipo de autenticação no documento de OpenAPI (Swagger) na [OpenAPI Specification][1].

Caso o ponto de extremidade de API permita o acesso não autenticado, será necessário remover o objeto ```securityDefintions``` do arquivo de OpenAPI (Swagger). No exemplo a seguir, remova todo o objeto ```securityDefintions```, mostrado abaixo:

```
  "securityDefinitions": {
    "AAD": {
      "type": "oauth2",
      "flow": "implicit",
      "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
      "scopes": []
    }
  },
```

> [AZURE.TIP] Em arquivos .json, não é possível adicionar comentários; todo o texto é considerado parte dos dados.

### Exemplos de autenticação
* [Azure Resource Manager](powerapps-azure-resource-manager-tutorial.md) com autenticação AAD
* [Aplicativo Web do Azure](powerapps-web-api-tutorial.md) com autenticação AAD

## Registrar uma API Personalizada

### Etapa 1: Criar um arquivo do Swagger

É possível criar um arquivo do Swagger por meio de **qualquer** ponto de extremidade de API, incluindo:

- Qualquer API que seja publicada e disponibilizada publicamente. Alguns exemplos incluem [Spotify][2], [Uber][3], [Slack][4], [Rackspace][5], e muito mais. 
- Uma API criada e implantada em qualquer serviço de nuvem no qual seja possível implantar aplicativos Web, incluindo AWS (Serviços Web da Amazon), Heroku, aplicativos Web do Azure, Google Cloud e muito mais.  
- Uma API implantada na rede ou no computador, desde que a API esteja disponível publicamente na Internet.

Ao criar o arquivo do Swagger, um arquivo .json é criado. Mantenha esse arquivo .json acessível.

#### Obter ajuda para criar arquivos do Swagger

- Caso não esteja familiarizado com a criação de um arquivo do Swagger ou se nunca criou um antes, a página [Get started with Swagger][6] (Introdução ao Swagger) é um bom recurso. 
 
- Para criar sua própria API, implante-a no Azure e crie um arquivo do Swagger baseado nessa nova API. Depois, considere o uso do [Web API tutorial](powerapps-web-api-tutorial.md) (Tutorial da API Web). Este tutorial fornece um arquivo de trabalho do Swagger. Você também pode encontrar um [exemplo do Hello World][7] no GitHub.

- Para validar os arquivos do Swagger, use o [editor do Swagger][8]. É possível colar os dados do .json e a validação ocorrerá automaticamente.

- Para personalizar o documento do Swagger para trabalhar com o PowerApps e os Fluxos Lógicos, veja [Customize your Swagger definition](powerapps-how-to-swagger.md) (Personalizar a definição do Swagger).

### Etapa 2: Adicionar uma conexão com a API personalizada
Agora que o arquivo do Swagger (.json) é gerado para a API personalizada, adicione a conexão ao PowerApps. Você também precisará do ícone para a API personalizada.

1. Vá para o [portal da Web][9] do PowerApps e entre com sua conta corporativa.  

	> [AZURE.NOTE] No momento, as APIs personalizadas podem ser usadas apenas no portal da Web do PowerApps. Elas não podem ser usadas no cliente do PowerApps.

2. Selecione **Conexões** e **Adicionar uma conexão**: ![](./media/powerapps-register-custom-api/createnewconnection.png "Criar API Personalizada")

3. Selecione **Adicionar uma API personalizada**: ![](./media/powerapps-register-custom-api/connecttocustomapi.png "Criar API Personalizada") Adicionar as propriedades de sua API, incluindo os arquivos .json e de ícone. Em seguida, selecione **Avançar**:

	|Propriedade|Descrição|
|---|---|
|Nome|Insira o nome da API personalizada. Se esta for a API Web de exemplo, você poderá nomeá-la **MySampleWebAPI**.|
|Definição de API do Swagger|Navegue até o arquivo .json criado por meio do Swagger.|
|Carregar o ícone de API|Navegue até o arquivo de ícone.|
|Descrição|Insira uma descrição da API personalizada. Se esta for a API Web de exemplo, você poderá entrar no **Tutorial da API Web de exemplo**.|

4. Insira as propriedades de autenticação. Se o arquivo .json usar a autenticação OAuth2 no objeto ```securityDefintions```, será solicitado que você insira os seguintes valores:

	|Propriedade|Descrição|
|---|---|
|ID do cliente|Usando um dos provedores de identidade do OAuth com suporte (listados neste tópico), uma ID do cliente é fornecida. Insira esta ID do cliente.
|Segredo do cliente|Insira o segredo do cliente do provedor de identidade escolhido.|  

	Os **Exemplos de autenticação** descritos neste tópico fornecem exemplos dessas propriedades do OAuth.

	Se o arquivo .json não usar o objeto ```securityDefintions```, talvez não seja necessário inserir nenhum valor adicional.

5. Selecione **Criar**. A API personalizada agora é exibida em **Conexões Disponíveis**:

	![](./media/powerapps-register-custom-api/mycustomapi.png "Conexões disponíveis")


> [AZURE.TIP] Caso os arquivos do Swagger não sejam validados, poderá haver caracteres extras. Por exemplo, na maioria das vezes, todos os dados devem estar entre aspas, incluindo sites. Portanto, se o `https://mywebapi.mywebsite.com` estiver fora de aspas, o arquivo não será validado.


### Etapa 3: Adicionar a API personalizada a um Fluxo Lógico e ao PowerApp
Agora, você está pronto para usar a API personalizada com o PowerApp ou o Fluxo Lógico. Nesta seção, usamos uma API personalizada de Clima.

#### Adicionar a API personalizada ao fluxo lógico
Nesta etapa, criamos um fluxo lógico bem simples que mostra como adicionar a API personalizada. Para obter uma experiência mais detalhada, veja [Get started with logic flows][10] (Introdução aos fluxos lógicos).

1. No [portal da Web][9] do PowerApps, selecione a guia **Início**.
2. Em **Criar um fluxo lógico**, selecione **Introdução**. 
3. Nessa janela, há vários modelos de fluxo lógico já criados que usam alguns cenários comuns. Você pode usar qualquer um deles e adicionar sua API personalizada. Outra opção é escolher **Criar em branco** para criar um fluxo lógico do zero.  

	A maneira mais rápida de adicionar a API personalizada é selecionar **Criar em branco**. Isso abrirá o seguinte fluxo lógico: ![](./media/powerapps-register-custom-api/createfromblank.png "Início do Fluxo Lógico")

4. Selecione **Recorrência** e defina a frequência como 1 minuto: ![](./media/powerapps-register-custom-api/logicrecurrence.png "Selecionar Recorrência")

5. Selecione o sinal de adição (![](./media/powerapps-register-custom-api/flowplussign.png)) e selecione **Adicionar uma ação**. Na lista, a API personalizada é listada: ![](./media/powerapps-register-custom-api/logicflow.png "Sua API personalizada")

As próximas etapas são determinadas pelas ações que podem ser executadas pela API. Em um exemplo de clima, talvez a API obtenha a temperatura atual e envie um email usando o Office 365:

![](./media/powerapps-register-custom-api/logicflowexample.png "Exemplo de clima")



#### Adicionar a API personalizada ao PowerApp
Nesta etapa, criamos um PowerApp bem simples que mostra como adicionar a API personalizada. Para obter uma experiência mais detalhada, veja [Create an app from data][11] (Criar um aplicativo com base em dados).

> [AZURE.NOTE] No momento, as APIs personalizadas podem ser usadas apenas no portal da Web do PowerApps. Elas não podem ser usadas no cliente do PowerApps.

1. No [portal da Web][9] do PowerApps, selecione **Novo PowerApp**: ![](./media/powerapps-register-custom-api/newpowerapp.png "Selecionar Novo PowerApp")  
2. Uma nova guia é aberta no navegador. Nesta nova guia, um PowerApp em branco é criado automaticamente. Selecione **conectar-se a dados**: ![](./media/powerapps-register-custom-api/blankpowerapp.png "Selecionar conectar a dados")  
3. Na guia **Conteúdo**, selecione **Fontes de dados**: ![](./media/powerapps-register-custom-api/datasources.png "Selecionar conectar a dados")  
4. Na nova tela, em **Minhas conexões**, selecione a API personalizada: ![](./media/powerapps-register-custom-api/screencustomapi.png "Selecionar a API personalizada")  
5. Clique em **Adicionar fonte de dados**.

Depois de adicionada, a API personalizada pode ser usada na barra de função, em uma caixa de texto e muito mais. Por exemplo, na barra de função, você pode começar digitando **MySampleWebAPI** para ver as funções disponíveis. [Mostrar dados do Office 365][12] é um exemplo do uso da API do Office 365.


## Compartilhando uma API Personalizada
Os usuários também podem compartilhar APIs personalizadas entre si. Depois de adicionar uma API personalizada, selecione a guia **Conexões**, **APIs Personalizadas** e o ícone de compartilhamento:

![](./media/powerapps-register-custom-api/sharecustomapi.png "Compartilhar a API personalizada")

> [AZURE.NOTE] Você pode compartilhar APIs personalizadas com outros usuários apenas em sua organização.

## Cotas e limitação

- É possível criar até cinco APIs personalizadas em uma conta do PowerApps. APIs personalizadas que são compartilhadas com você não contam para essa cota.
- Para cada conexão criada em uma API personalizada, os usuários podem fazer até 500 solicitações por minuto.
- Tenha em mente que a exclusão de uma API personalizada exclui todas as conexões criadas a ela. 

Em caso de dúvidas ou comentários sobre as APIs personalizadas, envie um email para [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com).

<!--Reference links in article-->
[1]: https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#securityDefinitionsObject
[2]: https://developer.spotify.com/
[3]: https://developer.uber.com/
[4]: https://api.slack.com/
[5]: http://docs.rackspace.com/
[6]: http://swagger.io/getting-started/
[7]: https://github.com/OAI/OpenAPI-Specification/wiki/Hello-World-Sample
[8]: http://editor.swagger.io/#/
[9]: https://web.powerapps.com
[10]: https://powerapps.microsoft.com/tutorials/get-started-logic-flow/
[11]: https://powerapps.microsoft.com/tutorials/get-started-create-from-data/
[12]: https://powerapps.microsoft.com/tutorials/show-office-data/

<!---HONumber=AcomDC_0413_2016-->