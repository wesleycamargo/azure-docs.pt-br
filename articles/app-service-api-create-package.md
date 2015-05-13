<properties 
	pageTitle="Criar um pacote de aplicativos de API" 
	description="Saiba como criar um pacote de aplicativos de API." 
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
	ms.date="04/20/2015" 
	ms.author="guayan"/>

# Criar um pacote de aplicativos de API

## Visão geral

Este artigo mostra como criar o pacote de aplicativos de API para que você possa publicá-lo para o [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/).

- Para saber como criar um aplicativo de API, consulte [Criar um aplicativo de API usando o Visual Studio](app-service-dotnet-create-api-app.md).
- Para saber como publicar um pacote de aplicativos de API no Azure Marketplace, consulte [Publicar um pacote de aplicativos de API no Azure Marketplace](app-service-api-publish-package).

## Estrutura de pastas

Um pacote do Nuget \(arquivo \*.nupkg\) para um aplicativo de API tem os seguintes arquivos e pastas sob a pasta *Conteúdo*:

    apiapp.json
    Metadata
        apiDefinition.swagger.json
        icons
            <icon files>
        screenshots
            <screenshot files>
        deploymentTemplates
            <template files>
        UIDefinition.json
    <other artifacts like source code, binary, etc.>

Um arquivo *.nupkg* é criado com essa estrutura de pastas ao empacotar um projeto do Visual Studio que tem *apiapp.json* e *Metadados* na pasta do projeto, como mostra a ilustração a seguir:

![apiapp.json e metadados no Gerenciador de soluções](./media/app-service-api-create-package/metadatainse.png)

As seções a seguir documentam cada arquivo e pasta na estrutura de pastas do aplicativo de API antes de mostrar como empacotar e implantar um projeto.

## apiapp.json

Este é o arquivo de manifesto para seu aplicativo de API.

