
1. Se você não tiver registrado seu aplicativo, navegue até o [Enviar uma página de aplicativo] no Centro de Desenvolvimento de aplicativos da Windows Store, faça logon com sua conta da Microsoft e, em seguida, clique em **Nome do aplicativo**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png)

2. Digite um nome para o seu aplicativo em **Nome do aplicativo**, clique em **Reservar nome do aplicativo** e, em seguida, clique em **Salvar**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png)

   	Isso cria um novo registro da Windows Store para seu aplicativo.

3. No Visual Studio, abra o projeto criado quando você concluiu o tutorial [Introdução aos Serviços Móveis].

4. No gerenciador de soluções, clique com o botão direito no projeto, clique em **Armazenar** e, em seguida, clique em **Aplicativo associado à Store...**. 

  	![](./media/mobile-services-register-windows-store-app/mobile-services-store-association.png)

   	Isso exibe o assistente **Associar seu aplicativo à Windows Store**.

5. No assistente, clique em **Entrar** e, em seguida, faça login com sua conta da Microsoft.

6. Selecione o aplicativo que você registrou na etapa 2, clique em **Próximo**e, em seguida, clique em **Associar**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-select-app-name.png)

   	Isso adiciona as informações de registro necessárias da Windows Store para o manifesto do aplicativo.    

7. De volta à página do Centro de Desenvolvimento do Windows do seu novo aplicativo, clique em **Serviços**. 

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png) 

8. Na página Serviços, clique em **Site do Live Services** em **Serviços Móveis do Azure**.

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png) 

9. Em **Configurações do aplicativo**, anote os valores de **ID do cliente**, **Segredo do cliente**, e **Identificador de segurança do pacote (SID)**. 

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    >[AZURE.NOTE]O segredo do cliente e o SID do pacote são credenciais de segurança importantes. Não compartilhe esses segredos com ninguém nem os distribua com o seu aplicativo.

10. (Opcional) Clique em **Configurações de API**, habilite **Segurança de redirecionamento reforçada**, forneça um valor de  `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` em **URL de redirecionamento** e clique em **Salvar**.

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth-2.png)

	Isto habilita a autenticação de conta da Microsoft do seu aplicativo.

11. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique em seu aplicativo.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-selection.png)

12. Clique na guia **Push**, insira os valores **Segredo do cliente** e **SID do pacote** obtidos de WNS na etapa 4 e, em seguida, clique em **Salvar**.

   	![](./media/mobile-services-register-windows-store-app/mobile-push-tab.png)

13. Clique na guia **Identidade**. Observe que os valores **Segredo do cliente** e **SID do pacote** já foram definidos na etapa anterior. Insira a **ID do cliente** que você anotou antes e clique em **Salvar**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-identity-tab.png)
 
Agora você está pronto para usar uma Conta da Microsoft para autenticação em seu aplicativo.  

<!-- Anchors. -->

<!-- Images. -->
 

<!-- URLs. -->
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/#create-new-service
[Enviar uma página do aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
<!--HONumber=42-->
