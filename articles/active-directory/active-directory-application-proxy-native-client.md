---
title: "Como habilitar a publicação de aplicativos clientes nativos com aplicativos de proxy | Microsoft Docs"
description: Aborda como habilitar aplicativos clientes nativos para se comunicar com o Conector de Proxy do aplicativo Azure AD para fornecer acesso remoto seguro aos seus aplicativos locais.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: f0cae145-e346-4126-948f-3f699747b96e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: e65393c9582056f84530a32804e0d82fd451b688
ms.openlocfilehash: 34cacff4e8b13fa9d91387ca8762439908ed01fd


---
# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>Como habilitar aplicativos clientes nativos para interagir com aplicativos de proxy
O Proxy de Aplicativo Active Directory do Azure é amplamente usado para publicar os aplicativos de navegador, como SharePoint, Outlook Web Access e linha personalizada de aplicativos comerciais. Ele também pode ser usado para publicar aplicativos cliente nativos, que são diferentes de aplicativos Web porque são instalados em um dispositivo. Isso é feito com suporte a tokens emitidos pelo Azure AD que são enviados em cabeçalhos HTTP Authorize padrão.

![Relação entre os usuários finais, o Active Directory do Azure e os aplicativos publicados](./media/active-directory-application-proxy-native-client/richclientflow.png)

O método recomendado para publicar aplicativos é usar a Biblioteca de Autenticação do AD do Azure que se encarrega de todo o incômodo da autenticação e dá suporte a muitos ambientes de cliente diferentes. O Proxy de Aplicativo se encaixa no [cenário de Aplicativo Nativo para API Web](develop/active-directory-authentication-scenarios.md#native-application-to-web-api). O processo para realizar isso é o seguinte:

## <a name="step-1-publish-your-application"></a>Etapa 1: publicar seu aplicativo
Publique seu aplicativo proxy como faria com qualquer outro aplicativo, atribua usuários e dê a eles licenças basic ou premium. Para saber mais, consulte [Publicar aplicativos com o Proxy de Aplicativo](active-directory-application-proxy-publish.md).

## <a name="step-2-configure-your-application"></a>Etapa 2: configurar seu aplicativo
Configure seu aplicativo nativo da seguinte maneira:

1. Entre no portal clássico do Azure.
2. Selecione o ícone do Active Directory no menu esquerdo e selecione seu diretório.
3. No menu superior, clique em **Aplicativos**. Se nenhum aplicativo tiver sido adicionado ao diretório, essa página mostrará apenas o link **Adicionar um Aplicativo** . Clique no link ou, se preferir, você poderá clicar no botão **Adicionar** , na barra de comandos.
4. Na página **O que você deseja fazer**, clique no link **Adicionar um aplicativo que minha organização esteja desenvolvendo**.
5. Na página **Conte-nos sobre seu aplicativo**, especifique um nome para seu aplicativo e selecione **Aplicativo cliente nativo**. Clique no ícone de seta para continuar.
6. Na página **Informações do aplicativo**, forneça o **URI de redirecionamento** para o aplicativo cliente nativo e clique na marca de seleção para concluir.

O aplicativo foi adicionado e você será levado para a página Início Rápido do seu aplicativo.

## <a name="step-3-grant-access-to-other-applications"></a>Etapa 3: conceder acesso a outros aplicativos
Habilite o aplicativo nativo para ser exposto a outros aplicativos no seu diretório:

1. No menu superior, clique em **Aplicativos**, selecione o novo aplicativo nativo e clique em **Configurar**.
2. Role para baixo até a seção **permissões para outros aplicativos** . Clique no botão **Adicionar aplicativo** , selecione o aplicativo de proxy ao qual você deseja conceder acesso de aplicativo nativo e clique na marca de seleção no canto inferior direito. No menu suspenso **Permissões Delegadas** , escolha a nova permissão.

![Captura de tela de Permissões para outros aplicativos - adicionar aplicativo](./media/active-directory-application-proxy-native-client/delegate_native_app.png)

## <a name="step-4-edit-the-active-directory-authentication-library"></a>Etapa 4: Edite a Biblioteca de Autenticação do Active Directory
Edite o código de aplicativo nativo no contexto de autenticação da ADAL (Biblioteca de Autenticação do Active Directory) para incluir o seguinte:

        // Acquire Access Token from AAD for Proxy Application
        AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<TenantId>");
        AuthenticationResult result = authContext.AcquireToken("< Frontend Url of Proxy App >",
                                                        "< Client Id of the Native app>",
                                                        new Uri("< Redirect Uri of the Native App>"),
                                                        PromptBehavior.Never);

        //Use the Access Token to access the Proxy Application
        HttpClient httpClient = new HttpClient();
        httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
        HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");

As variáveis devem ser substituídas da seguinte maneira:

* **ID do locatário** pode ser encontrada no GUID na URL da página **Configuração** do aplicativo, logo após "/Directory/".
* **URL de front-end** é a URL de front-end inserida no Aplicativo de Proxy e pode ser encontrada na página **Configuração** do aplicativo proxy.
* A **Id do cliente** do aplicativo nativo pode ser encontrada na página **Configurar** do aplicativo nativo.
* O **URI de redirecionamento do aplicativo nativo** podem ser encontrado na página **Configurar** do aplicativo nativo.

![Captura de tela da página Configurar novo aplicativo nativo](./media/active-directory-application-proxy-native-client/new_native_app.png)

Para saber mais sobre o fluxo do aplicativo nativo, consulte [Aplicativo nativo para API Web](develop/active-directory-authentication-scenarios.md#native-application-to-web-api).

## <a name="see-also"></a>Consulte também
* [Publicar aplicativos usando seu próprio nome de domínio](active-directory-application-proxy-custom-domains.md)
* [Habilitar o acesso condicional](active-directory-application-proxy-conditional-access.md)
* [Trabalho com aplicativos com reconhecimento de declaração](active-directory-application-proxy-claims-aware-apps.md)
* [Habilitar o logon único](active-directory-application-proxy-sso-using-kcd.md)

Para obter as últimas notícias e atualizações, confira o [blog do Proxy de Aplicativo](http://blogs.technet.com/b/applicationproxyblog/)




<!--HONumber=Jan17_HO3-->


