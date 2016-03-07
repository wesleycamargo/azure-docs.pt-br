<properties
	pageTitle="Grupos dedicados do Active Directory do Azure | Microsoft Azure"
	description="Visão geral do funcionamento e da criação de grupos dedicados no Active Directory do Azure."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""
	/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="curtand"/>

# Grupos dedicados no Active Directory do Azure

No Active Directory do Azure, grupos dedicados são criados automaticamente e associação de grupo para os grupos dedicados também é automática. Você não pode adicionar ou remover membros e grupos dedicados por meio do portal do Azure, cmdlets do Windows PowerShell ou de maneira programática. Para habilitar grupos dedicados, no Portal do Azure, na guia Configurar, defina a **opção Habilitar Grupos Dedicados como Sim**.

Quando a opção Habilitar Grupos Dedicados é definida como **Sim**, você ainda pode habilitar o diretório para criar automaticamente o grupo dedicado Todos os Usuários definindo a **opção Habilitar o Grupo “Todos os Usuários”** como **Sim**. Você pode também editar o nome desse grupo dedicado digitando o **Nome de Exibição para o campo do Grupo “Todos os Usuários”**.

O grupo dedicado Todos os Usuários pode ser útil se você deseja atribuir as mesmas permissões a todos os usuários no diretório. Por exemplo, você pode conceder a todos os usuários no seu acesso ao diretório para um aplicativo SaaS atribuindo acesso para o grupo dedicado de Todos os Usuários a esse aplicativo.

Observe que o grupo dedicado "Todos os Usuários" inclui todos os usuários no diretório e isso inclui os convidados e usuários externos. Se você precisar de um grupo que exclui os usuários externos, é possível criar um grupo com uma regra dinâmica como

(user.userPrincipalName -notContains "#EXT#@")

Para um grupo que exclui todos os Convidados, use uma regra como

(user.userType -ne "Guest")

Este artigo explica mais sobre como criar uma regra para gerenciar os membros em um grupo no Azure Active Directory:

* [Criação de uma regra simples para configurar associações dinâmicas para um grupo](active-directory-accessmanagement-simplerulegroup.md)


Esses artigos fornecem mais informações sobre o Active Directory do Azure.

* [Gerenciamento de acesso a recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
* [O que é o Active Directory do Azure?](active-directory-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0224_2016-->