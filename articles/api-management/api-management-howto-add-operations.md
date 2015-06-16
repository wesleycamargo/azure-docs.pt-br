<properties 
	pageTitle="Como adicionar operações a uma API no Gerenciamento de API do Azure" 
	description="Saiba como adicionar operações a uma API no Gerenciamento de API do Azure." 
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

# Como adicionar operações a uma API no Gerenciamento de API do Azure

Antes que uma API no Gerenciamento de API (visualização) possa ser usada, as operações devem ser adicionadas. Este guia mostra como adicionar e configurar tipos diferentes de operações para uma API em Gerenciamento de API.

## Neste tópico

-   [Adicionar uma operação][Adicionar uma operação]
-   [Cache da operação][Cache da operação]
-   [Parâmetros da solicitação][Parâmetros da solicitação]
-   [Corpo da solicitação][Corpo da solicitação]
-   [Respostas][Respostas]
-   [Próximas etapas][Próximas etapas]

## <a name="add-operation"> </a>Adicionar uma operação

Operações são adicionadas e configuradas em uma API no console de gerenciamento. Para acessar o console de gerenciamento, clique em **Console de Gerenciamento** no Portal do Azure para acessar o serviço de Gerenciamento de API.

> Se você ainda não criou uma instância de serviço de Gerenciamento de API, consulte [Criar uma instância de serviço de Gerenciamento de API][Criar uma instância de serviço de Gerenciamento de API] no tutorial [Introdução ao Gerenciamento de API do Azure][Introdução ao Gerenciamento de API do Azure].

![Console de Gerenciamento de API][Console de Gerenciamento de API]

Selecione a API desejada no Portal de gerenciamento de API e selecione a guia **Operações**.

![Operações][Operações]

Clique em **Adicionar operação** para adicionar uma nova operação. A janela **Nova operação** será exibida e a guia **Assinatura** será selecionada por padrão.

![Adicionar operação][Adicionar operação]

Especifique o **verbo HTTP** escolhendo na lista suspensa.

![Método HTTP][Método HTTP]

Defina o modelo do URL digitando um fragmento de URL consistindo de um ou mais segmentos de caminho URL e nenhum ou mais parâmetros de cadeia de consulta. O modelo do URL, anexado ao URL base da API, identifica uma única operação HTTP. Ele pode conter uma ou mais partes variáveis nomeadas que são identificadas por chaves. Essas partes variáveis se chamam parâmetros de modelo, que são valores atribuídos dinamicamente e extraídos do URL da solicitação quando a solicitação está sendo processada pela plataforma de Gerenciamento de API.

![Modelo do URL][Modelo do URL]

Se desejar, especifique **Reescrever modelo de URL**. Isso permite que você use o modelo de URL padrão para processar solicitações de entrada no front-end, enquanto chama o back-end por meio de um URL convertido de acordo com o modelo reescrito. Os parâmetros de modelo do modelo de URL devem ser usados no modelo reescrito. O exemplo a seguir mostra como o tipo de conteúdo codificado como um segmento de caminho no serviço Web do exemplo anterior pode ser fornecido como parâmetro de consulta na API publicada por meio da plataforma de Gerenciamento de API usando os modelos de URL.

![Modelo de URL reescrito][Modelo de URL reescrito]

Quem chamar a operação utilizará o formato `/customers?customerid=ALFKI` e ele será mapeado no `/Customers('ALFKI')` quando o serviço de back-end for invocado.

O nome de **exibição** e a **Descrição** oferecem uma descrição da operação e são usados para fornecer documentação aos desenvolvedores que utilizam essa API no portal do desenvolvedor.

![Descrição][Descrição]

A descrição da operação pode ser especificada como texto sem formatação ou HTML na caixa de texto **Descrição**.

## <a name="operation-caching"> </a>Cache da operação

O cache de respostas reduz a latência percebida pelos consumidores da API, reduz a largura de banda e diminui a carga no serviço Web HTTP que está implementando a API.

Para habilitar o cache para a operação de modo fácil e rápido, selecione a guia **Cache** e marque a caixa de seleção **Habilitar**.

![Caching][Caching]

**Duração** especifica o período de tempo durante o qual a resposta da operação permanece armazenada em cache. O valor padrão é 3.600 segundos, ou uma hora.

Chaves de cache são usadas para diferenciar as respostas, de modo que a resposta correspondente a cada chave de cache diferente tenha seu próprio valor em cache separado. Outra opção é inserir parâmetros de cadeia de consulta específicos e/ou cabeçalhos HTTP a serem utilizados para computar valores de chave de cache nas caixas de texto **Variar de acordo com os parâmetros da cadeia de consulta** e **Variar de acordo com cabeçalhos**. Quando nenhum é especificado, o URL de solicitação completo e os seguintes valores de cabeçalho HTTP são usados para gerar a chave de cache: **Accept** e **Accept-Charset**.

