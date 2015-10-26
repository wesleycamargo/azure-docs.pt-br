4. Navegue até a folha de gateway do seu aplicativo de API.

	![Clique em gateway](./media/app-service-api-gateway-config-auth/gateway.png)

7. Na folha **Gateway**, clique em **Configurações** e em **Identidade**.

	![Clique em Configurações](./media/app-service-api-gateway-config-auth/clicksettingsingateway.png)

	![Clique em Identidade](./media/app-service-api-gateway-config-auth/clickidentity.png)

	Da folha **Identidade**, você pode navegar para diferentes folhas para configurar a autenticação usando o Active Directory do Azure e vários outros provedores.

	![Folha de identidade](./media/app-service-api-gateway-config-auth/identityblade.png)
  
3. Escolha o provedor de identidade que você quer usar e siga as etapas no artigo correspondente para configurar seu aplicativo de API com esse provedor. Esses artigos foram escritos para aplicativos móveis, mas os procedimentos são os mesmos para os aplicativos de API. Alguns dos procedimentos exigem que você use o [Portal do Azure].

 - [Conta da Microsoft](../articles/app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication-preview.md)
 - [Logon no Facebook](../articles/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication-preview.md)
 - [Logon no Twitter](../articles/app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication-preview.md)
 - [Logon no Google](../articles/app-service-mobile/app-service-mobile-how-to-configure-google-authentication-preview.md)
 - [Active Directory do Azure](../articles/app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication-preview.md)

Por exemplo, as capturas de tela a seguir mostram o que você verá nas páginas do [portal do Azure] e nas folhas do [portal de visualização do Azure] depois de ativar a autenticação do Active Directory do Azure.

No Portal do Azure, a folha **Active Directory do Azure** tem uma **ID de Cliente** do aplicativo que você criou na guia Active Directory do Azure do portal do Azure, e a função **Locatários Permitidos** tem seu locatário do Active Directory do Azure (por exemplo, "contoso.onmicrosoft.com").

![Folha do Active Directory do Azure](./media/app-service-api-gateway-config-auth/tdinaadblade.png)

No portal do antigo, a guia **Configurar** do aplicativo que você criou no **Active Directory do Azure** tem a **URL de Logon**, o **URI de ID do Aplicativo** e a **URL de Resposta** da folha **Active Directory do Azure** no portal de visualização do Azure.

![](./media/app-service-api-gateway-config-auth/oldportal1.png)

![](./media/app-service-api-gateway-config-auth/oldportal2.png)

![](./media/app-service-api-gateway-config-auth/oldportal3.png)

![](./media/app-service-api-gateway-config-auth/oldportal4.png)

(A URL de resposta na imagem mostra a mesma URL duas vezes, uma vez com `http:` e uma vez com `https:`.)

<!---HONumber=Oct15_HO3-->