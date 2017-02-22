---
title: Publicar aplicativos com o Proxy de Aplicativo do Azure AD | Microsoft Docs
description: Publique aplicativos locais na nuvem com o Proxy de Aplicativo do Azure AD no Portal do Azure.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 5acfbfbe7327fc28fa909e6ef7e0f9b6ce8b0e54
ms.openlocfilehash: e0be8f4617c2fdbe1cb6fe2f904bf9ea7056c55e

---


# <a name="publish-applications-using-azure-ad-application-proxy---public-preview"></a>Publicar aplicativos usando o Proxy de Aplicativo do Azure AD - Visualização Pública

> [!div class="op_single_selector"]
> * [Portal do Azure](application-proxy-publish-azure-portal.md)
> * [Portal clássico do Azure](active-directory-application-proxy-publish.md)

O Proxy de Aplicativo do Active Directory (AD) do Azure o ajuda a dar suporte a funcionários remotos publicando aplicativos locais para serem acessados via Internet. Pelo Portal do Azure, é possível publicar aplicativos que são executados em sua rede local e fornecem acesso remoto seguro de fora de sua rede.

Este artigo explica as etapas para publicar um aplicativo local com o Proxy de Aplicativo. Depois de concluir este artigo, você estará pronto para configurar o aplicativo com o logon único, informações personalizadas ou requisitos de segurança.

Se você for iniciante com o Proxy de Aplicativo, aprenda mais sobre este recurso com o artigo [Como fornecer acesso remoto seguro a aplicativos locais](active-directory-application-proxy-get-started.md).


## <a name="publish-an-on-premises-app-for-remote-access"></a>Publicar um aplicativo local para acesso remoto


> [!TIP]
> Se esta for sua primeira vez usando o Proxy de Aplicativo, escolha um aplicativo que já esteja configurado para autenticação baseada em senha. O Proxy de Aplicativo oferece suporte a outros tipos de autenticação, mas os aplicativos baseados em senha são mais fáceis e rápidos de executar. 

1. Entre como administrador no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Azure Active Directory** > **Aplicativos empresariais** > **Adicionar**.

  ![Adicionar um aplicativo empresarial](./media/application-proxy-publish-azure-portal/add-app.png)

3. Na página de categorias, selecione **aplicativo local**.  

  ![Adicionar seu próprio aplicativo](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. Forneça as seguintes informações sobre o aplicativo:

   - **Nome**: o nome do aplicativo que será exibido no painel de acesso. 

   - **URL interna**: o endereço que o Conector de Proxy de Aplicativo usa para acessar o aplicativo dentro de sua rede privada. Você pode fornecer um caminho específico no servidor back-end para publicar, enquanto o restante do servidor é não publicado. Assim, você pode publicar sites diferentes no mesmo servidor como diferentes aplicativos, e dar a cada um deles seu próprio nome e suas regras de acesso.

     > [!TIP]
     > Se você publicar um caminho, verifique se ele inclui todas as imagens, scripts e folhas de estilo necessários para seu aplicativo. Por exemplo, se seu aplicativo está em https://seuaplicativo/app e usa imagens localizadas em https://seuaplicativo/media, você deve publicar https://seuaplicativo/ como o caminho.

   - **URL externa**: o endereço que seus usuários usam para acessar o aplicativo de fora da sua rede.
   - **Pré-autenticação**: como o Proxy de Aplicativo verifica os usuários antes de lhes dar acesso ao aplicativo. 

     - Azure Active Directory: o Proxy de Aplicativo redireciona os usuários para entrar com o Azure AD, que autentica as permissões para o diretório e o aplicativo. Recomendamos manter essa opção como o padrão.
     - Passagem: os usuários não precisam ser autenticados no Azure Active Directory para acessar o aplicativo. Você ainda pode configurar os requisitos de autenticação no back-end.
   - **Traduzir URL nos cabeçalhos?**: escolha se deseja traduzir a URL nos cabeçalhos ou manter a original. 
   - **Grupo de Conectores**: conectores processam o acesso remoto ao seu aplicativo e o ajudam a organizar conectores e aplicativos por região, rede ou finalidade. Se você ainda não criou grupos de conectores criados, seu aplicativo é atribuído a **Padrão** e você verá uma mensagem de aviso solicitando que [Crie um grupo de conectores](active-directory-application-proxy-connectors-azure-portal.md).

   ![Configurar seu aplicativo](./media/application-proxy-publish-azure-portal/configure-app.png)

8. Selecione **Adicionar**.


## <a name="add-a-test-user"></a>Adicionar um usuário de teste 

Para testar se o aplicativo foi publicado corretamente, adicione uma conta de usuário a que tem acesso. 

1. De volta à folha Início Rápido, selecione **Atribuir um usuário para teste**.

  ![Atribuir um usuário para teste](./media/application-proxy-publish-azure-portal/assign-user.png)

2. Na folha Usuários e grupos, selecione **Adicionar**.

  ![Adicionar um usuário ou grupo](./media/application-proxy-publish-azure-portal/add-user.png)

3. Na folha de Adicionar Atribuição, selecione **Usuários e grupos**, em seguida escolha a conta que você deseja adicionar. 
4. Selecione **Atribuir**.

## <a name="test-your-published-app"></a>Testar seu aplicativo publicado

No seu navegador, navegue até a URL externa configurada durante a etapa de publicação. Você deve ver a tela inicial e ser capaz de entrar com a conta de teste que configurou.

![Testar seu aplicativo publicado](./media/application-proxy-publish-azure-portal/test-app.png)


## <a name="next-steps"></a>Próximas etapas
- [Baixe conectores](active-directory-application-proxy-enable.md) e [crie grupos de conectores](active-directory-application-proxy-connectors-azure-portal.md) para publicar aplicativos em redes e locais separados.

- [Configurar o logon único](application-proxy-sso-azure-portal.md) para seu aplicativo recém-criado



<!--HONumber=Feb17_HO2-->


