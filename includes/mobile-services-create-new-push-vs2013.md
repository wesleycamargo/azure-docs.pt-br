As seguintes etapas registram seu aplicativo com a Windows Store, configuram o serviço móvel para habilitar as notificações por push e adicionam o código ao seu aplicativo para registrar um canal do dispositivo com seu hub de notificação. O Visual Studio 2013 se conecta ao Azure e à Windows Store ao usar as credenciais que você forneceu.

1.  No Visual Studio 2013, abra o Gerenciador de Soluções, clique com o botão direito do mouse no projeto de aplicativo da Windows Store, clique em **Adicionar** e em **Notificação por push**.

    ![Adicionar o assistente Notificação por Push no Visual Studio 2013][Adicionar o assistente Notificação por Push no Visual Studio 2013]

    Isso inicia o Assistente Adicionar Notificação por Push.

2.  Clique em **Próximo**, entre na sua conta da Windows Store e forneça um nome em **Reservar um novo nome** e clique em **Reservar**.

    ![Selecione o nome do aplicativo no assistente Adicionar Notificação por Push][Selecione o nome do aplicativo no assistente Adicionar Notificação por Push]

    Isso cria um novo registro de aplicativo.

3.  Clique no novo registro na lista **Nome do Aplicativo** e clique em **Próximo**.

    ![mobile-add-push-notifications-vs2013-3][mobile-add-push-notifications-vs2013-3]

4.  Na página **Selecione um serviço**, clique no nome do seu serviço móvel e clique em **Próximo** e **Concluir**.

    O hub de notificação utilizado pelo seu serviço móvel é atualizado com o registro dos Serviços de Notificação do Windows (WNS). É possível agora usar os Hubs de Notificação do Azure para enviar notificações de Serviços Móveis para seu aplicativo usando o WNS.

    > [WACOM.NOTE]Este tutorial demonstra como enviar notificações de um back-end do serviço móvel. Você pode usar o mesmo hub de notificação para enviar notificações de qualquer serviço de back-end. Para obter mais informações, consulte [Visão geral de Hubs de Notificação][Visão geral de Hubs de Notificação].

5.  Quando você concluir o assistente, uma nova página **Configuração de push está quase concluída** é aberta no Visual Studio. Esta página fornece os detalhes de um método alternativo para configurar o seu projeto de serviço móvel para enviar notificações que são diferentes deste tutorial.

    > [WACOM.NOTE]O código que é adicionado a sua solução de aplicativo universal do Windows pelo assistente Adicionar Notificação por Push é específico da plataforma. Mais tarde nesta seção, removeremos esta redundância compartilhando o código cliente dos Serviços Móveis, o que torna mais fácil a manutenção do aplicativo universal.



  [Adicionar o assistente Notificação por Push no Visual Studio 2013]: ../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013.png
  [Selecione o nome do aplicativo no assistente Adicionar Notificação por Push]: ../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013-2.png
  [mobile-add-push-notifications-vs2013-3]: ../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013-3.png
  [Visão geral de Hubs de Notificação]: http://msdn.microsoft.com/pt-br/library/azure/jj927170.aspx
