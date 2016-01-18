<properties
	pageTitle="Visualização do Active Directory B2C do Azure: configuração do LinkedIn | Microsoft Azure"
	description="Fornecer registro e conexão aos consumidores com contas do LinkedIn em seus aplicativos protegidos pelo Active Directory B2C do Azure"
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

# Visualização do AD (Active Directory) B2C do Azure: fornecer registro e conexão aos consumidores com contas do LinkedIn

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Criar um aplicativo do LinkedIn

Para usar o LinkedIn como um provedor de identidade no AD (Active Directory) B2C do Azure, primeiramente, você precisará criar um aplicativo do LinkedIn e fornecê-lo com os parâmetros certos. Você precisará de uma conta do LinkedIn para isso; se não tiver uma, você pode obtê-la em [https://www.linkedin.com/](https://www.linkedin.com/).

1. Acesse o [site de Desenvolvedores do LinkedIn](https://www.developer.linkedin.com/) e entre com suas credencias de conta do LinkedIn.
2. Clique em **My Apps (Meus Aplicativos)** na barra do menu superior e em **Create Application (Criar Aplicativo)**.

    ![LinkedIn — Novo aplicativo](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)

3. No formulário **Create a New Application (Criar um Novo Aplicativo)**, preencha as informações relevantes (**nome da empresa**, **nome**, **descrição**, **URL do logotipo do aplicativo**, **uso do aplicativo**, **URL do site**, **e-mail comercial** e **telefone comercial**).
4. Concorde com os Termos de Uso da API do LinkedIn e clique em **Submit (Enviar)**.

    ![LinkedIn — Registrar aplicativo](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)

5. Copie os valores de **Client ID (ID do Cliente)** e **Client Secret (Segredo do Cliente)** (você pode encontrá-los na seção **Authentication Keys (Chaves de Autenticação)**). Você precisará de ambos para configurar o LinkedIn como um provedor de identidade no seu locatário.

> [AZURE.NOTE]O **Segredo do cliente** é uma credencial de segurança importante.

6. Insira `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no campo **Authorized Redirect URLs** (na seção **OAuth 2.0**), em que **{tenant}** deve ser substituído pelo nome do locatário (por exemplo, contoso.onmicrosoft.com) e clique em **Adicionar**. Em seguida, clique em **Update (Atualizar)**.

    ![LinkedIn — Configurar aplicativo](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## Configurar o LinkedIn como um provedor de identidade no locatário

1. [Siga estas etapas para navegar até a folha de recursos do B2C no Portal do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Na folha de recursos do B2C, clique em **Provedores de identidade**.
3. Clique em **+Adicionar**, na parte superior da folha.
4. Forneça um **Nome** amigável para a configuração do provedor de identidade. Por exemplo, insira "LI".
5. Clique em **Tipo do provedor de identidade**, selecione **LinkedIn** e clique em **OK**.
6. Clique em **Configurar este provedor de identidade** e insira a **ID do Cliente** e o **Segredo do Cliente** do aplicativo do LinkedIn que você criou anteriormente.
7. Clique em **OK** e em **Criar** para salvar sua configuração do LinkedIn.

<!---HONumber=AcomDC_0107_2016-->