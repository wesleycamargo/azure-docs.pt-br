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

## Neste tópico

-   [Criar uma instância de Gerenciamento de API][Criar uma instância de Gerenciamento de API]
-   [Criar uma API][Criar uma API]
-   [Adicionar uma operação][Adicionar uma operação]
-   [Adicionar a nova API a um produto][Adicionar a nova API a um produto]
-   [Assinar o produto que contém a API][Assinar o produto que contém a API]
-   [Chamar uma operação no Portal do Desenvolvedor][Chamar uma operação no Portal do Desenvolvedor]
-   [Exibir análise][Exibir análise]
-   [Próximas etapas][Próximas etapas]

## <a name="create-service-instance"> </a>Criar uma instância de Gerenciamento de API

> Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][Avaliação gratuita do Azure].

Esta etapa sobre como trabalhar com o Gerenciamento de API serve para criar uma instância de serviço. Faça logon no [Portal de Gerenciamento][Portal de Gerenciamento] e clique em **Novo**, **Serviços de Aplicativos**, **Gerenciamento de API**, **Criar**.

![Nova instância do Gerenciamento de API][Nova instância do Gerenciamento de API]

Para obter a **URL**, especifique um nome de subdomínio exclusivo para usar na URL do serviço.

Selecione a **Camada de Preços**, **Assinatura** e a **Região** de sua instância de serviço. Todas as camadas de preços podem ser usadas neste tutorial. Após fazer suas seleções, clique no botão Avançar.

![Novo serviço de Gerenciamento de API][Novo serviço de Gerenciamento de API]

Insira **Contoso Ltd.** como o **Nome da Organização** e insira seu endereço de email no email do administrador.

> Esse endereço de email é usado para notificações do sistema de Gerenciamento de API. Para obter mais informações, consulte [Configurar Notificações][Configurar Notificações].

Clique na caixa de seleção para criar sua instância de serviço.

![Novo serviço de Gerenciamento de API][1]

![Novo serviço de Gerenciamento de API][2]

Após criar a instância de serviço, a próxima etapa é criar a API.

## <a name="create-api"> </a>Criar uma API

Uma API consiste em um conjunto de operações que podem ser iniciadas a partir de uma aplicativo cliente. Todas as operações de API são colocadas em proxies de serviços Web existentes.

Cada instância de serviço do Gerenciamento de API vem pré-configurada com uma API de eco função Web que retorna a entrada enviada. Para usá-la, você pode invocar qualquer verbo HTTP e o valor retornado será igual aos cabeçalhos e ao corpo enviado.

Este tutorial usa o serviço Web <http://echoapi.cloudapp.net/api> para criar uma nova API no Gerenciamento de API chamada **Meu Serviço de Eco**.

As APIs são criadas e configuradas a partir do console de Gerenciamento de APIs, acessado pelo Portal de Gerenciamento do Azure. Para acessar o console de Gerenciamento de API, clique em **Console de Gerenciamento** no Portal do Azure para acessar o serviço de Gerenciamento de API.

![Novo console de gerenciamento de API][Novo console de gerenciamento de API]

Para criar **Minha API de Eco**, clique em **APIs** no menu **Gerenciamento de API** à esquerda e depois clique em **Adicionar API**.

![Criar API][Criar API]

![Adicionar nova API][Adicionar nova API]

Os três campos a seguir são usados para configurar a nova API.

-   Digite **Minha API de Eco** na caixa de texto **Título da API Web**. O **Título da API Web** fornece um nome descritivo exclusivo para a API. Esse nome será exibido nos portais de gerenciamento e do desenvolvedor.
-   Digite **<http://echoapi.cloudapp.net/api>** em **URL de Serviço Web**. O **URL de Serviço Web** utiliza como referência o serviço HTTP que está implementando a API. O gerenciamento de API envia as solicitações para esse endereço.
-   Digite **myecho** no **sufixo de URL da API Web**. **O sufixo da URL da API Web** está anexado à URL base do serviço de gerenciamento de API. Suas APIs compartilharão uma URL de base comum e serão distinguidas por um sufixo exclusivo anexado após a base.

Clique em **Salvar** para criar a API. Após criar as novas APIs, a página de resumo das APIs será exibida no Portal de Gerenciamento.

![Resumo da API][Resumo da API]

A seção API possui quatro guias. A guia **Resumo** exibe as métricas básicas e as informações sobre a API. A guia **Configurações** é usada para exibir e editar a configuração de uma API, incluindo as credenciais do serviço back-end. A guia **Operações** é usada para gerenciar as operações de API na etapa seguinte do tutorial e a guia **Questões** pode ser usada para exibir os problemas reportados pelos desenvolvedores que usam suas APIs.

