1.  Se você não tiver registrado seu aplicativo, navegue até o [Enviar uma página de aplicativo][Enviar uma página de aplicativo] no Centro de Desenvolvimento de aplicativos da Windows Store, faça logon com sua conta da Microsoft e, em seguida, clique em **Nome do aplicativo**.

    ![][0]

2.  Digite um nome para o seu aplicativo em **Nome do aplicativo**, clique em **Reservar nome do aplicativo** e, em seguida, clique em **Salvar**.

    ![][1]

    Isso cria um novo registro da Windows Store para seu aplicativo.

3.  No Visual Studio, abra o projeto criado quando você concluiu o tutorial [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis].

4.  No gerenciador de soluções, clique com o botão direito no projeto, clique em **Armazenar** e, em seguida, clique em **Aplicativo associado à Store...**.

    ![][2]

    Isso exibe o assistente **Associar seu aplicativo à Windows Store**.

5.  No assistente, clique em **Entrar** e, em seguida, faça login com sua conta da Microsoft.

6.  Selecione o aplicativo que você registrou na etapa 2, clique em **Próximoe**, em seguida, clique em **Associar**.

    ![][3]

    Isso adiciona as informações de registro necessárias da Windows Store para o manifesto do aplicativo.

7.  De volta à página do Centro de Desenvolvimento do Windows do seu novo aplicativo, clique em **Serviços**.

    ![][4]

8.  Na página Serviços, clique em **Site do Live Services** em **Serviços Móveis do Azure**.

    ![][5]

9.  Em **Configurações de aplicativo**, anote os valores de **ID do cliente**, **Segredo do cliente** e **Identificador de Segurança de Pacote (SID)**.

    ![][6]

    > [WACOM.NOTE]O segredo do cliente e o SID do pacote são credenciais de segurança importantes. Não compartilhe esses segredos com ninguém nem os distribua com o seu aplicativo.

10. (Opcional) Clique em **Configurações de API**, habilite **Segurança de redirecionamento reforçada**, forneça um valor de `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` em **URL de redirecionamento** e clique em **Salvar**.

    ![][7]

    Isto habilita a autenticação de conta da Microsoft do seu aplicativo.

11. Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique em seu aplicativo.

    ![][8]

12. Clique na guia **Push**, insira os valores **Segredo do cliente** e **SID do pacote** obtidos de WNS na etapa 4 e, em seguida, clique em **Salvar**.

    ![][9]

13. Clique na guia **Identidade**. Observe que os valores **Segredo do cliente** e **SID do pacote** já foram definidos na etapa anterior. Insira a **ID do cliente** que você anotou antes e clique em **Salvar**.

    ![][10]

Agora você está pronto para usar uma Conta da Microsoft para autenticação em seu aplicativo.



  [Enviar uma página de aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
  [0]: ./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png
  [1]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png
  [Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/#create-new-service
  [2]: ./media/mobile-services-register-windows-store-app/mobile-services-store-association.png
  [3]: ./media/mobile-services-register-windows-store-app/mobile-services-select-app-name.png
  [4]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png
  [5]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png
  [6]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png
  [7]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth-2.png
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [8]: ./media/mobile-services-register-windows-store-app/mobile-services-selection.png
  [9]: ./media/mobile-services-register-windows-store-app/mobile-push-tab.png
  [10]: ./media/mobile-services-register-windows-store-app/mobile-services-identity-tab.png
