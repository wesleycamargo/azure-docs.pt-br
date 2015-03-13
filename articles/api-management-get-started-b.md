<properties 
	pageTitle="Introdução ao Gerenciamento de API do Azure" 
	description="Saiba como criar APIs, operações e obtenha uma introdução ao Gerenciamento de API." 
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
	ms.date="2/11/2015" 
	ms.author="sdanie"/>

# Introdução ao Gerenciamento de API do Azure

Este guia mostra como iniciar rapidamente a usar o Gerenciamento de API e como fazer sua primeira chamada à API.

> Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][].

Esta etapa sobre como trabalhar com o Gerenciamento de API serve para criar uma instância de serviço. Faça logon no [Portal de Gerenciamento][] e clique em **Novo**, **Serviços de Aplicativos**, **Gerenciamento de API**, **Criar**.

![API Management new instance][api-management-create-instance-menu]

Para obter a **URL**, especifique um nome de subdomínio exclusivo para usar na URL do serviço.

Selecione a **Assinatura** e a **Região** de sua instância de serviço. Após fazer suas seleções, clique no botão Avançar.

![New API Management service][api-management-create-instance-step1]

Insira **Contoso Ltd.** como o **Nome da Organização** e insira seu endereço de email no email do administrador.

>Esse endereço de email é usado para notificações do sistema de Gerenciamento de API. Para obter mais informações, consulte [Configurar Notificações][].

![New API Management service][api-management-create-instance-step2]

As instâncias do serviço de gerenciamento de API estão disponíveis em duas camadas: Developer e Standard. Por padrão, as novas instâncias de serviço de gerenciamento de API são criadas usando a camada do desenvolvedor. Para selecionar a camada Standard, marque **Configurações avançadas** caixa de seleção e selecione a camada Standard na próxima tela.

>O Microsoft Azure oferece duas camadas em que você pode executar o serviço de Gerenciamento de API: Developer e Standard. A Camada do Desenvolvedor é para programas pilotos de API, teste e desenvolvimento, onde a alta disponibilidade não é uma preocupação. Na camada Standard, você pode dimensionar sua contagem de unidade reservada para lidar com mais tráfego. A camada Standard fornece um serviço de Gerenciamento de API com mais poder de processamento e desempenho. Este tutorial pode ser concluído com qualquer camada. Para obter mais informações sobre os níveis de gerenciamento de API, consulte [preços de Gerenciamento de API][].

Clique na caixa de seleção para criar sua instância de serviço.

![New API Management service][api-management-instance-created]

Após criar a instância de serviço, a próxima etapa é criar a API.

## <a name="create-api"> </a>Criar uma API

Uma API consiste em um conjunto de operações que podem ser iniciadas a partir de uma aplicativo cliente. Todas as operações de API são colocadas em proxies de serviços Web existentes.

Cada instância de serviço do Gerenciamento de API vem pré-configurada com uma amostra de API de ECO na qual você pode invocar qualquer verbo HTTP e o valor retornado será igual aos cabeçalhos e ao corpo enviado. Este tutorial usa o serviço Web de back-end para a API de ECO, para criar uma nova API no Gerenciamento de API chamada **Meu Serviço de Eco**.

As APIs são criadas e configuradas a partir do console de Gerenciamento de APIs, acessado pelo Portal de Gerenciamento do Azure. Para acessar o console de Gerenciamento de API, clique em **Console de Gerenciamento** no Portal do Azure para acessar o serviço de Gerenciamento de API.

![New API Management console][api-management-management-console]

Para criar **Minha API de Eco**, clique em **APIs** no menu **Gerenciamento de API** à esquerda e depois clique em **Adicionar API**.

![Create API][api-management-create-api]

![Add new API][api-management-add-new-api]

Os três campos a seguir são usados para configurar a nova API.

-	Digite **Minha API de Eco** na caixa de texto **Título da API Web**. **O Título da API Web** fornece um nome descritivo exclusivo para a API. Esse nome será exibido nos portais de gerenciamento e do desenvolvedor.
-	Digite **http://echoapi.cloudapp.net/api** em **URL de Serviço Web**. **O URL de Serviço Web** utiliza como referência o serviço HTTP que está implementando a API. O gerenciamento de API envia as solicitações para esse endereço.
-	Digite **myecho** no **sufixo de URL da API Web**. **O sufixo da URL da API Web** está anexado à URL base do serviço de gerenciamento de API. Suas APIs compartilharão uma URL de base comum e serão distinguidas por um sufixo exclusivo anexado após a base.
-	O **Esquema de URL da API da Web** determina quais protocolos podem ser usados para acessar a API. A HTTPs é especificada por padrão.

