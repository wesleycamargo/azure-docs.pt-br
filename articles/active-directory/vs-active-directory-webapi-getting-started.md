<properties 
	pageTitle="Introdução ao Active Directory do Azure e aos serviços conectados do Visual Studio (projetos da API Web) |Microsoft Azure" 
	description="Como começar a usar o Active Directory do Azure em projetos da API Web após a conexão ou criação de um AD do Azure usando os serviços conectados do Visual Studio" 
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
	ms.date="09/03/2015" 
	ms.author="patshea123"/>

# Introdução ao Active Directory do Azure e aos serviços conectados do Visual Studio (projetos da API Web)

> [AZURE.SELECTOR]
> - [Getting Started](vs-active-directory-webapi-getting-started.md)
> - [What Happened](vs-active-directory-webapi-what-happened.md)

##Exigir autenticação para acessar os controladores
 
Todos os controladores em seu projeto foram marcados com o atributo **Autorizar**. Este atributo exigirá que o usuário seja autenticado antes de acessar as APIs definidas por esses controladores. Para permitir que o controlador seja acessado anonimamente, remova este atributo do controlador. Se desejar definir as permissões em um nível mais granular, aplique o atributo a cada método que necessita de autorização em vez de aplicá-lo à classe do controlador.

[Saiba mais sobre o Active Directory do Azure](http://azure.microsoft.com/services/active-directory/)
 

<!---HONumber=Sept15_HO2-->