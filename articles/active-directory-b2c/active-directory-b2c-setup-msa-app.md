<properties
	pageTitle="Visualização do Active Directory B2C do Azure: configuração de Conta da Microsoft | Microsoft Azure"
	description="Fornecer registro e conexão aos consumidores com Contas da Microsoft em seus aplicativos protegidos pelo Active Directory B2C do Azure"
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
	ms.date="01/12/2016"
	ms.author="swkrish"/>

# Visualização do Active Directory B2C do Azure: fornecer registro e conexão aos consumidores com Contas da Microsoft

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Criar um aplicativo de Conta da Microsoft

Para usar a Conta da Microsoft como um provedor de identidade no AD (Active Directory) B2C do Azure, primeiramente, você precisará criar um aplicativo de Conta da Microsoft e fornecer a ele os parâmetros certos. Você precisará de uma Conta da Microsoft para isso; se não tiver uma, você pode obtê-la em [https://www.live.com/](https://www.live.com/).

1. Vá para o [Centro do Desenvolvedor da Conta da Microsoft](https://account.live.com/developers/applications) e entre com suas credenciais de Conta da Microsoft.
2. Clique em **Criar aplicativo**.

    ![MSA - Adicionar um novo aplicativo](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)

3. Forneça um **Nome de Aplicativo** e clique em **|aceitar**. Observação: isso requer que você aceite os que termos de uso de serviços da Microsoft.

    ![MSA - Nome do aplicativo](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)

4. Clique em **Configurações da API** na barra de navegação à esquerda. Digite um **Email de contato** válido.

    ![MSA- Configurações da API](./media/active-directory-b2c-setup-msa-app/msa-api-settings.png)

5. Insira `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` no campo **URLs de Redirecionamento**, em que **{tenant}** deve ser substituído pelo nome do locatário (por exemplo, contosob2c.onmicrosoft.com). Na parte inferior da página, clique em **Salvar**.

    ![MSA - URL de redirecionamento](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)

6. Clique em **Configurações do Aplicativo** na barra de navegação à esquerda. Copie os valores de **ID do cliente** e **Segredo do cliente**. Você precisará de ambos para configurar a Conta da Microsoft como um provedor de identidade no seu locatário. Observação: o **Segredo do cliente** é uma credencial de segurança importante.

    ![MSA - Segredo do cliente](./media/active-directory-b2c-setup-msa-app/msa-client-secret.png)

## Configurar a Conta da Microsoft como um provedor de identidade no locatário

1. [Siga estas etapas para navegar até a folha de recursos do B2C no Portal do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Na folha de recursos do B2C, clique em **Provedores de identidade**.
3. Clique em **+Adicionar**, na parte superior da folha.
4. Forneça um **Nome** amigável para a configuração do provedor de identidade. Por exemplo, insira "MSA".
5. Clique em **Tipo do provedor de identidade**, selecione **Conta da Microsoft** e clique em **OK**.
6. Clique em **Configurar este provedor de identidade** e insira a **ID do Cliente** e o **Segredo do Cliente** do aplicativo de Conta da Microsoft que você criou anteriormente.
7. Clique em **OK** e em **Criar** para salvar sua configuração da Conta da Microsoft.

<!---HONumber=AcomDC_0114_2016-->