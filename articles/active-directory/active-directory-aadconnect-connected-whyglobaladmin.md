<properties 
	pageTitle="Por que exigimos uma conta de administrador global do AD do Azure para configurar o Azure AD Connect" 
	description="Descrição de configurações personalizadas dos motivos pelos quais exigimos uma conta de administrador global." 
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

# Por que exigimos uma conta de administrador global do AD do Azure para configurar o Azure AD Connect

A tabela a seguir mostra os motivos pelos quais uma conta de administrador global do AD do Azure é necessária para configurar o Azure AD Connect.

Sob as seguintes condições | Descrição 
------------- | ------------- |
Para configurações expressas e atualização de DirSync | Habilitaremos a sincronização (se necessário) no diretório do AD do Azure e criaremos a conta do AD do Azure que será usada para as operações de sincronização em andamento (a conta de conector do AD do Azure). 
Para configurações personalizadas | Habilitaremos a sincronização no diretório do AD do Azure e criaremos a conta do AD do Azure que será usada para operações de sincronização em andamento (a conta de conector do AD do Azure). Para o logon único com a opção do AD FS, lemos e configuramos propriedades de federação no AD do Azure.



**Recursos adicionais**


* [Mais informações sobre permissões e contas do Azure AD Connect](active-directory-aadconnect-account-summary.md)
* [Instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
* [Azure AD Connect no MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx) 

<!---HONumber=July15_HO4-->