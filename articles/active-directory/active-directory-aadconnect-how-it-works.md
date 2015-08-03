<properties 
	pageTitle="Como o Azure AD Connect funciona" 
	description="Saiba como o Azure AD Connect funciona" 
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
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Como o Azure AD Connect funciona

<div class="dev-center-tutorial-selector sublanding">
<a href="../active-directory-aadconnect/" title="O que é">O que é</a> <a href="../active-directory-aadconnect-how-it-works/" title="Como funciona" class="current">Como funciona</a> <a href="../active-directory-aadconnect-get-started/" title="Introdução">Introdução</a> <a href="../active-directory-aadconnect-whats-next/" title="A seguir">A seguir</a> <a href="../active-directory-aadconnect-learn-more/" title="Saiba mais">Saiba mais</a>
</div>
O Azure Active Directory Connect é composto de três partes principais. Elas são: os serviços de sincronização, a parte opcional de Serviços de Federação do Active Directory e a parte de monitoramento que é feita usando o [Azure AD Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx).


<center>![Azure AD Connect Stack](./media/active-directory-aadconnect-how-it-works/AADConnectStack2.png) </center>

- Sincronização - Esta parte é composta de componentes e funcionalidades lançados anteriormente como Dirsync e AAD Sync. Essa é a parte responsável pela criação de usuários e grupos. Ela também é responsável por garantir que as informações sobre usuários e grupos em seu ambiente local correspondam às da nuvem.
- AD FS - Esta é uma parte opcional do Azure AD Connect e pode ser usada para configurar um ambiente híbrido usando uma infraestrutura de AD FS local. Esta parte pode ser usada pelas organizações para abordar implantações complexas que incluem itens como SSO de ingresso no domínio, imposição de política de logon de AD e cartão inteligente ou MFA de terceiros. Para obter informações adicionais sobre como configurar SSO, consulte [DirSync com logon único](https://msdn.microsoft.com/library/azure/dn441213.aspx).
- Monitoramento de integridade - Para implantações complexas usando AD FS, o Azure AD Connect Health pode fornecer monitoramento robusto de seus servidores de federação e uma localização central no portal do Azure para exibir essa atividade. Para obter informações adicionais, consulte [Azure Active Directory Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx).


## Componentes de suporte do Azure AD Connect

A seguir, uma lista de pré-requisitos e componentes de suporte que o Azure AD Connect irá instalar no servidor em que você instalou o Azure AD Connect. Esta lista é para uma instalação básica do Express. Se você optar por usar um SQL Server diferente na página de serviços de sincronização de instalação, os componentes do SQL Server 2012 listados abaixo não serão instalados.

- Azure AD Connect Azure AD Connector
- Utilitários de linha de comando do Microsoft SQL Server 2012
- Microsoft SQL Server 2012 Native Client
- LocalDB do Microsoft SQL Server 2012 Express
- Módulo do Active Directory do Azure para Windows PowerShell
- Assistente de Conexão do Microsoft Online Services para profissionais de TI
- Pacote de redistribuição de Microsoft Visual C++ 2013




 

<!---HONumber=July15_HO4-->