Clique em **Salvar** para criar a API. Após criar as novas APIs, a página de resumo das APIs será exibida no Portal de Gerenciamento.

![API summary][api-management-new-api-summary]


>A API eco função Web não usa autenticação, mas para obter mais informações sobre a configuração de autenticação, consulte [Definir Configurações de API][].


## <a name="add-operation"> </a>Adicionar uma operação

Clique em **Operações** para exibir o painel de operações da API. As definições de operações são usadas para validar solicitações recebidas e para gerar documentação automaticamente. Como ainda não adicionamos uma operação, não existe nenhuma exibida.

![Operations][api-management-myecho-operations]

Clique em **adicionar operação** para adicionar uma nova operação. A janela **Nova operação** será exibida e a guia **Assinatura** será selecionada por padrão.

![Operation signature][api-management-operation-signature]

Neste exemplo, especificaremos uma operação GET no serviço de eco. Insira os valores a seguir nos campos da guia **Assinatura**.

-	Digite **GET** na caixa de texto **Verbo HTTP**. À medida que começar a digitar, poderá selecionar **GET** na lista exibida de verbos HTTP.
-	Digite **/resource** na caixa de texto **Modelo de URL**.
-	Digite **Recurso GET** na caixa de texto de nome de **Exibição**.
-	Digite **Uma demonstração de chamada GET em um recurso função Web. Ela será gerenciada por um back-end de "eco" que retorna uma resposta igual para a solicitação (os cabeçalhos e o corpo fornecidos são retornados à medida que são recebidos)** na caixa de texto **Descrição**. Essa descrição é usada para gerar documentação para esta operação quando os desenvolvedores usarem essa API.

Clique em **Parâmetros** para configurar os parâmetros da cadeia de consulta para essa operação. Para adicionar um parâmetro de consulta, clique em **Adicionar parâmetro de consulta** e especifique os valores a seguir.

-	Digite **param1** na caixa de texto **Nome**.
-	Digite **Um parâmetro função Web é necessário.** na caixa de texto **Descrição**.
-	Clique no campo **Digitar** e selecione a **cadeia de caracteres** na lista. Os tipos com suporte são **cadeia de caracteres**, **número**, **booliano** e **data/hora**.
-	Clique no campo **Valores**, digite **exemplo** na caixa de texto e clique no sinal de mais para adicionar o texto de valor padrão para o parâmetro. Após adicionar o texto padrão, clique em qualquer lugar fora do campo **Valores** para fechar a janela Adicionar valor.
-	Marque a caixa de seleção **Obrigatório**.

Clique em **Salvar** para adicionar a operação que acabou de configurar para a API.


## <a name="add-api-to-product"> </a>Adicionar a nova API a um produto

Os desenvolvedores devem primeiro assinar um produto antes de poder fazer chamadas à API. Um produto fornece acesso a uma ou mais APIs e pode conter restrições de acesso como quotas de uso e limites de tarifa. Nesta etapa do tutorial você adicionará Minha API de ECO a uma produto existente.

Clique em **Produtos** no menu **Gerenciamento de API** à esquerda par exibir e configurar os produtos disponíveis nesta instância de API.

![Products][api-management-list-products]

Por padrão, cada instância de Gerenciamento de API é fornecida com dois produtos função Web:

-	**Início**
-	**Ilimitado**

Neste tutorial usaremos o produto de **Início**. Clique em **Início** para exibir as configurações, incluindo as APIs que estão associadas ao produto.

![Add API][api-management-add-api-to-product]

Clique em **Adicionar API ao produto**.

![Add API][api-management-add-myechoapi-to-product]

Marque a caixa de **Minha API de ECO** e clique em **Salvar**.

![API added][api-management-api-added-to-product]

Agora que **Minha API de Eco** está associada a um produto, os desenvolvedores podem assiná-lo e começar a usar a API.

>Esta etapa do tutorial usa o produto de **Início** fornecido pré-configurado e pronto para uso. Para obter um guia passo a passo sobre como criar e publicar um novo produto, consulte [Como criar e publicar um produto][].

O usuário Administrador assina automaticamente todos os produtos e pode acessar as APIs a que eles fornecem acesso. Portanto, não é necessário assinar manualmente o produto recém-criado para poder fazer uma chamada.

## <a name="call-operation"> </a>Chamar uma operação no Portal do Desenvolvedor

