---
title: 'Tutorial: Integração do Azure Active Directory com o ITRP | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ITRP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 343439bbf47914404c4598369926f2f02cf5c9af
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59268273"
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Tutorial: Integração do Azure Active Directory com o ITRP

Neste tutorial, você aprenderá a integrar o ITRP ao Azure AD (Azure Active Directory).
A integração do ITRP ao Azure AD oferece os seguintes benefícios:

* É possível controlar quem terá acesso ao ITRP no Azure AD.
* Você pode permitir que os usuários sejam conectados automaticamente ao IRTP (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o ITRP, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Uma assinatura habilitada para logon único do ITRP

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O IRTP dá suporte ao SSO iniciado por **SP**

## <a name="adding-itrp-from-the-gallery"></a>Adicionar o ITRP por meio da galeria

Para configurar a integração do ITRP ao Azure AD, você precisará adicionar o ITRP da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o ITRP da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **IRTP**, selecione **IRTP** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![IRTP na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o ITRP, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ITRP.

Para configurar e testar o logon único do Azure AD com o ITRP, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do ITRP](#configure-itrp-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criação de um usuário de teste do ITRP](#create-itrp-test-user)**: para ter um equivalente da Brenda Fernandes no ITRP que está vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o ITRP, execute as seguintes etapas:

1. No [Portal do Microsoft Azure](https://portal.azure.com/), na página de integração de aplicativos do **ITRP**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do ITRP](common/sp-identifier.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<tenant-name>.itrp.com`

    b. Na caixa de texto **Identificador (ID da Entidade)**, digite uma URL usando o seguinte padrão: `https://<tenant-name>.itrp.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte do Cliente ITRP](https://www.itrp.com/support) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na seção **Certificado de Autenticação SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**.

    ![Editar o Certificado de Autenticação SAML](common/edit-certificate.png)

6. Na seção **Certificado de Autenticação SAML**, copie a **Impressão Digital** e salve-a no computador.

    ![Copiar o valor da Impressão Digital](common/copy-thumbprint.png)

7. Na seção **Configurar o ITRP**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-itrp-single-sign-on"></a>Configurar logon único do ITRP

1. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do ITRP como administrador.

1. Na barra de ferramentas na parte superior, clique em **Configurações**.

    ![ITRP](./media/itrp-tutorial/ic775570.png "ITRP")

1. No painel de navegação à esquerda, selecione **Logon Único**.

    ![Logon Único](./media/itrp-tutorial/ic775571.png "Logon Único")

1. Na seção de configuração de logon único, execute as seguintes etapas:

    ![Logon Único](./media/itrp-tutorial/ic775572.png "Logon Único")

    ![Logon Único](./media/itrp-tutorial/ic775573.png "Logon Único")

     a. Clique em **habilitado**.

    b. Na caixa de texto  **URL de Logoff Remoto**, cole o valor da **URL de Logoff** copiado no portal do Azure.

    c. Na caixa de texto  **URL de SSO do SAML**, cole o valor da **URL de Logon** copiado do portal do Azure.

    d. Na caixa de texto **Impressão Digital do Certificado**, cole o valor de **Impressão Digital** do certificado, que você copiou do Portal do Azure.

    e. Clique em **Save** (Salvar).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com.

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure ao conceder acesso ao ITRP.

1. No portal do Azure, selecione **Aplicativos empresariais**, **Todos os aplicativos** e, em seguida, escolha **ITRP**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **ITRP**.

    ![O link do ITRP na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-itrp-test-user"></a>Criar um usuário de teste do ITRP

Para permitir que os usuários do Azure AD façam logon no ITRP, eles devem ser provisionados no ITRP. No caso do ITRP, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **ITRP**.

1. Na barra de ferramentas na parte superior, clique em **Registros**.

    ![Admin](./media/itrp-tutorial/ic775575.png "Admin")

1. No menu pop-up, selecione **Pessoas**.

    ![Pessoas](./media/itrp-tutorial/ic775587.png "Pessoas")

1. Clique em **Adicionar Nova Pessoa** (“+”).

    ![Admin](./media/itrp-tutorial/ic775576.png "Admin")

1. Na caixa de diálogo Adicionar nova pessoa, execute as seguintes etapas:

    ![Usuário](./media/itrp-tutorial/ic775577.png "Usuário")

     a. Digite o **Nome** e **Email** de uma conta válida do AAD que você deseja provisionar.

    b. Clique em **Save** (Salvar).

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do ITRP ou APIs fornecidas pelo ITRP para provisionar as contas de usuário do AAD.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do ITRP no Painel de Acesso, você será conectado automaticamente ao ITRP, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
