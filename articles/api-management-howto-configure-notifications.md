<properties pageTitle="How to configure notifications and email templates in Azure API Management" metaKeywords="" description="Learn how to configure notifications and email templates in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How to configure notifications and email templates in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Como configurar notificações e modelos de email no Gerenciamento de API do Azure

O Gerenciamento de API (visualização) possibilita configurar notificações de eventos específicos e modelos dos emails que são usados para se comunicar com os administradores e desenvolvedores de uma instância do Gerenciamento de API. Este tópico mostra como configurar notificações de eventos disponíveis e fornece uma visão geral da configuração dos modelos dos emails usados desses eventos.

## Neste tópico

-   [Configurar notificações do editor][Configurar notificações do editor]
-   [Configurar modelos de email][Configurar modelos de email]

## <a name="publisher-notifications"> </a>Configurar notificações do editor

Para configurar as notificações, clique em **Console de gerenciamento** no Portal do Azure para seu serviço de Gerenciamento de API. Isso levará você ao portal administrativo do Gerenciamento de API.

> Se você ainda não criou uma instância de serviço de Gerenciamento de API, consulte [Criar uma instância de serviço de Gerenciamento de API][Criar uma instância de serviço de Gerenciamento de API] no tutorial [Introdução ao Gerenciamento de API do Azure][Introdução ao Gerenciamento de API do Azure].

![Console de Gerenciamento de API][Console de Gerenciamento de API]

Clique em **Notificações** no menu **Gerenciamento de API** à esquerda para ver as notificações disponíveis.

![Notificações do editor][Notificações do editor]

Os eventos na lista a seguir podem ser configurados para enviar notificações.

-   **Solicitações de assinatura (que precisam de aprovação)** - Os usuários e destinatários de email especificados receberão notificações por email sobre solicitações de assinaturas de produtos de API que precisarem de aprovação.
-   **Novas assinaturas** - Os usuários e destinatários de email especificados receberão notificações por email sobre novas assinaturas de produtos de API.
-   **Solicitações da galeria de aplicativos** - Os usuários e destinatários de email especificados receberão notificações por email quando novos aplicativos forem enviados à galeria de aplicativos.
-   **CCO** - Os usuários e destinatários de email especificados receberão cópias ocultas de todos os emails enviados a desenvolvedores.
-   **Novo problema ou comentário** - Os usuários e destinatários de email especificados receberão notificações por email quando um novo problema ou comentário for enviado no portal do desenvolvedor.
-   **Mensagem de conta encerrada** - Os usuários e destinatários de email especificados receberão notificações por email quando uma conta for encerrada.
-   **Limite de cota de assinatura se aproximando** - Os usuários e destinatários de email a seguir receberão notificações por email quando o uso de assinaturas estiver se aproximando da cota.

Para cada evento, você pode especificar destinatários de email usando a caixa de texto de endereço de email ou selecionando os usuários em uma lista.

Para especificar os endereços de email a serem notificados, insira-os na caixa de texto de endereço de email. Se tiver diversos endereços de email, separe-os por vírgula.

![Destinatários da notificação][Destinatários da notificação]

Para especificar os usuários a serem notificados, clique em **adicionar destinatário**, marque as caixas de seleção ao lado dos usuários a serem notificados e clique em **OK**.

> Observe que somente administradores são exibidos na lista.

Após configurar os destinatários da notificação, clique em **Salvar** para aplicar a atualização dos destinatários da notificação.

> Se você navegar para fora da guia **Notificações do editor**, o Portal de gerenciamento de API o alertará se houver alterações não salvas.

## <a name="email-templates"> </a>Configurar modelos de email

O Gerenciamento de API fornece modelos de email para mensagens de email que são enviadas no decorrer da administração e da utilização do serviço. Os seguintes modelos de email são fornecidos.

-   Envio à galeria de aplicativos aprovado
-   Carta de despedida do desenvolvedor
-   Notificação de limite de cota se aproximando
-   Convidar usuário
-   Novo comentário adicionado a um problema
-   Novo problema recebido
-   Nova assinatura ativada
-   Confirmação de renovação de assinatura
-   Solicitação de assinatura negada
-   Solicitação de assinatura recebida

Esses modelos podem ser modificados da forma desejada.

Para ver e configurar os modelos de email para sua instância do Gerenciamento de API, clique em **Notificações** no menu **Gerenciamento da API** à esquerda e selecione a guia **Modelos de email**.

![Modelos de email][Modelos de email]

Para ver ou modificar um modelo específico, selecione-o na lista suspensa **Modelos**.

![Lista de modelos de email][Lista de modelos de email]

Cada modelo de email tem um assunto em texto sem formatação e uma definição de corpo em formato HTML. Cada item pode ser personalizado da forma desejada.

![Editor de modelos de email][Editor de modelos de email]

A lista **Parâmetros** contém parâmetros que, quando inseridos no assunto ou corpo, serão substituídos pelo valor designado quando o email for enviado. Para inserir um parâmetro, posicione o cursor onde quiser que ele seja colocado e clique na seta à esquerda do nome do parâmetro.

Clique em **Visualização** ou **Enviar um teste** para ver como o email ficará ou enviar um email de teste.

> Observe que os parâmetros não são substituídos por valores reais ao visualizar ou enviar um teste.
>
> Para salvar as alterações feitas no modelo de email, clique em **Salvar**, e para cancelar as alterações clique em **Cancelar**.

  [Configurar notificações do editor]: #publisher-notifications
  [Configurar modelos de email]: #email-templates
  [Criar uma instância de serviço de Gerenciamento de API]: ../api-management-get-started/#create-service-instance
  [Introdução ao Gerenciamento de API do Azure]: ../api-management-get-started
  [Console de Gerenciamento de API]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
  [Notificações do editor]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
  [Destinatários da notificação]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png
  [Modelos de email]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
  [Lista de modelos de email]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
  [Editor de modelos de email]: ./media/api-management-howto-configure-notifications/api-management-email-template.png
