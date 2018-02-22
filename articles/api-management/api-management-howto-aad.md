---
title: Autorizar contas de desenvolvedor usando o Azure Active Directory - Gerenciamento de API do Azure | Microsoft Docs
description: "Como autorizar usuários usando o Active Directory do Azure no Gerenciamento de API."
services: api-management
documentationcenter: API Management
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: apimpm
ms.openlocfilehash: d89257cba70fb82d56fb1beef8a8efe66a8af02d
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autorizar contas de desenvolvedor usando o Active Directory do Azure no Gerenciamento de API do Azure

Este artigo mostra como habilitar o acesso ao portal do desenvolvedor para os usuários do Azure Active Directory (Azure AD). Este guia também mostra como gerenciar grupos de usuários do Azure AD, adicionando grupos externos que contêm os usuários.

> [!NOTE]
> A integração do Azure AD está disponível somente nas camadas [Developer, Standard e Premium](https://azure.microsoft.com/pricing/details/api-management/).

## <a name="prerequisites"></a>pré-requisitos

- Conclua o seguinte guia de início rápido: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
- Importar e publicar uma instância de gerenciamento de API do Azure. Para obter mais informações, consulte [Importar e publicar](import-and-publish.md).

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autorizar contas de desenvolvedor usando o Azure AD

1. Entre no [Portal do Azure](https://portal.azure.com). 
2. Selecionar ![seta](./media/api-management-howto-aad/arrow.png).
3. Digite **api** na caixa de pesquisa.
4. Selecione **Serviços de Gerenciamento de API**.
5. Selecione uma instância do Serviço de Gerenciamento de API.
6. Em **SEGURANÇA**, selecione **Identidades**.

7. Selecione **+Adicionar** na parte superior.

    O painel **Adicionar provedor de identidade** aparece à direita.
8. Em **Tipo de provedor**, selecione **Azure Active Directory**.

    Os controles que permitem que você insira outras informações necessárias aparecem no painel. Os controles incluem **ID do cliente** e **Segredo do cliente**. (Você obterá informações sobre esses controles posteriormente neste artigo.)
9. Anote o conteúdo da **URL de redirecionamento**.
    
   ![Etapas para adicionar um provedor de identidade no portal do Azure](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. No navegador, abra uma guia diferente. 
11. Vá para o [Portal do Azure](https://portal.azure.com).
12. Selecionar ![seta](./media/api-management-howto-aad/arrow.png).
13. Digite **active**. O painel **Azure Active Directory** é exibido.
14. Selecione **Azure Active Directory**.
15. Em **GERENCIAR**, selecione **Registros do aplicativo**.
16. Selecione **Novo registro de aplicativo**.

    ![Seleções para criar um novo registro de aplicativo](./media/api-management-howto-aad/api-management-with-aad002.png)

    O painel **Criar** é exibido à direita. É nela em que você pode inserir as informações relevantes do aplicativo do Azure AD.
17. Insira um nome para o aplicativo.
18. Para o tipo de aplicativo, selecione **Aplicativo Web/API**.
19. Para a URL de entrada, insira a URL de entrada no portal do desenvolvedor. Neste exemplo, a URL de entrada é: https://apimwithaad.portal.azure-api.net/signin.
20. Selecione **Criar** para criar o aplicativo.
21. Para localizar seu aplicativo, selecione **Registros do aplicativo** e pesquisa pelo nome.

    ![Caixa onde você procura um aplicativo](./media/api-management-howto-aad/find-your-app.png)
22. Depois que o aplicativo for registrado, vá para **URL de resposta** e verifique se **Redirecionar URL** está definido para o valor que você obteve na etapa 9. 
23. Se você deseja configurar seu aplicativo (por exemplo, alterar a **URL da ID do aplicativo**), selecione **Propriedades**.

    ![Abertura do painel “Propriedades”](./media/api-management-howto-aad/api-management-with-aad004.png)

    Se várias instâncias do Azure AD forem usadas para este aplicativo, selecione **Sim** para **Multilocatário**. O padrão é **Não**.
24. Defina as permissões de aplicativo selecionando **Permissões necessárias**.
25. Selecione o aplicativo e marque as caixas de seleção **Ler dados do diretório** e **Entrar e ler o perfil de usuário**.

    ![Caixas de seleção de permissões](./media/api-management-howto-aad/api-management-with-aad005.png)

    Para obter mais informações sobre permissões de aplicativo e permissões delegadas, consulte [Acessando a API do Graph][Accessing the Graph API].
26. No painel esquerdo, copie o valor da **ID do Aplicativo**.

    ![Valor “ID do Aplicativo”](./media/api-management-howto-aad/application-id.png)
27. Alterne para o aplicativo de gerenciamento de API. 

    Na janela **Adicionar provedor de identidade**, cole o valor **ID do Aplicativo** na caixa **ID do Cliente**.
28. Volte para a configuração do Azure AD e selecione **Chaves**.
29. Crie uma nova chave especificando um nome e uma duração. 
30. Selecione **Salvar**. A chave é gerada.

    Copie a chave para a área de transferência.

    ![Seleções para criar uma chave](./media/api-management-howto-aad/api-management-with-aad006.png)

    > [!NOTE]
    > Anote esta chave. Depois que você fechar o painel de configuração do Azure AD, a chave não poderá ser exibida novamente.
    > 
    > 
31. Alterne para o aplicativo de gerenciamento de API. 

    Na janela **Adicionar provedor de identidade**, cole a chave na caixa de texto **Segredo do cliente**.
32. A janela **Adicionar provedor de identidade** também contém a caixa de texto **Locatários Permitidos**. Lá, especifique os domínios das instâncias do Azure AD para os quais você deseja conceder acesso às APIs da instância de serviço Gerenciamento da API. Você pode separar múltiplos domínios com novas linhas, espaços ou vírgulas.

    Você pode especificar vários domínios na seção **Locatários Permitidos**. Antes que qualquer usuário possa entrar por meio de um domínio diferente do domínio original em que o aplicativo foi registrado, um administrador global do domínio diferente deve conceder permissão para o aplicativo para acessar dados de diretório. Para conceder permissão, o administrador global deve:
    
    a. Vá para `https://<URL of your developer portal>/aadadminconsent` (por exemplo, https://contoso.portal.azure-api.net/aadadminconsent).
    
    b. Digite o nome de domínio do locatário do Azure AD ao qual você deseja conceder acesso.
    
    c. Selecione **Enviar**. 
    
    No exemplo a seguir, um administrador global de miaoaad.onmicrosoft.com está tentando dar permissão para este portal do desenvolvedor específico. 

33. Depois de especificar a configuração desejada, selecione **Adicionar**.

    ![Botão "Adicionar" no painel "Adicionar provedor de identidade"](./media/api-management-howto-aad/api-management-with-aad007.png)

Depois que as alterações forem salvas, os usuários na instância do Azure AD poderão entrar no portal do desenvolvedor seguindo as etapas em [Entrar no portal do desenvolvedor usando uma conta do Azure AD](#log_in_to_dev_portal).

![Inserção do nome de um locatário do Azure AD](./media/api-management-howto-aad/api-management-aad-consent.png)

Na próxima tela, o administrador global deve confirmar a concessão de permissão. 

![Confirmação de permissões atribuídas](./media/api-management-howto-aad/api-management-permissions-form.png)

Se um administrador não global tentar entrar antes de um administrador global conceder permissões, a tentativa de entrada falhará e uma tela de erro será exibida.

## <a name="add-an-external-azure-ad-group"></a>Adicionar um grupo do Azure AD externo

Depois de habilitar acesso para usuários em uma instância do Azure AD, você poderá adicionar grupos do Azure AD no Gerenciamento de API. Em seguida, você pode gerenciar mais facilmente a associação dos desenvolvedores no grupo com os produtos desejados.

Para configurar um grupo do Azure AD externo, você deve primeiro configurar a instância do Azure AD na guia **Identidades**, seguindo o procedimento na seção anterior. 

Você adiciona grupos do Azure AD externos na guia **Grupos** da instância Gerenciamento de API.

1. Selecione a guia **Grupos** .
2. Selecione o botão **Adicionar grupo do AAD**.
   ![Botão “Adicionar grupo do AAD”](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Selecione o grupo que deseja adicionar.
4. Pressione o botão **Selecionar**.

Depois de adicionar um grupo do Azure AD externo, você poderá revisar e configurar as propriedades. Selecione o nome do grupo na guia **Grupos**. A partir daqui, você pode editar as informações de **Nome** e **Descrição** do grupo.
 
Os usuários da instância do Azure AD configurada já podem entrar no portal do desenvolvedor. Eles podem exibir e se inscrever em todos os grupos para os quais têm visibilidade.

## <a name="a-idlogintodevportalsign-in-to-the-developer-portal-by-using-an-azure-ad-account"></a><a id="log_in_to_dev_portal"/>Entrar no portal do desenvolvedor usando uma conta do Azure AD

Para entrar no portal do desenvolvedor usando uma conta do Azure AD que você configurou nas seções anteriores:

1. Abra uma nova janela do navegador usando a URL de entrada na configuração do aplicativo do Active Directory e selecione **Azure Active Directory**.

   ![Página de entrada][api-management-dev-portal-signin]

2. Insira as credenciais de um dos usuários no Azure AD e selecione **Entrar**.

   ![Entrada com nome de usuário e senha][api-management-aad-signin]

3. Pode ser solicitado que você preencha um formulário de registro se qualquer informação adicional for necessária. Preencha o formulário de registro e selecione **Inscrever-se**.

   ![Botão "Inscrever-se" no formulário de registro][api-management-complete-registration]

Agora o usuário está conectado no portal do desenvolvedor para a instância de serviço de Gerenciamento de API.

![Portal do desenvolvedor após a conclusão do registro][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
