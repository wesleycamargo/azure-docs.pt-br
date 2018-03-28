---
title: Introdução ao Azure AD em projetos do .NET MVC do Visual Studio | Microsoft Docs
description: Como começar a usar o Azure Active Directory em projetos do .NET MVC após a conexão ou criação de um AD do Azure usando os serviços conectados do Visual Studio
services: active-directory
documentationcenter: ''
author: kraigb
manager: ghogen
editor: ''
ms.assetid: 1c8b6a58-5144-4965-a905-625b9ee7b22b
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: 07fa4655d9e7ad74cae33391d55c7c9be1d446a6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Introdução ao Active Directory do Azure (Projetos do ASP.NET MVC)

> [!div class="op_single_selector"]
> - [Introdução](vs-active-directory-dotnet-getting-started.md)
> - [O que aconteceu](vs-active-directory-dotnet-what-happened.md)

Este artigo fornece orientações adicionais depois de você ter adicionado o Active Directory a um projeto do ASP.NET MVC por meio do comando **Projeto > Serviços Conectados** do Visual Studio. Se você ainda não adicionou o serviço ao seu projeto, você pode fazer isso a qualquer momento.

Consulte [O que aconteceu com meu projeto do MVC?](vs-active-directory-dotnet-what-happened.md) para as alterações feitas ao seu projeto ao adicionar o serviço conectado.

## <a name="requiring-authentication-to-access-controllers"></a>Exigir autenticação para acessar os controladores

Todos os controladores em seu projeto foram marcados com o `[Authorize]` atributo. Este atributo exige que o usuário seja autenticado antes de acessar esses controladores. Para permitir que o controlador seja acessado anonimamente, remova este atributo do controlador. Se desejar definir as permissões em um nível mais granular, aplique o atributo a cada método que necessita de autorização em vez de aplicá-lo à classe do controlador.

## <a name="adding-signin--signout-controls"></a>Adicionar controles de SignIn / SignOut

Para adicionar os controles SignIn/SignOut ao modo de exibição, você pode usar a `_LoginPartial.cshtml` exibição parcial para adicionar a funcionalidade a um dos seus modos de exibição. Aqui está um exemplo da funcionalidade adicionada com o padrão de `_Layout.cshtml` exibição. (Observe o último elemento no div com classe navbar-collapse):

```html
<!DOCTYPE html>
 <html>
 <head>
     <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>@ViewBag.Title - My ASP.NET Application</title>
    @Styles.Render("~/Content/css")
    @Scripts.Render("~/bundles/modernizr")
</head>
<body>
    <div class="navbar navbar-inverse navbar-fixed-top">
        <div class="container">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
            </div>
            <div class="navbar-collapse collapse">
                <ul class="nav navbar-nav">
                    <li>@Html.ActionLink("Home", "Index", "Home")</li>
                    <li>@Html.ActionLink("About", "About", "Home")</li>
                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
                </ul>
                @Html.Partial("_LoginPartial")
            </div>
        </div>
    </div>
    <div class="container body-content">
        @RenderBody() 
        <hr />
        <footer>
            <p>&copy; @DateTime.Now.Year - My ASP.NET Application</p>
        </footer>
    </div>
    @Scripts.Render("~/bundles/jquery")
    @Scripts.Render("~/bundles/bootstrap")
    @RenderSection("scripts", required: false)
</body>
</html>
```

## <a name="next-steps"></a>Próximas etapas

- [Cenários de autenticação do Azure Active Directory](active-directory-authentication-scenarios.md)
- [Adicionar entrada com a Microsoft para um aplicativo Web ASP.NET](guidedsetups/active-directory-aspnetwebapp-v1.md)
