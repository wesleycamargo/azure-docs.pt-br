<properties 
	pageTitle="Habilitando seu diretório para gerenciamento híbrido com o Azure AD Connect." 
	description="Este é o Azure AD Connect que descreve o que ele é e por que usá-lo." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/22/2015" 
	ms.author="billmath"/>

# Habilitando seu diretório para gerenciamento híbrido com o Azure AD Connect

<div class="dev-center-tutorial-selector sublanding">
<a href="../active-directory-aadconnect/" title="O que é" class="current">O que é</a> <a href="../active-directory-aadconnect-how-it-works/" title="Como ele funciona">Como ele funciona</a> <a href="../active-directory-aadconnect-get-started/" title="Introdução">Introdução</a> <a href="../active-directory-aadconnect-whats-next/" title="Próximos passos">Próximos passos</a> <a href="../active-directory-aadconnect-learn-more/" title="Saiba mais">Saiba mais</a>
</div>


Hoje, os usuários desejam poder acessar aplicativos no local e na nuvem. Eles querem poder fazer isso de qualquer dispositivo, seja de um laptop, smartphone ou tablet. Para isso, você e sua organização precisam estar aptos a fornecer uma maneira para que os usuários acessem esses aplicativos. No entanto, migrar integralmente para a nuvem nem sempre será uma opção.

<center>![O que é o Azure AD Connect](./media/active-directory-aadconnect/arch.png)</center>

Com a introdução do Azure Active Directory Connect, fornecer acesso a esses aplicativos e migrar para a nuvem nunca foi tão fácil. O Azure AD Connect proporciona os seguintes benefícios:

- Os usuários podem entrar com a mesma identidade na nuvem e no local. Eles não precisam se lembrar de várias senhas ou de contas e os administradores não precisam se preocupar com a sobrecarga adicional que pode ser introduzida pelas várias contas.
- Uma única ferramenta e uma experiência orientada para conectar seus diretórios locais ao Active Directory do Azure. Depois de instalado, o assistente implanta e configura todos os componentes necessários para que a integração do seu diretório funcione, incluindo serviços de sincronização, sincronização de senhas ou AD FS e pré-requisitos, como o módulo PowerShell do AD do Azure.



<center>![O que é o Azure AD Connect](./media/active-directory-aadconnect/azuread.png)</center>




## Por que usar o Azure AD Connect 

A integração de seus diretórios locais ao AD do Azure torna os usuários mais produtivos ao fornecer uma identidade comum para acesso aos recursos na nuvem e locais. Com essa integração, os usuários e as organizações podem se beneficiar do seguinte:
	
* As organizações podem fornecer aos usuários uma identidade híbrida comum para serviços baseados em nuvem ou locais, aproveitando o Active Directory do Windows Server e, em seguida, conectando-se ao Active Directory do Azure. 
* Os administradores podem fornecer acesso condicional com base no recurso do aplicativo, na identidade de usuário e dispositivo, no local de rede e na autenticação multifator.
* Os usuários podem aproveitar sua identidade comum por meio das contas no AD do Azure para o Office 365, o Intune, os aplicativos SaaS e os aplicativos de terceiros.  
* Os desenvolvedores podem criar aplicativos que aproveitam o modelo de identidade comum, integrando aplicativos ao Active Directory local ou o Azure para aplicativos baseados em nuvem.

O Azure Connect AD facilita essa integração e simplifica o gerenciamento da infraestrutura de identidade local e na nuvem.



----------------------------------------------------------------------------------------------------------
## Baixar o Azure AD Connect

Para começar a usar o Azure AD Connect, você pode baixar a versão mais recente usando o seguinte: [Baixar o Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)

----------------------------------------------------------------------------------------------------------








 

<!---HONumber=62-->