---
title: 'Tutorial: integração do Microsoft Azure Active Directory com o Allbound SSO | Microsoft Docs'
description: Saiba como configurar o logon único entre o Microsoft Azure Active Directory e o Allbound SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 15011ddf-941f-4da2-b993-40ad94a08e42
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: 3446ccae631efb763d9a216aac313abf9e1af72f
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52166651"
---
# <a name="tutorial-azure-active-directory-integration-with-allbound-sso"></a>Tutorial: integração do Microsoft Azure Active Directory com o Allbound SSO

Neste tutorial, você aprenderá a integrar o Allbound SSO ao Microsoft Azure Active Directory (Azure Active Directory).

A integração do Allbound SSO com o Microsoft Azure Active Directory oferece os seguintes benefícios:

- É possível controlar, no Microsoft Azure Active Directory, quem tem acesso ao Allbound SSO.
- Você pode permitir que seus usuários façam logon automaticamente no Allbound SSO (logon único) com suas contas do Microsoft Azure Active Directory.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure Active Directory com o Allbound SSO, são necessários os seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Allbound SSO habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Allbound SSO da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-allbound-sso-from-the-gallery"></a>Adicionando o Allbound SSO da galeria

Para configurar a integração do Allbound SSO com o Microsoft Azure Active Directory, é necessário adicionar o Allbound SSO da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Allbound SSO da galeria, siga as etapas abaixo:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Allbound SSO**, selecione **Allbound SSO** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Allbound SSO na lista de resultados](./media/allbound-sso-tutorial/tutorial_allbound-sso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure Active Directory com o Allbound SSO, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Allbound SSO é equivalente a um usuário do Microsoft Azure Active Directory. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Microsoft Azure Active Directory e o usuário relacionado do Allbound SSO.

Para configurar e testar o logon único do Azure Active Directory com o Allbound SSO, você precisa completar os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste Allbound SSO](#creating-an-allbound-sso-test-user)** - para ter um equivalente de Britta Simon no Allbound SSO vinculado à representação de usuário do Microsoft Azure Active Directory.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Microsoft Azure AD no portal do Azure e configurará o logon único em seu aplicativo Allbound SSO.

**Para configurar o logon único do Microsoft Azure Active Directory com o Allbound SSO, siga as etapas abaixo:**

1. No portal do Azure, na página de integração do aplicativo **Allbound SSO**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](common/tutorial_general_301.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Configurar o logon único](common/editconfigure.png)

4. Na seção **Configuração Básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![Informações do domínio Allbound SSO Domain e URLs single sign-on](./media/allbound-sso-tutorial/tutorial_allbound-sso_url.png)

     a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.allbound.com/`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<SUBDOMAIN>.allbound.com/acs`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações do domínio Allbound SSO Domain e URLs single sign-on](./media/allbound-sso-tutorial/tutorial_allbound-sso_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.allbound.com/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Contate a [equipe de suporte ao Cliente do Allbound SSO](mailto:engineering@allbound.com) para obter esses valores.

6. Na página **Certificado de Assinatura SAML**, na seção **Certificado de Assinatura SAML**, clique em **Download** para fazer o download do **XML de Metadados da Federação** e salve o arquivo de metadados no computador.

    ![O link de download do Certificado](./media/allbound-sso-tutorial/tutorial_allbound-sso_certificate.png) 

7. Na seção **Configurar o Allbound SSO**, copie o URL apropriado de acordo com sua exigência.

     a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

    ![Configuração de SSO Allbound](common/configuresection.png)

8. Para configurar o logon único no **Allbound SSO**, é necessário enviar o **XML de metadados de Federação** baixado para a [equipe de suporte do Allbound](mailto:engineering@allbound.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

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

### <a name="creating-an-allbound-sso-test-user"></a>Criando um usuário de teste do SSO Allbound

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Allbound. O Allbound dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o Allbound, caso ele ainda não exista.
>[!Note]
>Se você precisar criar um usuário manualmente, contate a  [equipe de suporte do Allbound - SSO](mailto:engineering@allbound.com).

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que o Brenda Fernandes use o logon único do Azure, concedendo acesso ao Allbound SSO.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **Allbound SSO**.

    ![Configurar o logon único](./media/allbound-sso-tutorial/tutorial_allbound-sso_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar na peça Allbound SSO no Painel de Acesso, seu logon deverá ser feito automaticamente no aplicativo Allbound SSO.
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
