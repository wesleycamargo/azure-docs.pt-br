<properties
	pageTitle="Usar um aplicativo móvel como método de contato com a Autenticação Multifator do Azure| Microsoft Azure"
	description="Este artigo mostra como usar um aplicativo móvel como método de contato principal para a Autenticação Multifator do Azure."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtland"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/30/2016"
	ms.author="kgremban"/>

# Usar um aplicativo móvel como método de contato com a Autenticação Multifator do Azure

Se quiser usar o aplicativo Microsoft Authenticator como método de contato principal, você poderá usar este artigo. Ele orienta você durante a configuração da Autenticação Multifator do Azure para usar um aplicativo móvel como o método de contato principal.

O aplicativo Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## Usar o Microsoft Authenticator como método de contato


1. Na tela **Verificação de segurança adicional**, selecione **Aplicativo móvel** na lista suspensa.
2. Selecione **Notificação** ou **Senha de uso único** e selecione **Configurar**.

	![Tela de verificação de segurança adicional](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)

3. No telefone com o aplicativo Microsoft Authenticator instalado, abra o aplicativo e selecione **+** para adicionar uma nova conta.
4. Especifique que você deseja adicionar uma conta corporativa ou de estudante. Isso abre o scanner de código QR. Se a câmera não estiver funcionando corretamente, você optar por inserir as informações da sua empresa manualmente. Para obter mais informações, consulte [Adicionar uma conta manualmente](#add-an-account-manually).

	![Tela para selecionar uma conta](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

	![Tela para selecionar se deseja digitalizar um código QR ou inserir uma conta manualmente](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan4.png)

5. Digitalize a imagem do código QR que apareceu na tela para configurar o aplicativo móvel. Selecione **Concluído** para fechar a tela do código QR.

	![Tela do código QR](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

	Se não for possível digitalizar o código QR, você poderá inserir as informações manualmente.

	![Tela para inserir informações manualmente](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)

6. Quando a ativação for concluída no telefone, selecione **Contate-me**. Esta etapa envia uma notificação ou um código de verificação para seu telefone. Selecione **Verificar**.

	![Tela em que você seleciona Verificar para entrar](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

7. Se sua empresa exigir um PIN para aprovar a verificação de entrada, digite-o.

	![Caixa para inserir um PIN](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

8. Após digitar o PIN, selecione **Fechar**. Nesse ponto, sua verificação deve ter sido bem-sucedida.
9. É recomendável inserir o número de seu telefone celular caso você perca acesso ao aplicativo móvel. Especifique seu país na lista suspensa e insira o número de seu telefone celular na caixa ao lado do país. Selecione **Avançar**.
10. Neste ponto, você configurou o método de contato. Agora, é hora de configurar as senhas do aplicativo para os aplicativos sem navegador, como o Outlook 2010 ou anterior. Se você não usa esses aplicativos, selecione **Concluído**. Caso contrário, prossiga para a próxima etapa.

	![Tela para criar uma senha do aplicativo](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

11. Se estiver usando aplicativos sem navegador, copie a senha do aplicativo fornecida e cole-a em seus aplicativos. Para ver as etapas de aplicativos individuais, como o Outlook e o Lync, consulte Como alterar a senha em seu email para a senha do aplicativo e Como alterar a senha em seu aplicativo para a senha do aplicativo.
12. Selecione **Concluído**.


## Adicionar uma conta manualmente
Se quiser adicionar uma conta manualmente:

1. Selecione o botão **Inserir conta manualmente**.

	![Tela para inserir o código e a URL](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount.png)

	![Tela para inserir o código e a URL](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

2. Se você tiver uma conta que já tem a Autenticação Multifator do Azure, insira o código e a URL que são fornecidos na mesma página que exibe o código de barras. Essas informações são inseridas nas caixas **Código** e **URL** no aplicativo móvel.

	![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

3. Quando a ativação for concluída, selecione **Contate-me**. Esta etapa envia uma notificação ou um código de verificação para seu telefone. Selecione **Verificar**.

<!---HONumber=AcomDC_0921_2016-->