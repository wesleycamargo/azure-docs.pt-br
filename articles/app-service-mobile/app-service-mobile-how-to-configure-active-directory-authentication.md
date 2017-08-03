---
title: "Como configurar a autenticação do Active Directory do Azure para seu aplicativo de Serviços de Aplicativos"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 1f8813370995a7a55c9d87b8ec1b60ae0fe623f5
ms.contentlocale: pt-br
ms.lasthandoff: 03/09/2017

---
# <a name="how-to-configure-your-app-service-application-to-use-azure-active-directory-login"></a>Como configurar seu aplicativo de Serviço de Aplicativo para usar o logon do Active Directory do Azure
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar os Serviços de Aplicativos do Azure para usar o Azure Active Directory como um provedor de autenticação.

## <a name="express"> </a>Configurar o Active Directory do Azure usando configurações expressas
1. No [portal do Azure], navegue até o seu aplicativo. Clique em **Configurações** e depois em **Autenticação/Autorização**.
2. Se o recurso Autenticação / Autorização não estiver habilitado, mude a opção para **Ativado**.
3. Clique em **Azure Active Directory** e clique em **Expresso** em **Modo de Gerenciamento**.
4. Clique em **OK** para registrar seu aplicativo no Active Directory do Azure. Isso criará um novo registro. Caso queira escolher um registro existente, clique em **Selecionar um aplicativo existente** e pesquise pelo nome de um registro criado anteriormente no seu locatário.
   Clique no registro para selecioná-lo e clique em **OK**. Em seguida, clique em **OK** na folha de configurações do Active Directory do Azure.
   
   ![][0]
   
   Por padrão, o Serviço de Aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo do site e às APIs. Você deve autorizar os usuários no código do aplicativo.
5. (Opcional) Para restringir o acesso ao seu site somente para usuários autenticados pelo Azure Active Directory, defina a **Ação a ser executada quando a solicitação não for autenticada** como **Logon com o Azure Active Directory**. Isso exige que todas as solicitações sejam autenticadas e todas as solicitações não autenticadas sejam redirecionadas ao Active Directory do Azure para autenticação.
6. Clique em **Salvar**.

Agora você está pronto para usar um Active Directory do Azure para autenticação em seu aplicativo.

## <a name="advanced"> </a>(Método alternativo) Configurar manualmente o Active Directory do Azure com configurações avançadas
Você também pode optar por fornecer as configurações manualmente. Essa é a solução preferencial se o locatário do AAD que você deseja usar for diferente do locatário usado para entrar no Azure. Para concluir a configuração, você deve primeiro criar um registro no Active Directory do Azure e fornecer alguns detalhes de registro ao Serviço de Aplicativo.

### <a name="register"> </a>Registrar seu aplicativo com o Active Directory do Azure
1. Faça logon no [portal do Azure]e navegue até o seu aplicativo. Copie a **URL**. Você a utilizará para configurar seu aplicativo do Active Directory do Azure.
2. Entre no [portal clássico do Azure] e navegue até o **Active Directory**.
   
    ![][2]
3. Selecione o diretório e, em seguida, selecione a guia **Aplicativos** na parte superior. Clique em **ADICIONAR** na parte inferior para criar um novo registro de aplicativo.
4. Clique em **Adicionar um aplicativo que a minha organização está desenvolvendo**.
5. No Assistente de Adicionar Aplicativo, insira um **Nome** para seu aplicativo e clique no tipo **Aplicativo Web e/ou API Web**. Em seguida, clique em continuar.
6. Na caixa **URL DE LOGON** , cole a URL do aplicativo que foi copiada anteriormente. Insira essa mesma URL na caixa **URI de ID do Aplicativo** . Em seguida, clique em continuar.
7. Depois que o aplicativo tiver sido adicionado, clique na guia **Configurar** . Edite a **URL de Resposta** em **Logon Único** para ser a URL do seu aplicativo anexada ao caminho, */.auth/login/aad/callback*. Por exemplo: `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Certifique-se de que você está usando o esquema HTTPS.
   
    ![][3]
8. Clique em **Salvar**. Em seguida, copie a **ID do cliente** para o aplicativo. Você irá configurar seu aplicativo para usá-la posteriormente.
9. Na parte inferior da barra de comando, clique em **Exibir Pontos de Extremidade**, copie a URL do **Documento de Metadados Federados** e baixe esse documento ou navegue até ele em um navegador.
10. No elemento raiz **EntityDescriptor**, deve haver um atributo **entityID** do formato `https://sts.windows.net/` seguido por um GUID específico para seu locatário (chamado de "ID do locatário"). Copie esse valor - ele servirá como sua **URL do Emissor**. Você irá configurar seu aplicativo para usá-la posteriormente.

