## <a name="register-app-aad"></a>Registrar o seu aplicativo cliente com o Active Directory do Azure

1.  Navegue até **Active Directory** no [Portal de Gerenciamento do Azure][] e clique no seu diretório.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-select-aad.png)

1.  Clique na guia **Aplicativos** na parte superior e clique para **ADICIONAR** um aplicativo.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-aad-applications-tab.png)

1.  Clique em **Adicionar um aplicativo que a minha organização está desenvolvendo**.

2.  No Assistente para Adicionar aplicativo, insira um **Nome** para o seu aplicativo e clique no tipo **Aplicativo Cliente Nativo**. Em seguida, clique em continuar.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-selection.png)

1.  Na caixa **URI de Redirecionamento**, insira o ponto de extremidade /login/done para seu serviço móvel. Esse valor deve ser similar a <https://todolist.azure-mobile.net/login/done>.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-redirect-uri.png)

1.  Clique na guia **Configurar** para o aplicativo nativo e copie a **ID do Cliente**. Você precisará dessas informações posteriormente.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-client-id.png)

1.  Role a página para baixo até a seção **permissões para outros aplicativos** e dê acesso completo ao aplicativo do serviço móvel registrado anteriormente. Em seguida, clique em **Salvar**

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-add-permissions.png)

O seu serviço móvel agora está configurado no AAD para receber logons únicos do seu aplicativo.

  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/