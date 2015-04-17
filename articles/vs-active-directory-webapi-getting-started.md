<properties 
	pageTitle="" 
	description="Informações sobre a introdução ao assistente do Active Directory do Azure (projetos de API da Web)" 
	services="active-directory" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

# Introdução ao Azure Active Directory (Projetos de API da Web)

> [AZURE.SELECTOR]
> - [Introdução](vs-active-directory-webapi-getting-started.md)
> - [O que aconteceu](vs-active-directory-webapi-what-happened.md)

#####Exigir autenticação para acessar os controladores
 
Todos os controladores do seu projeto receberam o atributo **Authorize**.  Este atributo exigirá que o usuário seja autenticado antes de acessar as APIs definidas por esses controladores.  Para permitir que o controlador seja acessado anonimamente, remova este atributo do controlador.  Se desejar definir as permissões em um nível mais granular, aplique o atributo a cada método que necessita de autorização em vez de aplicá-lo à classe do controlador.

[Saiba mais sobre o Active Directory do Azure](http://azure.microsoft.com/services/active-directory/)

<!--HONumber=49-->