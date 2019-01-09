---
title: 'Tutorial: Integração do Azure Active Directory com GitHub | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o GitHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeedes
ms.openlocfilehash: 41e8fb40f07a88cb6fa2108a38db7f973fccd2f9
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53607791"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Tutorial: Integração do Azure Active Directory com GitHub

Neste tutorial, você aprenderá a integrar o GitHub ao Azure AD (Azure Active Directory).

A integração do GitHub ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem terá acesso ao GitHub.
- Você pode permitir que usuários façam logon automaticamente no GitHub usando logon único com as respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o GitHub, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único no GitHub

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.
 O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o GitHub da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-github-from-the-gallery"></a>Adicionar o GitHub da galeria
Para configurar a integração do GitHub ao Azure AD, você precisará adicionar o GitHub da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o GitHub por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![image](./media/github-tutorial/selectazuread.png)

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![image](./media/github-tutorial/a_select_app.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![image](./media/github-tutorial/a_new_app.png)

4. Na caixa de pesquisa, digite **GitHub**, selecione **GitHub** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![image](./media/github-tutorial/tutorial_github_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o GitHub, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do GitHub é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no GitHub.

Para configurar e testar o logon único do Azure AD com o GitHub, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do GitHub](#create-a-github-test-user)** – para ter um equivalente de Brenda Fernandes no GitHub que esteja vinculado à representação dela no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único em seu aplicativo GitHub.

**Para configurar o logon único do Azure AD com o GitHub, execute as seguintes etapas:**

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **GitHub**, clique em **Logon único**.

    ![image](./media/github-tutorial/b1_b2_select_sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![image](./media/github-tutorial/b1_b2_saml_sso.png)

3. Na página **Definir logon único com SAML**, clique no botão **Editar** para abrir o diálogo **Configuração básica de SAML**.

    ![image](./media/github-tutorial/b1-domains_and_urlsedit.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![image](./media/github-tutorial/tutorial_github_url.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://github.com/orgs/<entity-id>/sso`

    b. Na caixa de texto **Identificador (ID da Entidade)**, digite uma URL usando o seguinte padrão: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Observe que esses não são os valores reais. É necessário atualizar esses valores com a URL de Entrada e o Identificador reais. Aqui, sugerimos que você use o valor exclusivo de cadeia de caracteres no Identificador. Acesse a seção Administração do GitHub para recuperar esses valores.

5. O aplicativo GitHub espera as asserções SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Clique no botão **Editar** para abrir a caixa de diálogo **Atributos de usuário**.

    ![image](./media/github-tutorial/i3-attribute.png)

6. Na seção **Declarações de Usuário** do diálogo **Atributos de Usuário**, configure o atributo de token SAML conforme mostrado na imagem acima e execute as seguintes etapas:

     a. Clique em **Editar** para abrir a caixa de diálogo **Gerenciar declarações de usuário**.

    ![image](./media/github-tutorial/i2-attribute.png)

    ![image](./media/github-tutorial/i4-attribute.png)

    b. Na lista **Atributo de origem**, selecione o valor do atributo.

    c. Clique em **Salvar**.

7. Na seção **Certificado de Assinatura SAML**, clique em **Baixar** para baixar o **certificado (Base64)** e salve-o em seu computador.

    ![image](./media/github-tutorial/tutorial_github_certficate.png)

8. Na seção **Configurar o GitHub**, copie a URL apropriada, de acordo com suas necessidades.

     a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

    ![image](./media/github-tutorial/d1_samlsonfigure.png)

9. Em outra janela do navegador da Web, faça logon em seu site de organização do GitHub como administrador.

10. Navegue até **Configurações** e clique em **Segurança**

    ![Configurações](./media/github-tutorial/tutorial_github_config_github_03.png)

11. Marque a caixa **Habilitar autenticação SAML**, revelando os campos de configuração de Logon Único. Em seguida, use o valor de URL de logon único para atualizar a URL de Logon Único na configuração do Azure AD.

    ![Configurações](./media/github-tutorial/tutorial_github_config_github_13.png)

12. Configure os seguintes campos:

    ![Configurações](./media/github-tutorial/tutorial_github_config_github_051.png)

     a. Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** que você copiou do portal do Azure.

    b. Na caixa de texto **Emissor**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    c. Abra o certificado baixado do portal do Azure no bloco de notas e cole o conteúdo na caixa de texto **Certificado Público**.

    d. Clique no ícone **Editar** para editar o **Método de Assinatura** e **Método de Resumo** do **RSA-SHA1** e **SHA1** para **RSA-SHA256** e **SHA256** conforme mostrado abaixo.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

13. Clique em **Testar configuração de SAML** para confirmar que não há falhas de validação ou erros durante o SSO.

    ![Configurações](./media/github-tutorial/tutorial_github_config_github_06.png)

14. Clique em **Salvar**

> [!NOTE]
> O logon único no GitHub se autentica em uma organização específica no GitHub e não substitui a autenticação do GitHub em si. Portanto, se a sessão do github.com do usuário expirou, você poderá ser solicitado a autenticar-se com a ID/senha do GitHub durante o processo de logon único.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![image](./media/github-tutorial/d_users_and_groups.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![image](./media/github-tutorial/d_adduser.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![image](./media/github-tutorial/d_userproperties.png)

     a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.

### <a name="create-a-github-test-user"></a>Criar um usuário de teste do GitHub

O objetivo desta seção é criar um usuário chamado Britta Simon no GitHub. O GitHub dá suporte ao provisionamento automático de usuário, que está habilitado por padrão. Você pode encontrar [aqui](github-provisioning-tutorial.md) mais detalhes de como configurar o provisionamento automático de usuário.

**Se você precisar criar o usuário manualmente, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do GitHub como administrador.

2. Clique em **Pessoas**.

    ![Pessoas](./media/github-tutorial/tutorial_github_config_github_08.png "Pessoas")

3. Clique em **Convidar membro**.

    ![Convidar Usuários](./media/github-tutorial/tutorial_github_config_github_09.png "Convidar Usuários")

4. Na caixa de diálogo **Convidar membro**, execute as seguintes etapas:

     a. Na caixa de texto **Email**, digite o endereço de email da conta de Brenda Fernandes.

    ![Convidar Pessoas](./media/github-tutorial/tutorial_github_config_github_10.png "Convidar Pessoas")

    b. Clique em **Enviar Convite**.

    ![Convidar Pessoas](./media/github-tutorial/tutorial_github_config_github_11.png "Convidar Pessoas")

    > [!NOTE]
    > O titular da conta do Active Directory do Azure receberá um email e seguirá um link para confirmar a conta antes que ela se torne ativa.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Britta Simon use o logon único do Azure concedendo-lhe acesso ao GitHub.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![image](./media/github-tutorial/d_all_applications.png)

2. Na lista de aplicativos, selecione **GitHub**.

    ![image](./media/github-tutorial/tutorial_github_app.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![image](./media/github-tutorial/d_leftpaneusers.png)

4. Selecione o botão **Adicionar** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![image](./media/github-tutorial/d_assign_user.png)

4. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

5. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do GitHub no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo GitHub.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


