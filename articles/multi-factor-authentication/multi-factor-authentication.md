<properties 
	pageTitle="Azure Multi-Factor Authentication - o que é isso?" 
	description="O Azure Multi-Factor Authentication é um método de verificar quem você é e que requer o uso de mais do que apenas um nome de usuário e uma senha. Ela fornece uma camada adicional de segurança para logons de usuário e transações." 
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
	ms.date="06/02/2015" 
	ms.author="billmath"/>

# Segurança adicional para um mundo sempre conectado

O MFA (Multi-Factor Authentication) é um método de autenticação que exige o uso de mais de um método de verificação e adiciona uma segunda camada crítica de segurança aos logons e às transações dos usuários. Ela funciona, exigindo dois ou mais dos métodos de verificação a seguir:

- Algo que você sabe (normalmente, uma senha)
- Algo que você tem (um dispositivo confiável que não pode ser facilmente clonado, como um telefone)
- Algo seu (biometria)

<center>![Username and Password](./media/multi-factor-authentication/pword.png) &#160;&#160;&#160;&#160;&#160;![Certificates](./media/multi-factor-authentication/phone.png) &#160;&#160;&#160;&#160;&#160;![Smart Phone](./media/multi-factor-authentication/hware.png) &#160;&#160;&#160;&#160;&#160;![Smart Card](./media/multi-factor-authentication/smart.png) &#160;&#160;&#160;&#160;&#160;![Virtual Smart Card](./media/multi-factor-authentication/vsmart.png) &#160;&#160;&#160;&#160;&#160;![Username and Password](./media/multi-factor-authentication/cert.png)</center>

## O que é a Azure Multi-Factor Authentication?

O Azure Multi-Factor Authentication é um método de verificar quem você é e que requer o uso de mais do que apenas um nome de usuário e uma senha. Ele fornece uma segunda camada de segurança para logons de usuário e transações.

O Azure Multi-Factor Authentication ajuda a proteger o acesso a dados e aplicativos enquanto atende à demanda dos usuários para um processo de logon simples. Ele fornece autenticação forte por meio de uma variedade de opções de fácil verificação — chamada telefônica, mensagem de texto, notificação de aplicativo móvel ou verificação de código e tokens OATH de terceiros.

Para obter uma visão geral de como funciona o Azure Multi-Factor Authentication, consulte o vídeo a seguir.


<center>[AZURE.VIDEO multi-factor-authentication-overview]</center>

##Por que usar o Azure Multi-Factor Authentication?

Hoje, mais do que nunca, as pessoas estão cada vez mais conectadas. Com os Smartphones, tablets, laptops e PCs, as pessoas têm várias opções diferentes para se conectar e se manterem conectadas. As pessoas podem acessar suas contas e aplicativos de qualquer lugar e isso significa que eles podem fazer mais trabalho e atender melhor a seus clientes.

O Azure Multi-Factor Authentication é uma solução fácil de usar, escalonável e confiável que fornece um segundo método de autenticação para que os usuários sempre estejam protegidos.

![Fácil de usar](./media/multi-factor-authentication/simple.png)| ![Escalonável](./media/multi-factor-authentication/scalable.png)| ![Sempre protegidos](./media/multi-factor-authentication/protected.png)|![Confiável](./media/multi-factor-authentication/reliable.png)
:-------------: | :-------------: | :-------------: | :-------------: |
**Fácil de usar**|**Escalonável**|**Sempre protegidos**|**Confiável**

- **Fácil de usar** - a Autenticação Multifator do Azure é simples de configurar e usar. A proteção adicional que acompanha o Azure Multi-Factor Authentication permite que os usuários usem e gerenciem os seus próprios dispositivos e, em muitos casos, ele pode ser configurado com apenas alguns cliques.
- **Escalonável** - a Autenticação Multifator do Azure utiliza a força da nuvem e integra-se ao seu AD local e aos aplicativos personalizados. Essa proteção ainda é estendida aos seus cenários essenciais de missão de alto volume.
- **Sempre protegidos** - o Azure Multi-Factor Authentication fornece autenticação forte usando os mais altos padrões do setor.
- **Confiável** - Garantimos 99,9% de disponibilidade do Azure Multi-Factor Authentication. O serviço é considerado indisponível quando não é possível receber ou processar solicitações de autenticação parao Multi-Factor Authentication.  

Para obter informações adicionais sobre por que usar o Azure Multi-Factor Authentication, consulte o vídeo a seguir.

<center>[AZURE.VIDEO windows-azure-multi-factor-authentication]</center>

**Recursos adicionais**

* [Para usuários](multi-factor-authentication-end-user.md)
* [Azure Multi-Factor Authentication no MSDN](https://msdn.microsoft.com/library/azure/dn249471.aspx) 

<!---HONumber=July15_HO1-->