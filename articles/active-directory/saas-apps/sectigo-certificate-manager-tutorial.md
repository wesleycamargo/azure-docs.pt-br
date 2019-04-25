---
title: 'Tutorial: Integração do Azure Active Directory com o Sectigo Certificate Manager | Microsoft Docs'
description: ‘Saiba como configurar o logon único entre o Azure Active Directory e o Sectigo Certificate Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62cd6987-3373-4b58-b1ff-589f4a3d70a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 15-04-2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a847ea34182994eb6465905e042bfa0218993491
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59999154"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Tutorial: Integração do Azure Active Directory com o Sectigo Certificate Manager

Neste tutorial, você aprenderá como integrar o Sectigo Certificate Manager ao Azure AD (Azure Active Directory).
A integração do Sectigo Certificate Manager ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Sectigo Certificate Manager.
* Você pode permitir que os usuários entrem automaticamente no Sectigo Certificate Manager (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Sectigo Certificate Manager, são necessários os seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do Sectigo Certificate Manager

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Sectigo Certificate Manager dá suporte ao SSO iniciado por **SP e IDP**

## <a name="adding-sectigo-certificate-manager-from-the-gallery"></a>Como adicionar o Sectigo Certificate Manager da galeria

Para configurar a integração do Sectigo Certificate Manager ao Azure AD, será necessário adicionar o Sectigo Certificate Manager da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Sectigo Certificate Manager da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Sectigo Certificate Manager**, selecione **Sectigo Certificate Manager** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Sectigo Certificate Manager na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Sectigo Certificate Manager, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Sectigo Certificate Manager.

Para configurar e testar o logon único do Azure AD com o Sectigo Certificate Manager, é necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Sectigo Certificate Manager](#configure-sectigo-certificate-manager-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do Sectigo Certificate Manager](#create-sectigo-certificate-manager-test-user)** – para ter um equivalente de Brenda Fernandes no Sectigo Certificate Manager que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Sectigo Certificate Manager, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Sectigo Certificate Manager**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    ![Informações de logon único sobre Domínio e URLs do Sectigo Certificate Manager](common/idp-relay.png)

     a. Na caixa de texto **Identificador**, digite qualquer uma das URLs:
    
    | |
    |--|
    | `https://cert-manager.com/shibboleth` |
    | `https://hard.cert-manager.com/shibboleth` |

    b. Na caixa de texto **URL de Resposta**, digite qualquer uma das URLs:

    | |
    |--|
    | `https://cert-manager.com/Shibboleth.sso/SAML2/POST` |
    | `https://hard.cert-manager.com/Shibboleth.sso/SAML2/POST` |

    c. Clique em **Definir URLs adicionais**.

    d. Na caixa de texto **Estado de Retransmissão**, digite qualquer uma das URLs:
    
    | |
    |--|
    | `https://cert-manager.com/customer/SSLSupport/idp` |
    | `https://hard.cert-manager.com/customer/SSLSupport/idp` |

5.  Se desejar configurar o aplicativo no modo iniciado pelo **SP**, realize as seguintes etapas:

    ![Informações de logon único sobre Domínio e URLs do Sectigo Certificate Manager](common/both-signonurl.png)

    Na caixa de texto **URL de Logon**, digite qualquer uma das URLs:
    
    | |
    |--|
    | `https://cert-manager.com/Shibboleth.sso/Login`|
    | `https://hard.cert-manager.com/Shibboleth.sso/Login`|

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

7. Na seção **Configurar o Sectigo Certificate Manager**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Configurar o logon único do Sectigo Certificate Manager

Para configurar o logon único no lado do **Sectigo Certificate Manager**, é necessário enviar o **Certificado (Base64)** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do Sectigo Certificate Manager](https://sectigo.com/support). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure, concedendo acesso ao Sectigo Certificate Manager.

1. No portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos** e, em seguida, **Sectigo Certificate Manager**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Sectigo Certificate Manager**.

    ![O link do Sectigo Certificate Manager na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-sectigo-certificate-manager-test-user"></a>Criar um usuário de teste do Sectigo Certificate Manager

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Sectigo Certificate Manager. Trabalhe com a  [equipe de suporte do Sectigo Certificate Manager](https://sectigo.com/support) para adicionar os usuários na plataforma do Sectigo Certificate Manager. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Sectigo Certificate Manager no Painel de Acesso, você deverá entrar automaticamente no Sectigo Certificate Manager para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

