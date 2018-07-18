---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: c1971e1eb3abc653ad8bdc6af772c699f8549019
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
## <a name="register-your-application"></a>Registre seu aplicativo
Para registrar seu aplicativo e adicionar suas informações de registro de aplicativo à sua solução, você tem duas opções:

### <a name="option-1-express-mode"></a>Opção 1: modo Expresso

Registre rapidamente seu aplicativo fazendo o seguinte:
1. Registre o aplicativo por meio do [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure)
2.  Insira um nome para o aplicativo e seu email
3.  Verifique se a opção Instalação Guiada está marcada
4.  Siga as instruções para adicionar uma URL de Redirecionamento ao aplicativo

### <a name="option-2-advanced-mode"></a>Opção 2: modo Avançado

Para registrar seu aplicativo e adicionar suas informações de registro de aplicativo à sua solução, faça o seguinte:

1. Acesse o [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/portal/register-app) para registrar um aplicativo
2. Insira um nome para o aplicativo e seu email 
3.  Verifique se a opção Instalação Guiada está desmarcada
4.  Clique em `Add Platform` e selecione `Web`
5.  Volte ao Visual Studio e, no Gerenciador de Soluções, selecione o projeto e examine a janela Propriedades (se uma janela Propriedades não for exibida, pressione F4)
6.  Altere SSL habilitado para `True`
7.  Copie a URL do SSL e adicione essa URL à lista de URLs de Redirecionamento na lista de URLs de Redirecionamento do Portal de Registro:<br/><br/>![Propriedades do projeto](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
8.  Adicione o seguinte em `web.config` localizado na pasta raiz, na seção `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
    ```

9. Substitua `ClientId` pela ID do Aplicativo que você acabou de registrar
10. Substitua `redirectUri` pela URL do SSL do projeto 

