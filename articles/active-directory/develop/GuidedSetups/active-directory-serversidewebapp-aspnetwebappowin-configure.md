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
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: ad4160abfef748033eeb84a2f07a37124ce3b4b1
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
<!-- Workaround for Docs conversion bug -->
<ol start="9">
<li>
Substitua `ClientId` pela ID do Aplicativo que você acabou de registrar
</li>
<li>
Substitua `redirectUri` pela URL do SSL do projeto
</li>
</ol>
<!-- End Docs -->

