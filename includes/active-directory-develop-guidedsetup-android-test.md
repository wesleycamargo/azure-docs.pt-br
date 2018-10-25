---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 991709ee635872e33dc89dcededc7f6ac3b28ea3
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48842971"
---
## <a name="test-your-app"></a>Testar seu aplicativo

1. Execute seu código em seu dispositivo/emulador.

2. Experimente entrar com uma conta do Azure Active Directory (conta corporativa ou de estudante) ou uma conta Microsoft (live.com, outlook.com). 

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

>[!NOTE]
>Talvez seja necessário dar um consentimento adicional se você alterar o registro do aplicativo.

<!--end-collapse-->

[!INCLUDE [Help and support](active-directory-develop-help-support-include.md)]