> A API eco função Web não usa autenticação, mas para obter mais informações sobre a configuração de autenticação, consulte [Definir Configurações de API][Definir Configurações de API].

Após criar as API is e definir as configurações, a próxima etapa será adicionar operações à API. As definições de operações são usadas para validar solicitações recebidas e para gerar documentação automaticamente.

## <a name="add-operation"> </a>Adicionar uma operação

Clique em **Operações** para exibir o painel de operações da API. Como ainda não adicionamos uma operação, não existe nenhuma exibida.

![Operações][Operações]

Clique em **adicionar operação** para adicionar uma nova operação. A janela **Nova operação** será exibida e a guia **Assinatura** será selecionada por padrão.

![Assinatura de operação][Assinatura de operação]

Neste exemplo, especificaremos uma operação GET no serviço de eco. Insira os valores a seguir nos campos da guia **Assinatura**.

-   Digite **GET** na caixa de texto **Verbo HTTP**. À medida que começar a digitar, poderá selecionar **GET** na lista exibida de verbos HTTP.
-   Digite **/resource** na caixa de texto **Modelo de URL**.
-   Digite **Recurso GET** na caixa de texto de nome de **Exibição**.
-   Digite **Uma demonstração de chamada GET em um recurso função Web. Ela será gerenciada por um back-end de "eco" que retorna uma resposta igual para a solicitação (os cabeçalhos e o corpo fornecidos são retornados à medida que são recebidos)** na caixa de texto **Descrição**. Essa descrição é usada para gerar documentação para esta operação quando os desenvolvedores usarem essa API.

Clique em **Parâmetros** para configurar os parâmetros da cadeia de consulta para essa operação. Neste exemplo existem dois parâmetros da cadeia de consulta. Para adicionar um parâmetro de consulta, clique em **Adicionar parâmetro de consulta** e especifique os valores a seguir.

Para obter o primeiro parâmetro de consulta, configure os valores a seguir.

-   Digite **param1** na caixa de texto **Nome**.
-   Digite **Um parâmetro função Web é necessário.** na caixa de texto **Descrição**.
-   Clique no campo **Digitar** e selecione a **cadeia de caracteres** na lista. Os tipos com suporte são **cadeia de caracteres**, **número**, **booliano** e **data/hora**.
-   Clique no campo **Valores**, digite **exemplo** na caixa de texto e clique no sinal de mais para adicionar o texto de valor padrão para o parâmetro. Após adicionar o texto padrão, clique em qualquer lugar fora do campo **Valores** para fechar a janela Adicionar valor.
-   Marque a caixa de seleção **Obrigatório**.

Para obter o segundo parâmetro de consulta, insira os valores a seguir.

-   **Nome**: **param2**
-   **Descrição**: **Outro parâmetro função Web, defina como não obrigatório.**
-   **Tipo**: **número**

É uma prática recomendável fornecer exemplos de respostas para todos os códigos de status que a operação possa produzir. Cada código de status pode ter mais de um exemplo de corpo de resposta, um para cada um dos tipos de conteúdos com suporte. Neste tutorial estamos adicionando um código de resposta **200 OK**.

Clique em **Adicionar** na seção Respostas, comece a digitar **200** na caixa de texto e depois selecione **200 OK** na lista suspensa.

![Adicionar resposta][Adicionar resposta]

Após selecionar **200 OK**, um novo código de resposta será adicionado à operação e a janela de resposta será exibida. Digite **Retornado em todos os casos.** na caixa de texto **Descrição**.

![Adicionar resposta][3]

> A opção **Adicionar Representação** é usada para configurar respostas em várias representações. Para obter mais informações, consulte [Respostas][Respostas].

Clique em **Salvar** para adicionar a operação que acabou de configurar para a API.

## <a name="add-api-to-product"> </a>Adicionar a nova API a um produto

Os desenvolvedores devem primeiro assinar um produto antes de poder fazer chamadas à API. Um produto fornece acesso a uma ou mais APIs e pode conter restrições de acesso como quotas de uso e limites de tarifa. Nesta etapa do tutorial você adicionará Minha API de ECO a uma produto existente.

Clique em **Produtos** no menu **Gerenciamento de API** à esquerda par exibir e configurar os produtos disponíveis nesta instância de API.

![Produtos][Produtos]

Por padrão, cada instância de Gerenciamento de API é fornecida com dois produtos função Web:

-   **Início**
-   **Ilimitado**

