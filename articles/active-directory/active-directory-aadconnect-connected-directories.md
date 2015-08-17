<properties 
	pageTitle="Conectar seus diretórios com o Azure AD Connect" 
	description="Descrição de configurações personalizadas de diretórios conectados do Azure AD Connect." 
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



# Conectar seus diretórios com o Azure AD Connect

Para Configurações Personalizadas, não é necessária uma conta de administrador corporativa para se conectar às suas florestas do Active Directory. O assistente aceitará um domínio ou uma conta de usuário local. No entanto, a conta é usada como a conta do Conector do AD local, ou seja, é a conta que lê e grava as informações de diretório para sincronização.

Isso significa que você precisará atribuir permissões adicionais para habilitar os seguintes cenários:

Cenário |Permissão
------------- | ------------- |
Sincronização de senha| <li>Replicar alterações de diretório.</li> <li>Replicar todas as alterações de diretório.</li>
Implantação híbrida do Exchange|Consulte [Permissões e atributos de write-back de sincronização do Office 365 Exchange Hybrid AAD](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange).
Write-back de senha | <li>Alterar senha</li><li>Redefinir senha</li>
Write-back de usuário, grupo e dispositivo|Permissões de gravação para os objetos de diretório e os atributos nos quais você deseja fazer "write-back"
Logon único e AD FS| Permissões de administrador de domínio no domínio em que os servidores federados estão localizados.





**Recursos adicionais**

* [Mais informações sobre permissões e contas do Azure AD Connect](active-directory-aadconnect-account-summary.md)
* [Permissões para sincronização de senha](https://msdn.microsoft.com/library/azure/dn757602.aspx#psynch)
* [Permissões para Exchange híbrido](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange)
* [Permissões para write-back de senha](https://msdn.microsoft.com/library/azure/dn757602.aspx#pwriteback)
* [Instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
* [Azure AD Connect no MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx)
 

<!---HONumber=August15_HO6-->