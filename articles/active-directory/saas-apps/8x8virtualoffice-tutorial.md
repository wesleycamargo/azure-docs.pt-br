---
title: 'Tutorial: Integração do Azure Active Directory com o 8x8 Virtual Office | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o 8x8 Virtual Office.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: jeedes
ms.openlocfilehash: 53db637bf7ad47896747b491fcbe31123fdb104e
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741803"
---
# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>Tutorial: Integração do Azure Active Directory com o 8x8 Virtual Office

Neste tutorial, você aprende a integrar o 8x8 Virtual Office ao Azure AD (Azure Active Directory).

A integração do 8x8 Virtual Office ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao 8x8 Virtual Office.
- Você pode permitir que seus usuários façam logon automaticamente no 8x8 Virtual Office (Single Sign-On) com suas contas do AD do Azure.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o 8x8 Virtual Office, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do 8x8 Virtual Office

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o 8x8 Virtual Office da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-8x8-virtual-office-from-the-gallery"></a>Adicionando o 8x8 Virtual Office da galeria

Para configurar a integração do 8x8 Virtual Office ao Azure AD, você precisará adicionar o 8x8 Virtual Office à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o 8x8 Virtual Office por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **8x8 Virtual Office**, selecione **8x8 Virtual Office** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![8x8 Virtual Office na lista de resultados](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o 8x8 Virtual Office com base em um usuário de teste chamado "Britta Simon".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do 8x8 Virtual Office é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do 8x8 Virtual Office.

Para configurar e testar o logon único do Azure AD com o 8x8 Virtual Office, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do 8x8 Virtual Office](#creating-a-8x8-virtual-office-test-user)** – para ter um equivalente de Brenda Fernandes no 8x8 Virtual Office que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo 8x8 Virtual Office.

**Para configurar o logon único do Azure AD com o 8x8 Virtual Office, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo do **8x8 Virtual Office**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](common/tutorial_general_301.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Configurar o logon único](common/editconfigure.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Domínio 8x8 Virtual Office e informações de logon único de URLs](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL: `https://sso.8x8.com/saml2`

    b. Na caixa de texto **URL de resposta**, insira uma URL: `https://sso.8x8.com/saml2`

5. Na página **Certificado de assinatura SAML**, na seção **Certificado de assinatura SAML**, clique em **Download** para fazer download do **certificado (Raw)** e salvar o certificado arquivo no seu computador.

    ![O link de download do Certificado](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_certificate.png) 

6. Na seção **Configurar o escritório virtual 8x8**, copie o URL apropriado de acordo com sua necessidade.

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

    ![Configuração do 8x8 Virtual Office](common/configuresection.png)

7. Faça logon no seu locatário do 8x8 Virtual Office como administrador.

8. Selecione **Virtual Office Account Mgr** no painel do aplicativo.

    ![Configurar no lado do aplicativo](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

9. Selecione a **conta comercial** que deseja gerenciar e clique no botão **Entrar**.

    ![Configurar no lado do aplicativo](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

10. Clique em **contas** guia na lista de menus.

    ![Configurar no lado do aplicativo](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

11. Clique em **Single Sign On** na lista Accounts.
  
    ![Configurar no lado do aplicativo](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

12. Selecione **Logon único** em Métodos de autenticação e clique em **SAML**.

    ![Configurar no lado do aplicativo](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

13. No **logon único do SAML** , realize as seguintes etapas:

    ![Configurar no lado do aplicativo](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    a. Na caixa de texto **URL de login**, cole o **valor do URL de login** que você copiou do portal do Azure.

    b. No **URL de logoff** caixa de texto, cole **URL de logoff** valor que você copiou do portal do Azure.

    c. Na caixa de texto **URL do emissor**, cole o valor **Identificador do Azure AD** que você copiou do portal do Azure.

    d. Clique em **procurar** botão para carregar o certificado que você baixou do portal do Azure.

    e. Clique no botão **Salvar** .

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Criar um usuário do AD do Azure][100]

2. Selecione **Novo usuário** na parte superior da tela.

    ![Criação de um usuário de teste do AD do Azure](common/create_aaduser_01.png) 

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![Criação de um usuário de teste do AD do Azure](common/create_aaduser_02.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No **nome de usuário** , digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.
  
### <a name="creating-a-8x8-virtual-office-test-user"></a>Criar um usuário de teste no 8x8 Virtual Office

O objetivo desta seção é criar uma usuária chamada Brenda Fernandes no 8x8 Virtual Office. O 8x8 Virtual Office dá suporte ao provisionamento just-in-time, que está habilitado por padrão.

Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o 8x8 Virtual Office, caso ele ainda não exista.

> [!NOTE]
> Se precisar criar um usuário manualmente, será necessário contatar a [equipe de suporte do 8x8 Virtual Office](https://www.8x8.com/about-us/contact-us).

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao 8x8 Virtual Office.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **8x8 Virtual Office**.

    ![Configurar o logon único](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do 8x8 Virtual Office no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo do 8x8 Virtual Office.
Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

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
