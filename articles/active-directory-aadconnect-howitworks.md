<properties 
	pageTitle="Como o Azure AD Connect funciona" 
	description="Saiba como o Azure AD Connect funciona" 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="billmath"/>

# Como o Azure AD Connect funciona

O Azure Active Directory Connect é constituído de três partes principais que compõem o Azure Active Directory Connect. São elas os serviços de sincronização, a parte opcional de serviços de federação do Active Directory e a parte de monitoramento que é feita usando o Azure AD Connect Health.


<center>![Azure AD Connect Stack](./media/active-directory-aadconnect-howitworks/AADConnectStack.png) </center>

- Sincronização - Esta parte é composta de componentes e funcionalidades lançados anteriormente como Dirsync e AAD Sync. Essa é a parte responsável pela criação de usuários e grupos. Também é responsável por garantir que as informações sobre usuários e grupos em seu ambiente local correspondam às da nuvem.
- AD FS - Esta é uma parte opcional do Azure AD Connect e pode ser usada para configurar um ambiente híbrido usando uma infraestrutura de AD FS local. Esta parte pode ser usada pela organização para abordar implantações complexas que incluem coisas como SSO de ingresso em domínio, imposição de política de logon de AD e cartão inteligente ou MFA de terceiros. Para obter informações adicionais sobre como configurar SSO, consulte [DirSync com logon único](https://msdn.microsoft.com/library/azure/dn441213.aspx).
- Monitoramento de integridade - Para implantações complexas usando AD FS, o Azure AD Connect Health pode fornecer monitoramento robusto de seus servidores de federação e uma localização central no portal do Azure para exibir essa atividade. Para obter informações adicionais, consulte [Azure Active Directory Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx).






**Recursos adicionais**

* [Usar sua infraestrutura de identidade local na nuvem](active-directory-aadconnect.md)
* [Introdução ao Azure AD Connect](active-directory-aadconnect-getstarted.md)
* [Gerenciar o Azure AD Connect](active-directory-aadconnect-manage.md)
* [Azure AD Connect no MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx)

<!---HONumber=58-->