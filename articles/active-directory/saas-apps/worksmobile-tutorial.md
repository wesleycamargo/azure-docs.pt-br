---
title: 'Tutorial: Integração do Azure Active Directory com LINE WORKS | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o LINE WORKS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 725f32fd-d0ad-49c7-b137-1cc246bf85d7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c70df45f448502978254a60747eb168ea33dd86
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60521389"
---
# <a name="tutorial-azure-active-directory-integration-with-line-works"></a>Tutorial: Integração do Azure Active Directory com LINE WORKS

Neste tutorial, você aprende a integrar o LINE WORKS ao Azure Active Directory (Azure AD).

A integração do LINE WORKS ao Azure AD proporciona os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso ao LINE WORKS.
- Você pode permitir que seus usuários façam logon automaticamente no LINE WORKS (Logon Único) com as contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao LINE WORKS, você precisará dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura do LINE WORKS com logon único habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o LINE WORKS da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-line-works-from-the-gallery"></a>Adicionar o LINE WORKS da galeria

Para configurar a integração do LINE WORKS ao Azure AD, você precisará adicionar o LINE WORKS da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o LINE WORKS da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **LINE WORKS**, selecione **LINE WORKS** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![LINE WORKS na lista de resultados](./media/worksmobile-tutorial/tutorial_lineworks_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o LINE WORKS com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do LINE WORKS é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do LINE WORKS.

Para configurar e testar o logon único do Azure AD com o LINE WORKS, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do LINE WORKS](#creating-a-line-works-test-user)** - para ter um equivalente de Britta Simon no LINE WORKS vinculado à representação de usuário do Microsoft Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal do Azure e configura o logon único no aplicativo LINE WORKS.

**Para configurar o logon único do Azure AD com o LINE WORKS, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **LINE WORKS**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](common/tutorial_general_301.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Configurar o logon único](common/editconfigure.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do LINE WORKS](./media/worksmobile-tutorial/tutorial_lineworks_url.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://auth.worksmobile.com/d/login/<domain>/`

    b. Na caixa de texto **Identificador**, digite uma URL: `worksmobile.com`

5. Na página **Certificado de assinatura SAML**, na seção **Certificado de assinatura SAML**, clique em **Download** para fazer download do **certificado (Raw)** e salvar o certificado arquivo no seu computador.

    ![O link de download do Certificado](./media/worksmobile-tutorial/tutorial_lineworks_certificate.png) 

6. Na seção **Configurar LINE WORKS**, copie a URL apropriada, de acordo com seus requisitos.

     a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

    ![Configuração do LINE WORKS](common/configuresection.png)

7. Para configurar o logon único no **LINE WORKS**, leia os [documentos de SSO do LINE WORKS](https://developers.worksmobile.com/jp/document/1001080101) e defina uma configuração do LINE WORKS.

> [!NOTE]
> É necessário converter o arquivo de certificado baixado de .cert para .pem

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Criar um usuário do AD do Azure][100]

2. Selecione **Novo usuário** na parte superior da tela.

    ![Criação de um usuário de teste do AD do Azure](common/create_aaduser_01.png) 

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![Criação de um usuário de teste do AD do Azure](common/create_aaduser_02.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brendafernandes\@domíniodaempresa.extensão**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.

### <a name="creating-a-line-works-test-user"></a>Criando um usuário de teste do LINE WORKS

Nesta seção, você criará uma usuária chamada Brenda Fernandes no LINE WORKS. Acesse a [página de administração do LINE WORKS](https://admin.worksmobile.com) e adicione os usuários na plataforma do LINE WORKS.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao LINE WORKS.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, escolha **LINE WORKS**.

    ![Configurar o logon único](./media/worksmobile-tutorial/tutorial_lineworks_app.png)

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco LINE WORKS no Painel de Acesso, você deverá ser automaticamente conectado ao seu aplicativo LINE WORKS.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
