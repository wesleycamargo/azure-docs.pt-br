1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique no seu aplicativo.

    ![][0]

2.  Clique na guia **Dados** e **Criar**.

    ![][1]

    Isso exibe a caixa de diálogo **Criar uma nova tabela**.

3.  Mantendo a configuração padrão **Qualquer pessoa com a chave do aplicativo** para todas as permissões, digite *Registrations* em **Nome da tabela** e, em seguida, clique no botão de seleção.

    ![][2]

    Isso cria a tabela **Registros**, que armazena o canal URIs usado para enviar notificações de push.

Em seguida, você modificará o seu aplicativo para ativar as notificações de push.

<!-- URLs -->

  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-create-new-push-table/mobile-services-selection.png
  [1]: ./media/mobile-services-create-new-push-table/mobile-create-table.png
  [2]: ./media/mobile-services-create-new-push-table/mobile-create-registrations-table.png
