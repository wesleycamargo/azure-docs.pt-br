<properties linkid="service-bus-manage-messaging-entitites" urlDisplayName="Traffic Manager" pageTitle="Manage Service Bus Messaging Entities - Azure" metaKeywords="" description="Learn how to create and manage your Service Bus entities using the Azure Management Portal." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="service-bus" documentationCenter="" title="How to Manage Service Bus Messaging Entities" authors="sethm" solutions="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="sethm" />

# Como gerenciar entidades de mensagens do Barramento de Serviço

Este tópico descreve como criar e gerenciar suas entidades do Barramento de Serviço usando o [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure]. Você pode usar o portal para criar novos namespaces de serviço ou entidades de mensagens (filas, tópicos ou assinaturas). Você também pode excluir entidades ou alterar o status de entidades.

## Sumário

-   [Como: criar uma entidade do Barramento de Serviço][Como: criar uma entidade do Barramento de Serviço]
-   [Como: excluir uma entidade do Barramento de Serviço][Como: excluir uma entidade do Barramento de Serviço]
-   [Como: desabilitar ou habilitar uma entidade do Barramento de Serviço][Como: desabilitar ou habilitar uma entidade do Barramento de Serviço]
-   [Recursos adicionais][Recursos adicionais]

## <span id="create"></span></a>Como: criar uma entidade do Barramento de Serviço

O Portal de Gerenciamento do Azure oferece suporte a duas maneiras de criar uma entidade do Barramento de Serviço: *Criação Rápida* ou *Criação Personalizada*.

### Criação Rápida

A Criação Rápida permite criar uma fila, um tópico ou um namespace do serviço de retransmissão do Barramento de Serviço em uma única etapa simples. Siga estas etapas para criar uma nova entidade do Barramento de Serviço.

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  Clique no ícone **Novo** na parte inferior esquerda do Portal de Gerenciamento.
3.  Clique no ícone **Serviços de Aplicativo** e clique em **Fila do Barramento de Serviço** (tópico ou retransmissão). Clique em **Criação Rápida** e digite o nome da fila, da região e a id da assinatura do Azure.

    a. Se este for seu primeiro namespace na região selecionada, o portal sugerirá uma fila de namespace; [nome de sua entidade]-ns. Você pode alterar esse valor.

    b. Se você já tiver pelo menos um namespace de serviço nessa região, um namespace será selecionado automaticamente. Você pode alterar esse namespace selecionado.

4.  Clique na marca de seleção ao lado de **criar uma nova fila** (ou tópico).
5.  Quando a fila ou o tópico tiver sido criado, você verá a mensagem **Criação de fila '[nome da fila]' concluída**.

    a. Se não tiver nenhum namespace nessa região ou nessa assinatura do Azure, um novo namespace será criado automaticamente para você. Nesse caso, você receberá duas mensagens de êxito: uma para a criação do namespace e outra para a criação da entidade.

    ![][0]

Clique no ícone de **Barramento de Serviço** na barra de navegação à esquerda para obter uma lista de namespaces. Você localizará o novo namespace que acabou de criar. Clique no namespace na lista. Você verá a entidade que acabou de criar nesse namespace.

**Observação** você pode não ver o namespace listado imediatamente. Leva alguns segundos para criar o namespace do serviço e atualizar a interface de portal.

**Observação** o uso da opção **Criação Rápida** para uma retransmissão não cria um novo ponto de extremidade de retransmissão. Cria apenas um namespace sob o qual você pode criar um ponto de extremidade de retransmissão de forma programática. Para obter mais detalhes, consulte a [Documentação do Barramento de Serviço][Documentação do Barramento de Serviço].

### Criação Personalizada

A **Criação Personalizada** é a versão mais detalhada que fornece botões para alterar os valores padrão das propriedades da entidade (fila ou tópico) que está sendo criada. Para criar um tópico ou uma entidade usando a **Criação Personalizada**, execute as etapas a seguir:

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  Clique em **Novo** na parte inferior esquerda do Portal de Gerenciamento.
3.  Clique no ícone **Serviços de Aplicativo** e clique em **Fila do Barramento de Serviço** (tópico ou retransmissão). Em seguida, clique em Criação Personalizada.
4.  Na primeira tela de diálogo, digite o nome da fila, a região e a id da assinatura do Azure.

    a. Se este for seu primeiro namespace de serviço na região selecionada, o portal sugerirá uma fila de namespace; [nome de sua entidade]-ns. Você pode alterar esse valor.

    b. Se você já tiver pelo menos um namespace nessa região, um namespace será selecionado automaticamente. Você pode alterar esse namespace selecionado.

