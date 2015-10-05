<properties 
	pageTitle="Grupos dedicados do Active Directory do Azure | Microsoft Azure" 
	description="Visão geral dos grupos dedicados no AD do Azure e como eles são criados." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="swadhwa" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="femila"/>

# Grupos dedicados no Active Directory do Azure

No Active Directory do Azure, grupos dedicados são criados automaticamente e associação de grupo para os grupos dedicados também é automática. Você não pode adicionar ou remover membros e grupos dedicados por meio do portal de gerenciamento do Azure, cmdlets do Windows PowerShell ou por meio de programa. Para habilitar grupos dedicados, no Portal de gerenciamento do Azure, na guia Configurar, defina a opção Habilitar grupos dedicados como Sim.

Na versão atual de demonstração pública, quando a opção Habilitar grupos dedicados é definida como Sim, você ainda pode habilitar o diretório para criar automaticamente o grupo dedicado de todos os usuários, definindo Habilitar o Grupo "Todos os Usuários" como Sim. Você pode também editar o nome desse grupo dedicado digitando o nome de exibição para o campo de grupo "Todos os Usuários".

O grupo dedicado Todos os Usuários pode ser útil se você deseja atribuir as mesmas permissões a todos os usuários no diretório. Por exemplo, você pode conceder a todos os usuários no seu acesso ao diretório para um aplicativo SaaS atribuindo acesso para o grupo dedicado de Todos os Usuários a esse aplicativo.

Estes são alguns tópicos que fornecem informações adicionais sobre o Active Directory do Azure

* [Gerenciando acesso a recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)
* [O que é o Active Directory do Azure?](active-directory-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

<!---HONumber=Sept15_HO4-->