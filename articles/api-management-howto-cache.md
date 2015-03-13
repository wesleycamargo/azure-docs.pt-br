<properties 
	pageTitle="Como armazenar em cache os resultados de operações no Gerenciamento de API do Azure" 
	description="Saiba como aprimorar a latência, carregar o consumo de largura de banda e o serviço Web para chamadas de serviço de Gerenciamento de API." 
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

# Como armazenar em cache os resultados de operações no Gerenciamento de API do Azure

É possível configurar as operações do Gerenciamento de API (visualização) para cache das respostas. O cache das respostas pode reduzir significativamente a latência da API, o consumo da largura de banda e a carga de serviço Web para dados que não são alterados com frequência.

Neste tutorial, você revisará as configurações e políticas de cache para um dos exemplos de operação de API de ECO e chamará a operação no portal do desenvolvedor para ver o armazenamento em cache em funcionamento.

## Neste tópico

-   [Configurar uma operação para cache][Configurar uma operação para cache]
-   [Revisar as políticas de cache][Revisar as políticas de cache]
-   [Chamar uma operação e testar o cache][Chamar uma operação e testar o cache]
-   [Próximas etapas][Próximas etapas]

## <a name="configure-caching"> </a>Configurar uma operação para cache

Nesta etapa, você revisará as configurações de cache da operação **Recurso GET (em cache)** do exemplo de API de ECO.

> Cada instância de serviço de Gerenciamento de API vem pré-configurada com uma API de ECO que pode ser usada para experimentar e aprender sobre o Gerenciamento de API. Para obter mais informações, consulte [Introdução ao Gerenciamento de API do Azure][Introdução ao Gerenciamento de API do Azure].

Para começar, clique em **Console de gerenciamento** no Portal do Azure para acessar o serviço de Gerenciamento de API. Isso levará você ao portal administrativo do Gerenciamento de API.

![Console de Gerenciamento de API][Console de Gerenciamento de API]

> Se você ainda não criou uma instância de serviço de Gerenciamento de API, consulte [Criar uma instância de serviço de Gerenciamento de API][Criar uma instância de serviço de Gerenciamento de API] no tutorial [Introdução ao Gerenciamento de API do Azure][Introdução ao Gerenciamento de API do Azure].

Clique em **APIs** no menu **Gerenciamento de API** à esquerda e depois clique em **API de ECO**.

![API de ECO][API de ECO]

Selecione a guia **Operações** e clique na operação **Recurso GET (em cache)**, na lista **Operações**.

![Operações de API de ECO][Operações de API de ECO]

Selecione a guia **Cache** para ver as configurações de armazenamento em cache para esta operação.

![Guia Cache][Guia Cache]

Para habilitar o cache de uma operação, marque a caixa de seleção **Habilitar**. Neste exemplo, o cache está habilitado.

A resposta de cada operação tem uma chave baseada nos valores dos campos **Variar de acordo com os parâmetros da cadeia de consulta** e **Variar de acordo com cabeçalhos**. Se quiser armazenar em cache várias respostas com base em cabeçalhos ou parâmetros de cadeias de consulta, você pode configurá-las nesses dois campos.

**Duração** especifica o intervalo de vencimento das respostas armazenadas em cache. Neste exemplo, o intervalo é de **3.600** segundos, que equivale a uma hora.

Utilizando a configuração de cache neste exemplo, a primeira solicitação para a operação **Recurso GET (em cache)** retornará uma resposta do serviço de back-end. Esta resposta será armazenada em cache, com uma chave de acordo com os parâmetros de cadeia de consulta e cabeçalhos especificados. Chamadas subsequentes para a operação, com parâmetros correspondentes, retornarão respostas em cache até que o intervalo de duração de cache expire.

## <a name="caching-policies"> </a>Revisar as políticas de cache

Quando as configurações de cache são definidas para uma operação na guia **Cache**, políticas de cache são adicionadas à operação. Essas políticas podem ser vistas e editadas no editor de políticas.

Clique em **Políticas** no menu **Gerenciamento de API** à esquerda e selecione **API de ECO / Recurso GET (em cache)** no menu suspenso **Operação**.

![Operação de escopo da política][Operação de escopo da política]

