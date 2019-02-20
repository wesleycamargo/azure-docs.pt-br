---
title: 'Tutorial: Integração do Azure Active Directory com o Shuccho Navi | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e Shuccho Navi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32b6676c-d1ec-48c2-91b1-41990ed0560c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1709abdf1d5e90cb253e67b29c0e48f6aa29a712
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56209669"
---
# <a name="tutorial-azure-active-directory-integration-with-shuccho-navi"></a>Tutorial: Integração do Azure Active Directory com o Shuccho Navi

Neste tutorial, você aprenderá a integrar o Shuccho Navi ao Azure Active Directory (Azure AD).

A integração do Shuccho Navi com o Azure Active Directory oferece os seguintes benefícios:

- Você pode controlar no Azure Active Directory quem tem acesso ao Shuccho Navi.
- Você pode habilitar seus usuários para obter logon automaticamente ao Shuccho Navi (logon único) com suas contas do Azure Active Directory.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure Active Directory com o Shuccho Navi, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura do Shuccho Navi habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Shuccho Navi da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-shuccho-navi-from-the-gallery"></a>Adicionando Shuccho Navi da galeria
Para configurar a integração do Shuccho Navi ao Azure Active Directory, você precisará adicionar o Shuccho Navi da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar Shuccho Navi da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![image](./media/shucchonavi-tutorial/selectazuread.png)

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![image](./media/shucchonavi-tutorial/a_select_app.png)
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![image](./media/shucchonavi-tutorial/a_new_app.png)

4. Na caixa de pesquisa, digite **Shuccho Navi**, selecione **Shuccho Navi** no painel de resultados, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![image](./media/shucchonavi-tutorial/tutorial_shucchonavi_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure Active Directory com o SAP Shuccho Navi, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Shuccho Navi é equivalente a um usuário do Azure Active Directory. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure Active Directory e o usuário relacionado no Shuccho Navi.

Para configurar e testar o logon único do Azure Active Directory com o Shuccho Navi, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste Shuccho Navi ](#create-a-shuccho-navi-test-user)** – para ter um equivalente de Brenda Fernandes no Shuccho Navi que esteja vinculado à representação do usuário no Azure Active Directory.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure Active Directory no portal do Azure e configurará o logon único no aplicativo portal Shuccho Navi.

**Para configurar o logon único do Microsoft Azure Active Directory com o Shuccho Navi, execute as seguintes etapas:**

1. No portal do [Azure ](https://portal.azure.com/),na página de integração do aplicativo **Shuccho Navi**, selecione **Logon único**.

    ![image](./media/shucchonavi-tutorial/b1_b2_select_sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![image](./media/shucchonavi-tutorial/b1_b2_saml_sso.png)

3. Na página **Definir logon único com SAML**, clique no botão **Editar** para abrir o diálogo **Configuração básica de SAML**.

    ![image](./media/shucchonavi-tutorial/b1-domains_and_urlsedit.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://naviauth.nta.co.jp/saml/login?ENTP_CD=<Your company code>`

    ![image](./media/shucchonavi-tutorial/tutorial_shucchonavi_url.png)

    > [!NOTE]
    > O valor da URL de logon não é real. Atualize o valor com a URL de Logon real. Contate a [equipe de suporte do Shuccho Navi](mailto:sys_ntabtm@nta.co.jp) para obter o valor.
 
5. Na página **Configurar logon único com SAML**, na seção **Certificado de assinatura SAML**, clique em **Download** para fazer o download do **XML de metadados de federação** e salve-o no seu computador.

    ![image](./media/shucchonavi-tutorial/tutorial_shucchonavi_certificate.png) 

6. Para configurar o logon único no **Shuccho Navi**, é necessário enviar o **XML de metadados de Federação** para [equipe de suporte do Shuccho Navi](mailto:sys_ntabtm@nta.co.jp). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![image](./media/shucchonavi-tutorial/d_users_and_groups.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![image](./media/shucchonavi-tutorial/d_adduser.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![image](./media/shucchonavi-tutorial/d_userproperties.png)

     a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.
 
### <a name="create-a-shuccho-navi-test-user"></a>Criar um usuário de teste Shuccho Navi

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Shuccho Navi. Trabalhe com a  [equipe de suporte do Shuccho Navi](mailto:sys_ntabtm@nta.co.jp) para adicionar os usuários na plataforma do Shuccho Navi. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Shuccho Navi.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![image](./media/shucchonavi-tutorial/d_all_applications.png)

2. Na lista de aplicativos, selecione **Shuccho Navi**.

    ![image](./media/shucchonavi-tutorial/tutorial_shucchonavi_app.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![image](./media/shucchonavi-tutorial/d_leftpaneusers.png)

4. Selecione o botão **Adicionar** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![image](./media/shucchonavi-tutorial/d_assign_user.png)

4. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

5. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar na peça Shuccho Navi no painel de acesso, obterá o logon automaticamente no seu aplicativo Shuccho Navi.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


