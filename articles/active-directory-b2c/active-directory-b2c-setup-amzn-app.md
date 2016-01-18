<properties
	pageTitle="Visualização do Active Directory B2C do Azure: configuração da Amazon | Microsoft Azure"
	description="Forneça as opções de inscrição e conexão aos consumidores com contas LinkedIn em seus aplicativos protegidos pelo Active Directory B2C do Azure"
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

# Visualização do Active Directory B2C do Azure: permita a conexão e a inscrição de clientes com contas da Amazon

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Criar um aplicativo Amazon

Para usar a Amazon como um provedor de identidade no Azure AD B2C, você primeiro precisa criar um aplicativo Amazon e fornecê-lo com os parâmetros corretos. Você precisará de uma conta Amazon para fazer isso; se não tiver uma, pode obtê-la em [http://www.amazon.com/](http://www.amazon.com/).

1. Vá para o [Centro de Desenvolvedores da Amazon](https://login.amazon.com/) e entre com suas credenciais de conta da Amazon.
2. Se você ainda não tiver feito isso, clique em **Inscrever-se**, siga as etapas de registro do desenvolvedor e aceite a política.
3. Clique em **Registrar novo aplicativo**.

    ![Amazon - Novo aplicativo](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)

4. Forneça informações do aplicativo (**Nome**, **Descrição** e **URL do Aviso de Privacidade**) e clique em **Salvar**.

    ![Amazon - Registrar aplicativo](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)

5. Na seção **Configurações da Web**, copie os valores de **ID do cliente** e **Segredo do cliente** (você precisará clicar no botão **Mostrar Segredo** para vê-lo). Você precisará que ambos configurem a Amazon como um provedor de identidade em seu locatário. Clique em **Editar** na parte inferior da seção.

> [AZURE.NOTE]O **Segredo do cliente** é uma credencial de segurança importante.

    ![Amazon - Client secret](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)

6. Digite [https://login.microsoftonline.com](https://login.microsoftonline.com) no campo **Origens JavaScript permitidas** e `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no campo **URLs de Retorno Permitidas**, em que **{tenant}** deve ser substituído pelo nome do locatário (por exemplo, contoso.onmicrosoft.com). Clique em **Salvar**.

> [AZURE.NOTE]O valor **{tenant}** diferencia letras maiúsculas de minúsculas.

    ![Amazon - URLs](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## Configurar a Amazon como um provedor de identidade em seu locatário

1. [Siga estas etapas para navegar até a folha de recursos do B2C no Portal do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Na folha de recursos do B2C, clique em **Provedores de identidade**.
3. Clique em **+Adicionar**, na parte superior da folha.
4. Forneça um **Nome** amigável para a configuração do provedor de identidade. Por exemplo, insira “Amzn”.
5. Clique em **Tipo de provedor de identidade**, selecione **Amazon** e clique em **OK**.
6. Clique em **Configurar esse provedor de identidade** e insira a **ID do cliente** e o **Segredo do cliente** do aplicativo Amazon que você criou anteriormente.
7. Clique em **OK** e em **Criar** para salvar sua configuração da Amazon.

<!---HONumber=AcomDC_0107_2016-->