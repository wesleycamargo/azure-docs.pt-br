<properties pageTitle="Get started with Azure API Management" metaKeywords="" description="Learn how to create APIs, operations, and get started with API Management." metaCanonical="" services="" documentationCenter="API Management" title="Get started with Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

# Introdução ao Gerenciamento de API do Azure

Este guia mostra como iniciar rapidamente a usar o Gerenciamento de API e como fazer sua primeira chamada à API.

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

Cada instância de serviço do Gerenciamento de API vem pré-configurada com uma amostra de API de ECO na qual você pode invocar qualquer verbo HTTP e o valor retornado será igual aos cabeçalhos e ao corpo enviado. Este tutorial usa o serviço Web de back-end para a API de ECO, para criar uma nova API no Gerenciamento de API chamada **Meu Serviço de Eco**.

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

> A API eco função Web não usa autenticação, mas para obter mais informações sobre a configuração de autenticação, consulte [Definir Configurações de API][Definir Configurações de API].

## <a name="add-operation"> </a>Adicionar uma operação

Clique em **Operações** para exibir o painel de operações da API. As definições de operações são usadas para validar solicitações recebidas e para gerar documentação automaticamente. Como ainda não adicionamos uma operação, não existe nenhuma exibida.

![Operações][Operações]

Clique em **adicionar operação** para adicionar uma nova operação. A janela **Nova operação** será exibida e a guia **Assinatura** será selecionada por padrão.

![Assinatura de operação][Assinatura de operação]

Neste exemplo, especificaremos uma operação GET no serviço de eco. Insira os valores a seguir nos campos da guia **Assinatura**.

-   Digite **GET** na caixa de texto **Verbo HTTP**. À medida que começar a digitar, poderá selecionar **GET** na lista exibida de verbos HTTP.
-   Digite **/resource** na caixa de texto **Modelo de URL**.
-   Digite **Recurso GET** na caixa de texto de nome de **Exibição**.
-   Digite **Uma demonstração de chamada GET em um recurso função Web. Ela será gerenciada por um back-end de "eco" que retorna uma resposta igual para a solicitação (os cabeçalhos e o corpo fornecidos são retornados à medida que são recebidos)** na caixa de texto **Descrição**. Essa descrição é usada para gerar documentação para esta operação quando os desenvolvedores usarem essa API.

Clique em **Parâmetros** para configurar os parâmetros da cadeia de consulta para essa operação. Para adicionar um parâmetro de consulta, clique em **Adicionar parâmetro de consulta** e especifique os valores a seguir.

-   Digite **param1** na caixa de texto **Nome**.
-   Digite **Um parâmetro função Web é necessário.** na caixa de texto **Descrição**.
-   Clique no campo **Digitar** e selecione a **cadeia de caracteres** na lista. Os tipos com suporte são **cadeia de caracteres**, **número**, **booliano** e **data/hora**.
-   Clique no campo **Valores**, digite **exemplo** na caixa de texto e clique no sinal de mais para adicionar o texto de valor padrão para o parâmetro. Após adicionar o texto padrão, clique em qualquer lugar fora do campo **Valores** para fechar a janela Adicionar valor.
-   Marque a caixa de seleção **Obrigatório**.

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

![Adicionar API][3]

Marque a caixa de **Minha API de ECO** e clique em **Salvar**.

![API adicionada][API adicionada]

Agora que **Minha API de Eco** está associada a um produto, os desenvolvedores podem assiná-lo e começar a usar a API.

> Esta etapa do tutorial usou o produto de **Início** fornecido pré-configurado e pronto para uso. Para obter um guia passo a passo sobre como criar e publicar um novo produto, consulte [Como criar e publicar um produto][Como criar e publicar um produto].

O usuário Administrador assina automaticamente todos os produtos e pode acessar as APIs a que eles fornecem acesso. Portanto, não é necessário assinar manualmente o produto recém-criado para poder fazer uma chamada.

## <a name="call-operation"> </a>Chamar uma operação no Portal do Desenvolvedor

As operações podem ser chamadas diretamente do Portal do Desenvolvedor, que fornece uma forma conveniente de exibir e testar as operações de uma API. Nesta etapa do tutorial você chamará o método GET adicionado à **Minha API de ECO**. Clique em **Portal do Desenvolvedor** no menu da parte superior direita do Portal de Gerenciamento.

![Portal do desenvolvedor][Portal do desenvolvedor]

Clique em **APIs** no menu superior e depois clique em **Minha API de ECO** para ver as operações disponíveis.

![Portal do desenvolvedor][4]

Observe que a descrição e os parâmetros que foram adicionados quando você criou a operação foram exibidos, fornecendo documentação para os desenvolvedores que usarão essa operação.

Clique em **Recurso GET** e depois clique em **Abrir Console**.

![Console de operação][Console de operação]

Insira alguns valores para os parâmetros e especifique sua chave de desenvolvedor e depois clique em **HTTP Get**.

![HTTP Get][HTTP Get]

Após invocar uma operação, o portal do desenvolvedor exibe a **URL solicitada** do serviço back-end, o **Status de resposta**, os **Cabeçalhos de resposta** e qualquer **Conteúdo de resposta**.

![Resposta][Resposta]

## <a name="next-steps"> </a>Próximas etapas

-   Configurar políticas
-   Personalizar o portal do desenvolvedor
-   Rastrear chamadas usando o inspetor de API

  [Avaliação gratuita do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/
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
  [Produtos]: ./media/api-management-get-started/api-management-list-products.png
  [Adicionar API]: ./media/api-management-get-started/api-management-add-api-to-product.png
  [3]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
  [API adicionada]: ./media/api-management-get-started/api-management-api-added-to-product.png
  [Como criar e publicar um produto]: ../api-management-howto-add-products
  [Portal do desenvolvedor]: ./media/api-management-get-started/api-management-developer-portal-menu.png
  [4]: ./media/api-management-get-started/api-management-developer-portal-myecho-api.png
  [Console de operação]: ./media/api-management-get-started/api-management-developer-portal-myecho-api-console.png
  [HTTP Get]: ./media/api-management-get-started/api-management-invoke-get.png
  [Resposta]: ./media/api-management-get-started/api-management-invoke-get-response.png