|Nome \(em negrito = requerido\)|Tipo|Formatar|Descrição|
|:---------------------|:-----|:-------|:------------|
|**ID**|string|[a-zA-Z0-9 \_.]|A ID desse pacote. Deve ser exclusiva dentro de um namespace e pode conter apenas caracteres alfanuméricos, "\_" e ".". Deve começar com um caractere alfanumérico.|
|**namespace**|string|nome de domínio|O namespace que, junto com a propriedade **id**, identifica exclusivamente o aplicativo de API. Deve ser um dos nomes de domínio de locatário do AAD do editor.|
|**versão**|string|[semver](http://docs.nuget.org/Create/Versioning)|A versão deste pacote. Quando a atualização automática está habilitada por usuários para este pacote, ela será aplicada apenas a novas versões dentro da mesma versão principal.|
|**gateway**|string|2015-01-14|A versão de API do gateway que este pacote está usando. Um gateway é um aplicativo Web especial por meio do qual todas as solicitações para aplicativos de API em um grupo de recursos são roteados. Uma das suas principais funções é tratar a autenticação. No momento única versão de gateway é 2015-01-14. No futuro, quando novas versões de gateway forem lançadas, essa propriedade lhe dará a oportunidade de evitar alterações significativas e de continuar a usar o API de gateway anterior.| 
|**título**|string||O nome exibido do aplicativo de API.|
|**resumo**|string|máximo de 100 caracteres|Um breve resumo do aplicativo de API.
|description|string|máximo de 1500 caracteres|A descrição completa do aplicativo de API. Pode conter HTML. Os elementos e atributos permitidos são "h1", "h2", "h3", "h4", "h5", "p", "ol", "ul", "li", "a[target\|href]", "br", "strong", "em", "b" e "i".|
|**autor**|string|máximo de 256 caracteres|Autor\(es\) do aplicativo de API.|
|home page|string|URL|Home page do aplicativo API.|
|pontos de extremidade|objeto||Uma lista de pontos de extremidade que a plataforma de aplicativo de API pode consultar para obter informações sobre os métodos e o status do aplicativo de API.|
|endpoints.apiDefinition|string|Caminho da URL|URL relativa de uma API exposta pelo aplicativo de API que retorna uma definição de API Swagger 2.0 em uma solicitação GET \(por exemplo, "/ documentos/swagger/v1"\). Se for definido, ele será usado em vez do arquivo estático apiDefinition.swagger.json no pacote, se houver algum.|
|endpoints.status|string|Caminho da URL|URL relativa de uma API exposta pelo aplicativo de API que retorna informações de status de tempo de execução sobre o aplicativo de API em uma solicitação GET.|
|categorias|cadeia de caracteres[]|comunidade, social, enterprise, integração, protocolo, aplicativo datasvc, outros|A categoria do Azure Marketplace na qual esse pacote de aplicativos de API aparecerá. Por padrão, o aplicativo de API sempre aparecerá na categoria de comunidade. Depois que o aplicativo de API for aprovado, ele aparecerá na categoria especificada.|
|licença|objeto||A licença do aplicativo de API.|
|**license.type**|string||O identificador da licença SPDX, p. ex., MIT.|
|license.url|string|URL|A URL absoluta apontando para o texto de licença completa.|
|license.requireAcceptance|bool|verdadeiro, falso|Se uma licença precisa ser aceita antes da instalação. Padrão: falso.|
|links|objeto[]||Uma matriz de links para adicionar à página do Marketplace.|
|**links.text**|string||O texto do link.|
|**links.url**|string|URL|A URL absoluta do link.|
|autenticação|objeto[]||Uma matriz que indica o tipo de autenticação que esse aplicativo API precisa para fazer chamadas de API. No momento, apenas uma autenticação tem suporte por pacote de aplicativo de API.|
|**authentication.type**|string|Box, DropBox, Facebook, Google, Office365, OneDrive, Quickbooks, Salesforce, SharePointOnline, Twitter, Yammer|A autenticação requerida pelo aplicativo de API. Atualmente, o tipo de autenticação 11 tem suporte. Mais tipos serão adicionados no futuro.| 
|authentication.scopes|cadeia de caracteres[]||Uma matriz de escopos que são específicos para o tipo de autenticação.|
|copyright|string|O aviso de direitos autorais do aplicativo de API.
|brandColor|string|Qualquer formato compatível com o CSS|Uma cor de marca opcional para orientar a experiência de interface do usuário. Por exemplo, o designer de aplicativo lógico usa essa propriedade para desenhar a cor de plano de fundo do cabeçalho do cartão.|

## metadata/apiDefinition.swagger.json

Opcionalmente, você pode fornecer um arquivo estático Swagger 2.0 JSON aqui para expor a definição de API do seu aplicativo de API. A plataforma verificará primeiro se a propriedade **endpoints.apiDefinition** está configurada em **apiapp.json**. Em caso positivo, ele receberá a definição de API da URL especificada na propriedade. Caso contrário, ele vai tentar localizar esse arquivo.

- Para obter informações sobre o padrão de Swagger 2.0, consulte [http://swagger.io/](http://swagger.io/). \<!--todo provide URLs
- Para obter informações sobre como personalizar a definição de API para otimizá-lo para aplicativos lógicos, consulte o [título do documento]().
- Para obter informações sobre como exibir uma definição de API dinâmica, consulte [título do documento](). --\>

## metadados/ícones

Opcionalmente, você pode fornecer seu próprio conjunto de ícones para seu pacote de aplicativos de API. Se os arquivos de ícone obrigatórios não forem fornecidos, um ícone padrão será usado conforme mostrado abaixo.

![Ícone grande padrão do aplicativo de API](./media/app-service-api-create-package/api-app-default-large-icon.png)

|Arquivo |Largura|Altura|Descrição|
|:--------------------|:----|:-----|:----------|
|metadata/icons/small.png|40px|40px|Obrigatório se você desejar usar seus próprios ícones.|
|metadata/icons/medium.png|90px|90px|Obrigatório se você desejar usar seus próprios ícones.|
|metadata/icons/large.png|115px|115px|Obrigatório se você desejar usar seus próprios ícones.|
|metadata/icons/wide.png|255px|115px|Obrigatório se você desejar usar seus próprios ícones.|
|metadata/icons/hero.png|815px|290px|Opcional se você desejar usar seus próprios ícones.|

## metadata/screenshots

Opcionalmente, você pode fornecer 5 capturas de tela para seu pacote de aplicativos de API.

|Arquivo|Largura|Altura|Descrição|
|:--------------------|:----|:-----|:----------|
|metadata/screenshots/\*.png|533px|324px|Capturas de tela do seu pacote de aplicativos de API.|

## metadata/deploymentTemplates

Às vezes, um pacote de aplicativos de API exige alguma configuração personalizada durante a implantação. Por exemplo, o [Conector de Blob de armazenamento do Azure](http://azure.microsoft.com/marketplace/partners/microsoft_com/azurestorageblobconnector/) requer o URI do contêiner do blob de armazenamento do Azure. Opcionalmente, você também pode configurar uma chave de acesso.

Para oferecer suporte a esse cenário, você pode adicionar uma lista de arquivos JSON de modelo do ARM \(Azure Resource Manager\) nessa pasta para personalizar a implantação do aplicativo de API. A plataforma do aplicativo de API mesclará seus modelos personalizados de ARM com nosso modelo de sistema para produzir um modelo final para implantação. Todos os parâmetros definidos em seus modelos de ARM personalizados \(esperado para **$system**\) também serão solicitados automaticamente na folha **criar** do portal de visualização do Azure, para que os usuários de seu aplicativo de API possam inserir os valores.

Abaixo, uma amostra de modelo ARM demonstrando como solicitar o URI do contêiner de blob e chave de acesso durante a implantação de aplicativos de API.

    {
      "$schema": "http://schemas.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "$system": {
          "type": "object"
        },
        "BlobConnector_ContainerUrl": {
          "type": "string"
        },
        "BlobConnector_AccessKey": {
          "type": "securestring"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-04-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('$system').siteName]",
          "location": "[parameters('$system').location]",
          "properties": {
            "siteConfig": {
              "appSettings": [
                {
                  "name": "BlobConnector_ContainerUrl",
                  "value": "[parameters('BlobConnector_ContainerUrl')]"
                },
                {
                  "name": "BlobConnector_AccessKey",
                  "value": "[parameters('BlobConnector_AccessKey')]"
                }
              ]
            }
          }
        }
      ]
    }

