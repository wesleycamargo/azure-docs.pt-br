---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: c8e5e4f826d7835a1fd38d1db5bfeab19b679b30
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203672"
---
## <a name="test-your-app"></a>Testar seu aplicativo

1. Execute seu código em seu dispositivo/emulador.
2. Tente entrar com uma conta do Azure Active Directory (conta corporativa ou de estudante) ou uma conta da Microsoft (live.com, outlook.com). 

    ![Teste seu aplicativo](media/active-directory-develop-guidedsetup-android-test/mainwindow.png)
    <br/><br/>
    ![Nome de usuário e senha](media/active-directory-develop-guidedsetup-android-test/usernameandpassword.png)

### <a name="consent-to-your-app"></a>Consentimento para seu aplicativo

Na primeira vez que um usuário entrar no seu aplicativo, ele deverá consentir com as permissões exigidas pelo seu aplicativo, conforme mostrado aqui: 

![Forneça sua autorização para acesso do aplicativo](media/active-directory-develop-guidedsetup-android-test/androidconsent.png)

### <a name="success"></a>Sucesso!

Após entrar e consentir, o aplicativo exibirá a resposta da API do Microsoft Graph. Essa chamada específica é realizada para o ponto de extremidade **/me** e retorna o [perfil do usuário](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_get). Para obter uma lista de outros pontos de extremidade do Microsoft Graph, confira [Documentação para desenvolvedores da API do Microsoft Graph](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries).

<!--start-collapse-->
### <a name="scopes-and-delegated-permissions"></a>Escopos e permissões delegadas

A API do Microsoft Graph requer o escopo *User.Read* para ler o perfil do usuário. Esse escopo está automaticamente em todo aplicativo registrado no Portal de Registro de Aplicativo. Outras APIs exigirão escopos adicionais. Por exemplo, a API do Microsoft Graph requer o escopo *Calendars.Read* para listar os calendários do usuário.

Para acessar os calendários do usuário, adicione a permissão delegada *Calendars.Read* às informações de registro de aplicativo. Em seguida, adicione o escopo *Calendars.Read* à chamada `acquireTokenSilent`. 

> [!NOTE]
> Talvez seja necessário dar um consentimento adicional se você alterar o registro do aplicativo.

<!--end-collapse-->

[!INCLUDE [Help and support](active-directory-develop-help-support-include.md)]
