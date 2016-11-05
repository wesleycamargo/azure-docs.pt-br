---
title: Introdução ao Active Directory do Azure e aos serviços conectados do Visual Studio (projetos da API Web) | Microsoft Docs
description: Como começar a usar o Active Directory do Azure em projetos da API Web após a conexão ou criação de um AD do Azure usando os serviços conectados do Visual Studio
services: active-directory
documentationcenter: ''
author: TomArcher
manager: douge
editor: ''

ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: tarcher

---
# Introdução ao Active Directory do Azure e aos serviços conectados do Visual Studio (projetos da API Web)
> [!div class="op_single_selector"]
> * [Introdução](vs-active-directory-webapi-getting-started.md)
> * [O que aconteceu](vs-active-directory-webapi-what-happened.md)
> 
> 

## Exigir autenticação para acessar os controladores
Todos os controladores em seu projeto foram marcados com o atributo **Autorizar**. Este atributo exigirá que o usuário seja autenticado antes de acessar as APIs definidas por esses controladores. Para permitir que o controlador seja acessado anonimamente, remova este atributo do controlador. Se desejar definir as permissões em um nível mais granular, aplique o atributo a cada método que necessita de autorização em vez de aplicá-lo à classe do controlador.

[Saiba mais sobre o Active Directory do Azure](https://azure.microsoft.com/services/active-directory/)

<!---HONumber=AcomDC_0817_2016-->