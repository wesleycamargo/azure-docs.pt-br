<properties
	pageTitle="Visualização do Active Directory B2C do Azure: configuração do Google+ | Microsoft Azure"
	description="Fornecer registro e conexão aos consumidores com contas do Google+ em seus aplicativos protegidos pelo Active Directory B2C do Azure"
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
	ms.date="09/15/2015"
	ms.author="swkrish"/>

# Visualização do Active Directory B2C do Azure: fornecer registro e conexão aos consumidores com contas do Google+

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Criar um aplicativo do Google+

Para usar o Google+ como um provedor de identidade no AD (Active Directory) B2C do Azure, primeiramente, você precisará criar um aplicativo do Google+ e fornecê-lo com os parâmetros certos. Você precisará de uma conta do Google+ para isso; se não tiver uma, você pode obtê-la em [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Acesse o [Console de Desenvolvedores do Google](https://console.developers.google.com/) e entre com suas credenciais de conta do Google+.
2. Clique em **Create Project (Criar Projeto)**, insira um **Project Name (Nome de projeto)**, concorde com os Termos de Serviço e clique em **Create (Criar)**.

    ![G+ — Introdução](./media/active-directory-b2c-setup-goog-app/google-get-started.png)

    ![G+ — Novo Projeto](./media/active-directory-b2c-setup-goog-app/google-new-project.png)

3. Clique em **APIS & Auth (APIs e Autorização)** e em **Credentials (Credenciais)** no painel de navegação esquerdo.
4. Clique na guia **OAuth consent screen (Tela de consentimento do OAuth)** na parte superior.

    ![G+ — Credenciais](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)

5. Selecione ou especifique um **endereço de email** válido, forneça um **nome de produto** e clique em **Save (Salvar)**.

    ![G + — Tela de consentimento do OAuth](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)

6. Em **Application type** (Tipo de aplicativo), selecione **Web application** (Aplicativo Web).
7. Forneça um **nome** para o aplicativo, insira [https://login.microsoftonline.com](https://login.microsoftonline.com) no campo **Authorized redirect URIs** e [https://login.microsoftonline.com/te/{directory}/oauth2/authresp](https://login.microsoftonline.com/te/{directory}/oauth2/authresp) no campo **Authorized redirect URIs **, em que **{directory}** deve ser substituído pelo nome do seu diretório (por exemplo, contosob2c.onmicrosoft.com). Clique em **Criar**.

    > [AZURE.NOTE]O valor de **{directory}** diferencia letras maiúsculas de minúsculas.

    ![G+ — Criar ID de cliente](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)

8. Copie os valores de **Client ID (ID do Cliente)** e **Client secret (Segredo do cliente)**. Você precisará de ambos para configurar o Google+ como um provedor de identidade no seu diretório.

    > [AZURE.NOTE]O **Segredo do cliente** é uma credencial de segurança importante.

    ![G+ — Segredo do cliente](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## Configurar o Google+ como um provedor de identidade no diretório

1. [Navegue até a folha de recursos do B2C no portal de visualização do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Na folha de recursos do B2C, clique em **Provedores de identidade social**.
3. Clique em **+Adicionar**, na parte superior da folha.
4. Forneça um **Nome** amigável para a configuração do provedor de identidade. Por exemplo, insira "G+".
5. Clique em **Tipo do provedor de identidade**, selecione **Google** e clique em **OK**.
6. Clique em **Configurar este provedor de identidade** e insira a **ID do Cliente** e o **Segredo do Cliente** do aplicativo do Google+ que você criou anteriormente.
7. Clique em **OK** e em **Criar** para salvar sua configuração do Google+.

<!---HONumber=Sept15_HO3-->