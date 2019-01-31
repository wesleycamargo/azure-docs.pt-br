---
title: 'Tutorial: Integração do Azure Active Directory com o Tidemark | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o Tidemark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 5cf80d4e-6e8b-48ec-81c8-27872af5e5d5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.openlocfilehash: 8e04f6801a8ed0343f964dbfe9eb66c8938218f3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55188245"
---
# <a name="tutorial-azure-active-directory-integration-with-tidemark"></a>Tutorial: Integração do Azure Active Directory com o Tidemark

Neste tutorial, você aprenderá como integrar o Tidemark ao Azure AD (Azure Active Directory).

A integração do Tidemark ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no AD do Azure quem tem acesso ao Tidemark
- Você pode permitir que os usuários façam logon automaticamente no Tidemark (Logon Único) com suas contas do AD do Azure
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do AD do Azure com o Tidemark, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Tidemark habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, é possível obter uma avaliação por um mês aqui: [Oferta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Tidemark por meio da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-tidemark-from-the-gallery"></a>Adicionando o Tidemark por meio da galeria
Para configurar a integração do Tidemark com o AD do Azure, você precisa adicionar o Tidemark por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Tidemark por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **Tidemark**.

    ![Criação de um usuário de teste do AD do Azure](./media/tidemark-tutorial/tutorial_tidemark_search.png)

1. No painel de resultados, selecione **Tidemark** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/tidemark-tutorial/tutorial_tidemark_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Tidemark, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Tidemark é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado do Tidemark.

No Tidemark, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do AD do Azure com o Tidemark, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criação de um usuário de teste do Tidemark](#creating-a-tidemark-test-user)** – para ter um equivalente de Brenda Fernandes no Tidemark que esteja vinculado à representação de usuário no Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo Tidemark.

**Para configurar o logon único do AD do Azure com o Tidemark, realize as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Tidemark**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/tidemark-tutorial/tutorial_tidemark_samlbase.png)

1. Na seção **URLs e Domínio do Tidemark**, siga as etapas abaixo:

    ![Configurar o logon único](./media/tidemark-tutorial/tutorial_tidemark_url.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL usando os seguintes padrões: 
    | |
    |--|
    | `https://<subdomain>.tidemark.com/login` |
    | `https://<subdomain>.tidemark.net/login` |

    b. Na caixa de texto **Identificador**, digite uma URL usando os seguintes padrões: 
    | |
    |--|
    | `https://<subdomain>.tidemark.com/saml` |
    | `https://<subdomain>.tidemark.net/saml` |

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao cliente do Tidemark](http://www.tidemark.com/contact-us) para obter esses valores. 
 
1. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![Configurar o logon único](./media/tidemark-tutorial/tutorial_tidemark_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/tidemark-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do Tidemark**, clique em **Configurar o Tidemark** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configurar o logon único](./media/tidemark-tutorial/tutorial_tidemark_configure.png) 

1. Para configurar o logon único no lado do **Tidemark**, é necessário enviar o **Certificado (Base64), a ID da Entidade SAML, a URL do Serviço de Logon Único de SAML e a URL de Saída** baixados para a [equipe de suporte do Tidemark](http://www.tidemark.com/contact-us). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre o recurso de documentação inserida aqui: [Documentação inserida do Microsoft Azure Active Directory]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/tidemark-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/tidemark-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/tidemark-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/tidemark-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-tidemark-test-user"></a>Criar um usuário de teste do Tidemark

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Tidemark. Trabalhe com a [equipe de suporte do Tidemark](http://www.tidemark.com/contact-us) para adicionar usuários na conta do Tidemark. 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Tidemark.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Tidemark, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **Tidemark**.

    ![Configurar o logon único](./media/tidemark-tutorial/tutorial_tidemark_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco Tidemark no Painel de Acesso, deverá ser conectado automaticamente ao aplicativo Tidemark.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tidemark-tutorial/tutorial_general_01.png
[2]: ./media/tidemark-tutorial/tutorial_general_02.png
[3]: ./media/tidemark-tutorial/tutorial_general_03.png
[4]: ./media/tidemark-tutorial/tutorial_general_04.png

[100]: ./media/tidemark-tutorial/tutorial_general_100.png

[200]: ./media/tidemark-tutorial/tutorial_general_200.png
[201]: ./media/tidemark-tutorial/tutorial_general_201.png
[202]: ./media/tidemark-tutorial/tutorial_general_202.png
[203]: ./media/tidemark-tutorial/tutorial_general_203.png