A folha Criar do portal de visualização do Azure correspondente é mostrada na captura de tela abaixo. \(A captura de tela mostra um pacote de aplicativos de API usando UIDefinition.json para aprimorar a folha criar. Para obter detalhes, consulte [metadata/UIDefinition.json](#metadata-uidefinition-json).

![Exemplo de folha Criar de modelo ARM personalizado](./media/app-service-api-create-package/custom-arm-template-create-blade-example.png)

Para obter mais informações, consulte [Linguagem de Modelo do Gerenciador de Recursos do Azure \(a página pode estar em inglês\)](https://msdn.microsoft.com/library/dn835138).

## metadata/UIDefinition.json

Quando você precisa usar modelos ARM personalizados para especificar a configuração personalizada necessária para implantar um aplicativo de API, a folha **criar** do Portal de Visualização do Azure solicita automaticamente os parâmetros de modelo do ARM, para que os usuários do seu aplicativo de API possam inserir os valores. Você pode usar o arquivo *UIDefinition.json* para melhorar a interface do usuário dessa folha **criar**, fornecendo valores padrão, dicas de ferramentas, validações, etc.

Para fornecer um *arquivo UIDefinition.json*, comece com o seguinte esqueleto e siga a tabela a seguir para configurar cada parâmetro.


    {
      "parameters": {
        "<your API app package id>": {
            "<name of the ARM template parameter>": { },
            "<name of the ARM template parameter>": { }
        }
      }
    }

|Nome |Tipo|Descrição|
|:---------------------|:-------|:------------|
|defaultValue|string|O valor padrão de controle de entrada na folha criar do Portal de Visualização do Azure. |
|displayName|string|O rótulo de controle de entrada na folha criar do Portal de Visualização do Azure.  Ele deve ser curto.|
|description|string|Descrição do controle de entrada.|
|dica de ferramenta|string|Dica de ferramenta de controle de entrada na folha criar do Portal de Visualização do Azure.  Ela será exibida quando os usuários clicarem na bolha de informações no lado direito do rótulo. Ela pode ser longa e abrangente.|
|restrições|Objeto|Restrições a verificar em relação ao parâmetro.|
|constraints.required|bool|Se o parâmetro é necessário ou não. O padrão é falso.|

Por exemplo, aqui está o arquivo *UIDefinition.json* para o [Conector de Blob de armazenamento do Azure](http://azure.microsoft.com/marketplace/partners/microsoft_com/azurestorageblobconnector/):

    {
      "parameters": {
        "AzureStorageBlobConnector": {
          "BlobConnector_ContainerUrl": {
            "defaultValue": "",
            "displayName": "Container/SAS URI",
            "description": "Specify the URI of the Blob Container. The URI may also include the SAS token. Example http://storageaccountname.blob.core.windows.net/containername or http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah",
            "tooltip": "Specify the URI of the Blob Container. The URI may also include the SAS token. Example http://storageaccountname.blob.core.windows.net/containername or http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah",
            "constraints": {
              "required": "true"
            }
          },
          "BlobConnector_AccessKey": {
            "defaultValue": "",
            "displayName": "Access Key",
            "description": "Specify a valid primary/secondary storage account access key. Leave this field empty if you are using SAS token for authentication.",
            "tooltip": "Specify a valid primary/secondary storage account access key. Leave this field empty if you are using SAS token for authentication.",
            "constraints": {
              "required": "false"
            }
          }
        }
      }
    }

Isso configura a folha **criar** do Portal de Visualização do Azure para mostrar a folha **Configurações do Pacote** a seguir.

![Exemplo de folha Criar de UIDefinition](./media/app-service-api-create-package/uidefinition-create-blade-example.png)

<!--todo add when ready to document status URI
A URI to a web service Get method that returns a value that indicates the API app's current status. If you provide this URI, the portal will show the API app's current operational status along with other information about the API app, for example:  running, nearing quota, SSL certificate expiring, etc. The format of the JSON the portal expects to receive is shown below, following the end of this table. If you don't provide an endpoints.status URI, the portal shows the Azure platform status as the API app's status.
Here is an example that shows the expected format of the JSON response from the Get method that `endpoints.status` points to:
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
The `name` property is a short description of the status, `message` is a longer description, and `level` can be "Error", "Warning", or "Info" for normal status.
-->

## Criar um pacote de aplicativos de API

Para criar um pacote de aplicativos de API

1. [Baixar e instalar a Interface de linha de comando entre plataformas do Azure](xplat-cli).
2. Alternar para o modo do Gerenciador de recursos do Azure executando o seguinte comando

        azure config mode arm

3. Criar um pacote do nuget para seu aplicativo de API executando o seguinte comando

        azure apiapp package create -p <package folder> -o <destination folder>

    Por exemplo:

        azure apiapp package create -p c:\ContactList\ContactList -o c:\ContactListPackage

Isso validará o conteúdo do pacote para certificar-se

- Ele segue o formato descrito acima
- Metadata\\apiDefinition.swagger.json \(se fornecido\) contém a definição válida de API do Swagger 2.0

Se houver qualquer problema, ele exibirá os detalhes para que você possa corrigir o problema e criar um pacote de aplicativos de API válido.

## Próximas etapas

Seu pacote de aplicativos de API agora está pronto para ser publicado com o Azure Marketplace. Para saber como fazer isso, execute o tutorial [Publicar um pacote de aplicativos de API no Azure Marketplace](app-service-api-publish-package).

<!--HONumber=52-->