As operações podem ser chamadas diretamente do Portal do Desenvolvedor, que fornece uma forma conveniente de exibir e testar as operações de uma API. Nesta etapa do tutorial você chamará o método GET adicionado à **Minha API de ECO**. Clique em **Portal do Desenvolvedor** no menu da parte superior direita do Portal de Gerenciamento.

![Developer portal][api-management-developer-portal-menu]

Clique em **APIs** no menu superior e depois clique em **Minha API de ECO** para ver as operações disponíveis.

![Developer portal][api-management-developer-portal-myecho-api]

Observe que a descrição e os parâmetros que foram adicionados quando você criou a operação foram exibidos, fornecendo documentação para os desenvolvedores que usarão essa operação.

Clique em **Recurso GET** e depois clique em **Abrir Console**. 

![Operation console][api-management-developer-portal-myecho-api-console]

Insira alguns valores para os parâmetros e especifique sua chave de desenvolvedor e depois clique em **HTTP Get**.

€

Após invocar uma operação, o portal do desenvolvedor exibe a **URL solicitada** do serviço back-end, o **Status de resposta**, os **Cabeçalhos de resposta** e qualquer **Conteúdo de resposta**. 

![Response][api-management-invoke-get-response]



## <a name="next-steps"> </a>Próximas etapas

-   Configurar políticas
-   Personalizar o portal do desenvolvedor
-   Rastrear chamadas usando o inspetor de API

[Avaliação gratuita do Azure]: http://azure.microsoft.com/pricing/free-trial/

[Criar uma instância de Gerenciamento de API]: #create-service-instance
[Criar uma API]: #create-api
[Adicionar uma operação]: #add-operation
[Adicionar a nova API a um produto]: #add-api-to-product
[Assinar o produto que contém a API]: #subscribe
[Chamar uma operação no Portal do Desenvolvedor]: #call-operation
[Exibir análise]: #view-analytics
[Próximas etapas]: #next-steps

[Definir configurações de API]: ../api-management-howto-create-apis/#configure-api-settings
[Configurar Notificações]: ../api-management-howto-configure-notifications
[Respostas]: ../api-management-howto-add-operations/#responses
[Como criar e publicar um produto]: ../api-management-howto-add-products
[Introdução à configuração avançada de API]: ../api-management-get-started-advanced
[Preços de gerenciamento da API]: http://azure.microsoft.com/pricing/details/api-management/
[Portal de Gerenciamento]: https://manage.windowsazure.com/

[Configurar Políticas]: ../api-management-howto-policies
[Personalizar o portal do desenvolvedor]: ../api-management-customize-portal
[Rastrear chamadas usando o inspetor de API]: ../api-management-howto-api-inspector

[api-management-management-console]: ./media/api-management-get-started-b/api-management-management-console.png
[api-management-create-instance-menu]: ./media/api-management-get-started-b/api-management-create-instance-menu.png
[api-management-create-instance-step1]: ./media/api-management-get-started-b/api-management-create-instance-step1.png
[api-management-create-instance-step2]: ./media/api-management-get-started-b/api-management-create-instance-step2.png
[api-management-instance-created]: ./media/api-management-get-started-b/api-management-instance-created.png
[api-management-create-api]: ./media/api-management-get-started-b/api-management-create-api.png
[api-management-add-new-api]: ./media/api-management-get-started-b/api-management-add-new-api.png
[api-management-new-api-summary]: ./media/api-management-get-started-b/api-management-new-api-summary.png
[api-management-myecho-operations]: ./media/api-management-get-started-b/api-management-myecho-operations.png
[api-management-operation-signature]: ./media/api-management-get-started-b/api-management-operation-signature.png
[api-management-list-products]: ./media/api-management-get-started-b/api-management-list-products.png
[api-management-add-api-to-product]: ./media/api-management-get-started-b/api-management-add-api-to-product.png
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started-b/api-management-add-myechoapi-to-product.png
[api-management-api-added-to-product]: ./media/api-management-get-started-b/api-management-api-added-to-product.png



[api-management-developer-portal-menu]: ./media/api-management-get-started-b/api-management-developer-portal-menu.png
[api-management-developer-portal-myecho-api]: ./media/api-management-get-started-b/api-management-developer-portal-myecho-api.png
[api-management-developer-portal-myecho-api-console]: ./media/api-management-get-started-b/api-management-developer-portal-myecho-api-console.png
[api-management-invoke-get]: ./media/api-management-get-started-b/api-management-invoke-get.png
[api-management-invoke-get-response]: ./media/api-management-get-started-b/api-management-invoke-get-response.png

<!--HONumber=35.2-->

<!--HONumber=46--> 
