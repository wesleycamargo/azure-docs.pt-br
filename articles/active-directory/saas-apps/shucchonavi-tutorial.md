---
title: 'Tutorial: Integração do Azure Active Directory com o Shuccho Navi | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e Shuccho Navi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 32b6676c-d1ec-48c2-91b1-41990ed0560c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: e4fcc261a2952224870d5f1a0db4b9ddb666034e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57850336"
---
# <a name="tutorial-azure-active-directory-integration-with-shuccho-navi"></a>Tutorial: Integração do Azure Active Directory com o Shuccho Navi

Neste tutorial, você aprenderá a integrar o Shuccho Navi ao Azure Active Directory (Azure AD).
A integração do Shuccho Navi com o Azure Active Directory oferece os seguintes benefícios:

* Você pode controlar no Azure Active Directory quem tem acesso ao Shuccho Navi.
* Você pode habilitar seus usuários ser automaticamente conectado no Shuccho Navi (logon único) com suas contas do AD do Azure.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure Active Directory com o Shuccho Navi, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único Shuccho Navi

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* Dá suporte a Shuccho Navi **SP** SSO iniciado

## <a name="adding-shuccho-navi-from-the-gallery"></a>Adicionando Shuccho Navi da galeria

Para configurar a integração do Shuccho Navi ao Azure Active Directory, você precisará adicionar o Shuccho Navi da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar Shuccho Navi da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Shuccho Navi**, selecione **Shuccho Navi** no painel de resultados, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Shuccho Navi na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com Shuccho Navi com base em um usuário de teste chamado **Britta Simon**.
Para logon único funcione, uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado do Shuccho Navi precisa ser estabelecida.

Para configurar e testar o logon único do Azure Active Directory com o Shuccho Navi, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único de Navi Shuccho](#configure-shuccho-navi-single-sign-on)**  – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste Shuccho Navi](#create-shuccho-navi-test-user)**  – para ter um equivalente de Britta Simon no Shuccho Navi que esteja vinculado à representação de usuário do AD do Azure.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com Shuccho Navi, execute as seguintes etapas:

1. No portal do [Azure ](https://portal.azure.com/),na página de integração do aplicativo **Shuccho Navi**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único Shuccho Navi domínio e URLs](common/sp-signonurl.png)

    Na caixa de texto **URL de login**, digite um URL usando o seguinte padrão: `https://naviauth.nta.co.jp/saml/login?ENTP_CD=<Your company code>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Entre em contato com [equipe de suporte do cliente de Navi Shuccho](mailto:sys_ntabtm@nta.co.jp) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Sobre o **configurar Shuccho Navi** seção, copie a URL (s) apropriado, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-shuccho-navi-single-sign-on"></a>Configurar o logon único Shuccho Navi

Para configurar o logon único no **Shuccho Navi** , é necessário enviar o **XML Metadata** e apropriado URLs copiadas do portal do Azure para [equipe de suporte do Shuccho Navi](mailto:sys_ntabtm@nta.co.jp). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No **nome de usuário** tipo de campo **brendafernandes\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Shuccho Navi.

1. No portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Shuccho Navi**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Shuccho Navi**.

    ![O link Shuccho Navi na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-shuccho-navi-test-user"></a>Criar usuário de teste Shuccho Navi

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Shuccho Navi. Trabalhe com a  [equipe de suporte do Shuccho Navi](mailto:sys_ntabtm@nta.co.jp) para adicionar os usuários na plataforma do Shuccho Navi. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco Shuccho Navi no painel de acesso, você deve ser conectado automaticamente ao Shuccho Navi para os quais você configura o logon único. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
