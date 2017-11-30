---
title: "Configurar a autenticação do Azure Active Directory para seu aplicativo de Serviços de Aplicativos"
description: "Saiba como configurar a autenticação do Active Directory do Azure para seu aplicativo de Serviços de Aplicativos."
author: mattchenderson
services: app-service
documentationcenter: 
manager: syntaxc4
editor: 
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 990fab9aeea71b8cf344b9a49a5ed438db6663c0
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2017
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-login"></a>Configure seu aplicativo do Serviço de Aplicativo para usar o logon do Azure Active Directory
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este artigo mostra como configurar os Serviços de Aplicativos do Azure para usar o Azure Active Directory como um provedor de autenticação.

## <a name="express"> </a>Configurar o Active Directory do Azure usando configurações expressas
1. No [Portal do Azure], navegue até o aplicativo do Serviço de Aplicativo. No painel de navegação esquerdo, selecione **Autenticação / Autorização**.
2. Se **Autenticação / Autorização** não estiver habilitada, selecione **Ativar**.
3. Selecione **Azure Active Directory**e, em seguida, selecione **Expresso** no **Modo de Gerenciamento**.
4. Selecione **OK** para registrar o aplicativo do Serviço de Aplicativo no Azure Active Directory. Isso cria um novo registro de aplicativo. Se você deseja escolher um registro de aplicativo existente, clique em **Selecionar um aplicativo existente** e, em seguida, pesquise o nome de um registro de aplicativo criado anteriormente no seu locatário.
   Clique no registro do aplicativo para selecioná-lo e clique em **OK**. Em seguida, clique em **OK** na página de configurações do Azure Active Directory.
   Por padrão, o Serviço de Aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo do site e às APIs. Você deve autorizar os usuários no código do aplicativo.
5. (Opcional) Para restringir o acesso ao seu site somente para usuários autenticados pelo Azure Active Directory, defina a **Ação a ser executada quando a solicitação não for autenticada** como **Logon com o Azure Active Directory**. Isso exige que todas as solicitações sejam autenticadas e todas as solicitações não autenticadas sejam redirecionadas ao Active Directory do Azure para autenticação.
6. Clique em **Salvar**.

Agora você está pronto para usar o Azure Active Directory para autenticação no aplicativo do Serviço de Aplicativo.

## <a name="advanced"> </a>(Método alternativo) Configurar manualmente o Active Directory do Azure com configurações avançadas
Você também pode optar por fornecer as configurações manualmente. Essa é a solução preferencial se o locatário do AAD que você deseja usar for diferente do locatário usado para entrar no Azure. Para concluir a configuração, você deve primeiro criar um registro no Active Directory do Azure e fornecer alguns detalhes de registro ao Serviço de Aplicativo.

### <a name="register"> </a>Registrar seu aplicativo do Serviço de Aplicativo com o Azure Active Directory
1. Faça logon no [Portal do Azure]e navegue até o aplicativo do Serviço de Aplicativo. Copie a **URL** de seu aplicativo. Você usará isso para configurar o registro do aplicativo do Azure Active Directory.
2. Navegue até **Active Directory** e, em seguida, selecione os **Registros do aplicativo**, então clique em **Novo registro de aplicativo** na parte superior para iniciar um novo registro de aplicativo. 
3. Na página **Criar**, digite um **Nome** para o registro do aplicativo, selecione o tipo **Aplicativo Web/API** na caixa **URL de Entrada** e cole a URL do Aplicativo (da etapa 1). Em seguida, clique em **Criar**.
4. Em alguns segundos, você deverá visualizar o novo registro do aplicativo que você acabou de criar.
5. Depois que o registro do aplicativo for adicionado, clique no nome do registro do aplicativo, clique em **Configurações** na parte superior e clique em **Propriedades** 
6. Cole a URL do Aplicativo (da etapa 1) na caixa **URI da ID do aplicativo**, cole a URL do Aplicativo (da etapa 1) também na **URL da Home Page** e clique em **Salvar**
7. Agora, clique nas **URLs de Resposta**, edite a **URL de Resposta**, cole a URL do Aplicativo (da etapa 1), modifique o protocolo para certificar-se de que você tem o protocolo **https://** (não http://) e, acrescentado ao final da URL, */.auth/login/aad/callback* (Por exemplo, `https://contoso.azurewebsites.net/.auth/login/aad/callback`). Clique em **Salvar**.   
8.  Neste ponto, copie a **ID do Aplicativo** para o aplicativo. Guarde para uso posterior. Isso será necessário para configurar seu aplicativo do Serviço de Aplicativo.
9. Feche a página **Aplicativo registrado**. Na página **Registros de aplicativo**, clique no botão **Pontos de Extremidade** na parte superior e, em seguida, copie a URL do **Documento de Metadados Federados**. 
10. Abra uma nova janela do navegador e navegue até a URL, colando e navegando até a página XML. No topo do documento há um elemento **EntityDescriptor** e deverá haver um atributo **entityID** do formato `https://sts.windows.net/` seguido por um GUID específico para seu locatário (chamado de "ID do locatário"). Copie esse valor - ele serve como sua **URL do Emissor**. Você irá configurar seu aplicativo para usar posteriormente.

