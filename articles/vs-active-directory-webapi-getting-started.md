<properties title="Getting Started with Active Directory Authentication" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />
  
<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="vs-getting-started" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Introdução](/documentation/articles/vs-active-directory-webapi-getting-started/)
> - [O que aconteceu](/documentation/articles/vs-active-directory-webapi-what-happened/)


##Introdução ao Azure Active Directory (Projetos de API da Web)

#####Exigir autenticação para acessar os controladores
 
Todos os controladores do seu projeto receberam o atributo **Authorize**. Este atributo exigirá que o usuário seja autenticado antes de acessar as APIs definidas por esses controladores. Para permitir que o controlador seja acessado anonimamente, remova este atributo do controlador. Se desejar definir as permissões em um nível mais granular, aplique o atributo a cada método que necessita de autorização em vez de aplicá-lo à classe do controlador.

[Saiba mais sobre o Azure Active Directory](http://azure.microsoft.com/services/active-directory/)
