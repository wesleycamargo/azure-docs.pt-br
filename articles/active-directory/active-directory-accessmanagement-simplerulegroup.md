<properties
	pageTitle="Criação de uma regra simples para configurar associações dinâmicas para um grupo | Microsoft Azure"
	description="Explica como criar uma regra simples para configurar associações dinâmicas para um grupo"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/09/2015" 
	ms.author="femila"/>


# Criação de uma regra simples para configurar associações dinâmicas para um grupo

**Para habilitar a associação dinâmica de um determinado grupo, execute as seguintes etapas:**

1. No Portal de gerenciamento do Azure, sob a guia **Grupos**, selecione o grupo que você deseja editar, e, em seguida, na guia **Configurar** esse grupo, defina a chave **Habilitar Associações Dinâmicas** para **Sim**.


2. Agora você pode configurar uma única regra simples para o grupo que controlará a associação dinâmica como para esse grupo de funções. Verifique se o botão de opção **Adicionar usuários onde** está marcado e, em seguida, selecione uma propriedade de usuário no menu suspenso (por exemplo, departamento, jobTitle, etc.)

3. Em seguida, selecione uma condição (Not Equals, Equals, Not Starts With, Starts With, Not Contains, Contains, Not Match, Match) e finalmente especificar um valor para a propriedade do usuário selecionado. Por exemplo, se um grupo é atribuído a um aplicativo de SaaS e você habilita associações dinâmicas a este grupo, definindo uma regra na qual **Adicionar usuários em que** é definido como o jobTitle that Equals(-eq)Sales Rep, todos os usuários no diretório do AD do Azure, cujos cargos são definidos como representante de vendas, terão acesso a esse aplicativo SaaS.

Estes são alguns tópicos que fornecem informações adicionais sobre o Active Directory do Azure:

* [Gerenciamento de acesso a recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)
* [O que é o Active Directory do Azure?](active-directory-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

<!---HONumber=Oct15_HO3-->