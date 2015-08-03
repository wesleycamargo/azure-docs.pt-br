<properties 
	pageTitle="Usar o aplicativo móvel como método de contato com o Azure MFA" 
	description="Esta página mostrará aos usuários como usar o aplicativo móvel como o método de contato principal para o Azure MFA." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/30/2015" 
	ms.author="billmath"/>

# Usar o aplicativo móvel como método de contato com o Azure Multi-Factor Authentication

Se desejar usar o aplicativo móvel como o método de contato principal, você poderá usar este artigo. Ele vai ajudar a configurar a autenticação multifator para usar o aplicativo móvel como o método de contato principal.

O aplicativo Azure Authenticator está disponível para [Windows Phone](http://www.windowsphone.com/en-us/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50), [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) e [IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458).

## Para usar um aplicativo móvel como método de contato
<ol>
<li>Selecione Aplicativo Móvel no menu suspenso.</li>

<center>![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)</center>

<li>Selecione Notificação ou Senha de Uso Único e clique em Configurar.</li>
<li>No telefone com o aplicativo Azure Authenticator instalado, inicie o aplicativo e clique em Verificar código de barras.</li>


<center>![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)</center>


<li>Examine a imagem do código de barras que aparece com a tela Configurar aplicativo móvel. Clique em Concluído para fechar a tela de código de barras. Se não for possível ver o código de barras, você poderá inserir as informações manualmente.</li>

<center>![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)</center>

<li>No telefone, ele começará a ser ativado; depois de concluído, clique em Contate-me. Isso enviará uma notificação ou um código de verificação ao seu telefone. Clique em Verificar.</li>

<center>![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)</center>

<li>Clique em Fechar. Nesse ponto, sua verificação deve ter sido bem-sucedida.</li>
<li>Agora é recomendável inserir o número do celular, caso você perca o acesso ao aplicativo móvel. <li>Especifique o seu país na lista suspensa e insira o número do celular na caixa próxima ao país. Clique em Avançar.</li> <li>Até aqui, você configurou o método de contato e agora é hora de configurar as senhas de aplicativo para aplicativos que não usam navegador, como o Outlook 2010 ou anterior. Se você não usa esses aplicativos, clique em **Concluído**. Caso contrário, passe para a próxima etapa.</li>

<center>![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)</center>

<li>Se estiver usando esses aplicativos, copie a senha de aplicativo fornecida e cole-a no aplicativo que não usa navegador. Para ver as etapas de aplicativos individuais, como Outlook e Lync, consulte Como alterar a senha no seu email para a senha de aplicativo e Como alterar a senha no seu aplicativo para a senha de aplicativo.</li>
<li>Clique em Concluído.</li>

<!---HONumber=July15_HO4-->