### <a name="secrets"> </a>Adicionar informações do Active Directory do Azure ao seu aplicativo
1. De volta ao [portal do Azure], navegue até o seu aplicativo. Clique em **Configurações** e depois em **Autenticação/Autorização**.
2. Se o recurso Autenticação/Autorização não estiver habilitado, mude a opção para **Ativado**.
3. Clique em **Azure Active Directory** e clique em **Avançado** em **Modo de Gerenciamento**. Cole os valores de ID do Cliente e URL do Emissor que obteve anteriormente. Em seguida, clique em **OK**.
   
   ![][1]
   
   Por padrão, o Serviço de Aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo do site e às APIs. Você deve autorizar os usuários no código do aplicativo.
4. (Opcional) Para restringir o acesso ao seu site somente para usuários autenticados pelo Azure Active Directory, defina a **Ação a ser executada quando a solicitação não for autenticada** como **Logon com o Azure Active Directory**. Isso exige que todas as solicitações sejam autenticadas e todas as solicitações não autenticadas sejam redirecionadas ao Active Directory do Azure para autenticação.
5. Clique em **Salvar**.

Agora você está pronto para usar um Active Directory do Azure para autenticação em seu aplicativo.

## <a name="optional-configure-a-native-client-application"></a>(Opcional) Configurar um aplicativo de cliente nativo
O Active Directory do Azure também permite que você registre clientes nativos, o que oferece maior controle sobre o mapeamento de permissões. Isso é necessário se você desejar executar logons usando uma biblioteca, como a **Biblioteca de Autenticação do Active Directory**.

1. Navegue até **Active Directory** no [portal clássico do Azure].
2. Selecione o diretório e, em seguida, selecione a guia **Aplicativos** na parte superior. Clique em **ADICIONAR** na parte inferior para criar um novo registro de aplicativo.
3. Clique em **Adicionar um aplicativo que a minha organização está desenvolvendo**.
4. No Assistente para Adicionar aplicativo, insira um **Nome** para o seu aplicativo e clique no tipo **Aplicativo Cliente Nativo**. Em seguida, clique em continuar.
5. Na caixa **URI de redirecionamento**, digite o ponto de extremidade do site */.auth/login/done*, usando o esquema HTTPS. Esse valor deve ser similar a *https://contoso.azurewebsites.net/.auth/login/done*. Caso queira criar um aplicativo do Windows, use o [SID de pacote](app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) como o URI.
6. Depois que o aplicativo nativo tiver sido adicionado, clique na guia **Configurar** . Encontre a **ID do cliente** e anote esse valor.
7. Role a página para baixo até a seção de **Permissões para outros aplicativos** e clique em **Adicionar aplicativo**.
8. Procure o aplicativo Web que você registrou anteriormente e clique no ícone de adição. Em seguida, clique em verificar para fechar a caixa de diálogo. Se o aplicativo Web não puder ser encontrado, navegue até seu registro e adicione uma nova URL de resposta (por exemplo, a versão HTTP da sua URL atual), clique em Salvar e repita essas etapas - o aplicativo deverá aparecer na lista.
9. Na nova entrada que você acabou de adicionar, abra a lista suspensa **Permissões Delegadas** e selecione **Acessar (nome do aplicativo)**. Em seguida, clique em **Salvar**.

Você configurou um aplicativo de cliente nativo que pode acessar o aplicativo de Serviço de Aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-express-settings.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-advanced-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png

<!-- URLs. -->

[portal do Azure]: https://portal.azure.com/
[portal clássico do Azure]: https://manage.windowsazure.com/
[alternative method]:#advanced

