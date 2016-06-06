<properties 
	pageTitle="Como criar APIs, operações e produto no Gerenciamento de API do Azure" 
	description="Aprenda a criar produtos, operações e APIs no Gerenciamento de API." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/25/2016" 
	ms.author="sdanie"/>

# Como criar APIs, operações e produto no Gerenciamento de API do Azure

No Gerenciamento de API do Azure, as APIs e suas operações são adicionadas aos produtos, onde podem ser usadas pelos desenvolvedores para criar aplicativos que usam APIs. Os guias desta seção mostram como criar uma API, adicionar operações a ela e depois associar a API a um produto e publicá-la para que os desenvolvedores a utilizem.

## <a name="create-apis"> </a>Como criar APIs

Uma API no Gerenciamento de API representa um conjunto de operações que pode ser invocado pelas aplicações clientes. Novas APIs são criadas no Portal do editor.

Este guia mostra como criar e configurar uma nova API no Gerenciamento de API.

-   [Como criar APIs][]

## <a name="add-operations"> </a>Como adicionar operações a uma API

Antes que uma API no Gerenciamento de API possa ser usada, as operações devem ser adicionadas. Este guia mostra como adicionar e configurar tipos diferentes de operações para uma API em Gerenciamento de API.

-   [Como adicionar operações a uma API][]

Uma API e suas operações também podem ser importadas em etapas de um, nos formatos WADL ou Swagger.

-	[Como importar a definição de uma API com operações][]

## <a name="add-product"> </a>Como criar e publicar um produto

No Gerenciamento de API, um produto contém uma ou mais APIs, bem como uma quota de uso e os termos de uso. Uma vez publicado o produto, os desenvolvedores podem assinar o produto e começar a usar as APIs do produto. Estes tópicos fornecem um guia para criar um produto, adicionar uma API e publicá-la para os desenvolvedores.

-   [Como adicionar e publicar um produto][]
-	[Como criar e definir configurações avançadas de produto][]

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[View subscribers to a product]: #view-subscribers
[Next steps]: #next-steps

[api-management-]: ./media/

[Como criar APIs]: api-management-howto-create-apis.md
[Como adicionar operações a uma API]: api-management-howto-add-operations.md
[Como adicionar e publicar um produto]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Como importar a definição de uma API com operações]: api-management-howto-import-api.md
[Como criar e definir configurações avançadas de produto]: api-management-howto-product-with-rules.md

<!---HONumber=AcomDC_0525_2016-->