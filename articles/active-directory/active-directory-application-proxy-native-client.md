---
title: Publicar aplicativos cliente nativos - Azure AD | Microsoft Docs
description: Aborda como habilitar aplicativos clientes nativos para se comunicar com o Conector de Proxy do aplicativo Azure AD para fornecer acesso remoto seguro aos seus aplicativos locais.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: f0cae145-e346-4126-948f-3f699747b96e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: bdaa5af6ff5331bc310499586615b48a864c3c5e
ms.contentlocale: pt-br
ms.lasthandoff: 08/24/2017

---

# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>Como habilitar aplicativos clientes nativos para interagir com aplicativos de proxy

Além dos aplicativos Web, o Proxy de Aplicativo do Azure Active Directory também pode ser usado para publicar aplicativos cliente nativos. Os aplicativos cliente nativos diferem dos aplicativos Web porque eles são instalados em um dispositivo, enquanto os aplicativos Web são acessados por meio de um navegador. 

O Proxy de Aplicativo dá suporte a aplicativos cliente nativos aceitando os tokens emitidos pelo Azure AD que são enviados nos cabeçalhos padrão Autorizar HTTP.

![Relação entre os usuários finais, o Active Directory do Azure e os aplicativos publicados](./media/active-directory-application-proxy-native-client/richclientflow.png)

Use a Biblioteca de Autenticação do Azure AD, que trata da autenticação e dá suporte a vários ambientes de cliente, para publicar aplicativos nativos. O Proxy de Aplicativo se encaixa no [cenário de Aplicativo Nativo para API Web](develop/active-directory-authentication-scenarios.md#native-application-to-web-api). Este artigo explica as quatro etapas para publicar um aplicativo nativo com o Proxy de Aplicativo e a Biblioteca de Autenticação do Azure AD. 

## <a name="step-1-publish-your-application"></a>Etapa 1: publicar seu aplicativo
Publique seu aplicativo de proxy como faria com qualquer outro aplicativo e atribua aos usuários acesso a ele. Para saber mais, consulte [Publicar aplicativos com o Proxy de Aplicativo](active-directory-application-proxy-publish.md).

## <a name="step-2-configure-your-application"></a>Etapa 2: configurar seu aplicativo
Configure seu aplicativo nativo da seguinte maneira:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Navegue até **Azure Active Directory** > **Registros de aplicativo**.
3. Selecione **Novo registro de aplicativo**.
4. Especifique um nome para o aplicativo, selecione **Nativo** como o tipo de aplicativo e forneça o URI de Redirecionamento para seu aplicativo. 

   ![Criar um novo registro de aplicativo](./media/active-directory-application-proxy-native-client/create.png)
5. Selecione **Criar**.

Para obter informações mais detalhadas sobre como criar um novo registro de aplicativo, confira [Integrando aplicativos ao Azure Active Directory](.//develop/active-directory-integrating-applications.md).


## <a name="step-3-grant-access-to-other-applications"></a>Etapa 3: conceder acesso a outros aplicativos
Habilite o aplicativo nativo para ser exposto a outros aplicativos no seu diretório:

1. Ainda nos **Registros de aplicativo**, selecione o novo aplicativo nativo que você acabou de criar.
2. Selecione **Permissões necessárias**.
3. Selecione **Adicionar**.
4. Abra a primeira etapa, **Selecionar uma API**.
5. Use a barra de pesquisa para encontrar o aplicativo Proxy de Aplicativo que você publicou na primeira seção. Escolha esse aplicativo e clique em **Selecionar**. 

   ![Procurar o aplicativo de proxy](./media/active-directory-application-proxy-native-client/select_api.png)
6. Abra a segunda etapa, **Selecionar permissões**.
7. Use a caixa de seleção para conceder ao aplicativo nativo acesso ao aplicativo de proxy e clique em **Selecionar**.

   ![Conceder acesso ao aplicativo de proxy](./media/active-directory-application-proxy-native-client/select_perms.png)
8. Selecione **Concluído**.


## <a name="step-4-edit-the-active-directory-authentication-library"></a>Etapa 4: Edite a Biblioteca de Autenticação do Active Directory
Edite o código de aplicativo nativo no contexto de autenticação da ADAL (Biblioteca de Autenticação do Active Directory) para incluir o seguinte texto:

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = authContext.AcquireToken("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

As variáveis no exemplo de código devem ser substituídas da seguinte maneira:

* A **ID do locatário** pode ser encontrada no Portal do Azure. Navegue até **Azure Active Directory** > **Propriedades** e copie a ID de Diretório. 
* A **URL externa** é a URL de front-end que você inseriu no Aplicativo de Proxy. Para encontrar esse valor, navegue até a seção **Proxy de aplicativo** do aplicativo de proxy.
* A **ID do aplicativo** do aplicativo nativo pode ser encontrada na página **Propriedades** do aplicativo nativo.
* O **URI de Redirecionamento do aplicativo nativo** pode ser encontrado na página **URIs de Redirecionamento** do aplicativo nativo.


## <a name="see-also"></a>Consulte também

Para saber mais sobre o fluxo do aplicativo nativo, confira [Aplicativo nativo para API Web](develop/active-directory-authentication-scenarios.md#native-application-to-web-api)

Para obter as últimas notícias e atualizações, confira o [blog do Proxy de Aplicativo](http://blogs.technet.com/b/applicationproxyblog/)

