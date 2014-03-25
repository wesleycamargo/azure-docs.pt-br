
1. Faça logon no [Portal de Gerenciamento do Windows Azure], clique em **Serviços Móveis** e, em seguida, clique no seu aplicativo.

	![](./media/mobile-services-create-new-push-table/mobile-services-selection.png)

2. Clique na guia **Dados** e, em seguida, clique em **Criar**.

	![](./media/mobile-services-create-new-push-table/mobile-create-table.png)

	Isso exibe a caixa de diálogo **Criar uma nova tabela**.

3. Mantendo a configuração padrão **Qualquer pessoa com a chave do aplicativo** para todas as permissões, digite _Registrations_ em **Nome de Tabela** e, em seguida, clique no botão de seleção.

	![](./media/mobile-services-create-new-push-table/mobile-create-registrations-table.png)

  Isso cria a tabela **Registros**, que armazena o canal URIs usado para enviar notificações de push.

Em seguida, você modificará o seu aplicativo para ativar as notificações de push.

<!-- URLs -->
[Portal de Gerenciamento do Windows Azure]: https://manage.windowsazure.com/