Neste tutorial usaremos o produto de **Início**. Clique em **Início** para exibir as configurações, incluindo as APIs que estão associadas ao produto.

![Adicionar API][Adicionar API]

Clique em **Adicionar API ao produto**.

![Adicionar API][4]

Marque a caixa de **Minha API de ECO** e clique em **Salvar**.

![API adicionada][API adicionada]

Agora que **Minha API de Eco** está associada a um produto, os desenvolvedores podem assiná-lo e começar a usar a API.

> Esta etapa do tutorial usa o produto de Início fornecido pré-configurado e pronto para uso. Para obter um guia passo a passo sobre como criar e publicar um novo produto, consulte [Como criar e publicar um produto][Como criar e publicar um produto].

## <a name="subscribe"> </a>Assinar o produto que contém a API

Para fazer chamadas a uma API, os desenvolvedores precisam primeiro inscrever-se em um produto que fornece acesso a ela. Os desenvolvedores podem assinar os produtos no Portal do Desenvolvedor ou os administradores podem inscrever os desenvolvedores nos produtos no Console de gerenciamento. Por padrão, você é o administrador porque criou a instância de Gerenciamento de API nas etapas anteriores do tutorial, e por isso assinará uma conta no produto de **Início**.

Clique em **Desenvolvedores** no menu **Gerenciamento de API** à esquerda para exibir e configurar os desenvolvedores desta instância de serviço.

![Desenvolvedores][Desenvolvedores]

Clique no nome do desenvolvedor para definir as configurações do usuário, incluindo as assinaturas.

> Neste exemplo, estamos inscrevendo um desenvolvedor chamado Clayton Gragg. Se você não tiver nenhuma conta de desenvolvedor criada, poderá inscrever a conta do administrador. Para obter informações sobre como criar contas de desenvolvedor, consulte [Como gerenciar contas de desenvolvedor no gerenciamento de API do Azure][Como gerenciar contas de desenvolvedor no gerenciamento de API do Azure].

![Adicionar assinatura][Adicionar assinatura]

Clique em **Adicionar assinatura**.

![Adicionar assinatura][5]

Clique na caixa de texto **Início** e clique em **Assinar**.

![Assinatura adicionada][Assinatura adicionada]

Após inscrever a conta do desenvolvedor, você poderá chamar a API do produto.

## <a name="call-operation"> </a>Chamar uma operação no Portal do Desenvolvedor

As operações podem ser chamadas diretamente do Portal do Desenvolvedor, que fornece uma forma conveniente de exibir e testar as operações de uma API. Nesta etapa do tutorial você chamará o método GET adicionado à **Minha API de ECO**. Clique em **Portal do Desenvolvedor** no menu da parte superior direita do Portal de Gerenciamento.

![Portal do desenvolvedor][Portal do desenvolvedor]

Clique em **APIs** no menu superior e depois clique em **Minha API de ECO** para ver as operações disponíveis.

![Portal do desenvolvedor][6]

Observe que a descrição e os parâmetros que foram adicionados quando você criou a operação foram exibidos, fornecendo documentação para os desenvolvedores que usarão essa operação.

Clique em **Recurso GET** e depois clique em **Abrir Console**.

![Console de operação][Console de operação]

Insira alguns valores para os parâmetros e especifique sua chave de desenvolvedor e depois clique em**HTTP Get**.

![HTTP Get][HTTP Get]

Após invocar uma operação, o portal do desenvolvedor exibe a **URL solicitada** do serviço back-end, o **Status de resposta**, os **Cabeçalhos de resposta** e qualquer **Conteúdo de resposta**.

![Resposta][Resposta]

## <a name="view-analytics"> </a>Exibir análise

Para exibir a análise de **Minha API de ECO**, alterne para o Portal Administrativo selecionando **Gerenciar** no menu de usuário na parte direita superior do portal do desenvolvedor.

![Gerenciar][Gerenciar]

A visualização padrão do Portal Administrativo é o Painel, que fornece uma visão geral da instância de Gerenciamento da API.

![Painel][Painel]

Passe o mouse sobre o gráfico de Minha API de ECO para ver as métricas específicas de utilização da API durante um período de tempo específico.

> Se você não vir linhas no gráfico, volte ao Portal do desenvolvedor e faça algumas chamadas à API, aguarde alguns momentos e volte para o Painel.

![Análise][Análise]

Clique em**Exibir detalhes** para exibir a página de resumo da API, incluindo uma versão maior das métricas exibidas.

![Resumo][Resumo]