> Para obter mais informações sobre cache e políticas de cache, consulte [Como armazenar em cache os resultados de operações no Gerenciamento de API do Azure][Como armazenar em cache os resultados de operações no Gerenciamento de API do Azure].

## <a name="request-parameters"> </a>Parâmetros da solicitação

Os parâmetros da operação são gerenciados na guia Parâmetros. Parâmetros especificados no **Modelo do URL**, na guia **Assinatura**, são adicionados automaticamente e somente podem ser alterados editando o modelo do URL. Parâmetros adicionais podem ser inseridos manualmente.

Para adicionar um novo parâmetro de consulta, clique em **Adicionar parâmetro de consulta** e insira as informações a seguir:

-   **Nome** - nome do parâmetro.
-   **Descrição** - uma breve descrição do parâmetro (opcional).
-   **Tipo** - tipo do parâmetro, selecionado no menu suspenso.
-   **Valores** - valores que podem ser atribuídos a esse parâmetro. Um dos valores pode ser marcado como padrão (opcional).
-   **Obrigatório** - faça com que o parâmetro seja obrigatório marcando a caixa de seleção.

![Parâmetros da solicitação][1]

## <a name="request-body"> </a>Corpo da solicitação

Se a operação permitir (por exemplo, PUT, POST) e precisar de um corpo, você poderá fornecer um exemplo dele em todos os formatos de representação com suporte (por exemplo, json, XML).

> O corpo da solicitação é usado somente para fins de documentação e não é validado.

Para inserir um corpo de solicitação, vá até a guia **Corpo**.

Clique em **Adicionar representação**, comece a digitar o nome do tipo de conteúdo desejado (por exemplo, aplicativo/json), selecione-o no menu suspenso e cole o exemplo de corpo de solicitação desejado, no formato selecionado, na caixa de texto.

![Corpo da solicitação][2]

Além de representações, você também pode especificar um texto de descrição opcional na caixa de texto **Descrição**.

## <a name="responses"> </a>Respostas

É uma prática recomendável fornecer exemplos de respostas para todos os códigos de status que a operação possa produzir. Cada código de status pode ter mais de um exemplo de corpo de resposta, um para cada um dos tipos de conteúdos com suporte.

Para adicionar uma resposta, clique em **Adicionar** e comece a digitar o código de status desejado. Neste exemplo, o código de status é **200 OK**. Quando o código for exibido no menu suspenso, selecione-o e o código de resposta será criado e adicionado à sua operação.

![Código de resposta][Código de resposta]

Clique em **Adicionar representação**, comece a digitar o nome do tipo de conteúdo desejado (por exemplo, aplicativo/json) e selecione-o no menu suspenso.

![Tipo de conteúdo do corpo][Tipo de conteúdo do corpo]

Copie o exemplo de corpo de resposta no formato selecionado na caixa de texto.

![Corpo da resposta][Corpo da resposta]

Se desejar, adicione uma descrição opcional na caixa de texto **Descrição**.

Após configurar a operação, clique em **Salvar**.

## <a name="next-steps"> </a>Próximas etapas

Após as operações serem adicionadas a uma API, a próxima etapa é associar a API a um produto e publicá-la para que os desenvolvedores possam chamar suas operações.

-   [Como criar e publicar um produto][Como criar e publicar um produto]

  [Adicionar uma operação]: #add-operation
  [Cache da operação]: #operation-caching
  [Parâmetros da solicitação]: #request-parameters
  [Corpo da solicitação]: #request-body
  [Respostas]: #responses
  [Próximas etapas]: #next-steps
  [Criar uma instância de serviço de Gerenciamento de API]: ../api-management-get-started/#create-service-instance
  [Introdução ao Gerenciamento de API do Azure]: ../api-management-get-started
  [Console de Gerenciamento de API]: ./media/api-management-howto-add-operations/api-management-management-console.png
  [Operações]: ./media/api-management-howto-add-operations/api-management-operations.png
  [Adicionar operação]: ./media/api-management-howto-add-operations/api-management-add-operation.png
  [Método HTTP]: ./media/api-management-howto-add-operations/api-management-http-method.png
  [Modelo do URL]: ./media/api-management-howto-add-operations/api-management-url-template.png
  [Modelo de URL reescrito]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
  [Descrição]: ./media/api-management-howto-add-operations/api-management-description.png
  [Caching]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
  [Como armazenar em cache os resultados de operações no Gerenciamento de API do Azure]: ../api-management-howto-cache
  [1]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
  [2]: ./media/api-management-howto-add-operations/api-management-request-body.png
  [Código de resposta]: ./media/api-management-howto-add-operations/api-management-response-code.png
  [Tipo de conteúdo do corpo]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
  [Corpo da resposta]: ./media/api-management-howto-add-operations/api-management-response-body.png
  [Como criar e publicar um produto]: ../api-management-howto-add-products

<!--HONumber=46--> 
 