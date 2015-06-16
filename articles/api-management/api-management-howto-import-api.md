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
	ms.date="11/18/2014" 
	ms.author="sdanie"/>

# Como importar a definição de uma API com operações no Gerenciamento da API do Azure

No Gerenciamento da API (em modo de visualização), novas APIs podem ser criadas e as operações podem ser adicionadas manualmente, ou a API pode ser importada em conjunto com as operações em uma etapa.

As APIs e suas operações podem ser importadas usando os formatos a seguir.

-   WADL
-   Swagger

Este guia mostra como criar uma nova API e importar suas operações em uma etapa.

> Para obter mais informações sobre como criar uma API e adicionar operações manualmente, consulte [Como criar APIs][Como criar APIs] e [Como adicionar operações a uma API][Como adicionar operações a uma API].

## Neste tópico

-   [Importar uma API][Importar uma API]
-   [Exportar uma API][Exportar uma API]
-   [Próximas etapas][Próximas etapas]

## <a name="import-api"> </a>Importar uma API

Para criar e configurar APIs, clique em **Console de Gerenciamento** no Portal do Azure para acessar o serviço de Gerenciamento de API. Isso levará você ao portal administrativo do Gerenciamento de API.

> Se você ainda não criou uma instância de serviço de Gerenciamento de API, consulte [Criar uma instância de serviço de Gerenciamento de API][Criar uma instância de serviço de Gerenciamento de API] no tutorial [Introdução ao Gerenciamento de API do Azure][Introdução ao Gerenciamento de API do Azure].

![Console de gerenciamento][Console de gerenciamento]

Clique em **APIs** no menu **Gerenciamento de API** à esquerda e depois clique em **Importar API**.

![Importar API][Importar API]

A janela **Importar API** possui três guias que correspondem às três maneiras de fornecer a especificação da API.

-   **A partir da área de transferência** permite que você cole a especificação da API na caixa de texto designada.
-   **A partir do arquivo** permite que você navegue até e selecione o arquivo que contém a especificação da API.
-   **A partir da URL** permite que você forneça a URL até a especificação para a API.

![Importar formato de API][Importar formato de API]

Após fornecer a especificação da API, use os botões de opção à direita para indicar o formato da especificação. Os formatos a seguir são suportados.

-   WADL
-   Swagger

Em seguida, insira um **sufixo de URL de API Web**. Ele está anexado à URL base do serviço de gerenciamento de API. A URL base é comum para todas as APIs hospedadas em cada instância de um serviço de Gerenciamento da API. O Gerenciamento da API diferencia as APIs pelo sufixo e, portanto, o sufixo deve ser único para cada API em uma instância de serviço de gerenciamento de API específica.

Após inserir todos os valores, clique em **Salvar** para criar a API e as operações associadas.

## <a name="export-api"> </a> Exportar uma API

Além de importar novas APIs, você pode exportar as definições de suas APIs a partir do console de gerenciamento. Para fazer isso, clique em **Exportar API** na **guia de Resumo** de sua **API**.

![Exportar API][Exportar API]

As APIs podem ser exportadas usando WADL ou Swagger. Selecione o formato desejado, clique em **Salvar** e escolha o local onde deseja salvar o arquivo.

![Exportar formato de API][Exportar formato de API]

## <a name="next-steps"> </a>Próximas etapas

Após criar a API e importar as operações, você pode revisar e definir quaisquer configurações adicionais, adicionar a API a um produto e publicá-la para que fique disponível para desenvolvedores. Para obter mais informações, consulte os guias a seguir.

-   [Como definir configurações de API][Como definir configurações de API]
-   [Como criar e publicar um produto][Como criar e publicar um produto]

  [Como criar APIs]: ../api-management-howto-create-apis
  [Como adicionar operações a uma API]: ../api-management-howto-add-operations
  [Importar uma API]: #import-api
  [Exportar uma API]: #export-api
  [Próximas etapas]: #next-steps
  [Criar uma instância de serviço de Gerenciamento de API]: ../api-management-get-started/#create-service-instance
  [Introdução ao Gerenciamento de API do Azure]: ../api-management-get-started
  [Console de gerenciamento]: ./media/api-management-howto-import-api/api-management-management-console.png
  [Importar API]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
  [Importar formato de API]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
  [Exportar API]: ./media/api-management-howto-import-api/api-management-export-api.png
  [Exportar formato de API]: ./media/api-management-howto-import-api/api-management-export-api-format.png
  [Como definir configurações de API]: ../api-management-howto-create-apis/#configure-api-settings
  [Como criar e publicar um produto]: ../api-management-howto-add-products

<!--HONumber=46--> 
 