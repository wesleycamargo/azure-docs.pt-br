---
title: 'Tutorial: Integração do Azure Active Directory ao Egnyte | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Egnyte.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2018
ms.author: jeedes
ms.openlocfilehash: 33989d04cba06c789089dfc0db62d177d258292a
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2018
ms.locfileid: "51976093"
---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Tutorial: integração do Active Directory do Azure ao Egnyte

Neste tutorial, você aprenderá a integrar o Egnyte ao Azure AD (Azure Active Directory).

A integração do Egnyte ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Egnyte.
- Você pode permitir que seus usuários entrem automaticamente no Egnyte (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Egnyte, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Egnyte

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Egnyte da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-egnyte-from-the-gallery"></a>Adicionando Egnyte da galeria

Para configurar a integração do Egnyte ao Azure AD, você precisará adicionar o Egnyte da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Egnyte da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Egnyte**, selecione **Egnyte** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Egnyte na lista de resultados](./media/egnyte-tutorial/tutorial_egnyte_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Egnyte, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Egnyte é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Egnyte.

Para configurar e testar o logon único do Azure AD com o Egnyte, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do Egnyte](#creating-an-egnyte-test-user)** – para ter um equivalente de Brenda Fernandes no Egnyte que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Egnyte.

**Para configurar o logon único do Azure AD com o Egnyte, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **Egnyte**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](common/tutorial_general_301.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Configurar o logon único](common/editconfigure.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de domínio único do Egnyte Domain e URLs](./media/egnyte-tutorial/tutorial_egnyte_url.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.egnyte.com`

    > [!NOTE] 
    > Esse valor não é real. Atualize esse valor com a URL de Logon real. Entre em contato com a [equipe de suporte do Cliente do Egnyte](https://www.egnyte.com/corp/contact_egnyte.html) para obter esse valor. 

5. Na página **Certificado de assinatura SAML**, na seção **Certificado de assinatura SAML**, clique em **Download** para fazer o download do **Certificado (Base64)** e salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/egnyte-tutorial/tutorial_egnyte_certificate.png) 

6. Sobre a seção **configurar Egnyte**, copie a URL apropriada, de acordo com suas necessidades.

     a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

    ![Configuração do Egnyte](common/configuresection.png)

7. Em uma janela diferente do navegador da Web, faça logon no site da empresa do Egnyte como administrador.

8. Clique em **Configurações**.
   
    ![Configurações](./media/egnyte-tutorial/ic787819.png "Configurações")

9. No menu, clique em **Configurações**.

    ![Configurações](./media/egnyte-tutorial/ic787820.png "Configurações")

10. Clique na guia **Configuração** e, em seguida, clique em **Segurança**.

    ![Segurança](./media/egnyte-tutorial/ic787821.png "Segurança")

11. Na seção **Autenticação de Logon Único** , realize as seguintes etapas:

    ![Autenticação de Logon Único](./media/egnyte-tutorial/ic787822.png "Autenticação de Logon Único")   
    
     a. Para **Autenticação de logon único**, selecione **SAML 2.0**.
   
    b. Para **Provedor de identidade**, selecione **AzureAD**.
   
    c. Cole o **URL de login** copiado do portal do Azure na caixa de texto **URL de login do provedor de identidade**.
   
    d. Cole o **Identificador do Azure AD** que você copiou do portal do Azure na caixa de texto **Identidade da entidade do provedor de identidade**.
      
    e. Abra seu certificado codificado em base-64 no bloco de notas baixado do portal do Azure, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Certificado do provedor de identidade**.
   
    f. Para **Mapeamento de usuário padrão**, selecione **Endereço de email**.
   
    g. Para **Usar valor do emissor específico do domínio**, selecione **desabilitado**.
   
    h. Clique em **Salvar**.

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

### <a name="creating-an-egnyte-test-user"></a>Como criar um usuário de teste do Egnyte

Para permitir que os usuários do Azure AD façam logon no Egnyte, eles deverão ser provisionados no Egnyte. No caso do Egnyte, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Faça logon no site de empresa do **Egnyte** como administrador.

2. Vá para **Configurações \> Usuários e Grupos**.

3. Clique em **Adicionar Novo Usuário**e selecione o tipo de usuário que deseja adicionar.
   
    ![Usuários](./media/egnyte-tutorial/ic787824.png "Usuários")

4. Na seção **Novo Usuário Avançado**, execute as seguintes etapas:
    
    ![Novo Usuário Padrão](./media/egnyte-tutorial/ic787825.png "Novo Usuário Padrão")   

     a. Na caixa de texto **E-mail**, insira o e-mail do usuário como**Brittasimon@contoso.com**.

    b. Na caixa de texto **Username**, insira o nome de usuário como **Brittasimon**.

    c. Selecione **logon único** como **tipo de autenticação**.
   
    d. Clique em **Salvar**.
    
    >[!NOTE]
    >O titular da conta do Active Directory do Azure receberá um email de notificação.
    >

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do Egnyte ou as APIs fornecidas pelo Egnyte para provisionar as contas de usuário do AAD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Egnyte.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **Egnyte**.

    ![Configurar o logon único](./media/egnyte-tutorial/tutorial_egnyte_app.png)

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Egnyte no Painel de Acesso, você deverá ser conectado automaticamente a seu aplicativo do Egnyte.
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
