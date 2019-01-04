---
title: 'Tutorial: Integração do Azure Active Directory com iWellnessNow | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o iWellnessNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 24ffc841-7a77-481c-9cc4-6f8bda58fe66
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: b02a5ec5a68743e148bcfef3c631d7777fd0a067
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52871666"
---
# <a name="tutorial-azure-active-directory-integration-with-iwellnessnow"></a>Tutorial: Integração do Azure Active Directory com iWellnessNow

Neste tutorial, você aprenderá a integrar o iWellnessNow ao Azure AD (Azure Active Directory).

A integração do iWellnessNow ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso ao iWellnessNow.
- Pode habilitar a entrada automática para seus usuários no iWellnessNow (Logon Único) com as suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o iWellnessNow, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do iWellnessNow

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do iWellnessNow por meio da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-iwellnessnow-from-the-gallery"></a>Adição do iWellnessNow por meio da galeria
Para configurar a integração do iWellnessNow ao Azure AD, você precisará adicioná-lo por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o iWellnessNow por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **iWellnessNow**, selecione **iWellnessNow** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![iWellnessNow na lista de resultados](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você fará a configuração e o teste do logon único do Azure AD com o iWellnessNow, com base em uma usuária de testes chamada “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa conhecer o usuário equivalente do iWellnessNow no Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o respectivo usuário no iWellnessNow.

Para fazer a configuração e o teste do logon único do Azure AD com o iWellnessNow, conclua os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de testes do iWellnessNow](#create-an-iwellnessnow-test-user)**: para ter um equivalente de Brenda Fernandes no iWellnessNow vinculado à representação do usuário no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo iWellnessNow.

**Para configurar o logon único do Azure AD com o iWellnessNow, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **iWellnessNow**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_samlbase.png)

1. Na seção **Domínio e URLs do iWellnessNow**, se você tiver **arquivos de metadados do provedor de serviços** e deseja configurar o aplicativo no modo iniciado **IDP**, execute as seguintes etapas:

    ![Carregamento de logon único em Domínio e URLs do iWellnessNow](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_upload.png)

     a. Clique em **Carregar arquivo de metadados**.

    ![Configuração de carregamento de logon único em Domínio e URLs do iWellnessNow](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_uploadconfig.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.
    
    c. Após a conclusão com êxito do carregamento do **arquivo de metadados do provedor de serviços**, os valores **Identificador** e **URL de resposta** serão preenchidos automaticamente na caixa de texto da seção **Domínio e URLs do iWellnessNow**, conforme mostrado abaixo:

    ![Domínio iWellnessNow e informações de logon único de URLs](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_url3.png)

1. Se não tiver o **arquivo de metadados do provedor de serviços** e deseja configurar o aplicativo no modo iniciado **IDP**, execute as seguintes etapas:

    ![Domínio iWellnessNow e informações de logon único de URLs](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_url.png)

     a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `http://<CustomerName>.iwellnessnow.com`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<CustomerName>.iwellnessnow.com/ssologin`

1. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Domínio iWellnessNow e informações de logon único de URLs](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<CustomerName>.iwellnessnow.com/`
     
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Entre em contato com a equipe de suporte do [ iWellnessNow Client ](mailto:info@iwellnessnow.com) para obter esses valores.

1. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_certificate.png) 

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/iwellnessnow-tutorial/tutorial_general_400.png)
    
1. Para configurar o lado do **iWellnessNow** de logon único, você precisa enviar os **Metadados XML** baixados à [equipe de suporte do iWellnessNow](mailto:info@iwellnessnow.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/iwellnessnow-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/iwellnessnow-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/iwellnessnow-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/iwellnessnow-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-an-iwellnessnow-test-user"></a>Criar um usuário de testes do iWellnessNow

Nesta seção, você criará uma usuária chamada Brenda Fernandes no iWellnessNow. Trabalhe com a  [equipe de suporte do iWellnessNow](mailto:info@iwellnessnow.com) para adicionar os usuários na plataforma iWellnessNow. Os usuários devem ser criados e ativados antes de você usar o logon único

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilita Brenda Fernandes a usar o logon único do Azure concedendo acesso ao iWellnessNow.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao iWellnessNow, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **iWellnessNow**.

    ![O link iWellnessNow na lista de aplicativos](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco iWellnessNow no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo iWellnessNow.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/iwellnessnow-tutorial/tutorial_general_01.png
[2]: ./media/iwellnessnow-tutorial/tutorial_general_02.png
[3]: ./media/iwellnessnow-tutorial/tutorial_general_03.png
[4]: ./media/iwellnessnow-tutorial/tutorial_general_04.png

[100]: ./media/iwellnessnow-tutorial/tutorial_general_100.png

[200]: ./media/iwellnessnow-tutorial/tutorial_general_200.png
[201]: ./media/iwellnessnow-tutorial/tutorial_general_201.png
[202]: ./media/iwellnessnow-tutorial/tutorial_general_202.png
[203]: ./media/iwellnessnow-tutorial/tutorial_general_203.png

