<properties linkid="develop-mobile-how-to-guides-register-for-active-directory-authentication" urlDisplayName="Registrar-se para autenticação do Active Directory do Azure" pageTitle="Registrar-se para autenticação do Active Directory do Azure - Serviços Móveis" metaKeywords="aplicativo de registro do Azure, autenticação do Active Directory do Azure, aplicativo de autenticação, autenticação dos serviços móveis" description="Saiba como registrar-se para autenticação do Active Directory do Azure em seu aplicativo de Serviços Móveis." title="Registrar sua conta para usar um logon de conta do Active Directory do Azure" authors="" />

# Registrar seus aplicativos para usar um logon de conta do Active Directory do Azure

Este tópico mostra como registrar seus aplicativos para poderem usar o Active Directory do Azure como um provedor de autenticação para os Serviços Móveis do Azure. 

> [WACOM.NOTE] No momento, o provedor de autenticação do Active Directory do Azure para Serviços Móveis está em fase de visualização. Se você desejar inscrever-se na visualização, entre em contato com <a href="mailto:mobileservices@microsoft.com">mobileservices@microsoft.com</a>. Caso contrário, o provedor de autenticação não estará visível na guia `Identidade` de seu serviço móvel.

> [WACOM.NOTE] Quando você também planeja fornecer autenticação controlada por cliente para SSO (logon único) ou enviar notificações por push de um aplicativo da Windows Store, considere também registrar seu aplicativo da Windows Store. Para obter mais informações, consulte <a href="/pt-br/develop/mobile/how-to-guides/register-for-single-sign-on">Registrar seus aplicativos da Windows Store para autenticação do Windows Live Connect</a>. 

1. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e, em seguida, clique em seu serviço móvel.

    ![][1]

2. Clique na guia **Identidade** de seu serviço móvel. 

    ![][2]

3. Role para baixo até a seção do provedor de identidade do **Active Directory do Azure** e copie a **URL do aplicativo** da lista.

    ![][3]

4. Navegue para **Active Directory** no Portal de Gerenciamento e clique em seu diretório.

    ![][4] 

5. Clique na guia **Aplicativos** na parte superior e clique para **ADICIONAR** um aplicativo. 

    ![][10]

6. Clique em **Adicionar um aplicativo que minha organização está desenvolvendo**.

7. No Assistente de Adicionar Aplicativo, insira um **Nome** para seu aplicativo e clique no tipo **Aplicativo Web e/ou API Web**. Em seguida, clique em continuar.

    ![][5]

8. Na caixa **URL DE LOGON**, cole a ID do aplicativo copiada nas configurações do provedor de identidade do Active Directory de seu serviço móvel. Insira também um identificador de recurso exclusivo na caixa **URI da ID do Aplicativo**. O aplicativo usará o URI para enviar solicitações de logon único ao Active Directory do Azure. Em seguida, clique em continuar.

    ![][6]

9. Clique para habilitar o tipo de acesso **Logon Único** do aplicativo. Clique para concluir o Assistente de Adicionar Aplicativo.

    ![][7]

10. Depois que o aplicativo tiver sido adicionado, role para a parte inferior. Em seguida, clique na seção **Habilitar o aplicativo para ler ou gravar dados do diretório** para expandi-la. Em seguida, clique para copiar a **ID do cliente** do aplicativo.

    ![][8]


11. Retorne para a guia **Identidade** do serviço móvel. Na parte inferior, cole a configuração de **ID do Cliente** no provedor de identidade do Active Directory do Azure. Em seguida, clique em **Salvar**.

    ![][9]



Agora você está pronto para usar um Active Directory do Azure para autenticação em seu aplicativo. 



<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-selection.png
[2]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-identity-tab.png
[3]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-copy-app-url-waad-auth.png
[4]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-select-ad-waad-auth.png
[5]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-1-waad-auth.png
[6]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-2-waad-auth.png
[7]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-3-waad-auth.png
[8]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-waad-auth.png
[9]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-pasted-waad-auth.png
[10]:./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-waad-idenity-tab-selection.png

<!-- URLs. -->
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Download do SDK da Visualização dos Serviços Móveis]: http://www.windowsazure.com/

