<properties 
	pageTitle="Azure MFA A experiência de conexão com o Azure Multi-Factor Authentication" 
	description="Esta página fornecerá orientação de onde procurar os vários métodos de conexão disponíveis com o Azure MFA."
	keywords="autenticação do usuário, experiência de conexão, conectar com telefone celular, conectar com telefone do escritório" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/22/2016" 
	ms.author="billmath"/>

# A experiência de conexão com o Azure Multi-Factor Authentication
> [AZURE.NOTE]  A documentação a seguir fornecida nesta página mostra uma experiência de logon típica. Para obter ajuda com a conexão, consulte [Problemas com a Azure Multi-Factor Authentication](multi-factor-authentication-end-user-manage-settings.md)



## Qual será sua experiência de conexão?
Sua experiência variará de acordo com o modo que você se conecta e usa a autenticação multifator. Nesta seção, forneceremos informações sobre o que esperar quando você se conecta. Escolha a opção que melhor descreve o que você está fazendo:


O que você está fazendo?|Descrição
:------------- | :------------- | 
[Conectando-se com o telefone celular ou comercial](#signing-in-with-mobile-or-office-phone) | Isso é o que você pode esperar ao entrar usando o telefone celular ou comercial.
[Entrando com o aplicativo Microsoft Authenticator usando notificação](#signing-in-with-the-microsoft-authenticator-app-using-notification) | Isso é o que você pode esperar ao usar o aplicativo Microsoft Authenticator com notificações.
[Entrando com o aplicativo Microsoft Authenticator usando o código de verificação](#signing-in-with-the-microsoft-authenticator-app-using-verification-code)|Isso é o que você pode esperar ao usar o aplicativo Microsoft Authenticator com um código de verificação.
[Conectando-se com um método alternativo](#signing-in-with-an-alternate-method)|Isso mostrará o que esperar se você desejar usar um método alternativo.

## Conectando-se com o telefone celular ou comercial

As informações a seguir descrevem a experiência de usar a autenticação multifator com seu telefone celular ou comercial.

### Para se conectar com uma ligação para seu telefone comercial ou celular

- Entre em um aplicativo ou serviço, como o Office 365, usando seu nome de usuário e senha.
- A Microsoft ligará para você.

![A Microsoft liga](./media/multi-factor-authentication-end-user-signin-phone/call.png)

- Atenda o telefone e pressione a tecla #.

![Resposta](./media/multi-factor-authentication-end-user-signin-phone/phone.png)

- Agora você deve estar conectado.</li>

## Entrando com o aplicativo Microsoft Authenticator usando notificação

As informações a seguir descrevem a experiência de usar a autenticação multifator com o aplicativo Microsoft Authenticator quando você recebe uma notificação.

### Para entrar com uma notificação enviada pelo aplicativo Microsoft Authenticator

- Entre em um aplicativo ou serviço, como o Office 365, usando seu nome de usuário e senha.
- A Microsoft enviará uma notificação.

![A Microsoft envia notificação](./media/multi-factor-authentication-end-user-signin-app-notify/notify.png)


- Atenda o telefone e pressione a tecla de verificação.

![Verificar](./media/multi-factor-authentication-end-user-signin-app-notify/phone.png)


- Agora você deve estar conectado.


## Entrando com o aplicativo Microsoft Authenticator usando o código de verificação

As informações a seguir descrevem a experiência de usar autenticação multifator com o aplicativo Microsoft Authenticator quando você o usa com um código de verificação.

### Para entrar usando um código de verificação com o aplicativo Microsoft Authenticator

- Entre em um aplicativo ou serviço, como o Office 365, usando seu nome de usuário e senha.
- A Microsoft solicitará um código de verificação.

![Inserir código de verificação](./media/multi-factor-authentication-end-user-signin-app-verify/verify.png)

- Abra o aplicativo Microsoft Authenticator em seu telefone e digite o código na caixa de conexão.

![Obter o código](./media/multi-factor-authentication-end-user-signin-app-verify/phone.png)

- Agora você deve estar conectado.


## Conectando-se com um método alternativo


A seção a seguir mostrará como se conectar com um método alternativo quando seu método principal não estiver disponível.

### Para se conectar com um método alternativo

- Entre em um aplicativo ou serviço, como o Office 365, usando seu nome de usuário e senha.
- Selecione a opção Usar uma verificação diferente. Opções diferentes serão exibidas. O número que você vê se baseia na quantidade que foi configurada.

![Usar método alternativo](./media/multi-factor-authentication-end-user-signin-alt/alt.png)

- Escolha um método alternativo e conecte-se.

 

<!---HONumber=AcomDC_0824_2016-->