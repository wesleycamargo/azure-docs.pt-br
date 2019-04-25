---
title: Autorizar contas de desenvolvedor usando o Azure Active Directory - Gerenciamento de API do Azure | Microsoft Docs
description: Como autorizar usuários usando o Active Directory do Azure no Gerenciamento de API.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: apimpm
ms.openlocfilehash: 95e501eca0f7765cc5201f7b315703c3ca43df8f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60529411"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autorizar contas de desenvolvedor usando o Active Directory do Azure no Gerenciamento de API do Azure

Este artigo mostra como habilitar o acesso ao portal do desenvolvedor para os usuários do Azure Active Directory (Azure AD). Este guia também mostra como gerenciar grupos de usuários do Azure AD, adicionando grupos externos que contêm os usuários.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua o início rápido a seguir: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
- Importar e publicar uma instância de gerenciamento de API do Azure. Para obter mais informações, consulte [Importar e publicar](import-and-publish.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autorizar contas de desenvolvedor usando o Azure AD

1. Entre no [Portal do Azure](https://portal.azure.com). 
2. Selecionar ![seta](./media/api-management-howto-aad/arrow.png).
3. Digite **api** na caixa de pesquisa.
4. Selecione **Serviços de Gerenciamento de API**.
5. Selecione uma instância do Serviço de Gerenciamento de API.
6. Sob **segurança**, selecione **identidades**.
7. Selecione **+Adicionar** na parte superior.

    O painel **Adicionar provedor de identidade** aparece à direita.
8. Em **Tipo de provedor**, selecione **Azure Active Directory**.

    Os controles que permitem que você insira outras informações necessárias aparecem no painel. Os controles incluem **ID do cliente** e **Segredo do cliente**. (Você obterá informações sobre esses controles posteriormente neste artigo.)
9. Anote o conteúdo do **URL de redirecionamento**.
    
   ![Etapas para adicionar um provedor de identidade no portal do Azure](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. No navegador, abra uma guia diferente. 
11. Navegue até a [portal do Azure - registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) para registrar um aplicativo no Active Directory.
12. Sob **Manage**, selecione **registros de aplicativo**.
13. Selecione **Novo registro**. Sobre o **registrar um aplicativo** , defina os valores da seguinte maneira:
    
* Definir **nome** com um nome significativo. e.g., *developer-portal*
* Definir **suporte para tipos de conta** à **contas neste diretório organizacional apenas**. 
* Definir **URI de redirecionamento** para o valor que você obteve na etapa 9. 
* Escolher **registrar**. 

14.  Depois que o aplicativo está registrado, copie o **ID do aplicativo (cliente)** da **visão geral** página. 
15. Volte para sua instância de gerenciamento de API. No **Adicionar provedor de identidade** janela, cole o **ID do aplicativo (cliente)** valor na **ID do cliente** caixa.
16. Volte para a configuração do AD do Azure, selecione **certificados e segredos** sob **gerenciar**. Selecione o **novo segredo do cliente** botão. Insira um valor em **descrição**, selecione qualquer opção para **Expires** e escolha **adicionar**. Copie o valor do segredo de cliente antes de sair da página. Isso será necessário na próxima etapa. 
17. Volte para sua instância de gerenciamento de API, cole o segredo para o **segredo do cliente** caixa.

    > [!IMPORTANT]
    > Certifique-se de atualizar o **Segredo do cliente** antes de a chave expirar. 
    >  
    >

18. A janela **Adicionar provedor de identidade** também contém a caixa de texto **Locatários Permitidos**. Lá, especifique os domínios das instâncias do Azure AD para os quais você deseja conceder acesso às APIs da instância de serviço Gerenciamento da API. Você pode separar múltiplos domínios com novas linhas, espaços ou vírgulas.

> [!NOTE]
> Você pode especificar vários domínios na seção **Locatários Permitidos**. Antes que qualquer usuário possa entrar por meio de um domínio diferente do domínio original em que o aplicativo foi registrado, um administrador global do domínio diferente deve conceder permissão para o aplicativo para acessar dados de diretório. Para conceder permissão, o administrador global deve: um. Vá para `https://<URL of your developer portal>/aadadminconsent` (por exemplo, https://contoso.portal.azure-api.net/aadadminconsent).
> b. Digite o nome de domínio do locatário do Azure AD ao qual você deseja conceder acesso.
> c. Selecione **Enviar**. 

19.  Depois de especificar a configuração desejada, selecione **Adicionar**.

Depois que as alterações forem salvas, os usuários na instância do Azure AD poderão entrar no portal do desenvolvedor seguindo as etapas em [Entrar no portal do desenvolvedor usando uma conta do Azure AD](#log_in_to_dev_portal).


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

1. Insira as credenciais de um dos usuários no Azure AD e selecione **Entrar**.

   ![Entrada com nome de usuário e senha][api-management-aad-signin]

1. Pode ser solicitado que você preencha um formulário de registro se qualquer informação adicional for necessária. Preencha o formulário de registro e selecione **Inscrever-se**.

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

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: https://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
