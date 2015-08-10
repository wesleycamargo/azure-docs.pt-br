
<properties 
	pageTitle="Usar um grupo para gerenciar o acesso a aplicativos SaaS| Microsoft Azure" 
	description="Um tópico que explica como usar grupos no Azure AD Premium para atribuir acesso a aplicativos SaaS que estão integradas ao AD do Azure." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="swadhwa" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" identity
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="femila"/>


# Usar um grupo para gerenciar o acesso a aplicativos SaaS

Com o Azure AD Premium, você pode usar grupos para atribuir acesso a um aplicativo SaaS que esteja integrado ao AD do Azure. Por exemplo, se você quiser atribuir acesso ao departamento de marketing usar cinco aplicativos SaaS diferentes, você pode criar um grupo que contém os usuários no departamento de marketing e atribuir esse grupo a esses cinco aplicativos SaaS que são necessários para o departamento de marketing. Dessa forma, você pode poupar tempo gerenciando a associação no departamento de marketing em um único lugar. Os usuários são atribuídos ao aplicativo quando eles são adicionados como membros do grupo de marketing, e sua atribuição é removida do aplicativo quando eles são removidos do grupo de marketing.

Esse recurso pode ser usado com centenas de aplicativos que você adiciona na Galeria de aplicativos do Azure AD.

**Para atribuir acesso de um grupo a um aplicativo SaaS**


1. Abra um navegador de sua escolha e acesse o Portal de Gerenciamento do Azure. No Portal de Gerenciamento do Azure, localize a extensão do Active Directory na barra de navegação à esquerda. Na guia **Diretório**, clique no diretório no qual você deseja atribuir o acesso de um grupo a um aplicativo Saas.


2. Clique na guia aplicativos para seu diretório. Clique em um aplicativo que você adicionou na Galeria de aplicativos, clique na guia Usuários e Grupos.

3. Na guia Usuários e Grupos, em Iniciar com um campo, insira o nome do grupo ao qual você deseja atribuir acesso e clique na marca de seleção no canto superior direito. Você só precisa digitar a primeira parte do nome do grupo. Em seguida, clique no grupo para selecioná-lo, clique no botão **Atribuir acesso** e clique em **Sim**quando vir a mensagem de confirmação.


4. Você também pode ver quais usuários são atribuídos ao aplicativo, diretamente ou por associação em um grupo. Para fazer isso, altere **Mostrar lista suspensa de "Grupos"**para**"Todos usuários"**. A lista mostra usuários no diretório e se cada usuário está ou não atribuído ao aplicativo. A lista também mostra se os usuários atribuídos estão atribuídos diretamente ao aplicativo (tipo de atribuição mostrado como "Direto"), ou por meio de associação de grupo (tipo de atribuição mostrado como "Herdado".)


> [AZURE.NOTE]Você verá a guia Usuários e Grupos somente se tiver habilitado o Azure AD Premium.

Estes são alguns tópicos que fornecem informações adicionais sobre o Active Directory do Azure

* [Gerenciamento de acesso a recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)

* [O que é o Active Directory do Azure?](active-directory-whatis.md)

* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

<!---HONumber=July15_HO5-->