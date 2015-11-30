
<properties
	pageTitle="Usar um grupo para gerenciar o acesso a aplicativos SaaS| Microsoft Azure"
	description="Como usar grupos no Azure Active Directory Premium ou Basic para atribuir acesso a aplicativos SaaS que estão integrados ao Active Directory do Azure."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/17/2015"
	ms.author="curtand"/>


# Usar um grupo para gerenciar o acesso a aplicativos SaaS

Com o AD do Azure (Active Directory do Azure) Premium, você pode usar grupos para atribuir acesso a um aplicativo SaaS que esteja integrado ao AD do Azure. Por exemplo, se você quiser atribuir acesso ao departamento de marketing usar cinco aplicativos SaaS diferentes, você pode criar um grupo que contém os usuários no departamento de marketing e atribuir esse grupo a esses cinco aplicativos SaaS que são necessários para o departamento de marketing. Dessa forma, você pode poupar tempo gerenciando a associação no departamento de marketing em um único lugar. Os usuários são atribuídos ao aplicativo quando eles são adicionados como membros do grupo de marketing, e sua atribuição é removida do aplicativo quando eles são removidos do grupo de marketing.

Esse recurso pode ser usado com centenas de aplicativos que você adiciona na Galeria de aplicativos do Azure AD.

**Para atribuir acesso de um grupo a um aplicativo SaaS**

1. Abra um navegador de sua escolha e acesse o portal do Azure. No portal do Azure, localize a extensão do Active Directory na barra de navegação à esquerda. Na guia **Diretório**, clique no diretório no qual você deseja atribuir o acesso de um grupo a um aplicativo Saas.


2. Clique na guia **Aplicativos** do diretório. Selecione um aplicativo que você adicionou na Galeria de Aplicativos e clique na guia **Usuários e Grupos**.

3. Na guia **Usuários e Grupos**, no campo **Iniciando com**, insira o nome do grupo ao qual você deseja atribuir acesso e clique na marca de seleção no canto superior direito. Você só precisa digitar a primeira parte do nome do grupo. Em seguida, clique no grupo para realçá-lo, clique no botão **Atribuir Acesso** e clique em **Sim** quando receber a mensagem de confirmação.


4. Você também pode ver quais usuários são atribuídos ao aplicativo, diretamente ou por associação em um grupo. Para fazer isso, altere **Mostrar lista suspensa de “Grupos”** para **“Todos os Usuários”**. A lista mostra usuários no diretório e se cada usuário está ou não atribuído ao aplicativo. A lista também mostra se os usuários atribuídos estão atribuídos diretamente ao aplicativo (tipo de atribuição mostrado como "Direto"), ou por meio de associação de grupo (tipo de atribuição mostrado como "Herdado".)


> [AZURE.NOTE]Você verá a guia Usuários e Grupos somente depois que tiver habilitado Azure AD Premium e Azure AD Basic.

Esses artigos fornecem mais informações sobre o Active Directory do Azure.

* [Gerenciamento de acesso a recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)

* [O que é o Active Directory do Azure?](active-directory-whatis.md)

* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

<!---HONumber=Nov15_HO4-->