### <a name="secrets"> </a>Adicionar informações do Azure Active Directory ao aplicativo do Serviço de Aplicativo
1. Retorne ao [Portal do Azure], navegue até o aplicativo do Serviço de Aplicativo. Clique em **Autenticação/Autorização**. Se o recurso Autenticação/Autorização não estiver habilitado, mude a opção para **Ativado**. Clique em **Azure Active Directory**, em Provedores de Autenticação, para configurar seu aplicativo. (Opcional) Por padrão, o Serviço de Aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo do site e às APIs. Você deve autorizar os usuários no código do aplicativo. Configure **Ação a ser tomada quando a solicitação não for autenticada** para **Logon com o Azure Active Directory**. Essa opção exige que todas as solicitações sejam autenticadas e todas as solicitações não autenticadas sejam redirecionadas para o Azure Active Directory para autenticação.
2. Na configuração da Autenticação do Active Directory, clique em **Avançado** em **Modo de Gerenciamento**. Cole a ID do aplicativo na caixa ID do Cliente (da etapa 8) e cole a entityId (da etapa 10) no valor de URL do Emissor. Em seguida, clique em **OK**.
3. Na página de configuração da Autenticação do Active Directory, clique em **Salvar**.

Agora você está pronto para usar o Azure Active Directory para autenticação no aplicativo do Serviço de Aplicativo.

## <a name="optional-configure-a-native-client-application"></a>(Opcional) Configurar um aplicativo de cliente nativo
O Active Directory do Azure também permite que você registre clientes nativos, o que oferece maior controle sobre o mapeamento de permissões. Isso é necessário se você desejar executar logons usando uma biblioteca, como a **Biblioteca de Autenticação do Active Directory**.

1. Navegue até o **Azure Active Directory** no [Portal do Azure].
2. No painel de navegação esquerdo, selecione **Registros de aplicativo**. Clique em **Novo registro de aplicativo** na parte superior.
4. Na página **Criar** , insira um **Nome** para o registro do seu aplicativo. Selecione **Nativo** em **Tipo de aplicativo**.
5. Na caixa **URI de redirecionamento**, digite o ponto de extremidade do site */.auth/login/done*, usando o esquema HTTPS. Esse valor deve ser similar a *https://contoso.azurewebsites.net/.auth/login/done*. Caso queira criar um aplicativo do Windows, use o [SID de pacote](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) como o URI.
5. Clique em **Criar**.
6. Uma vez que o registro do aplicativo foi adicionado, selecione-o para abri-lo. Localize o **ID do Aplicativo** e anote esse valor.
7. Clique em **Todas as configurações** > **Permissões necessárias** > **Adicionar** > **Selecionar um API**.
8. Digite o nome do aplicativo do Serviço de Aplicativo que você registrou anteriormente para pesquisá-lo e, em seguida, selecione-o e clique em **Selecionar**. 
9. Selecione **Acessar \<app_name>**. Em seguida, clique em **Selecionar**. Em seguida, clique em **Concluído**.

Você já configurou um aplicativo de cliente nativo que pode acessar seu aplicativo do Serviço de aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-url.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app_registration.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-create.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-appidurl.png
[4]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-replyurl.png
[5]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints.png
[6]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints-fedmetadataxml.png
[7]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth.png
[8]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth-config.png



<!-- URLs. -->

[Portal do Azure]: https://portal.azure.com/
[alternative method]:#advanced