Para obter métricas e relatórios detalhados, clique em **Análise** no menu **Gerenciamento de API** à esquerda.

![Visão geral][Visão geral]

A seção **Análise** possui as quatro guias a seguir.

-   **Em um relance** fornece a utilização e as métricas de integridade gerais, bem como desenvolvedores, APIs e operações principais.
-   **Uso** fornece uma visão mais detalhada das chamadas à API e da largura de banda, incluindo uma representação geográfica.
-   **Integridade** abrange os códigos de status, taxas de êxito do cache, tempos de resposta e tempos de resposta de serviço e da API.
-   **Atividade** fornece os relatórios que fazer o detalhamento até a atividade específica por desenvolvedor, produto, API e operação.

## <a name="next-steps"> </a>Próximas etapas

-   Verifique os outros tópicos no tutorial [Introdução à configuração avançada de API][Introdução à configuração avançada de API].

  [Criar uma instância de Gerenciamento de API]: #create-service-instance
  [Criar uma API]: #create-api
  [Adicionar uma operação]: #add-operation
  [Adicionar a nova API a um produto]: #add-api-to-product
  [Assinar o produto que contém a API]: #subscribe
  [Chamar uma operação no Portal do Desenvolvedor]: #call-operation
  [Exibir análise]: #view-analytics
  [Próximas etapas]: #next-steps
  [Avaliação gratuita do Azure]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=api_management_hero_a
  [Portal de Gerenciamento]: https://manage.windowsazure.com/
  [Nova instância do Gerenciamento de API]: ./media/api-management-get-started/api-management-create-instance-menu.png
  [Novo serviço de Gerenciamento de API]: ./media/api-management-get-started/api-management-create-instance-step1.png
  [Configurar Notificações]: ../api-management-howto-configure-notifications
  [1]: ./media/api-management-get-started/api-management-create-instance-step2.png
  [2]: ./media/api-management-get-started/api-management-instance-created.png
  [Novo console de gerenciamento de API]: ./media/api-management-get-started/api-management-management-console.png
  [Criar API]: ./media/api-management-get-started/api-management-create-api.png
  [Adicionar nova API]: ./media/api-management-get-started/api-management-add-new-api.png
  [Resumo da API]: ./media/api-management-get-started/api-management-new-api-summary.png
  [Definir Configurações de API]: ../api-management-howto-create-apis/#configure-api-settings
  [Operações]: ./media/api-management-get-started/api-management-myecho-operations.png
  [Assinatura de operação]: ./media/api-management-get-started/api-management-operation-signature.png
  [Adicionar resposta]: ./media/api-management-get-started/api-management-add-response.png
  [3]: ./media/api-management-get-started/api-management-add-response-window.png
  [Respostas]: ../api-management-howto-add-operations/#responses
  [Produtos]: ./media/api-management-get-started/api-management-list-products.png
  [Adicionar API]: ./media/api-management-get-started/api-management-add-api-to-product.png
  [4]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
  [API adicionada]: ./media/api-management-get-started/api-management-api-added-to-product.png
  [Como criar e publicar um produto]: ../api-management-howto-add-products
  [Desenvolvedores]: ./media/api-management-get-started/api-management-developers.png
  [Como gerenciar contas de desenvolvedor no gerenciamento de API do Azure]: ../api-management-howto-create-or-invite-developers/
  [Adicionar assinatura]: ./media/api-management-get-started/api-management-add-subscription.png
  [5]: ./media/api-management-get-started/api-management-add-subscription-window.png
  [Assinatura adicionada]: ./media/api-management-get-started/api-management-subscription-added.png
  [Portal do desenvolvedor]: ./media/api-management-get-started/api-management-developer-portal-menu.png
  [6]: ./media/api-management-get-started/api-management-developer-portal-myecho-api.png
  [Console de operação]: ./media/api-management-get-started/api-management-developer-portal-myecho-api-console.png
  [HTTP Get]: ./media/api-management-get-started/api-management-invoke-get.png
  [Resposta]: ./media/api-management-get-started/api-management-invoke-get-response.png
  [Gerenciar]: ./media/api-management-get-started/api-management-manage-menu.png
  [Painel]: ./media/api-management-get-started/api-management-dashboard.png
  [Análise]: ./media/api-management-get-started/api-management-mouse-over.png
  [Resumo]: ./media/api-management-get-started/api-management-api-summary-metrics.png
  [Visão geral]: ./media/api-management-get-started/api-management-analytics-overview.png
  [Introdução à configuração avançada de API]: ../api-management-get-started-advanced

<!--HONumber=46--> 
 