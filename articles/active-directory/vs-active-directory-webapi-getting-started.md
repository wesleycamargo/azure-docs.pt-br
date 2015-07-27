<properties 
	pageTitle="" 
	description="Informações sobre a introdução ao assistente do Active Directory do Azure (projetos de API da Web)" 
	services="active-directory" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="patshea123"/>

# Introdução ao Azure Active Directory (Projetos de API da Web)

> [AZURE.SELECTOR]
> - [Getting Started](vs-active-directory-webapi-getting-started.md)
> - [What Happened](vs-active-directory-webapi-what-happened.md)

#####Exigir autenticação para acessar os controladores
 
Todos os controladores em seu projeto foram marcados com o atributo **Autorizar**. Este atributo exigirá que o usuário seja autenticado antes de acessar as APIs definidas por esses controladores. Para permitir que o controlador seja acessado anonimamente, remova este atributo do controlador. Se desejar definir as permissões em um nível mais granular, aplique o atributo a cada método que necessita de autorização em vez de aplicá-lo à classe do controlador.

[Saiba mais sobre o Active Directory do Azure](http://azure.microsoft.com/services/active-directory/)
 

<!---HONumber=July15_HO3-->