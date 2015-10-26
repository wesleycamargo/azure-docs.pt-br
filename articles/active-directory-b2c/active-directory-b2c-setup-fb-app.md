<properties
	pageTitle="Visualização do Active Directory B2C do Azure: configuração do Facebook | Microsoft Azure"
	description="Forneça as opções de inscrição e conexão aos consumidores com contas do Facebook em seus aplicativos protegidos pelo Active Directory B2C do Azure"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/08/2015"
	ms.author="swkrish"/>

# Visualização do Active Directory B2C do Azure: permitir a conexão e a inscrição de clientes com contas do Facebook

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Criar um aplicativo do Facebook

Para usar o Facebook como provedor de identidade no Active Directory (AD) B2C do Azure, primeiro você precisará criar um aplicativo do Facebook e fornecê-lo com os parâmetros corretos. Você precisará de uma conta do Facebook para fazer isso. Se você não tiver uma, pode obtê-la em [https://www.facebook.com/](https://www.facebook.com/).

1. Vá até o site [Desenvolvedores do Facebook](https://developers.facebook.com/) e entre com suas credenciais de conta do Facebook.
2. Se você ainda não o tiver feito, clique em **Aplicativos** e em **Registrar como um desenvolvedor**, aceite a política e siga as etapas de registro.
3. Clique em **Aplicativos** e em **Adicionar um novo aplicativo**. Escolha **Site** como a plataforma e clique em **Ignorar e Criar ID do Aplicativo**.

    ![FB - Adicionar um novo aplicativo](./media/active-directory-b2c-setup-fb-app/fb-add-new-app.png)

    ![FB - Adicionar um novo aplicativo - Site](./media/active-directory-b2c-setup-fb-app/fb-add-new-app-website.png)

    ![FB - Criar ID do aplicativo](./media/active-directory-b2c-setup-fb-app/fb-new-app-skip.png)

4. No formulário, forneça um **Nome de Exibição**, escolha **Categoria** apropriada e clique em **Criar ID do Aplicativo**. Observação: isso requer que você aceite as Políticas de Plataforma do Facebook e conclua uma Verificação de Segurança online.

    ![FB - Criar ID do aplicativo](./media/active-directory-b2c-setup-fb-app/fb-create-app-id.png)

5. Clique em **Configurações** na barra de navegação à esquerda. Digite um **Email de contato** válido.
6. Clique em **+ Adicionar Plataforma** e selecione **Site**.

    ![FB - Configurações](./media/active-directory-b2c-setup-fb-app/fb-settings.png)

    ![FB - Configurações](./media/active-directory-b2c-setup-fb-app/fb-website.png)

7. Digite [https://login.microsoftonline.com/](https://login.microsoftonline.com/) no campo **URL do Site** e clique em **Salvar Alterações**.
8. Copie o valor de **ID do aplicativo**. Clique em **Mostrar** e copie o valor de **Segredo do Aplicativo**. Você precisará de ambos para configurar o Facebook como um provedor de identidade em seu diretório.

    > [AZURE.NOTE]O **Segredo do Aplicativo** é uma credencial de segurança importante.

    ![FB - URL do site](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

9. Clique na guia **Avançado** na parte superior e digite [https://login.microsoftonline.com/te/{directory}/oauth2/authresp](https://login.microsoftonline.com/te/{directory}/oauth2/authresp) no campo **URIs de Redirecionamento válido do OAuth** (na seção **Segurança**), onde **{directory}** deve ser substituído pelo nome do diretório (por exemplo, contosob2c.onmicrosoft.com). Clique em **Salvar Alterações** na parte inferior da página.

    ![FB - URI de redirecionamento de OAuth](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)

10. Para tornar seu aplicativo do Facebook utilizável pelo Azure AD B2C, você precisa torná-lo público. Você pode fazer isso clicando em **Status e Revisão** na parte de navegação à esquerda e ativando a opção na parte superior da página para **SIM**. E clique em **Confirmar**.

    ![FB - URI de redirecionamento de OAuth](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## Configurar o Facebook como um Provedor de Identidade em seu Diretório

1. [Navegue até a folha dos recursos B2C no portal de visualização do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Na folha de recursos do B2C, clique em **Provedores de identidade**.
3. Clique em **+Adicionar** na parte superior da folha.
4. Forneça um **Nome** amigável para a configuração do provedor de identidade. Por exemplo, insira “FB”.
5. Clique em **Tipo de provedor de identidade**, selecione **Facebook** e clique em **OK**.
6. Clique em **Configurar este provedor de identidade** e insira a **ID do aplicativo** e o **Segredo de Aplicativo** do aplicativo do Facebook que você criou anteriormente nos campos **ID do cliente** e **Segredo do cliente**, respectivamente.
7. Clique em **OK** e em **Criar** para salvar sua configuração do Facebook.

<!---HONumber=Oct15_HO3-->