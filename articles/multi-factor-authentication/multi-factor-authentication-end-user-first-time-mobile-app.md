<properties 
	pageTitle="Usar o aplicativo móvel como método de contato com o Azure MFA" 
	description="Esta página mostrará aos usuários como usar o aplicativo móvel como o método de contato principal para o Azure MFA." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenp" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/30/2016" 
	ms.author="billmath"/>

# Usar o aplicativo móvel como método de contato com o Azure Multi-Factor Authentication

Se você quiser usar o aplicativo Microsoft Authenticator como o método de contato principal, você poderá usar este artigo. Ele vai ajudar a configurar a autenticação multifator para usar o aplicativo móvel como o método de contato principal.

O aplicativo Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## Para usar o Microsoft Authenticator como método de contato


- Na tela de Verificação de Segurança Adicional, selecione Aplicativo Móvel na lista suspensa.


![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)

- Selecione Notificação ou Senha de Uso Único e clique em Configurar.
- No telefone com o aplicativo Microsoft Authenticator instalado, inicie o aplicativo e clique em “+” para adicionar uma nova conta. Em seguida, especifique que você deseja adicionar uma conta corporativa ou de estudante, a qual iniciará o scanner de código QR. Se a câmera não estiver funcionando corretamente, você optar por inserir as informações da sua empresa manualmente. [Adicionar uma conta manualmente](#adding-an-account-manually).

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan4.png)

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

- Digitalize a imagem do código de barras que aparece com a tela configurar aplicativo móvel. Clique em Concluído para fechar a tela de código QR.

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

- Se não for possível digitalizar o código QR, você poderá inserir as informações manualmente.

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)

- No telefone, ele começará a ser ativado; depois de concluído, clique em Contate-me. Isso enviará uma notificação ou um código de verificação ao seu telefone. Clique em Verificar.

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

- Algumas empresas podem exigir um PIN durante a verificação.

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)


- Quando isso estiver concluído, você poderá clicar em Fechar. Nesse ponto, sua verificação deve ter sido bem-sucedida.
- Agora é recomendável inserir o número do celular, caso você perca o acesso ao aplicativo móvel.
- Especifique o seu país na lista suspensa e insira o número do celular na caixa próxima ao país. Clique em Próximo.
- Até aqui, você configurou o método de contato e agora é hora de configurar as senhas de aplicativo para aplicativos que não usam navegador, como o Outlook 2010 ou anterior. Se você não usa esses aplicativos, clique em **Concluído**. Caso contrário, passe para a próxima etapa.

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

- Se estiver usando esses aplicativos, copie a senha de aplicativo fornecida e cole-a no aplicativo que não usa navegador. Para ver as etapas de aplicativos individuais, como Outlook e Lync, consulte Como alterar a senha no seu email para a senha de aplicativo e Como alterar a senha no seu aplicativo para a senha de aplicativo.
- Clique em Concluído.


## Adicionando uma conta manualmente
Se você deseja adicionar uma conta manualmente, selecione o botão Inserir conta manualmente.

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount.png)


![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

Agora, se você tiver uma conta que tenha o Azure MFA, insira o código e a URL que é fornecida na mesma página que exibe o código de barras. Isso vai nas caixas de código e URL no aplicativo móvel. Isso iniciará a ativação.

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

Quando isso for concluído, clique em Contato. Isso enviará uma notificação ou um código de verificação ao seu telefone. Clique em Verificar.



 

<!---HONumber=AcomDC_0831_2016-->