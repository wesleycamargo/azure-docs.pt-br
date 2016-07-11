<properties
	pageTitle="Preview do Azure Active Directory B2C: configuração da conta da Microsoft | Microsoft Azure"
	description="Forneça inscrição e entrada aos consumidores com contas da Microsoft em seus aplicativos protegidos pelo Azure Active Directory B2C."
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
	ms.date="06/27/2016"
	ms.author="swkrish"/>

# Preview do Azure Active Directory B2C: fornecer inscrição e entrada aos consumidores com contas da Microsoft

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Criar um aplicativo de conta da Microsoft

Para usar a conta da Microsoft como um provedor de identidade no Azure AD (Azure Active Directory) B2C, você precisará criar um aplicativo de conta da Microsoft e fornecer a ele os parâmetros certos. Para fazer isso, é necessário ter uma conta da Microsoft. Se não tiver uma, você poderá obtê-la em [https://www.live.com/](https://www.live.com/).

1. Vá para a [Central de desenvolvedores da conta da Microsoft](https://account.live.com/developers/applications) e entre com suas credenciais da conta da Microsoft.
2. Clique em **Criar aplicativo**.

    ![Conta da Microsoft – Adicionar um novo aplicativo](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)

3. Forneça um **Nome de aplicativo** e clique em **Aceito**. Isso requer a aceitação dos termos de uso dos serviços da Microsoft.

    ![Conta da Microsoft – Nome do aplicativo](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)

4. Clique em **Configurações de API** na barra de navegação à esquerda. Digite um **Email de contato** válido.

    ![Conta da Microsoft – Configurações de API](./media/active-directory-b2c-setup-msa-app/msa-api-settings.png)

5. Insira `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no campo **URLs de Redirecionamento**. Substitua **{tenant}** pelo nome do locatário (por exemplo, contosob2c.onmicrosoft.com). Na parte inferior da página, clique em **Salvar**.

    ![Conta da Microsoft – URL de redirecionamento](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)

6. Clique em **Configurações do Aplicativo** na barra de navegação à esquerda. Copie os valores de **ID do cliente** e **Segredo do cliente**. Você precisará de ambos para configurar a conta da Microsoft como um provedor de identidade em seu locatário. O **Segredo do cliente** é uma credencial de segurança importante.

    ![Conta da Microsoft – Segredo do cliente](./media/active-directory-b2c-setup-msa-app/msa-client-secret.png)

## Configurar a conta da Microsoft como um provedor de identidade no locatário

1. Siga estas etapas para [navegar até a folha de recursos do B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) no portal do Azure.
2. Na folha de recursos do B2C, clique em **Provedores de identidade**.
3. Clique em **+Adicionar**, na parte superior da folha.
4. Forneça um **Nome** amigável para a configuração do provedor de identidade. Por exemplo, insira "MSA".
5. Clique em **Tipo de provedor de identidade**, selecione **Conta da Microsoft** e clique em **OK**.
6. Clique em **Configurar este provedor de identidade** e insira a ID do cliente e o segredo do cliente do aplicativo da conta da Microsoft criado anteriormente.
7. Clique em **OK** e em **Criar** para salvar a configuração da conta da Microsoft.

<!---HONumber=AcomDC_0629_2016-->