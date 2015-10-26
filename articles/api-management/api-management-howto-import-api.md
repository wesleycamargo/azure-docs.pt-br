<properties 
	pageTitle="Principais conceitos de Gerenciamento de API" 
	description="Saiba mais sobre APIs, produtos, funções, grupos e outros conceitos principais do Gerenciamento de API." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/10/2015" 
	ms.author="sdanie"/>

# Como importar a definição de uma API com operações no Gerenciamento da API do Azure

No Gerenciamento de API, novas APIs podem ser criadas e as operações podem ser adicionadas manualmente, ou a API pode ser importada em conjunto com as operações em uma etapa.

As APIs e suas operações podem ser importadas usando os formatos a seguir.

-	WADL
-	Swagger

Este guia mostra como criar uma nova API e importar suas operações em uma etapa. Para obter mais informações sobre como criar uma API e adicionar operações manualmente, consulte [Como criar APIs][] e [Como adicionar operações a uma API][].

## <a name="import-api"> </a>Importar uma API

APIs são criadas e configuradas no Portal do editor. Para acessar o Portal do editor, clique em **Gerenciar** no Portal do Azure para acessar o serviço de Gerenciamento de API. Se você ainda não criou uma instância de serviço de Gerenciamento de API, consulte [Criar uma instância de serviço de Gerenciamento de API][] no tutorial [Introdução ao Gerenciamento de API do Azure][].

![Portal do editor][api-management-management-console]

Clique em **APIs** no menu **Gerenciamento de API** à esquerda e depois clique em **Importar API**.

![Importar API][api-management-import-apis]

A janela **Importar API** possui três guias que correspondem às três maneiras de fornecer a especificação da API.

-	**A partir da área de transferência** permite que você cole a especificação da API na caixa de texto designada.
-	**A partir do arquivo** permite que você navegue até e selecione o arquivo que contém a especificação da API.
-	**A partir da URL** permite que você forneça a URL até a especificação para a API.

![Importar formato de API][api-management-import-api-clipboard]

Após fornecer a especificação da API, use os botões de opção à direita para indicar o formato da especificação. Os formatos a seguir são suportados.

-	WADL
-	Swagger

Em seguida, insira um **sufixo de URL de API Web**. Ele está anexado à URL base do serviço de gerenciamento de API. A URL base é comum para todas as APIs hospedadas em cada instância de um serviço de Gerenciamento da API. O Gerenciamento da API diferencia as APIs pelo sufixo e, portanto, o sufixo deve ser único para cada API em uma instância de serviço de gerenciamento de API específica.

Após inserir todos os valores, clique em **Salvar** para criar a API e as operações associadas.

>[AZURE.NOTE]Para obter um tutorial de importação de uma API básica de calculadora no formato Swagger, consulte [Gerenciar sua primeira API no Gerenciamento de API do Azure](api-management-get-started.md).

## <a name="export-api"> </a>Exportar uma API

Além de importar novas APIs, você pode exportar as definições de suas APIs no Portal do editor. Para fazer isso, clique em **Exportar API** na **guia de Resumo** de sua **API**.

![Exportar API][api-management-export-api]

As APIs podem ser exportadas usando WADL ou Swagger. Selecione o formato desejado, clique em **Salvar** e escolha o local onde deseja salvar o arquivo.

![Exportar formato de API][api-management-export-api-format]

## <a name="next-steps"> </a>Próximas etapas

Após criar a API e importar as operações, você pode revisar e definir quaisquer configurações adicionais, adicionar a API a um produto e publicá-la para que fique disponível para desenvolvedores. Para obter mais informações, consulte os guias a seguir.

-	[Como definir configurações de API][]
-	[Como criar e publicar um produto][]




[api-management-management-console]: ./media/api-management-howto-import-api/api-management-management-console.png
[api-management-import-apis]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
[api-management-import-api-clipboard]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
[api-management-export-api]: ./media/api-management-howto-import-api/api-management-export-api.png
[api-management-export-api-format]: ./media/api-management-howto-import-api/api-management-export-api-format.png

[Import an API]: #import-api
[Export an API]: #export-api
[Configure API settings]: #configure-api-settings
[Next steps]: #next-steps

[Introdução ao Gerenciamento de API do Azure]: api-management-get-started.md
[Criar uma instância de serviço de Gerenciamento de API]: api-management-get-started.md#create-service-instance

[Como adicionar operações a uma API]: api-management-howto-add-operations.md
[Como criar e publicar um produto]: api-management-howto-add-products.md
[Como criar APIs]: api-management-howto-create-apis.md
[Como definir configurações de API]: api-management-howto-create-apis.md#configure-api-settings

<!---HONumber=Oct15_HO3-->