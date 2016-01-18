<properties
	pageTitle="Visualização do Active Directory B2C do Azure: configuração do Google+ | Microsoft Azure"
	description="Fornecer registro e conexão aos consumidores com contas do Google+ em seus aplicativos protegidos pelo Active Directory B2C do Azure"
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
	ms.date="01/06/2016"
	ms.author="swkrish"/>

# Visualização do Active Directory B2C do Azure: fornecer registro e conexão aos consumidores com contas do Google+

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Criar um aplicativo do Google+

Para usar o Google+ como um provedor de identidade no AD (Active Directory) B2C do Azure, primeiramente, você precisará criar um aplicativo do Google+ e fornecê-lo com os parâmetros certos. Você precisará de uma conta do Google+ para isso; se não tiver uma, você pode obtê-la em [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Acesse o [Console de Desenvolvedores do Google](https://console.developers.google.com/) e entre com suas credenciais de conta do Google+.
2. Clique em **Criar Projeto**, digite um **Nome de projeto** e clique em **Criar**.

    ![G+ — Introdução](./media/active-directory-b2c-setup-goog-app/google-get-started.png)

    ![G+ — Novo Projeto](./media/active-directory-b2c-setup-goog-app/google-new-project.png)

3. Clique em **API Manager** e em **Credenciais** no painel de navegação esquerdo.
4. Clique na guia **OAuth consent screen (Tela de consentimento do OAuth)** na parte superior.

    ![G+ — Credenciais](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)

5. Selecione ou especifique um **endereço de email** válido, forneça um **nome de produto** e clique em **Save (Salvar)**.

    ![G + — Tela de consentimento do OAuth](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)

6. Clique em **Adicionar credenciais** e escolha **ID do cliente OAuth 2.0**.

    ![G + — Tela de consentimento do OAuth](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)

7. Em **Tipo de aplicativo**, selecione **Aplicativo Web**.

    ![G + — Tela de consentimento do OAuth](./media/active-directory-b2c-setup-goog-app/google-web-app.png)

8. Forneça um **Nome** para o aplicativo, insira [https://login.microsoftonline.com](https://login.microsoftonline.com) no campo **Origens de JavaScript autorizadas** e `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no campo **URIs de redirecionamento autorizados**, em que **{tenant}** deve ser substituído pelo nome do seu locatário (por exemplo, contosob2c.onmicrosoft.com). Clique em **Criar**.

> [AZURE.NOTE]O valor **{tenant}** diferencia letras maiúsculas de minúsculas.

    ![G+ - Create client ID](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)

9. Copie os valores de **ID do cliente** e **Segredo do cliente**. Você precisará de ambos para configurar o Google+ como um provedor de identidade no seu locatário.

> [AZURE.NOTE]O **Segredo do cliente** é uma credencial de segurança importante.

    ![G+ - Client secret](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## Configurar o Google+ como um provedor de identidade no locatário

1. [Siga estas etapas para navegar até a folha de recursos do B2C no Portal do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Na folha de recursos do B2C, clique em **Provedores de identidade**.
3. Clique em **+Adicionar**, na parte superior da folha.
4. Forneça um **Nome** amigável para a configuração do provedor de identidade. Por exemplo, insira "G+".
5. Clique em **Tipo de provedor de identidade**, selecione **Google** e clique em **OK**.
6. Clique em **Configurar este provedor de identidade** e insira a **ID do cliente** e o **Segredo do cliente** do aplicativo do Google+ que você criou anteriormente.
7. Clique em **OK** e em **Create** (Criar) para salvar sua configuração do Google+.

<!---HONumber=AcomDC_0107_2016-->