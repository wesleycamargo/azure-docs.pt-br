---
title: 'Tutorial: Integração do Azure Active Directory ao GitHub | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o GitHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2018
ms.author: jeedes
ms.openlocfilehash: b2a90a4599e5d07baba721d5649b72422dc5cb4d
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818738"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Tutorial: Integração do Azure Active Directory ao GitHub

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

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o GitHub da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-github-from-the-gallery"></a>Adicionar o GitHub da galeria

Para configurar a integração do GitHub ao Azure AD, você precisará adicionar o GitHub da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o GitHub por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **GitHub**, selecione **GitHub** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![GitHub na lista de resultados](./media/github-tutorial/tutorial_github_addfromgallery.png)

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

1. No portal do Azure, na página de integração de aplicativos do **GitHub**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Caixa de diálogo Logon único](./media/github-tutorial/tutorial_github_samlbase.png)

3. Na seção **URLs e Domínio do GitHub**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do GitHub](./media/github-tutorial/tutorial_github_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://github.com/orgs/<entity-id>/sso`

    b. Na caixa de texto **Identificador (ID da Entidade)**, digite uma URL usando o seguinte padrão: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Observe que esses não são os valores reais. É necessário atualizar esses valores com a URL de Entrada e o Identificador reais. Aqui, sugerimos que você use o valor exclusivo de cadeia de caracteres no Identificador. Acesse a seção Administração do GitHub para recuperar esses valores.

4. Na seção **Atributos do Usuário**, selecione **Identificador de Usuário** como user.mail.

    ![Configurar o logon único](./media/github-tutorial/tutorial_github_attribute_new01.png)

5. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/github-tutorial/tutorial_github_certificate.png) 

6. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/github-tutorial/tutorial_general_400.png)

7. Na seção **Configuração do GitHub**, clique em **Configurar o GitHub** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do GitHub](./media/github-tutorial/tutorial_github_configure.png) 

8. Em outra janela do navegador da Web, faça logon em seu site de organização do GitHub como administrador.

9. Navegue até **Configurações** e clique em **Segurança**

    ![Configurações](./media/github-tutorial/tutorial_github_config_github_03.png)

10. Marque a caixa **Habilitar autenticação SAML**, revelando os campos de configuração de Logon Único. Em seguida, use o valor de URL de logon único para atualizar a URL de Logon Único na configuração do Azure AD.

    ![Configurações](./media/github-tutorial/tutorial_github_config_github_13.png)

11. Configure os seguintes campos:

    a. Na caixa de texto **URL de Logon Único**, cole o valor da **URL do Serviço de Logon Único do SAML** copiado do portal do Azure.

    b. Na caixa de texto **Emissor**, cole o valor da **ID da Entidade SAML** copiada do portal do Azure.

    c. Abra o certificado baixado do portal do Azure no bloco de notas e cole o conteúdo na caixa de texto **Certificado Público**.

    ![Configurações](./media/github-tutorial/tutorial_github_config_github_051.png)

12. Clique em **Testar configuração de SAML** para confirmar que não há falhas de validação ou erros durante o SSO.

    ![Configurações](./media/github-tutorial/tutorial_github_config_github_06.png)

13. Clique em **Salvar**

> [!NOTE]
> O logon único no GitHub se autentica em uma organização específica no GitHub e não substitui a autenticação do GitHub em si. Portanto, se a sessão do usuário GitHub.com tiver expirado, poderá ser solicitado que você se autentique com a ID e senha do GitHub durante o processo de logon único.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/github-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/github-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/github-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/github-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.

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

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao GitHub, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **GitHub**.

    ![O link do GitHub na lista de Aplicativos](./media/github-tutorial/tutorial_github_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do GitHub no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo GitHub.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/github-tutorial/tutorial_general_01.png
[2]: ./media/github-tutorial/tutorial_general_02.png
[3]: ./media/github-tutorial/tutorial_general_03.png
[4]: ./media/github-tutorial/tutorial_general_04.png

[100]: ./media/github-tutorial/tutorial_general_100.png

[200]: ./media/github-tutorial/tutorial_general_200.png
[201]: ./media/github-tutorial/tutorial_general_201.png
[202]: ./media/github-tutorial/tutorial_general_202.png
[203]: ./media/github-tutorial/tutorial_general_203.png