1.  Se você não tiver registrado seu aplicativo, navegue até o [Enviar uma página de aplicativo][Enviar uma página de aplicativo] no Centro de Desenvolvimento de aplicativos da Windows Store, faça logon com sua conta da Microsoft e, em seguida, clique em **Nome do aplicativo**.

    ![][]

2.  Digite um nome para o seu aplicativo em **Nome do aplicativo**, clique em **Reservar nome do aplicativo** e, em seguida, clique em **Salvar**.

    ![][1]

    Isso cria um novo registro da Windows Store para seu aplicativo.

3.  No Visual Studio, abra o projeto que você criou quando concluiu o tutorial **Introdução aos Serviços Móveis**.

4.  No gerenciador de soluções, clique com o botão direito no projeto, clique em **Armazenar** e, em seguida, clique em **Aplicativo associado à Store...**.

    ![][2]

    Isso exibe o assistente **Associar seu aplicativo à Windows Store**.

5.  No assistente, clique em **Entrar** e, em seguida, faça login com sua conta da Microsoft.

6.  Selecione o aplicativo que você registrou na etapa 2, clique em **Próximo**e, em seguida, clique em **Associar**.

    ![][3]

    Isso adiciona as informações de registro necessárias da Windows Store para o manifesto do aplicativo.

7.  (Opcional) Repetir as etapas 4-6 para registrar também o projeto do Windows Phone de um aplicativo universal do Windows.

8.  De volta à página do Centro de Desenvolvimento do Windows do seu novo aplicativo, clique em **Serviços**.

    ![][4]

9.  Na página Serviços, clique em **Site de Serviços do Live** em **Serviços Móveis do Azure**.

    ![][5]

10. Clique em **Autenticação do seu serviço** e anote os valores de **Segredo do cliente** e **Identificador de segurança do pacote (SID)**.

    ![][6]

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o de Seguran&ccedil;a</b>
<p>O segredo do cliente e o SID do pacote s&atilde;o credenciais de seguran&ccedil;a importantes. N&atilde;o compartilhe esses segredos com ningu&eacute;m nem os distribua com o seu aplicativo.</p>
</div>

11. Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique em seu aplicativo.

    ![][7]

12. Clique na guia **Push**, insira os valores **Segredo do cliente** e **SID do pacote** obtidos de WNS e, em seguida, clique em **Salvar**.

    > [WACOM.NOTE]Quando você estiver concluindo este tutorial usando um serviço móvel mais antigo, é possível que você veja um link na parte inferior da guia **Push** que diz **Habilitar Push aprimorado**. Clique aqui agora para atualizar seu serviço móvel para se integrar com Hubs de Notificação. Essa alteração não pode ser revertida. Para obter detalhes sobre como habilitar as notificações por push aprimoradas em um serviço móvel em produção, consulte [esta orientação][esta orientação].

    ![][8]

    > [WACOM.NOTE]Quando você define suas credenciais dos WNS para notificações por push aprimoradas na guia **Enviar por Push** no portal, elas são compartilhadas com os Hubs de Notificação para configurar o hub de notificação para seu aplicativo.

<!-- URLs. -->

  [Enviar uma página de aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
  []: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-submit-win8-app.png
  [1]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-app-name.png
  [2]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-store-association.png
  [3]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-select-app-name.png
  [4]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-edit-app.png
  [5]: ./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png
  [6]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-app-push-auth.png
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [7]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-selection.png
  [esta orientação]: http://go.microsoft.com/fwlink/p/?LinkId=391951
  [8]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-push-tab.png
