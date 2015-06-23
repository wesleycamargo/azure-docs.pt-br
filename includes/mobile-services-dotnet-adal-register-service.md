## <a name="register-mobile-service-aad"></a>Registrar o seu serviço móvel com o Active Directory do Azure


Nesta seção, você registrará seu serviço móvel com o Active Directory do Azure e configurará permissões para permitir representação de logon único.

1. Registre seu aplicativo com seu Active Directory do Azure seguindo o tópico [Como registrar-se com o Active Directory do Azure].

2. No [Portal de Gerenciamento do Azure], retorne à extensão do Active Directory do Azure e clique no seu diretório ativo

3. Clique na guia **Aplicativos** e em seu aplicativo.

4. Clique em **Gerenciar Manifesto**. Depois, clique em **Baixar Manifesto** e salve o manifesto do aplicativo em um diretório local.

   ![](./media/mobile-services-dotnet-adal-register-service/mobile-services-aad-app-manage-manifest.png)

5. Abra o arquivo de manifesto do aplicativo com o Visual Studio. Na parte superior do arquivo, localize a linha de permissões do aplicativo que tem a seguinte aparência:

        "oauth2Permissions": [],

    Substitua essa linha pelas permissões do aplicativo a seguir e salve o arquivo.

        "oauth2Permissions": [
            {
                "adminConsentDescription": "Allow the application access to the mobile service",
                "adminConsentDisplayName": "Have full access to the mobile service",
                "id": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
                "isEnabled": true,
                "origin": "Application",
                "type": "User",
                "userConsentDescription": "Allow the application full access to the mobile service on your behalf",
                "userConsentDisplayName": "Have full access to the mobile service",
                "value": "user_impersonation"
            }
        ],

6. No Portal de Gerenciamento do Azure, clique novamente em **Gerenciar Manifesto** para o aplicativo e clique em **Carregar Manifesto**. Navegue até o local do manifesto do aplicativo acabou de ser atualizado e carregue o manifesto.

<!-- URLs. -->
[Como registrar-se com o Active Directory do Azure]: ../articles/mobile-services/mobile-services-how-to-register-active-directory-authentication.md
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
<!--HONumber=54-->