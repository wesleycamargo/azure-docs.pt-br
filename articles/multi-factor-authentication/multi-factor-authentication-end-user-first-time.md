<properties 
	pageTitle="Conectando-se pela primeira vez com o Multi-Factor Authentication" 
	description="Esta página descreve qual será a experiência do usuário na primeira vez que se conectar." 
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

# Experiência de configuração do Azure Multi-Factor Authentication

 As configurações da verificação de segurança adicional são usadas quando um administrador configura sua conta para exigir que a senha e uma resposta de seu telefone sejam usadas para verificar sua identidade. Se um administrador tiver configurado a conta para exigir a verificação de segurança adicional, **não será possível se conectar até concluir o processo de registro automático**.

## Determinar como você usará o Multi-Factor Authentication

 Na primeira vez que você se conectar depois que sua conta tiver sido configurada, será solicitado que você inicie o processo de registro automático. É possível começar esse processo clicando em **Configurá-lo agora.**

![Configuração](./media/multi-factor-authentication-end-user-first-time/first.png)

Usando o processo de registro, você poderá especificar seu método de verificação preferido, que pode ser qualquer um dos seguintes na tabela abaixo. Para obter informações adicionais, incluindo um passo a passo, basta clicar em um dos métodos.

Método|Descrição
:------------- | :------------- | 
[Ligação de celular](multi-factor-authentication-end-user-first-time-mobile-phone.md)| Faz uma chamada de voz automática para o telefone de Autenticação. O usuário atende à chamada e pressiona # no teclado do telefone para autenticar. Esse número de telefone não será sincronizado com o Active Directory local.
[Mensagem de texto no celular](multi-factor-authentication-end-user-first-time-mobile-phone.md)|Envia para o usuário uma mensagem de texto que contém um código de verificação. O usuário é solicitado a responder à mensagem de texto com o código de verificação ou a inserir o código de verificação na interface de entrada.
[Ligação para telefone comercial](multi-factor-authentication-end-user-first-time-office-phone.md)|Faz uma chamada de voz automática para o usuário. O usuário atende à chamada e pressiona # no teclado do telefone para autenticar.
[Aplicativo móvel](multi-factor-authentication-end-user-first-time-mobile-app.md)|Envia uma notificação por push ao aplicativo móvel Multi-Factor no smartphone ou tablet do usuário. O usuário toca em "Verificar" no aplicativo para autenticar. Como alternativa, o aplicativo também pode ser usado como um token OTP para autenticação offline. O usuário insere o token na tela de entrada para autenticar.<br><p> O aplicativo Multi-Factor Authentication pode funcionar em 2 modos diferentes para fornecer a segurança adicional que um serviço de autenticação multifator pode fornecer. São eles:<li>**Notificação**: nesse modo, o aplicativo Multi-Factor Authentication impede o acesso não autorizado a contas e interrompe transações fraudulentas. Isso é feito usando uma notificação por push para seu telefone ou dispositivo registrado. Basta ver a notificação e, se ela for legítima, selecione Autenticar. Caso contrário, você pode escolher Negar ou optar por negar e reportar a notificação fraudulenta. Para obter informações sobre como reportar notificações fraudulentas, consulte Como usar o recurso Negar e Reportar Fraude do Multi-Factor Authentication.</li><p><li>**Senha de Uso Único**: nesse modo, o aplicativo Multi-Factor Authentication pode ser usado como um token de software para gerar um código de verificação OATH. Esse código de verificação pode ser inserido juntamente com o nome de usuário e a senha, a fim de fornecer a segunda forma de autenticação.</li><br><p> O aplicativo Azure Authenticator está disponível para [Windows Phone](http://www.windowsphone.com/pt-br/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50), [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) e [IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458).

 

<!---HONumber=August15_HO6-->