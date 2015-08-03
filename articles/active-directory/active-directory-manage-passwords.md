<properties 
	pageTitle="Gerenciar senhas no AD do Azure" 
	description="Um tópico que explica como gerenciar senhas no AD do Azure." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="Justinha"/>

# Gerenciar senhas no AD do Azure

Se você for um administrador, poderá redefinir a senha do usuário no Azure por meio do portal clássico do Azure. Clique no nome do diretório e na página Usuários, clique no nome do usuário e na parte inferior do portal, clique em **Redefinir senha**.

O restante deste tópico aborda o conjunto completo de recursos de gerenciamento de senha para o qual o Active Directory do Azure dá suporte, incluindo:

- A alteração de **senha por autoatendimento** permite que os usuários finais ou os administradores alterem suas senhas expiradas ou não expiradas sem chamar um administrador ou o suporte técnico em busca de suporte.
- A redefinição de **senha por autoatendimento** permite que os usuários finais ou os administradores redefinam suas senhas automaticamente, sem chamar um administrador ou o suporte técnico em busca de suporte. A redefinição de senha por autoatendimento exige o AD Premium ou Basic do Azure. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).
- A **Redefinição de senha iniciada pelo administrador** permite que um administrador redefina a senha de um usuário final ou de outro administrador de dentro do Portal de Gerenciamento do Azure.
- **Relatórios de atividade de gerenciamento de senha** fornecem aos administradores percepções sobre as atividades de registro e de redefinição de senha que ocorrem em suas organizações. 
- O **Write-back de senha** permite o gerenciamento de senhas locais na nuvem, de modo que todos os cenários acima podem ser executados por, ou em nome de, usuários federados ou sincronizados por senha. O write-back de senha requer o AD Premium do Azure. Para obter mais informações, consulte [Introdução ao Azure Active Directory Premium](active-directory-get-started-premium.md).

> [AZURE.NOTE]O AD Premium do Azure está disponível para clientes chineses usando a instância do AD do Azure que abrange todo o mundo. O Azure AD Premium não tem suporte atualmente no serviço Microsoft Azure operado pela 21Vianet na China. Para obter mais informações, entre em contato conosco no [Fórum do Active Directory do Azure](http://feedback.azure.com/forums/169401-azure-active-directory).

Use os links a seguir para ir para a documentação na qual você está mais interessado:

- [Visão geral: gerenciamento de senhas no AD do Azure](https://msdn.microsoft.com/library/azure/dn683880.aspx)
- [Redefinição de senha por autoatendimento no AD do Azure: como habilitar, configurar e testar a redefinição de senha por autoatendimento](https://msdn.microsoft.com/library/azure/dn683881.aspx)
- [Redefinição de senha por autoatendimento no AD do Azure: como personalizar a redefinição de senha para atender às suas necessidades](https://msdn.microsoft.com/library/azure/dn688249.aspx)
- [Redefinição de senha por autoatendimento no AD do Azure: práticas recomendadas de implantação e gerenciamento](https://msdn.microsoft.com/library/azure/dn903643.aspx)
- [Relatórios de gerenciamento de senhas no AD do Azure: como exibir a atividade de gerenciamento de senha em seu locatário](https://msdn.microsoft.com/library/azure/dn903641.aspx)
- [Write-back de senha: como configurar o AD do Azure para gerenciar senhas locais](https://msdn.microsoft.com/library/azure/dn903642.aspx)
- [Perguntas Frequentes/solução de problemas do AD do Azure para gerenciamento de senhas](https://msdn.microsoft.com/library/azure/dn683878.aspx)

## O que vem a seguir

- [Administrando o AD do Azure](active-directory-administer.md)
- [Criar ou editar usuários no AD do Azure](active-directory-create-users.md)
- [Gerenciar grupos no AD do Azure](active-directory-manage-groups.md)
 

<!---HONumber=July15_HO4-->