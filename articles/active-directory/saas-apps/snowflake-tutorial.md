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
ms.date: 09/10/2018
ms.author: jeedes
ms.openlocfilehash: c611fd7893a96113a4a9f2454bcd0b11db02be29
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/14/2018
ms.locfileid: "45605090"
---
# <a name="tutorial-azure-active-directory-integration-with-snowflake"></a>Tutorial: Integração do Active Directory do Azure com o Snowflake

Neste tutorial, você aprenderá como integrar o Snowflake ao Azure AD (Azure Active Directory).

A integração do Snowflake com o Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Snowflake.
- Você pode permitir que seus usuários façam logon automaticamente no Snowflake (Logon único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Snowflake, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Assinatura habilitada para um floco de neve logon único
- Clientes que não têm uma conta do Snowflake e gostariam de experimentá-lo por meio da Galeria de aplicativos do Microsoft Azure Active Directory, consulte [isso](https://trial.snowflake.net/?cloud=azure&utm_source=azure-marketplace&utm_medium=referral&utm_campaign=self-service-azure-mp) link.

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

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Crie um usuário de teste do Snowflake](#create-a-snowflake-test-user)** - para ter um equivalente de Brenda Fernandes no floco de neve que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único em seu aplicativo do Snowflake.

**Para configurar o logon único do Azure AD com o Snowflake, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Floco de neve**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/snowflake-tutorial/tutorial_snowflake_samlbase.png)

3. Em **Snowflake Domain e URLs do**, realize as seguintes etapas:

    ![Informações de logon de único de URLs e domínio do floco de neve](./media/snowflake-tutorial/tutorial_snowflake_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<SNOWFLAKE-URL>.snowflakecomputing.com`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/login`

4. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon de único de URLs e domínio do floco de neve](./media/snowflake-tutorial/tutorial_snowflake_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<SNOWFLAKE-URL>.snowflakecomputing.com`
     
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada.

5. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/snowflake-tutorial/tutorial_snowflake_certificate.png) 

6. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/snowflake-tutorial/tutorial_general_400.png)
    
7. Na seção **Configuração do floco de neve**, clique em **Configurar floco de neve** para abrir a janela **Configurar logon**. Copie a **URL de serviço de logon único SAML** da **seção de Referência Rápida.**

    ![Configuração de floco de neve](./media/snowflake-tutorial/tutorial_snowflake_configure.png) 

8. Em uma janela diferente do navegador da Web, efetue login no Snowflake como um administrador de segurança.

9. Execute a consulta SQL na planilha, definindo o valor do **certificado** para o **certificado baixado** e **ssoUrl** para o **SAML Single Sign-On URL copiado do serviço** do Microsoft Azure Active Directory para o valor, conforme mostrado abaixo.

    ![Sql Snowflake](./media/snowflake-tutorial/tutorial_snowflake_sql.png) 

    ```
    use role accountadmin;
    alter account set saml_identity_provider = '{
    "certificate": "<Paste the content of downloaded certificate from Azure portal>",
    "ssoUrl":"<SAML single sign-on service URL value which you have copied from the Azure portal>",
    "type":"custom",
    "label":"AzureAD"
    }';
    alter account set sso_login_page = TRUE;
    ```

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/snowflake-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/snowflake-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/snowflake-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/snowflake-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-snowflake-test-user"></a>Criar um usuário de teste de floco de neve

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

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure, concedendo acesso ao Floco de Neve.

![Atribuir a função de usuário][200] 

**Para atribuir Britta Simon ao floco de neve, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **floco de neve**.

    ![O link de floco de neve na lista de aplicativos](./media/snowflake-tutorial/tutorial_snowflake_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco de floco de neve no Painel de Acesso, você deverá fazer logon automaticamente no aplicativo do Snowflake.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/snowflake-tutorial/tutorial_general_01.png
[2]: ./media/snowflake-tutorial/tutorial_general_02.png
[3]: ./media/snowflake-tutorial/tutorial_general_03.png
[4]: ./media/snowflake-tutorial/tutorial_general_04.png

[100]: ./media/snowflake-tutorial/tutorial_general_100.png

[200]: ./media/snowflake-tutorial/tutorial_general_200.png
[201]: ./media/snowflake-tutorial/tutorial_general_201.png
[202]: ./media/snowflake-tutorial/tutorial_general_202.png
[203]: ./media/snowflake-tutorial/tutorial_general_203.png

