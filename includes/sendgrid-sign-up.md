Os clientes do Azure podem desbloquear 25.000 emails livres por mês. Esses 25.000 e-mails mensais gratuitos lhe darão acesso a uma emissão avançada de relatórios e análises e a [todas as APIs][] (Web, SMTP, Evento, Análise, Subsistema de usuário). Para obter informações sobre os serviços adicionais fornecidos por SendGrid, consulte a página [Recursos do SendGrid][].

### Para se inscrever em uma conta do SendGrid

1. Faça logon no [Portal de Gerenciamento do Azure][].

2. No painel inferior do portal de gerenciamento, clique em **Novo**.

	![command-bar-new][command-bar-new]

3. Clique em **Marketplace**.

	![sendgrid-store][sendgrid-store]

4. Na caixa de diálogo **Escolher um aplicativo e serviço**, selecione **SendGrid** e clique na seta à direita.

5. Na caixa de diálogo **Personalizar aplicativo e serviço**, selecione o plano do **SendGrid** no qual você deseja se inscrever.

6. Digite um nome para identificar o serviço de **SendGrid** nas suas configurações do Azure ou use o valor padrão de **SendGridEmailDelivery.Simplified.SMTPWebAPI**. Os nomes devem ter entre 1 e 100 caracteres e conter somente caracteres alfanuméricos, traços, pontos e caracteres de sublinhado. O nome deve ser exclusivo na sua lista de itens inscritos da Azure Store.

	![store-screen-2][store-screen-2]

7. Escolha um valor para a região; por exemplo, oeste dos Estados Unidos.

8. Clique na seta à direita.

9. Na guia **Revisar Compra**, revise o plano e as informações sobre preços, bem como os termos legais. Se você concordar com os termos, clique na marca de seleção. Depois que clicar na marca de seleção, sua conta do SendGrid iniciará o [processo de provisionamento do SendGrid].

	![store-screen-3][store-screen-3]

10. Depois de confirmar sua compra, você será redirecionado para o painel de complementos e verá a mensagem **Compra de SendGrid**.

	![sendgrid-purchasing-message][sendgrid-purchasing-message]

	Sua conta do SendGrid será provisionada imediatamente e você verá a mensagem **SendGrid de complemento adquirido com êxito**. Sua conta e as credenciais serão criadas agora. Você está pronto para enviar emails neste ponto. 

	Para modificar o plano de assinatura ou consultar as configurações de contato do SendGrid, clique no nome do serviço do SendGrid para abrir o painel SendGrid Marketplace. 

	![sendgrid-add-on-dashboard][sendgrid-add-on-dashboard]

	Para enviar um email usando o SendGrid, você deve fornecer suas credenciais de conta (nome de usuário e senha).

### Para localizar suas credenciais do SendGrid ###

1. Clique em **Informações de Conexão**.

	![sendgrid-connection-info-button][sendgrid-connection-info-button]

2. Na caixa de diálogo *Connection info*, copie a **Senha** e o Nome de usuário para usar neste tutorial posteriormente.

	![sendgrid-connection-info][sendgrid-connection-info]

	Para definir suas configurações de entrega de email, clique no botão **Gerenciar**. Isso abrirá a interface da Web do Sendgrid.com, onde você poderá fazer logon e abrir o Painel de Controle do SendGrid. 

	![sendgrid-control-panel][sendgrid-control-panel]

	Para obter mais informações sobre como começar com o SendGrid, consulte [Começar com o SendGrid][].

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/sendgrid_BAR_NEW.PNG
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid_offerings_store.png
[store-screen-2]: ./media/sendgrid-sign-up/sendgrid_store_scrn2.png
[store-screen-3]: ./media/sendgrid-sign-up/sendgrid_store_scrn3.png
[sendgrid-purchasing-message]: ./media/sendgrid-sign-up/sendgrid_purchasing_message.png
[sendgrid-add-on-dashboard]: ./media/sendgrid-sign-up/sendgrid_add-on_dashboard.png
[sendgrid-connection-info]: ./media/sendgrid-sign-up/sendgrid_connection_info.png
[sendgrid-connection-info-button]: ./media/sendgrid-sign-up/sendgrid_connection_info_button.png
[sendgrid-control-panel]: ./media/sendgrid-sign-up/sendgrid_control_panel.png

<!--Links-->

[Recursos do SendGrid]: http://sendgrid.com/features
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
[Guia de Introdução do SendGrid]: http://sendgrid.com/docs
[Começar com o SendGrid]: http://sendgrid.com/docs
[Processo de provisionamento de SendGrid]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[processo de provisionamento do SendGrid]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[todas as APIs]: https://sendgrid.com/docs/API_Reference/index.html


<!--HONumber=49-->