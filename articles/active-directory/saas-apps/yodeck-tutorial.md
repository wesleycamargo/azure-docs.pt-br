---
title: 'Tutorial: Integração do Azure Active Directory ao Yodeck | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Yodeck.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b2c8dccb-eeb0-4f4d-a24d-8320631ce819
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7564710b282c7e2ac586980896a28c00bcb2fe80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60259543"
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>Tutorial: Integração do Azure Active Directory ao Yodeck

Neste tutorial, você aprenderá a integrar o Yodeck ao Azure AD (Azure Active Directory).

A integração do Yodeck ao AD do Azure oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem terá acesso ao Yodeck
- Você pode permitir que usuários façam logon automaticamente no Yodeck (logon único) com as respectivas contas do AD do Azure.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do AD do Azure com o Yodeck, você precisará dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura habilitada para logon único do Yodeck

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Yodeck da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-yodeck-from-the-gallery"></a>Adição do Yodeck da galeria
Para configurar a integração do Yodeck ao Azure AD, você precisará adicionar o Yodeck da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Yodeck por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **Yodeck**, selecione **Yodeck** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Yodeck na lista de resultados](./media/yodeck-tutorial/tutorial_yodeck_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Yodeck, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Yodeck é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado no Yodeck.

Para configurar e testar o logon único do Azure AD com o Yodeck, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar de um usuário de teste do Yodeck](#create-a-yodeck-test-user)** : para ter um equivalente de Brenda Fernandes no Yodeck que esteja vinculado à representação de usuário no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo Yodeck.

**Para configurar o logon único do AD do Azure com o Yodeck, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Yodeck**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Caixa de diálogo Logon único](./media/yodeck-tutorial/tutorial_yodeck_samlbase.png)

1. Na seção **Domínio e URLs do Yodeck**, realize as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo **IdP**:

    ![Informações de logon único de Domínio e URLs do Yodeck](./media/yodeck-tutorial/tutorial_yodeck_url.png)

    Na caixa de texto **Identificador (ID da entidade)**, digite a URL: `https://app.yodeck.com/api/v1/account/metadata/`

1. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único de Domínio e URLs do Yodeck](./media/yodeck-tutorial/tutorial_yodeck_url1.png)

    Na caixa de texto **URL de Logon**, digite a URL: `https://app.yodeck.com/login`

1. Na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar a **URL de metadados de federação do aplicativo** e cole-a no bloco de notas.

    ![O link de download do Certificado](./media/yodeck-tutorial/tutorial_yodeck_certificate.png)

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/yodeck-tutorial/tutorial_general_400.png)
    
1. Em outra janela do navegador da Web, faça logon em seu site de empresa do Yodeck como administrador.

1. Clique no formulário de opções **Configurações do usuário** no canto superior direito da página e selecione **Configurações da conta**.

    ![Configuração do Yodeck](./media/yodeck-tutorial/configure1.png)

1. Selecione **SAML** e execute as seguintes etapas:

    ![Configuração do Yodeck](./media/yodeck-tutorial/configure2.png)

     a. Selecione **Importar da URL**.

    b. Na caixa de texto **URL**, cole o valor da **URL de metadados de federação do aplicativo** que você copiou do Portal do Azure e clique em **Importar**.
    
    c. Depois de importar a **URL de metadados de federação do aplicativo**, os campos restantes são preenchidos automaticamente.

    d. Clique em **Salvar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/yodeck-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/yodeck-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/yodeck-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/yodeck-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-yodeck-test-user"></a>Criar um usuário de teste do Yodeck

Para permitir que os usuários do Azure AD façam logon no Yodeck, eles devem ser provisionados no Yodeck.
No caso do Yodeck, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do Yodeck como administrador.

1. Clique no formulário de opções **Configurações do usuário** no canto superior direito da página e selecione **Usuários**.

    ![Adicionar Funcionário](./media/yodeck-tutorial/user1.png)

1. Clique em **+Usuário** para abrir a guia **Detalhes do usuário**.

    ![Adicionar Funcionário](./media/yodeck-tutorial/user2.png)

1. Na página do diálogo **Detalhes do Usuário** , realize as seguintes etapas:

    ![Adicionar Funcionário](./media/yodeck-tutorial/user3.png)

     a. Na caixa de texto **Nome**, digite o nome do usuário, como **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite o Sobrenome do usuário, como **Fernandes**.

    c. No **E-mail** caixa de texto, digite o endereço de email do usuário, como **brendafernandes\@contoso.com**.

    d. Selecione a opção de **Permissões de conta** adequada de acordo com suas necessidades organizacionais.
    
    e. Clique em **Salvar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Yodeck.

![Atribuir a função de usuário][200]

**Para atribuir Brenda Fernandes ao Yodeck, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201]

1. Na lista de aplicativos, selecione **Yodeck**.

    ![O link do Yodeck na lista de Aplicativos](./media/yodeck-tutorial/tutorial_yodeck_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco Yodeck no Painel de Acesso, deverá ser automaticamente conectado ao seu aplicativo Yodeck.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/yodeck-tutorial/tutorial_general_01.png
[2]: ./media/yodeck-tutorial/tutorial_general_02.png
[3]: ./media/yodeck-tutorial/tutorial_general_03.png
[4]: ./media/yodeck-tutorial/tutorial_general_04.png

[100]: ./media/yodeck-tutorial/tutorial_general_100.png

[200]: ./media/yodeck-tutorial/tutorial_general_200.png
[201]: ./media/yodeck-tutorial/tutorial_general_201.png
[202]: ./media/yodeck-tutorial/tutorial_general_202.png
[203]: ./media/yodeck-tutorial/tutorial_general_203.png

