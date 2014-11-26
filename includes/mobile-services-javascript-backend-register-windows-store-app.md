

1. Se você não tiver registrado seu aplicativo, navegue até [Enviar uma página do aplicativo] no Centro de Desenvolvimento de aplicativos da Windows Store, faça logon com sua conta da Microsoft e, em seguida, clique em **Nome do Aplicativo**.

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-submit-win8-app.png)

2. Digite um nome para seu aplicativo em **Nome do aplicativo**, clique em **Reservar nome do aplicativo** e, em seguida, clique em **Salvar**.

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-app-name.png)

   	Isso cria um novo registro da Windows Store para seu aplicativo.

3. No Visual Studio 2012 Express para Windows 8, abra o projeto que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis].

4. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto, clique em **Loja** e clique em **Associar o Aplicativo à Loja...**. 

  	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-store-association.png)

   	Isso exibe o assistente **Associar seu aplicativo à Windows Store**.

5. No assistente, clique em **Entrar** e, em seguida, faça logon com sua conta da Microsoft.

6. Selecione o aplicativo que você registrou na etapa 2, clique em **Avançar** e, em seguida, clique em **Associar**.

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-select-app-name.png)

   	Isso adiciona as informações de registro necessárias da Windows Store para o manifesto do aplicativo.    

7. De volta na página do Centro de Desenvolvimento do Windows do seu novo aplicativo, clique em **Serviços**. 

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit-app.png) 

8. Na página Serviços, clique em **Site do Live Services** em **Serviços Móveis do Azure**.

	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png)

9. Clique em **Autenticando seu serviço** e anote os valores de **Segredo do cliente** e **SID (Identificador de Segurança de Pacote)**. 

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    <div class="dev-callout"><b>Observação de Segurança</b>
	<p>O segredo do cliente e o SID do pacote são credenciais de segurança importantes. Não compartilhe esses segredos com ninguém nem os distribua com o seu aplicativo.</p>
    </div> 

10. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e, em seguida, clique no seu aplicativo.

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-selection.png)

11. Clique na guia **Enviar por Push**, clique em **Habilitar push aprimorado** e clique em **Sim** para aceitar a alteração da configuração.


	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-enable-enhanced-push.png)

	Isso atualiza a configuração do seu serviço móvel para usar a funcionalidade de notificação por push aprimorada fornecida pelos Hubs de Notificação. O uso de alguns Hubs de Notificação é fornecido gratuitamente com o serviço móvel pago. Para obter mais informações, consulte [Detalhes de preços dos Serviços Móveis](http://go.microsoft.com/fwlink/p/?LinkID=311786).

    <div class="dev-callout"><b>Importante</b>
	<p>Essa operação redefine suas credenciais de envio por push e altera o comportamento dos métodos de envio por push em seus scripts. Essas alterações não podem ser revertidas. Não use esse método para adicionar um hub de notificação para um serviço móvel em produção. Para obter orientação sobre como habilitar as notificações por push aprimoradas em um serviço móvel em produção, consulte <a href="http://go.microsoft.com/fwlink/p/?LinkId=391951">esta orientação</a>.</p>
    </div>

12. Digite os valores de **Segredo do Cliente** e **SID do Pacote** obtidos dos WNS na Etapa 4 e, em seguida, clique em **Salvar**.

   	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-push-tab.png)

	>[WACOM.NOTE]Quando você define suas credenciais dos WNS para notificações por push aprimoradas na guia **Enviar por Push** no portal, elas são compartilhadas com os Hubs de Notificação para configurar o hub de notificação com seu aplicativo.






[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-store-get-started/
[Enviar uma página do aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

