<properties pageTitle="How to create APIs in Azure API Management" metaKeywords="" description="Learn how to create and configure APIs in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How to create APIs in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Como criar APIs no Gerenciamento de API do Azure

Uma API no Gerenciamento de API (visualização) representa um conjunto de operações que pode ser invocado pelas aplicações clientes. Novas APIs são criadas no console de gerenciamento e, depois, as operações desejadas são adicionadas. Após as operações serem adicionadas, a API é adicionada a um produto e pode ser publicada. Após uma API ser publicada, ela pode ser usada e assinada por desenvolvedores.

Este guia mostra a primeira etapa do processo: como criar e configurar uma nova API no Gerenciamento de API. Para obter mais informações sobre como adicionar operações e publicar um produto, consulte [Como adicionar operações a uma API][Como adicionar operações a uma API] e [Como criar e publicar um produto][Como criar e publicar um produto].

## Neste tópico

-   [Criar uma nova API][Criar uma nova API]
-   [Definir configurações de API][Definir configurações de API]
-   [Próximas etapas][Próximas etapas]

## <a name="create-new-api"> </a>Criar uma nova API

Para criar e configurar APIs, clique em **Console de Gerenciamento** no Portal do Azure para acessar sua instância do serviço de Gerenciamento de API. Isso levará você ao portal administrativo do Gerenciamento de API.

> Se você ainda não criou uma instância de serviço de Gerenciamento de API, consulte [Criar uma instância de serviço de Gerenciamento de API][Criar uma instância de serviço de Gerenciamento de API] no tutorial [Introdução ao Gerenciamento de API do Azure][Introdução ao Gerenciamento de API do Azure].

![Console de gerenciamento][Console de gerenciamento]

Clique em **APIs** no menu **Gerenciamento de API** à esquerda e depois clique em **Adicionar API**.

![Criar API][Criar API]

Use a janela **Adicionar nova API** para configurar a nova API.

![Adicionar nova API][Adicionar nova API]

Os três campos a seguir são usados para configurar a nova API.

-   O **Título da API Web** fornece um nome descritivo exclusivo para a API. Esse nome será exibido nos portais de gerenciamento e do desenvolvedor.
-   O **URL de Serviço Web** utiliza como referência o serviço HTTP que está implementando a API. O gerenciamento de API envia as solicitações para esse endereço.
-   **O sufixo da URL da API Web** está anexado à URL base do serviço de gerenciamento de API. A URL base é comum para todas as APIs hospedadas por uma instância de um serviço de Gerenciamento de API. O Gerenciamento de API diferencia as APIs pelo sufixo e, portanto, o sufixo deve ser único para cada API para um editor específico.

Após configurar os três valores, clique em **Salvar**. Após criar as novas APIs, a página de resumo das APIs será exibida no Portal de Gerenciamento.

![Resumo da API][Resumo da API]

## <a name="configure-api-settings"> </a>Definir configurações de API

Você pode usar a guia **Configurações** para verificar e editar a configuração de uma API. O **Título da API Web**, o **URL do Serviço Web** e o **sufixo do URL da API Web** são definidos inicialmente, quando a API é criada, e podem ser modificados aqui. A **Descrição** fornece uma descrição opcional e **Com credenciais** permite configurar a autenticação HTTP básica.

![Configurações da API][Configurações da API]

Para configurar a Autenticação Básica HTTP para o serviço Web que está implementando a API, selecione **Básico** no menu suspenso **Com credenciais** e insira as credenciais desejadas.

![Configurações da autenticação Básica][Configurações da autenticação Básica]

Clique em **Salvar** para salvar quaisquer mudanças feitas nas configurações da API.

## <a name="next-steps"> </a>Próximas etapas

Após criar uma API e definir as configurações, as próximas etapas são adicionar as operações à API, adicionar a API a um produto e publicá-la para que fique disponível para desenvolvedores. Para obter mais informações, consulte os dois guias a seguir.

-   [Como adicionar operações a uma API][Como adicionar operações a uma API]
-   [Como criar e publicar um produto][Como criar e publicar um produto]

  [Como adicionar operações a uma API]: ../api-management-howto-add-operations
  [Como criar e publicar um produto]: ../api-management-howto-add-products
  [Criar uma nova API]: #create-new-api
  [Definir configurações de API]: #configure-api-settings
  [Próximas etapas]: #next-steps
  [Criar uma instância de serviço de Gerenciamento de API]: ../api-management-get-started/#create-service-instance
  [Introdução ao Gerenciamento de API do Azure]: ../api-management-get-started
  [Console de gerenciamento]: ./media/api-management-howto-create-apis/api-management-management-console.png
  [Criar API]: ./media/api-management-howto-create-apis/api-management-create-api.png
  [Adicionar nova API]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
  [Resumo da API]: ./media/api-management-howto-create-apis/api-management-api-summary.png
  [Configurações da API]: ./media/api-management-howto-create-apis/api-management-api-settings.png
  [Configurações da autenticação Básica]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png
