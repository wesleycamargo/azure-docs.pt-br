1. Registre o back-end do aplicativo móvel com seu locatário do Active Directory do Azure, seguindo o tópico [Como configurar seu aplicativo móvel com o Active Directory do Azure].

2. Navegue até **Active Directory** no [Portal de Gerenciamento do Azure]

   ![](./media/app-service-mobile-adal-register-app/app-service-navigate-aad.png)

3. Selecione o diretório e, em seguida, selecione a guia **Aplicativos** na parte superior. Clique em **ADICIONAR** na parte inferior para criar um novo registro de aplicativo. 

4. Clique em **Adicionar um aplicativo que a minha organização está desenvolvendo**.

5. No Assistente para Adicionar aplicativo, insira um **Nome** para o seu aplicativo e clique no tipo **Aplicativo Cliente Nativo**. Em seguida, clique em continuar.

6. Na caixa **URI de Redirecionamento**, insira o ponto de extremidade /login/done em seu gateway de Serviço de Aplicativo. Esse valor deve ser similar a https://contoso.azurewebsites.net/login/done.

7. Depois que o aplicativo nativo tiver sido adicionado, clique na guia **Configurar**. Copie a **ID do cliente**. Você precisará dessas informações posteriormente.

8. Role a página para baixo até a seção de **permissões para outros aplicativos** e clique em **Adicionar aplicativo**.

9. Procure o aplicativo Web que você registrou anteriormente e clique no ícone de adição. Em seguida, clique em verificar para fechar a caixa de diálogo.

10. Na nova entrada que você acabou de adicionar, abra a lista suspensa **Permissões Delegadas** e selecione **Acessar (nome do aplicativo)**. Em seguida, clique em **Salvar**

   ![](./media/app-service-mobile-adal-register-app/aad-native-client-add-permissions.png)

Seu aplicativo está configurado no AAD para que os usuários possam fazer logon usando o logon do AAD.

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Como configurar seu aplicativo móvel com o Active Directory do Azure]: ../articles/app-service-how-to-configure-active-directory-authentication-preview.md

<!---HONumber=Oct15_HO3-->