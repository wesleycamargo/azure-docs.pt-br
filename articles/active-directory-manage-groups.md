<properties 
	pageTitle="Gerenciar grupos no AD do Azure" 
	description="Um tópico que explica como gerenciar grupos no AD do Azure." 
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

# Gerenciar grupos no AD do Azure

Um grupo é uma coleção de usuários e grupos que podem ser gerenciados como uma única unidade. Usuários e grupos que pertencem a um determinado grupo são denominados membros do grupo. Usar grupos pode simplificar a administração atribuindo um conjunto comum de permissões e direitos a várias contas simultaneamente, em vez de atribuir permissões e direitos individualmente a cada conta.

Agora no AD do Azure, você pode criar grupos de segurança.

Você pode usar grupos como uma maneira conveniente de atribuir acesso a aplicativos para usuários em casos nos quais você precise atribuir vários usuários ao mesmo aplicativo. Você também pode usar grupos quando configurar o gerenciamento de acesso de outros serviços online que controlem o acesso aos recursos, como por exemplo o SharePoint Online.

Se você tiver configurado a sincronização de diretórios, você pode ver os grupos que foram sincronizados por meio do seu Windows Server Active Directory local, que tem o valor “Active Directory Local” na propriedade “Originado de”. Você deve continuar a gerenciar esses grupos no Active Directory local; esses grupos não podem ser gerenciados nem excluídos no Portal de Gerenciamento do Azure.

Se você tem o Office 365, você pode ver os grupos de distribuição e grupos de segurança habilitados para email que foram criados e gerenciados no Centro de administração do Exchange no Office 365. Esses grupos têm o valor "Office 365" na propriedade "Originado de", e devem continuar sendo gerenciados no Centro de Administração do Exchange.

Você também pode criar grupos unificados por meio do Painel de acesso. Na guia Configuração, em Gerenciamento de grupos, defina a widget **Os usuários podem criar grupos de O365** como **Sim**. Se você tiver grupos unificados do Office 365 que forem criados no Painel de acesso ou no Office 365, esses grupos terão a propriedade "Originado de" definida como “Active Directory do Azure” e poderão ser gerenciados por meio do Painel de acesso.

Se você habilitou o gerenciamento de grupos por autoatendimento para seus usuários (para obter mais informações, consulte Gerenciamento de grupos por autoatendimento para usuários no AD do Azure, como um administrador de locatários você também pode gerenciar esses grupos por meio do Portal de Gerenciamento do Azure. Você pode adicionar e remover membros do grupo, adicionar e remover proprietários do grupo, editar propriedades do grupo e exibir o relatório de atividade de histórico dos grupos, que exibe a ação que foi executada dentro do grupo, quem executou a ação e o horário.

> [AZURE.NOTE]Para poder atribuir um grupo a um aplicativo, você deve estar usando o AD Premium do Azure. Se você tiver o AD Premium do Azure, você também poderá usar grupos para atribuir acesso a aplicativos SaaS que estão integrados ao AD do Azure. Para obter mais informações, consulte Atribuir acesso a um aplicativo SaaS no AD do Azure para um grupo.

## O que vem a seguir

- [Administrando o AD do Azure](active-directory-administer.md)
- [Criar ou editar usuários no AD do Azure](active-directory-create-users.md)
- [Gerenciar senhas no AD do Azure](active-directory-manage-passwords.md)

<!---HONumber=58-->