---
title: 'Tutorial: Integração do Azure Active Directory com o ZephyrSSO | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o ZephyrSSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4d75e26b-c0fa-420b-93c1-e40b68562be8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3be3008367f5c405f65dfd37b4384726ab2456c5
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56209516"
---
# <a name="tutorial-azure-active-directory-integration-with-zephyrsso"></a>Tutorial: Integração do Azure Active Directory com o ZephyrSSO

Neste tutorial, você aprenderá como integrar o ZephyrSSO ao Azure AD (Azure Active Directory).

A integração do ZephyrSSO ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao ZephyrSSO.
- Você pode permitir que seus usuários façam logon automaticamente no ZephyrSSO (Single Sign-On) com suas contas do AD do Azure.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ZephyrSSO, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Assinatura habilitada para um ZephyrSSO o logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando ZephyrSSO da Galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-zephyrsso-from-the-gallery"></a>Adicionando ZephyrSSO da Galeria

Para configurar a integração do ZephyrSSO ao Azure AD, você precisa adicionar o ZephyrSSO da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar ZephyrSSO da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **ZephyrSSO**, selecione **ZephyrSSO** no painel de resultados, em seguida, clique em **Add** botão para adicionar o aplicativo.

    ![ZephyrSSO na lista de resultados](./media/zephyrsso-tutorial/tutorial_zephyrsso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com ZephyrSSO com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber o que o usuário de contraparte do ZephyrSSO é para um usuário no Azure AD. Em outras palavras, um relacionamento de link entre um usuário do AD do Azure e o usuário relacionado no ZephyrSSO precisa ser estabelecido.

Para configurar e testar o logon único do Azure AD com o ZephyrSSO, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Crie um usuário de teste do ZephyrSSO](#create-a-zephyrsso-test-user)** - para ter um equivalente de Brenda Fernandes no ZephyrSSO que esteja vinculado à representação do usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único em seu aplicativo ZephyrSSO.

**Para configurar o logon único do Azure AD com o ZephyrSSO, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **ZephyrSSO**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Caixa de diálogo Logon único](./media/zephyrsso-tutorial/tutorial_zephyrsso_samlbase.png)

3. Sobre o **ZephyrSSO domínio e URLs do** , realize as seguintes etapas:

    ![Domínio ZephyrSSO e informações de logon único de URLs](./media/zephyrsso-tutorial/tutorial_zephyrsso_url.png)

     a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.yourzephyr.com/Zephyrsso`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<SUBDOMAIN>.yourzephyr.com/flex/saml/sso`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Entre em contato com [equipe de suporte do ZephyrSSO](https://support.getzephyr.com) para obter esses valores.

4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/zephyrsso-tutorial/tutorial_zephyrsso_certificate.png)

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/zephyrsso-tutorial/tutorial_general_400.png)

6. Para configurar o single sign-on no lado do **ZephyrSSO**, você precisa enviar o **Metadata XML** para [equipe de suporte do ZephyrSSO](https://support.getzephyr.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/zephyrsso-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/zephyrsso-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/zephyrsso-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/zephyrsso-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
  
### <a name="create-a-zephyrsso-test-user"></a>Criar um usuário de teste ZephyrSSO

Nesta seção, você cria um usuário chamado Brenda Fernandes no ZephyrSSO. Trabalhar com a  [equipe de suporte do ZephyrSSO](https://support.getzephyr.com)  para adicionar os usuários em sua plataforma. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure, concedendo acesso ao ZephyrSSO.

![Atribuir a função de usuário][200]

**Para atribuir Britta Simon a ZephyrSSO, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **ZephyrSSO**.

    ![O link ZephyrSSO na lista de aplicativos](./media/zephyrsso-tutorial/tutorial_zephyrsso_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco ZephyrSSO no Painel de Acesso, você deve ser conectado automaticamente ao seu aplicativo ZephyrSSO.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zephyrsso-tutorial/tutorial_general_01.png
[2]: ./media/zephyrsso-tutorial/tutorial_general_02.png
[3]: ./media/zephyrsso-tutorial/tutorial_general_03.png
[4]: ./media/zephyrsso-tutorial/tutorial_general_04.png

[100]: ./media/zephyrsso-tutorial/tutorial_general_100.png

[200]: ./media/zephyrsso-tutorial/tutorial_general_200.png
[201]: ./media/zephyrsso-tutorial/tutorial_general_201.png
[202]: ./media/zephyrsso-tutorial/tutorial_general_202.png
[203]: ./media/zephyrsso-tutorial/tutorial_general_203.png
