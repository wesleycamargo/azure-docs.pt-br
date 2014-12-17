

1. Se você não tiver registrado seu aplicativo, navegue até [Enviar uma página do aplicativo] no Centro de Desenvolvimento de aplicativos da Windows Store, faça logon com sua Conta da Microsoft e, em seguida, clique em **Nome do Aplicativo**.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-submit-win8-app.png)

2.  Digite um nome para seu aplicativo em **Nome do aplicativo**, clique em **Reservar nome do aplicativo** e, em seguida, clique em **Salvar**.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-app-name.png)

   	Isso cria um novo registro da Windows Store para seu aplicativo.

3. No Visual Studio, abra o projeto criado quando você concluiu o tutorial **Introdução aos Serviços Móveis**.

4. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto, clique em **Loja** e clique em **Associar o Aplicativo à Loja...**. 

  	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-store-association.png)

   	   	Isso exibe o Assistente de **Associar o Aplicativo à Windows Store**.

5. No assistente, clique em **Entrar** e, em seguida, faça logon com sua conta da Microsoft.

6.  Selecione o aplicativo que você registrou na etapa 2, clique em **Avançar** e, em seguida, clique em **Associar**.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-select-app-name.png)

   	Isso adiciona as informações de registro da Windows Store necessárias para o manifesto do aplicativo.    

7. (Opcional) Repetir as etapas 4-6 para registrar também o projeto do Windows Phone de um aplicativo universal do Windows.

8. De volta na página do Centro de Desenvolvimento do Windows do seu novo aplicativo, clique em **Serviços**. 

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-edit-app.png) 

9. Na página Serviços, clique em **Site do Live Services** em **Serviços Móveis do Azure**.

	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png)

10. Clique em **Autenticando seu serviço** e anote os valores de **Segredo do cliente** e **SID (Identificador de Segurança de Pacote)**. 

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    <div class="dev-callout"><b>Observação de Segurança</b>
	<p>O segredo do cliente e o SID do pacote são credenciais de segurança importantes. Não compartilhe esses segredos com ninguém nem os distribua com o seu aplicativo.</p>
    </div> 

11. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e no aplicativo.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-selection.png)

12. Clique na guia **Push**, insira os valores **Segredo do cliente** e **SID do pacote** obtidos de WNS e, em seguida, clique em **Salvar**.

	>[WACOM.NOTE]Quando você estiver concluindo este tutorial usando um serviço móvel mais antigo, é possível que você veja um link na parte inferior da guia **Push** que diz **Habilitar Push Aprimorado**. Clique aqui agora para atualizar seu serviço móvel para se integrar com Hubs de Notificação. Essa alteração não pode ser revertida. Para obter detalhes sobre como habilitar as notificações por push aprimoradas em um serviço móvel em produção, consulte <a href="http://go.microsoft.com/fwlink/p/?LinkId=391951">esta orientação</a>. 

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-push-tab.png)

	>[WACOM.NOTE]Quando você define suas credenciais do WNS para notificações por push aprimoradas na guia **Push** no portal, elas são compartilhadas com os Hubs de Notificação para configurar o hub de notificação para seu aplicativo.

<!-- URLs. -->
[Introdução aos Serviços Móveis]w
[Enviar uma página do aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
