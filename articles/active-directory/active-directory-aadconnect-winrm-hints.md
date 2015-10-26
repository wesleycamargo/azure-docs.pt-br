<properties
	pageTitle="Azure AD Connect - dicas do Windows Remote Managed | Microsoft Azure"
	description="Dicas do Windows Remote Managed do Azure AD Connect para usar com o AD FS."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/13/2015"
	ms.author="billmath"/>

# AD do Azure Connect - dicas do Windows Remote Managed


Ao usar o Azure AD Connect para implantar Serviços de Federação do Active Directory ou o Proxy de Aplicativo Web, verifique as dicas de requisitos abaixo para garantir que a conectividade e a configuração sejam bem-sucedida:

- Se o servidor de destino for associado ao domínio, verifique se o Windows Remote Managed está habilitado
	* Em uma janela de comando PSH com privilégios elevados, use o comando "Enable-PSRemoting-force"

- Se o servidor de destino for um computador WAP não associado ao domínio, haverá alguns requisitos adicionais
	- No computador de destino (computador WAP):"

- Verifique se o serviço winrm (Windows Remote Management/WS-Management) está em execução por meio do snap-in de Serviços

- Em uma janela de comando PSH com privilégios elevados, use o comando "Enable-PSRemoting-force"
	- No computador que está executando o assistente (se o computador de destino não for associado ao domínio ou for de um domínio não confiável):

- Em uma janela de comando PSH com privilégios elevados, use o comando "Set-Item WSMan:\\localhost\\Client\\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate"
	- No Gerenciador de Servidores:
		- adicione o host DMZ WAP ao pool de computadores (gerenciador de servidores -> Gerenciar -> Adicionar Servidores... use a guia DNS)
		- Guia Todos os Servidores do Gerenciador de Servidores: clique com o botão direito do mouse no servidor WAP e escolha Gerenciar como... e digite credenciais locais (não de domínio) para o computador WAP
		- Para validar a conectividade PSH remota, na guia Todos os Servidores do Gerenciador de Servidores: clique com o botão direito do mouse no servidor WAP e escolha Windows PowerShell. Uma sessão remota do PSH deverá ser aberta para garantir que sessões remotas do PowerShell possam ser estabelecidas.

**Recursos adicionais**


* [Mais informações sobre permissões e contas do Azure AD Connect](active-directory-aadconnect-account-summary.md)
* [Instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
* [Azure AD Connect no MSDN](active-directory-aadconnect.md)

<!---HONumber=Oct15_HO3-->