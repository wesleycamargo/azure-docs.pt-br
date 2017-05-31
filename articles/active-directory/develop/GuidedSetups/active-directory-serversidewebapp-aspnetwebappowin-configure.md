---
title: "Introdução ao servidor Web ASP.NET no Azure AD v2 – Configuração | Microsoft Docs"
description: "Implementando a opção Entrar com uma Conta da Microsoft em uma solução ASP.NET com um aplicativo tradicional baseado em navegador da Web usando o padrão OpenID Connect"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6f50ced708cb79a39e190657e4720c515c09990f
ms.contentlocale: pt-br


---

## <a name="create-an-application-express"></a>Criar um aplicativo (Expresso)
Agora você precisa registrar seu aplicativo no *Portal de Registro de Aplicativos da Microsoft*:
1. Registre o aplicativo por meio do [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure)
2.    Insira um nome para o aplicativo e seu email
3.    Verifique se a opção Instalação Guiada está marcada
4.    Siga as instruções para adicionar uma URL de Redirecionamento ao aplicativo

## <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Adicionar as informações de registro do aplicativo à sua solução (Avançado)
Agora você precisa registrar seu aplicativo no *Portal de Registro de Aplicativos da Microsoft*:
1. Acesse o [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/portal/register-app) para registrar um aplicativo
2. Insira um nome para o aplicativo e seu email 
3.    Verifique se a opção Instalação Guiada está desmarcada
4.    Clique em `Add Platforms` e selecione `Web`
5.    Volte ao Visual Studio e, no Gerenciador de Soluções, selecione o projeto e examine a janela Propriedades (se uma janela Propriedades não for exibida, pressione F4)
6.    Altere Habilitado para SSL para `True`:<br/><br/>![Propriedades do projeto](media/active-directory-serversidewebapp-aspnetwebappowin-configure/vsprojectproperties.png)<br />

7.    Copie a URL do SSL e adicione essa URL à lista de URLs de Redirecionamento na lista do Portal de Registro de URLs de Redirecionamento
8.    Adicione o seguinte em `web.config` localizado na pasta raiz, na seção `configuration\appSettings`:

```xml
<add key="ClientId" value="Enter_the_Application_Id_here" />
<add key="redirectUri" value="Enter_the_Redirect_URL_here" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
9. Substitua `ClientId` pela ID do Aplicativo que você acabou de registrar
10. Substitua `redirectUri` pela URL do SSL do projeto 
<!-- End Docs -->

> Observação
> ### <a name="restricting-users-from-only-one-organization-to-sign-in-to-your-application"></a>Restringindo os usuários a somente uma organização para entrar em seu aplicativo
> Por padrão, contas pessoais (incluindo outlook.com, live.com e outras), bem como contas corporativas ou de estudante de qualquer empresa ou organização que foi integrada ao Azure Active Directory, podem entrar no aplicativo. Se você desejar que o aplicativo aceite conexões somente de uma organização, substitua o parâmetro `Tenant` em `web.config` de `Common` para o nome de locatário da organização – por exemplo, `contoso.onmicrosoft.com`. Depois disso, altere o argumento *ValidateIssuer* na Classe de Inicialização OWIN para `true`.
Para permitir usuários apenas de uma lista de organizações específicas, defina `ValidateIssuer` como `true` e use o parâmetro `ValidIssuers` para especificar uma lista de organizações.
Outra opção é implementar um método personalizado para validar os emissores usando `IssuerValidator parameter`. Para obter mais informações sobre `TokenValidationParameters`, consulte [este](https://msdn.microsoft.com/en-us/library/system.identitymodel.tokens.tokenvalidationparameters(v=vs.114).aspx) artigo do MSDN.


