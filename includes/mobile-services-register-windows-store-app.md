
1. Se você não tiver registrado seu aplicativo, navegue até o [Enviar uma página de aplicativo] no Centro de Desenvolvimento de aplicativos da Windows Store, faça logon com sua conta da Microsoft e, em seguida, clique em **Nome do aplicativo**.
   
       ![](./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png)
2. Selecione **Criar um novo aplicativo ao reservar um nome exclusivo** e clique em **Continuar**, em seguida, digite um nome para seu aplicativo no **Nome do aplicativo**, clique em **Reservar nome do aplicativo**, e, em seguida, clique em **Salvar**.
   
       ![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png)
   
       Isso cria um novo registro da Windows Store para seu aplicativo.
3. No Visual Studio, abra o projeto criado quando você concluiu o tutorial [Introdução aos Serviços Móveis].
4. No gerenciador de soluções, clique com o botão direito do mouse no projeto do aplicativo da Windows Store, clique em **Armazenar** e, em seguida, clique em **Aplicativo associado à Store...**.
   
      ![](./media/mobile-services-register-windows-store-app/mobile-services-store-association.png)
   
       Isso exibe o assistente **Associar seu aplicativo à Windows Store**.
5. No assistente, clique em **Entrar** e, em seguida, faça logon com sua conta da Microsoft, selecione o aplicativo que você registrou na etapa 2, clique em **Próximo**, e, em seguida, clique em **Associar**.
   
       Isso adiciona as informações de registro necessárias da Windows Store para o manifesto do aplicativo.
6. (Opcional) Para um aplicativo Windows universal, repita as etapas 4 e 5 para o projeto da Windows Phone Store.
7. De volta à página do Centro de Desenvolvimento do Windows do seu novo aplicativo, clique em **Serviços**.
   
       ![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png)
8. Na página Serviços, clique em **Site do Live Services** em **Serviços Móveis do Azure**.
   
    ![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png)
9. Clique em **Configurações da API**, selecione habilitar **Aplicativo cliente móvel ou de desktop**, forneça a URL de serviço móvel como o **Domínio de destino**, forneça um valor de `https://<mobile_service>.azure-mobile.net/login/microsoftaccount/` em **URL de redirecionamento**, em seguida, clique em **Salvar**.
   
    ![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth-2.png)
10. Em **Configurações de aplicativo**, anote os valores de **ID do cliente**, **Segredo do cliente** e **Identificador de Segurança de Pacote (SID)**.
    
        ![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png)
    
    > [!NOTE]
    > O segredo do cliente e o SID do pacote são credenciais de segurança importantes. Não compartilhe esses segredos com ninguém nem os distribua com o seu aplicativo.
    > 
    > 
11. Faça logon no [Portal clássico do Azure](https://manage.windowsazure.com/), clique em **Serviços Móveis** e clique em seu aplicativo.
12. Clique na guia **Identidade**, insira os valores **Segredo do cliente** e **SID do pacote** obtidos a partir de WNS na etapa 4 e, em seguida, clique em **Salvar**.
    
       ![](./media/mobile-services-register-windows-store-app/mobile-push-tab.png)
13. Clique na guia **Identidade**. Observe que os valores **Segredo do cliente** e **SID do pacote** já foram definidos na etapa anterior. Insira a **ID do cliente** que você anotou antes e clique em **Salvar**.
    
       ![](./media/mobile-services-register-windows-store-app/mobile-services-identity-tab.png)

Agora está pronto para usar uma conta da Microsoft para autenticação em seu aplicativo.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Introdução aos Serviços Móveis]: /develop/mobile/tutorials/get-started/#create-new-service
[Enviar uma página de aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582

<!---HONumber=AcomDC_1203_2015-->