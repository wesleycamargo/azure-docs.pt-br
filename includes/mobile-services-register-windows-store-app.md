
1. Se você não tiver registrado seu aplicativo, navegue até o [Enviar uma página de aplicativo] no Centro de Desenvolvimento de aplicativos da Windows Store, faça logon com sua conta da Microsoft e, em seguida, clique em **Nome do aplicativo**.

   	![][0]

2. Digite um nome para o seu aplicativo em **Nome do aplicativo**, clique em **Reservar nome do aplicativo** e, em seguida, clique em **Salvar**.

   	![][1]

   	Isso cria um novo registro da Windows Store para o seu aplicativo.

3. No Visual Studio 2012 Express para Windows 8, abra o projeto que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis].

4. No gerenciador de soluções, clique com o botão direito no projeto, clique em **Armazenar** e, em seguida, clique em **Aplicativo associado à Store...**. 

  	![][2]

   	Isso exibe o assistente **Associar seu aplicativo à Windows Store**.

5. No assistente, clique em **Entrar** e, em seguida, faça login com sua conta da Microsoft.

6. Selecione o aplicativo que você registrou na etapa 2, clique em **Próximo**e, em seguida, clique em **Associar**.

   	![][3]

   	Isso adiciona as informações de registro necessárias da Windows Store para o manifesto do aplicativo.    

7. De volta à página do Centro de Desenvolvimento do Windows do seu novo aplicativo, clique em **Serviços**. 

   	![][4] 

8. Na página Serviços, clique em **Site de Serviços do Live** em **Serviços Móveis do Windows Azure**.

	![][5]

9. Clique em **Autenticação do seu serviço** e anote os valores de **Segredo do cliente** e **Identificador de segurança do pacote (SID)**. 

   	![][6]

    >[WACOM.NOTE]O segredo do cliente e o SID do pacote são credenciais de segurança importantes. Não compartilhe esses segredos com ninguém nem os distribua com o seu aplicativo.

10. Faça logon no [Portal de Gerenciamento do Windows Azure], clique em **Serviços Móveis** e, em seguida, clique no seu aplicativo.

   	![][7]

11. Clique na guia **Push**, insira os valores **Segredo do cliente** e **SID do pacote** obtidos de WNS na etapa 4 e, em seguida, clique em **Salvar**.

   	![][8]

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png
[1]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png
[2]: ./media/mobile-services-register-windows-store-app/mobile-services-store-association.png
[3]: ./media/mobile-services-register-windows-store-app/mobile-services-select-app-name.png
[4]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png
[5]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png
[6]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png
[7]: ./media/mobile-services-register-windows-store-app/mobile-services-selection.png
[8]: ./media/mobile-services-register-windows-store-app/mobile-push-tab.png

<!-- URLs. -->
[Começar com os Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/#create-new-service
[Enviar uma página de aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Portal de Gerenciamento do Windows Azure]: https://manage.windowsazure.com/

