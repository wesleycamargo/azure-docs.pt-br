A maioria das propriedades no arquivo *apiapp.json* e nos arquivos da pasta *Metadata* afetam o modo como um pacote de aplicativos de API é apresentado no Azure Marketplace. As seções a seguir explicam quais arquivos e propriedades afetam aplicativos de API quando você implanta o código diretamente, em vez de instalar um aplicativo de API do Marketplace.

### ID do aplicativo de API 

A propriedade `id` determina o nome do aplicativo de API. Por exemplo:

		"id": "ContactsList",

![](./media/app-service-api-direct-deploy-metadata/apiappname.png)

### Namespace

Defina a propriedade `namespace` para o domínio do seu locatário do Active Directory do Azure. Para localizar seu domínio, abra seu navegador no [Portal clássico do Azure](https://manage.windowsazure.com/), procure **Active Directory** e selecione a guia **Domínios**. Por exemplo:

		"namespace": "contoso.onmicrosoft.com",

### Definição de API Swagger dinâmica

Para fornecer um ponto de extremidade de URL para uma definição de API [Swagger](http://swagger.io/) dinâmica, armazene na propriedade `endpoints.apiDefinition` a URL relativa de uma API exposta pelo aplicativo de API, que retorna uma definição de API Swagger 2.0 em uma solicitação GET. Por exemplo:

		"endpoints": {
		    "apiDefinition": "/swagger/docs/v1"
		}

### Definição de API Swagger estática

Para fornecer um arquivo de definição de API [Swagger](http://swagger.io/) 2.0 estático, armazene o arquivo na pasta *Metadata* e nomeie o arquivo como *apiDefinition.swagger.json*

![](./media/app-service-api-direct-deploy-metadata/apidefinmetadata.png)

Deixe `endpoints.apiDefinition` fora do arquivo *apiapp.json* ou definir seu valor como nulo. Se você incluir uma URL `endpoints.apiDefinition` e um arquivo *apiDefinition.swagger.json*, a URL terá precedência e o arquivo será ignorado.
 
### Outros metadados do aplicativo de API

Para obter mais informações sobre o arquivo *apiapp.json* e a pasta *Metadata*, consulte [Criar um pacote de aplicativos de API](app-service-api-create-package.md).


<!--HONumber=52-->
