<properties
	pageTitle="Visualização do Active Directory B2C do Azure: configuração do Facebook | Microsoft Azure"
	description="Forneça inscrição e entrada para consumidores com contas do Facebook em seus aplicativos protegidos pelo Azure Active Directory B2C."
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/06/2016"
	ms.author="swkrish"/>

# Visualização do Azure Active Directory B2C: fornecer inscrição e entrada para consumidores com contas do Facebook

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Criar um aplicativo do Facebook

Para usar o Facebook como provedor de identidade no Azure AD (Azure Active Directory) B2C, será necessário primeiro criar um aplicativo do Facebook e fornecê-lo com os parâmetros corretos. Você precisa de uma conta do Facebook para fazer isso. Se você não tiver uma, poderá obtê-la em [https://www.facebook.com/](https://www.facebook.com/).

1. Acesse o site [Desenvolvedores do Facebook](https://developers.facebook.com/) e entre com suas credenciais de conta do Facebook.
2. Se ainda não tiver feito isso, você precisará registrar-se como desenvolvedor do Facebook. Para fazer isso, clique em **Registrar** (no canto superior direito da página), aceite as políticas do Facebook e conclua as etapas de registro.
3. Clique em **Meus aplicativos** e em **Adicionar um novo Aplicativo**. Escolha **Site** como a plataforma e clique em **Ignorar e Criar a ID do Aplicativo**.

    ![Facebook - Adicionar um novo Aplicativo](./media/active-directory-b2c-setup-fb-app/fb-add-new-app.png)

    ![Facebook - Adicionar um Novo Aplicativo - Site](./media/active-directory-b2c-setup-fb-app/fb-add-new-app-website.png)

    ![Facebook - Criar a ID do Aplicativo](./media/active-directory-b2c-setup-fb-app/fb-new-app-skip.png)

4. No formulário, forneça um **Nome de Exibição**, um **Email de Contato** válido, uma **Categoria** apropriada e clique em **Criar ID do Aplicativo**. Isso requer que você aceite as políticas de plataforma do Facebook e conclua uma verificação de segurança online.

    ![Facebook - Criar uma Nova ID do Aplicativo](./media/active-directory-b2c-setup-fb-app/fb-create-app-id.png)

5. Clique em **Configurações** na barra de navegação à esquerda.
6. Clique em **+Adicionar Plataforma** e selecione **Site**.

    ![Facebook - Configurações](./media/active-directory-b2c-setup-fb-app/fb-settings.png)

    ![Facebook - Configurações - Site](./media/active-directory-b2c-setup-fb-app/fb-website.png)

7. Digite [https://login.microsoftonline.com/](https://login.microsoftonline.com/) no campo **URL do Site** e clique em **Salvar Alterações**.

    ![Facebook - URL do Site](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

8. Copie o valor de **ID do aplicativo**. Clique em **Mostrar** e copie o valor de **Segredo do Aplicativo**. Você precisará de ambos para configurar o Facebook como um provedor de identidade em seu locatário. O **Segredo do Aplicativo** é uma credencial de segurança importante.

    ![Facebook - ID do Aplicativo e Segredo do Aplicativo](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)

9. Clique em **+ Adicionar Produto** no painel de navegação esquerdo e, em seguida, no botão **Introdução** próximo ao **Logon do Facebook**.

    ![Facebook - Logon no Facebook](./media/active-directory-b2c-setup-fb-app/fb-login.png)

10. Digite `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no campo **URIs de redirecionamento OAuth válidos** na seção **Configurações do Cliente OAuth**. Substitua **{tenant}** pelo nome do locatário (por exemplo, contosob2c.onmicrosoft.com). Clique em **Salvar Alterações** na parte inferior da página.

    ![Facebook - URI de Redirecionamento de OAuth](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)

11. Para tornar seu aplicativo do Facebook utilizável pelo Azure AD B2C, você precisa torná-lo público. É possível fazer isso clicando em **Análise de Aplicativos** na navegação à esquerda, ativando a opção na parte superior da página para **SIM** e clicando em **Confirmar**.

    ![Facebook - Público do aplicativo](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## Configurar o Facebook como um provedor de identidade no seu locatário

1. Siga estas etapas para [navegar até a folha de recursos B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) no portal do Azure.
2. Na folha de recursos do B2C, clique em **Provedores de identidade**.
3. Clique em **+Adicionar**, na parte superior da folha.
4. Forneça um **Nome** amigável para a configuração do provedor de identidade. Por exemplo, insira “FB”.
5. Clique em **Tipo de provedor de identidade**, selecione **Facebook** e clique em **OK**.
6. Clique em **Configurar este provedor de identidade** e insira a ID e o segredo do aplicativo (do aplicativo do Facebook criado anteriormente) nos campos **ID do Cliente** e **Segredo do cliente**, respectivamente.
7. Clique em **OK** e em **Criar** para salvar sua configuração do Facebook.

<!---HONumber=AcomDC_0608_2016-->