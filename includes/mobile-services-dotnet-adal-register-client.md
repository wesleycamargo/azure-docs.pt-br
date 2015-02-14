## <a name="register-app-aad"></a>Registrar o seu aplicativo cliente com o Active Directory do Azure

1. Navegue em **Active Directory** no [Portal de Gerenciamento do Azure], e clique em seu diretório.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-select-aad.png)

2. Clique na guia **Aplicativos** na parte superior e clique para **ADICIONAR** um aplicativo. 

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-aad-applications-tab.png)

3. Clique em **Adicionar um aplicativo que minha organização está desenvolvendo**.

4. No Assistente para Adicionar aplicativo, insira um **Nome** para o seu aplicativo e clique no tipo **Aplicativo Cliente Nativo**. Em seguida, clique em continuar.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-selection.png)

5. Na caixa **URI de Redirecionamento**, insira o ponto de extremidade /login/done para seu serviço móvel. Esse valor deve ser similar a https://todolist.azure-mobile.net/login/done.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-redirect-uri.png)

6. Clique na guia **Configurar** para o aplicativo nativo e copie a **ID do cliente**. Você precisará dessas informações posteriormente.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-client-id.png)

7. Role a página para baixo até a seção de **permissões para outros aplicativos** e clique no botão **Adicionar aplicativo**. Escolha **Outros** no menu Exibir e procure por tarefas. Clique em **TodoList** para adicioná-lo ao serviço móvel que você registrou anteriormente e clique na marca de seleção para concluir. Conceda acesso ao aplicativo de serviço móvel. Em seguida, clique em **Salvar**

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-add-permissions.png)

O seu serviço móvel agora está configurado no AAD para receber logons únicos do seu aplicativo.


[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/<!--HONumber=42-->
