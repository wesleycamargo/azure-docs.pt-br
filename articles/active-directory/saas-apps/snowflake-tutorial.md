---
title: 'Tutorial: Integração do Active Directory do Azure com o Snowflake | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Snowflake.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3488ac27-0417-4ad9-b9a3-08325fe8ea0d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: jeedes
ms.openlocfilehash: 6e1f76548e2433f9bc4b0b26b0894a92b7ec0aa0
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092552"
---
# <a name="tutorial-azure-active-directory-integration-with-snowflake"></a>Tutorial: Integração do Active Directory do Azure com o Snowflake

Neste tutorial, você aprenderá como integrar o Snowflake ao Azure AD (Azure Active Directory).

A integração do Snowflake com o Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Snowflake.
- Você pode permitir que seus usuários façam logon automaticamente no Snowflake (Logon único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Snowflake, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Assinatura habilitada para um floco de neve logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando floco de neve da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-snowflake-from-the-gallery"></a>Adicionando floco de neve da galeria

Para configurar a integração do Snowflake ao Azure AD, você precisa adicionar o Floco de Neve da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar um floco de neve da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **floco de neve**, selecione **floco de neve** no painel de resultados, em seguida, clique em **Add** botão para adicionar o aplicativo.

    ![Floco de neve na lista de resultados](./media/snowflake-tutorial/tutorial_snowflake_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Jostle, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Jostle é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Samanage.

Para configurar e testar o logon único do Azure AD com o Snowflake, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Crie um usuário de teste do Snowflake](#creating-snowflake-test-user)** - para ter um equivalente de Brenda Fernandes no floco de neve que esteja vinculado à representação de usuário do Microsoft Azure Active Directory.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único em seu aplicativo do Snowflake.

**Para configurar o logon único do Azure AD com o Snowflake, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Floco de neve**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](common/tutorial_general_301.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Configurar o logon único](common/editconfigure.png)

4. Na seção **Configuração Básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![Informações de logon de único de URLs e domínio do floco de neve](./media/snowflake-tutorial/tutorial_snowflake_url1.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<SNOWFLAKE-URL>.snowflakecomputing.com`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/login`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon de único de URLs e domínio do floco de neve](./media/snowflake-tutorial/tutorial_snowflake_url2.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<SNOWFLAKE-URL>.snowflakecomputing.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada.

6. Na página **Certificado de assinatura SAML**, na seção **Certificado de assinatura SAML**, clique em **Download** para fazer o download do **Certificado (Base64)** e salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/snowflake-tutorial/tutorial_snowflake_certificate.png) 

7. Na seção**Configurar floco de Snowflake**, copie a URL apropriada, de acordo com suas necessidades.

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

    ![Configuração de floco de neve](common/configuresection.png)

8. Em uma janela diferente do navegador da Web, efetue login no Snowflake como um administrador de segurança.

9. **Alterne a função** à **ACCOUNTADMIN**, clicando no **perfil** no lado superior direito da página.

    > [!NOTE]
    > Isso é separado do contexto selecionado no canto superior direito em seu nome de usuário
    
    ![O administrador de Snowflake ](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

10. Abra o **certificado de Base 64 baixado** no bloco de notas. Copie o valor insira "---BEGIN CERTIFICATE---" e "---END CERTIFICATE---" e cole em aspas lado **certificado** abaixo. Em **ssoUrl**, cole **URL de logon** valor que você copiou do portal do Azure. Selecione o **Todas as consultas** e clique em **Executar**.

    ![Sql Snowflake](./media/snowflake-tutorial/tutorial_snowflake_sql.png)

    ```
    use role accountadmin;
    alter account set saml_identity_provider = '{
    "certificate": "<Paste the content of downloaded certificate from Azure portal>",
    "ssoUrl":"<Login URL value which you have copied from the Azure portal>",
    "type":"custom",
    "label":"AzureAD"
    }';
    alter account set sso_login_page = TRUE;
    ```

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Criar um usuário do AD do Azure][100]

2. Selecione **Novo usuário** na parte superior da tela.

    ![Criação de um usuário de teste do AD do Azure](common/create_aaduser_01.png) 

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![Criação de um usuário de teste do AD do Azure](common/create_aaduser_02.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.

### <a name="creating-snowflake-test-user"></a>Criar um usuário de teste do Snowflake

Para habilitar usuários do Microsoft Azure Active Directory para fazer logon no Snowflake, eles devem ser provisionados no Snowflake. No Snowflake, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon no Snowflake como Administrador de Segurança.

2. **Alterne a função** à **ACCOUNTADMIN**, clicando no **perfil** no lado superior direito da página.  

    ![O administrador de Snowflake ](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

3. Crie o usuário executando o abaixo de consulta SQL, garantindo que o "Nome de logon" é definido como o nome de usuário do Microsoft Azure Active Directory na planilha conforme mostrado abaixo.

    ![O adminsql Snowflake ](./media/snowflake-tutorial/tutorial_snowflake_usersql.png)

    ```
    use role accountadmin;
    CREATE USER britta_simon PASSWORD = '' LOGIN_NAME = 'BrittaSimon@contoso.com' DISPLAY_NAME = 'Britta Simon';
    ```

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure, concedendo acesso ao Floco de Neve.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **floco de neve**.

    ![Configurar o logon único](./media/snowflake-tutorial/tutorial_snowflake_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco de floco de neve no Painel de Acesso, você deverá fazer logon automaticamente no aplicativo do Snowflake.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

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
