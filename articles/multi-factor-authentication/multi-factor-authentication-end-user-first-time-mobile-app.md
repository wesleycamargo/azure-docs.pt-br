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
	ms.date="08/24/2015" 
	ms.author="billmath"/>

# Usar o aplicativo móvel como método de contato com o Azure Multi-Factor Authentication

Se desejar usar o aplicativo móvel como o método de contato principal, você poderá usar este artigo. Ele vai ajudar a configurar a autenticação multifator para usar o aplicativo móvel como o método de contato principal.

O aplicativo Azure Authenticator está disponível para [Windows Phone](http://www.windowsphone.com/pt-BR/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50), [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) e [IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458).

## Para usar um aplicativo móvel como método de contato


- Selecione Aplicativo Móvel no menu suspenso.


![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)

- Selecione Notificação ou Senha de Uso Único e clique em Configurar.
- No telefone com o aplicativo Azure Authenticator instalado, inicie o aplicativo e clique em Verificar código de barras. Para adicionar uma conta que já tem o Azure MFA ou uma conta de terceiros, consulte [Adicionando uma conta manualmente](#adding-an-account-manually).

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

- Examine a imagem do código de barras que aparece com a tela Configurar aplicativo móvel. Clique em Concluído para fechar a tela de código de barras. Se não for possível ver o código de barras, você poderá inserir as informações manualmente.

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)

- No telefone, ele começará a ser ativado; depois de concluído, clique em Contate-me. Isso enviará uma notificação ou um código de verificação ao seu telefone. Clique em Verificar.

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

- Clique em Fechar. Nesse ponto, sua verificação deve ter sido bem-sucedida.
- Agora é recomendável inserir o número do celular, caso você perca o acesso ao aplicativo móvel.
- Especifique o seu país na lista suspensa e insira o número do celular na caixa próxima ao país. Clique em Avançar.
- Até aqui, você configurou o método de contato e agora é hora de configurar as senhas de aplicativo para aplicativos que não usam navegador, como o Outlook 2010 ou anterior. Se você não usa esses aplicativos, clique em **Concluído**. Caso contrário, passe para a próxima etapa.

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

- Se estiver usando esses aplicativos, copie a senha de aplicativo fornecida e cole-a no aplicativo que não usa navegador. Para ver as etapas de aplicativos individuais, como Outlook e Lync, consulte Como alterar a senha no seu email para a senha de aplicativo e Como alterar a senha no seu aplicativo para a senha de aplicativo.
- Clique em Concluído.


## Adicionando uma conta manualmente
Se você deseja adicionar uma conta manualmente, selecione o botão Inserir conta manualmente.

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount.png)

Agora, se você tiver uma conta que tenha o Azure MFA, insira o código e a URL que é fornecida na mesma página que exibe o código de barras. Isso vai nas caixas de código e URL no aplicativo móvel. Isso iniciará a ativação.

![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

Quando isso for concluído, clique em Contato. Isso enviará uma notificação ou um código de verificação ao seu telefone. Clique em Verificar. Para concluir, siga as etapas acima começando pelo número 6.

Se você estiver usando uma conta de terceiros com o aplicativo móvel, insira o nome da conta e a chave de segurança nas caixas fornecidas e, em seguida, ative a conta. Após você ter feito isso e ter verificado a conta, siga as etapas acima começando pelo número 6.


![Configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/add3rdparty.png)

>[AZURE.NOTE]Se você vir "Adicionar conta de trabalho", isso é para a Junção de Local de Trabalho, e não para autenticação multifator. Você pode ignorar isso.
 

<!---HONumber=Oct15_HO3-->