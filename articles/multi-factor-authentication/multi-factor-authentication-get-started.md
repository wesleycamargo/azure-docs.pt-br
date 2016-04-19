<properties 
	pageTitle="Azure Multi-Factor Authentication - Introdução" 
	description="Escolha a solução de segurança de autenticação multifator ideal para você perguntando o que estou tentando proteger e onde os meus usuários estão localizados. Em seguida, escolha nuvem, Servidor MFA ou AD FS." 
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
	ms.topic="get-started-article" 
	ms.date="04/07/2016" 
	ms.author="billmath"/>

#Escolha a solução de segurança multifator para você

Como há vários tipos de Azure Multi-Factor Authentication, precisamos determinar algumas coisas para descobrir qual versão é a correta para usar. Esses elementos são:

-	[O que estou tentando proteger](#what-am-i-trying-to-secure)
-	[Onde os usuários estão localizados](#where-are-the-users-located)

As seções a seguir fornecem orientações sobre como determinar a cada um deles.

## O que estou tentando proteger?

Para determinar a solução de autenticação multifator correta, precisamos responder primeiro à pergunta de o que você está tentando proteger com um segundo método de autenticação. É um aplicativo no Azure? Ou é um sistema de acesso remoto, por exemplo. Ao determinar o que estamos tentando proteger, passaremos a responder à pergunta de onde a autenticação multifator precisa ser habilitada.


O que você está tentando proteger| Autenticação multifator na nuvem|Servidor Multi-Factor Authentication 
------------- | :-------------: | :-------------: |
Aplicativos primários da Microsoft|* |* |
Aplicativos SaaS da Galeria de Aplicativos|* |* |
Aplicativos IIS publicados por meio da Proxy de aplicativo do Azure AD|* |* |
Aplicativos IIS não publicados por meio da Proxy de aplicativo do Azure AD | |* |
Acesso remoto, como VPN, RDG| |* |



## Onde os usuários estão localizados

Em seguida, dependendo de onde os usuários estão localizados, podemos determinar a solução correta a ser usada, seja a autenticação multifator na nuvem ou no local, usando o Servidor MFA.



Local do usuário| Solução
------------- | :------------- | 
Active Directory do Azure| Autenticação multifator na nuvem|
Azure AD e AD local usando federação com AD FS| O MFA na nuvem e o Servidor MFA são opções disponíveis 
Azure AD e AD local usando o DirSync, o Azure AD Sync, o Azure AD Connect - sem sincronização de senha|O MFA na nuvem e o Servidor MFA são opções disponíveis 
Azure AD e AD local usando o DirSync, o Azure AD Sync, o Azure AD Connect - com sincronização de senha|Autenticação multifator na nuvem
Active Directory local|Servidor Multi-Factor Authentication

A tabela a seguir é uma comparação dos recursos que estão com uma autenticação multifator na nuvem e com o Servidor Multi-Factor Authentication.

 | Autenticação multifator na nuvem | Servidor Multi-Factor Authentication
------------- | :-------------: | :-------------: |
Notificação de aplicativo móvel como um segundo fator | ● | ● |
Código de verificação de aplicativo móvel como um segundo fator | ● | ●
Chamada telefônica como um segundo fator | ● | ● 
SMS unidirecional como segundo fator | ● | ●
SMS bidirecional como segundo fator | | ● 
Tokens de hardware como segundo fator | | ● 
Senhas de aplicativos para clientes que não oferecem suporte a MFA | ● |  
Controle do administrador sobre métodos de autenticação | ● | ● 
Modo PIN | | ●
Alerta de fraude | ● | ●
Relatórios de MFA | ● | ● 
Desvio único | | ● 
Saudações personalizadas para chamadas telefônicas | ● | ● 
ID do chamador personalizável para chamadas telefônicas | ● | ● 
IPs confiáveis | ● | ● 
Lembrar MFA para dispositivos confiáveis (Preview Pública) | ● |  
Acesso condicional | ● | ● 
Cache | | ● 

Agora que determinamos se usar a autenticação multifator na nuvem ou o Servidor MFA local, podemos pode começar a configurar e usar o Azure Multi-Factor Authentication. **Selecione o ícone que representa seu cenário.**

<center> [![Cloud](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md) &#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;[![Proofup](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md) &#160;&#160;&#160;&#160;&#160; </center>

<!---HONumber=AcomDC_0413_2016-->