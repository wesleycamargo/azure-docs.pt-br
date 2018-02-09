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
ms.openlocfilehash: c9d99d6f7c2777c8e68f5db50b402280377d88e9
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-in-azure-api-management"></a>Como autorizar contas de desenvolvedor usando o Active Directory do Azure no Gerenciamento de API do Azure

Este guia mostra como habilitar o acesso ao portal do desenvolvedor para os usuários do Azure Active Directory. Este guia também mostra como gerenciar grupos de usuários do Active Directory do Azure, adicionando grupos externos que contêm os usuários de um Active Directory do Azure.

> [!WARNING]
> A integração do Azure Active Directory está disponível somente nas camadas [Developer, Standard e Premium](https://azure.microsoft.com/pricing/details/api-management/).

## <a name="prerequisites"></a>pré-requisitos

- Conclua o seguinte guia de início rápido: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
- Importar e publicar uma instância de gerenciamento de API. Para obter mais informações, consulte [Importar e publicar](import-and-publish.md).

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory"></a>Como autorizar contas de desenvolvedor usando o Active Directory do Azure

1. Entre no [Portal do Azure](https://portal.azure.com). 
2. Selecionar ![seta](./media/api-management-howto-aad/arrow.png).
3. Digite "api" na caixa de pesquisa.
4. Clique em **Serviços de Gerenciamento de API**.
5. Selecione a instância de serviço do APIM.
6. Em **SEGURANÇA**, selecione **Identidades**.

    ![Identidades externas](./media/api-management-howto-aad/api-management-with-aad001.png)
7. Clique em **+Adicionar** na parte superior.

    A janela **Adicionar provedor de identidade** aparece à direita.
8. Em **Tipo de provedor**, selecione **Azure Active Directory**.

    Os controles que permitem que você insira outras informações necessárias aparecem na janela. Os controles incluem: **ID do cliente**, **Segredo do cliente** (você poderá ver informações mais à frente neste tutorial).
9. Anote a **URL de redirecionamento**.  
10. No navegador, abra uma guia diferente. 
11. Abra o [portal do Azure](https://portal.azure.com).
12. Selecionar ![seta](./media/api-management-howto-aad/arrow.png).
13. Digite "ativo", o **Azure Active Directory** é exibido.
14. Selecione **Azure Active Directory**.
15. Em **GERENCIAR**, selecione **Registro do aplicativo**.

    ![Registro do aplicativo](./media/api-management-howto-aad/api-management-with-aad002.png)
16. Clique em **Novo registro de aplicativo**.

    A janela **Criar** é exibida à direita. É onde você pode inserir informações relevantes do aplicativo do AAD.
17. Insira um nome para o aplicativo.
18. Para o tipo de aplicativo, selecione **Aplicativo Web/API**.
19. Para a URL de logon único, insira a URL de logon único no portal do desenvolvedor. Neste exemplo, a URL de logon único é: https://apimwithaad.portal.azure-api.net/signin.
20. Clique em **Criar** para criar o aplicativo.
21. Para localizar seu aplicativo, selecione **Registros do aplicativo** e pesquisa pelo nome.

    ![Registro do aplicativo](./media/api-management-howto-aad/find-your-app.png)
22. Depois que o aplicativo for registrado, vá para **URL de resposta** e verifique se "Redirecionar URL" está definido para o valor que você obteve na etapa 9. 
23. Se você deseja configurar seu aplicativo (por exemplo, alterar a **URL da ID do aplicativo**) selecione **Propriedades**.

    ![Registro do aplicativo](./media/api-management-howto-aad/api-management-with-aad004.png)

    Se vários Active Directories do Azure serão usados para esse aplicativo, clique em **Sim** para **Aplicativo é multilocatário**. O padrão é **Não**.
24. Defina as permissões de aplicativo selecionando **Permissões necessárias**.
25. Selecione o seu aplicativo e marque **Ler dados do diretório** e **Entrar e ler o perfil de usuário**.

    ![Registro do aplicativo](./media/api-management-howto-aad/api-management-with-aad005.png)

    Para obter mais informações sobre o aplicativo e permissões delegadas, consulte [Acessando a API do Graph][Accessing the Graph API].
26. Na janela esquerda, copie o valor da **ID do aplicativo**.

    ![Registro do aplicativo](./media/api-management-howto-aad/application-id.png)
27. Alterne para o aplicativo de gerenciamento de API. A janela **Adicionar provedor de identidade** deve ser exibida. <br/>Colar o valor da **ID do aplicativo** na caixa **Id do cliente**.
28. Volte para a configuração do Azure Active Directory do Azure e clique em **Chaves**.
29. Crie uma nova chave para especificar um nome e uma duração. 
30. Clique em **Salvar**. A chave é gerada.

    Copie a chave para a área de transferência.

    ![Registro do aplicativo](./media/api-management-howto-aad/api-management-with-aad006.png)

    > [!NOTE]
    > Anote esta chave. Depois que você fechar a janela de configuração do Active Directory do Azure, a chave não pode ser exibida novamente.
    > 
    > 
31. Alterne para o aplicativo de gerenciamento de API. <br/>Na janela **Adicionar provedor de identidade**, cole a chave na caixa de texto **Segredo do cliente**.
32. A janela **Adicionar provedor de identidade**, contém a caixa de texto **Locatários permitidos**, onde você especifica os diretórios que possuem acesso às APIs da instância do serviço de gerenciamento de API. <br/>Especifique os domínios das instâncias do Active Directory do Azure para os quais você deseja conceder acesso. Você pode separar múltiplos domínios com novas linhas, espaços ou vírgulas.

    Vários domínios podem ser especificados na seção **Locatários permitidos** . Antes que qualquer usuário possa fazer logon por meio de um domínio diferente do domínio original em que o aplicativo foi registrado, um administrador global do domínio diferente deve conceder permissão para o aplicativo para acessar dados de diretório. Para conceder permissão, o administrador global deve acessar `https://<URL of your developer portal>/aadadminconsent` (por exemplo, https://contoso.portal.azure-api.net/aadadminconsent), digitar o nome de domínio doe locatário do Active Directory ao qual ele deseja conceder acesso e clicar em Enviar. No exemplo a seguir, um administrador global de `miaoaad.onmicrosoft.com` está tentando dar permissão para este portal do desenvolvedor específico. 

33. Depois que a configuração desejada for especificada, clique em **Adicionar**.

    ![Registro do aplicativo](./media/api-management-howto-aad/api-management-with-aad007.png)

Depois que as alterações são salvas, os usuários no Azure Active Directory especificado podem entrar portal do desenvolvedor, seguindo as etapas em [Fazer logon no portal do desenvolvedor usando uma conta do Azure Active Directory](#log_in_to_dev_portal).

![Permissões](./media/api-management-howto-aad/api-management-aad-consent.png)

Na próxima tela, o administrador global receberá uma solicitação para confirmar a concessão de permissão. 

![Permissões](./media/api-management-howto-aad/api-management-permissions-form.png)

Se um administrador não global tenta efetuar logon antes de permissões serem concedidas por um administrador global, a tentativa de logon falha e uma tela de erro é exibida.

## <a name="how-to-add-an-external-azure-active-directory-group"></a>Como adicionar um grupo externo do Active Directory do Azure

Depois de habilitar o acesso de usuários em um Active Directory do Azure, você pode adicionar grupos do Active Directory do Azure a Gerenciamento de API para gerenciar com mais facilidade a associação dos desenvolvedores no grupo com os produtos desejados.

Para configurar um grupo externo do Azure Active Directory, o Azure Active Directory deve ser primeiro configurado na guia Identidades, seguindo o procedimento na seção anterior. 

Grupos externos do Azure Active Directory são adicionados na guia **Grupos** da sua instância de gerenciamento de API.

![Grupos](./media/api-management-howto-aad/api-management-with-aad008.png)

1. Selecione a guia **Grupos** .
2. Clique no botão **Adicionar grupo do AAD**.
3. Selecione o grupo que deseja adicionar.
4. Pressione o botão **Selecionar**.

Após o grupo do Azure Active Directory ter sido criado, você pode revisar e configurar as propriedades de grupos externos quando eles forem adicionados, clique no nome do grupo na guia **Grupos**.

Aqui, você pode editar o **Nome** e a **Descrição** do grupo.
 
Os usuários do Azure Active Directory configurado podem entrar no portal do Desenvolvedor, além de ver e assinar todos os grupos para os quais têm visibilidade, seguindo as instruções na seção a seguir.

## <a name="a-idlogintodevportalhow-to-log-in-to-the-developer-portal-using-an-azure-active-directory-account"></a><a id="log_in_to_dev_portal"/>Como fazer logon no portal do desenvolvedor usando uma conta do Azure Active Directory

Para fazer logon no portal do Desenvolvedor usando uma conta do Azure Active Directory configurada nas seções anteriores, abra uma nova janela de navegador usando a **URL de logon** da configuração de aplicativo do Active Directory e clique em **Azure Active Directory**.

![Portal do desenvolvedor][api-management-dev-portal-signin]

Insira as credenciais de um dos usuários no seu Active Directory do Azure e clique em **Entrar**.

![Entrar][api-management-aad-signin]

Pode ser solicitado que você preencha um formulário de registro se qualquer informação adicional for necessária. Preencha o formulário de registro e clique em **Inscrever-se**.

![Registro][api-management-complete-registration]

Agora, o usuário está conectado no portal do desenvolvedor para a instância do serviço de Gerenciamento de API.

![Registro completo][api-management-registration-complete]

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

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
