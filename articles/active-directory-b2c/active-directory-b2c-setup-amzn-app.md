<properties
	pageTitle="Visualização do Active Directory B2C do Azure: configuração da Amazon | Microsoft Azure"
	description="Forneça inscrição e entrada para consumidores com contas da Amazon em seus aplicativos protegidos pelo Azure Active Directory B2C."
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
	ms.date="04/04/2016"
	ms.author="swkrish"/>

# Visualização do Azure Active Directory B2C: fornecer inscrição e entrada para consumidores com contas da Amazon

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Criar um aplicativo da Amazon

Para usar a Amazon como um provedor de identidade no Azure AD (Azure Active Directory) B2C, é necessário primeiro criar um aplicativo da Amazon e fornecê-lo com os parâmetros corretos. Você precisa de uma conta do Amazon para fazer isso. Se você não tiver uma, poderá obtê-la em [http://www.amazon.com/](http://www.amazon.com/).

1. Acesse o [Centro de Desenvolvedores da Amazon](https://login.amazon.com/) e entre com suas credenciais de conta da Amazon.
2. Se você ainda não tiver feito isso, clique em **Inscrever-se**, siga as etapas de registro do desenvolvedor e aceite a política.
3. Clique em **Registrar novo aplicativo**.

    ![Registrando um novo aplicativo no site da Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)

4. Forneça as informações do aplicativo (**Nome**, **Descrição** e **URL do Aviso de Privacidade**) e clique em **Salvar**.

    ![Fornecendo informações de aplicativo para registrar um novo aplicativo na Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)

5. Na seção **Configurações da Web**, copie os valores de **ID do cliente** e **Segredo do Cliente**. (Você precisa clicar no botão **Mostrar Segredo** para exibi-lo.) Você precisará de ambos para configurar a Amazon como um provedor de identidade em seu locatário. Clique em **Editar** na parte inferior da seção. O **Segredo do cliente** é uma credencial de segurança importante.

	![Fornecendo a ID e o Segredo do Cliente para seu novo aplicativo na Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)

6. Digite `https://login.microsoftonline.com` no campo **JavaScript Origins Permitido** e `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no campo **URLs de Retorno Permitidas**. Substitua **{tenant}** pelo nome do locatário (por exemplo, contoso.onmicrosoft.com). Clique em **Salvar**. O valor **{tenant}** diferencia letras maiúsculas de minúsculas.

    ![Fornecendo JavaScript Origins e URLs de Retorno para o novo aplicativo na Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## Configurar a Amazon como um provedor de identidade em seu locatário

1. Siga estas etapas para [navegar até a folha de recursos do B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) no Portal do Azure.
2. Na folha de recursos do B2C, clique em **Provedores de identidade**.
3. Clique em **+Adicionar**, na parte superior da folha.
4. Forneça um **Nome** amigável para a configuração do provedor de identidade. Por exemplo, insira “Amzn”.
5. Clique em **Tipo de provedor de identidade**, selecione **Amazon** e clique em **OK**.
6. Clique em **Configurar esse provedor de identidade** e insira a ID e o segredo do cliente do aplicativo Amazon que você criou anteriormente.
7. Clique em **OK** e em **Criar** para salvar sua configuração da Amazon.

<!---HONumber=AcomDC_0406_2016-->