5.  Clique em **Avançar** para inserir as propriedades restantes.

    ![][1]

6.  Clique na marca de seleção para criar a fila.

    ![][2]

Clique no ícone de **Barramento de Serviço** na barra de navegação à esquerda para obter a lista de namespaces de serviço. Você localizará o novo namespace que acabou de criar. Clique no namespace na lista. Você verá a entidade que acabou de criar nesse namespace.

## <span id="delete"></span></a>Como: excluir uma entidade do Barramento de Serviço

Usando o portal, você pode excluir uma entidade de mensagens do Barramento de Serviço. Isso é aplicável a filas, tópicos e assinaturas do tópico. Para excluir uma fila ou tópico, faça o seguinte:

1.  Navegue até a exibição da lista de namespaces de serviço e clique no namespace sob o qual você criou a entidade (fila ou tópico).
2.  Clique no ícone **Excluir** na parte inferior da página e confirme a operação de exclusão.

    ![][3]

**Observação** a exclusão de entidades não é recuperável. Depois que a entidade for excluída, você não poderá recuperá-la. No entanto, você pode criar outra entidade com o mesmo nome.

Para excluir uma assinatura de tópico, faça o seguinte:

1.  Navegue até a exibição da lista de namespaces e clique no namespace sob o qual você criou o tópico.
2.  Clique no tópico no qual você criou a assinatura.
3.  Clique na guia **Assinaturas** e selecione a assinatura que você deseja excluir.
4.  Clique no ícone **Excluir** na parte inferior da página e confirme a operação de exclusão.

## <span id="disableenable"></span></a>Como: desabilitar ou habilitar uma entidade do Barramento de Serviço

Você pode usar o portal para alterar o status de uma entidade do Barramento de Serviço. Isso é aplicável a filas e tópicos. Para desabilitar ou habilitar uma fila ou tópico, faça o seguinte:

1.  Navegue até a exibição da lista de namespaces de serviço e clique no namespace sob o qual você criou a entidade (fila ou tópico).
2.  Clique em **Desabilitar** (ou **Habilitar**) na parte inferior da página.

    ![][4]

## <span id="seealso"></span></a> Recursos adicionais

[Service Bus do Azure][Service Bus do Azure]

[Central de desenvolvedores do .NET][Central de desenvolvedores do .NET] no site do Azure

[Criando aplicativos que usam tópicos e assinaturas do Barramento de Serviço][Criando aplicativos que usam tópicos e assinaturas do Barramento de Serviço]

[Filas, tópicos e assinaturas][Filas, tópicos e assinaturas]

  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Como: criar uma entidade do Barramento de Serviço]: #create
  [Como: excluir uma entidade do Barramento de Serviço]: #delete
  [Como: desabilitar ou habilitar uma entidade do Barramento de Serviço]: #disableenable
  [Recursos adicionais]: #seealso
  [0]: ./media/service-bus-manage-message-entities/QueueQuickCreate.png
  [Documentação do Barramento de Serviço]: http://www.windowsazure.com/pt-br/develop/net/how-to-guides/service-bus-relay/
  [1]: ./media/service-bus-manage-message-entities/AddQueue1.png
  [2]: ./media/service-bus-manage-message-entities/ConfigureQueue.png
  [3]: ./media/service-bus-manage-message-entities/DeleteEntity.png
  [4]: ./media/service-bus-manage-message-entities/DisableEnable.png
  [Service Bus do Azure]: http://go.microsoft.com/fwlink/?LinkId=266834
  [Central de desenvolvedores do .NET]: http://go.microsoft.com/fwlink/?LinkID=262187
  [Criando aplicativos que usam tópicos e assinaturas do Barramento de Serviço]: http://go.microsoft.com/fwlink/?LinkId=264293
  [Filas, tópicos e assinaturas]: http://go.microsoft.com/fwlink/?LinkId=264291
