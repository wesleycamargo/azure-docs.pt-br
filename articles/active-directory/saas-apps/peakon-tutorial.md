---
title: 'Tutorial: Integração do Azure Active Directory ao Peakon | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Peakon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a944c397-ed3f-4d45-b9b2-6d4bcb6b0a09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b841cbe838156cd00876a9a13c7a9cbe1790ea80
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57842185"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>Tutorial: Integração do Azure Active Directory ao Peakon

Neste tutorial, você aprenderá como integrar o Peakon ao Azure AD (Azure Active Directory).

A integração do Peakon ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Peakon.
- Você pode permitir que seus usuários façam logon automaticamente no Peakon (Logon único) com suas contas do AD do Azure.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Peakon, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Assinatura habilitada para um Peakon o logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Peakon da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-peakon-from-the-gallery"></a>Adicionando Peakon da galeria

Para configurar a integração do Peakon ao Azure AD, você precisa adicionar o Peakon da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Peakon da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Peakon**, selecione **Peakon** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Peakon na lista de resultados](./media/peakon-tutorial/tutorial_peakon_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Peakon com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual é o usuário de contraparte no Peakon para um usuário no Azure AD. Em outras palavras, um relacionamento de link entre um usuário do Azure AD e o usuário relacionado no Peakon precisa ser estabelecido.

Para configurar e testar o logon único do Azure AD com o Peakon, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do Peakon ](#creating-a-peakon-test-user)** - para ter um equivalente de Brenda Fernandes no Peakon que esteja vinculado à representação do usuário do Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único em seu aplicativo Peakon.

**Para configurar o logon único do Azure AD com o Peakon, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Peakon**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](common/tutorial_general_301.png)

3. Na página **Configurar Logon Único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração Básica do SAML**.

    ![Configurar o logon único](common/editconfigure.png)

4. Na seção **Configuração Básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![Domínio do pico e informações de logon único de URLs](./media/peakon-tutorial/tutorial_peakon_url.png)

     a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://app.peakon.com/saml/<companyid>/metadata`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://app.peakon.com/saml/<companyid>/assert`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Domínio do pico e informações de logon único de URLs](./media/peakon-tutorial/tutorial_peakon_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL: `https://app.peakon.com/login`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador real e a URL de resposta, explicados no tutorial.

6. Na seção **Certificado de assinatura SAML**, clique em **Download** para fazer o download do **Certificate (Raw)** e salve o arquivo de certificado em seu computador.

    ![O link de download do Certificado](./media/peakon-tutorial/tutorial_peakon_certificate.png) 

7. Na seção **Configurar o pico**, copie o URL apropriado de acordo com sua exigência.

     a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

    ![Configuração de Peakon](common/configuresection.png)

8. Em uma janela diferente do navegador da Web, faça login no Peakon como administrador.

9. Na barra de menu à esquerda da página, clique em  **Configuração** e navegue até  **Integrações**.

    ![A configuração](./media/peakon-tutorial/tutorial_peakon_config.png)

10. Na página **Integrações**, clique em **Single Sign-On**.

    ![O único](./media/peakon-tutorial/tutorial_peakon_single.png)

11. Na seção **Single Sign-On**, clique em **Ativar**.

    ![Permitir](./media/peakon-tutorial/tutorial_peakon_enable.png)

12. Na seção **Single sign-on para funcionários que usam o SAML**, execute as seguintes etapas:

    ![O saml](./media/peakon-tutorial/tutorial_peakon_saml.png)

     a. Na caixa de texto **SSO Login URL**, cole o valor de **Login URL**, copiado do portal do Azure.

    b. Na caixa de texto **SSO Logout URL**, cole o valor de **URL de logoff**, copiado do portal do Azure.

    c. Clique em **Escolha o arquivo** para carregar o certificado que você baixou do portal do Azure na caixa Certificado.

    d. Clique no  **ícone** para copiar o **ID de entidade** e cole na caixa de texto  **Identificador** na seção  **Configuração básica do SAML**  no portal do Azure.

    e. Clique no  **ícone** para copiar o **URL de resposta (ACS)** e cole na caixa de texto  **Responder URL** na seção  **Configuração básica do SAML** Portal do Azure.

    f. Clique em **Salvar**

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Criar um usuário do AD do Azure][100]

2. Selecione **Novo usuário** na parte superior da tela.

    ![Criação de um usuário de teste do AD do Azure](common/create_aaduser_01.png) 

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![Criação de um usuário de teste do AD do Azure](common/create_aaduser_02.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No **nome de usuário** , digite **brendafernandes\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.

### <a name="creating-a-peakon-test-user"></a>Criando um usuário de teste Peakon

Para permitir que usuários do Azure AD entrem no Peakon, eles devem ser provisionados no Peakon.  
No caso do Peakon, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no seu site da empresa Peakon como administrador.

2. Na barra de menu à esquerda da página, clique em  **Configuração** e navegue para  **Funcionários**.

    ![O funcionário](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. No canto superior direito da página, clique em  **Adicionar empregado**.

      ![Adicionar funcionário](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. Na página de diálogo **Novo funcionário**, execute as seguintes etapas:

     ![O novo funcionário](./media/peakon-tutorial/tutorial_peakon_create.png)

     a. No **nome** caixa de texto, digite o nome como **Brenda** e o sobrenome como **simon**.

    b. No **E-mail** caixa de texto, digite o endereço de email, como **Brendafernandes\@contoso.com**.

    c. Clique em **criar funcionário**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você deve habilitar Britta Simon usar logon único do Azure, concedendo acesso ao Peakon.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **Peakon**.

    ![Configurar o logon único](./media/peakon-tutorial/tutorial_peakon_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Peakon no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Peakon.
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