Exibe as políticas para esta operação no editor de políticas.

![Editor de políticas de Gerenciamento de API][Editor de políticas de Gerenciamento de API]

A definição de política para esta operação inclui as políticas que definem a configuração de cache, que foram revisadas usando a guia **Cache** na etapa anterior.

    <policies>
        <inbound>
            <base />
            <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
                <vary-by-header>Accept</vary-by-header>
                <vary-by-header>Accept-Charset</vary-by-header>
            </cache-lookup>
            <rewrite-uri template="/resource" />
        </inbound>
        <outbound>
            <base />
            <cache-store caching-mode="cache-on" duration="3600" />
        </outbound>
    </policies>

> Alterações feitas nas políticas de cache no editor de políticas serão refletidas na guia **Cache** de uma operação e vice-versa.

## <a name="test-operation"> </a>Chamar uma operação e testar o cache

Para ver o cache em funcionamento, podemos chamar a operação por meio do portal do desenvolvedor. Clique em **Portal do desenvolvedor** no menu no canto superior direito.

![Portal do desenvolvedor][Portal do desenvolvedor]

Clique em **APIs** no menu superior e selecione **API de Eco**.

![API de ECO][1]

> Se você tem apenas uma API configurada ou visível na conta, clicar em APIs levará você diretamente às operações dessa API.

Selecione a operação **Recurso GET (em cache)** e clique em **Abrir console**.

![Abrir console][Abrir console]

O console permite que você invoque operações diretamente por meio do portal do desenvolvedor.

![Console][Console]

Mantenha os valores padrão para **param1** e **param2**.

Selecione a chave desejada no menu suspenso **subscription-key**. Se a sua conta tiver somente uma assinatura, ela já estará selecionada.

Insira **sampleheader:value1** na caixa de texto **Cabeçalhos de solicitação**.

Clique em **HTTP Get** e anote os cabeçalhos de resposta.

Insira **sampleheader:value2** na caixa de texto **Cabeçalhos de solicitação** e clique em **HTTP Get**.

Observe que o valor de **sampleheader** ainda será **value1** na resposta. Teste alguns valores diferentes e observe que a resposta armazenada em cache da primeira chamada será retornada.

Insira **25** no campo **param2** e clique em **HTTP Get**.

Observe que agora o valor de **sampleheader** na resposta será **value2**. Como os resultados da operação têm uma chave de acordo com a cadeia de consulta, a resposta em cache anterior não foi retornada.

## <a name="next-steps"> </a>Próximas etapas

-   Verifique os outros tópicos no tutorial [Introdução à configuração avançada de API][Introdução à configuração avançada de API].
-   Para obter mais informações sobre as políticas de cache, consulte [Políticas de cache][Políticas de cache] na [Referência de política do Gerenciamento de API][Referência de política do Gerenciamento de API].

  [Configurar uma operação para cache]: #configure-caching
  [Revisar as políticas de cache]: #caching-policies
  [Chamar uma operação e testar o cache]: #test-operation
  [Próximas etapas]: #next-steps
  [Introdução ao Gerenciamento de API do Azure]: ../api-management-get-started
  [Console de Gerenciamento de API]: ./media/api-management-howto-cache/api-management-management-console.png
  [Criar uma instância de serviço de Gerenciamento de API]: ../api-management-get-started/#create-service-instance
  [API de ECO]: ./media/api-management-howto-cache/api-management-echo-api.png
  [Operações de API de ECO]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
  [Guia Cache]: ./media/api-management-howto-cache/api-management-caching-tab.png
  [Operação de escopo da política]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
  [Editor de políticas de Gerenciamento de API]: ./media/api-management-howto-cache/api-management-policy-editor.png
  [Portal do desenvolvedor]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
  [1]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
  [Abrir console]: ./media/api-management-howto-cache/api-management-open-console.png
  [Console]: ./media/api-management-howto-cache/api-management-console.png
  [Introdução à configuração avançada de API]: ../api-management-get-started-advanced
  [Políticas de cache]: ../api-management-policy-reference/#caching-policies
  [Referência de política do Gerenciamento de API]: ../api-management-policy-reference

<!--HONumber=46--> 
