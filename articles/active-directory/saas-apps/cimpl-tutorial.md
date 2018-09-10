---
title: 'Tutorial: integração do Azure Active Directory ao Cimpl | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o Cimpl.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 58ee5481-ae40-4e4a-a3c9-86343851fc9a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: jeedes
ms.openlocfilehash: 8668b1c355e7d19943596fb94d6a7d260c0bca43
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445610"
---
# <a name="tutorial-azure-active-directory-integration-with-cimpl"></a>Tutorial: Integração do Active Directory do Azure com o Cimpl

Neste tutorial, você aprende a integrar o Cimpl ao Azure AD (Azure Active Directory).

A integração do Cimpl ao Azure AD oferece os seguintes benefícios:

- Controlar no AD do Azure quem terá acesso ao Cimpl
- Permitir que seus usuários façam logon automaticamente no Cimpl (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do AD do Azure ao Cimpl, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Cimpl

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar Cimpl a partir da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-cimpl-from-the-gallery"></a>Adicionar Cimpl a partir da galeria
Para configurar a integração do Cimpl ao AD do Azure, você precisará adicionar o Cimpl da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Cimpl da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **Cimpl**.

    ![Criação de um usuário de teste do AD do Azure](./media/cimpl-tutorial/tutorial_cimpl_search.png)

1. No painel de resultados, selecione **Cimpl** e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/cimpl-tutorial/tutorial_cimpl_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configura e testa o logon único do Azure AD com o Cimpl com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Cimpl é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Cimpl.

No Cimpl, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do AD do Azure com o Cimpl, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Como criar de um usuário de teste do Cimpl](#creating-a-cimpl-test-user)** – para ter um equivalente de Brenda Fernandes no Cimpl que esteja vinculado à representação do usuário no Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Cimpl.

**Para configurar o logon único do AD do Azure com o Cimpl, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **Cimpl**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/cimpl-tutorial/tutorial_cimpl_samlbase.png)

1. Na seção **URLs e Domínio do Cimpl**, execute as seguintes etapas:

    ![Configurar o logon único](./media/cimpl-tutorial/tutorial_cimpl_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://sso.etelesolv.com/<TENANTNAME>`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://sso.etelesolv.com/<TENANTNAME>`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com a equipe do Cimpl pelo telefone **+1 866-982-8250** para obter esses valores. 
 
1. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar o logon único](./media/cimpl-tutorial/tutorial_cimpl_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/cimpl-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do Cimpl**, clique em **Configurar Cimpl** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção Referência Rápida.**

    ![Configurar o logon único](./media/cimpl-tutorial/tutorial_cimpl_configure.png) 

1. Para configurar o logon único no lado do **Cimpl**, é necessário enviar o **Certificado (Base64)** baixado, a **ID da Entidade do SAML e a URL do Serviço de Logon Único do SAML** ao suporte do Cimpl no telefone **+1 866-982-8250**.


> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/cimpl-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/cimpl-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/cimpl-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/cimpl-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-cimpl-test-user"></a>Criação de um usuário de teste do Cimpl

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Cimpl. Trabalhe com o suporte do Cimpl pelo telefone **+1 866-982-8250** para adicionar os usuários à conta do Cimpl. 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Cimpl.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Cimpl, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, escolha **Cimpl**.

    ![Configurar o logon único](./media/cimpl-tutorial/tutorial_cimpl_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

O objetivo desta seção é testar sua configuração de SSO do Azure AD usando o Painel de Acesso.  Ao clicar no bloco do Cimpl no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Cimpl. 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/cimpl-tutorial/tutorial_general_01.png
[2]: ./media/cimpl-tutorial/tutorial_general_02.png
[3]: ./media/cimpl-tutorial/tutorial_general_03.png
[4]: ./media/cimpl-tutorial/tutorial_general_04.png

[100]: ./media/cimpl-tutorial/tutorial_general_100.png

[200]: ./media/cimpl-tutorial/tutorial_general_200.png
[201]: ./media/cimpl-tutorial/tutorial_general_201.png
[202]: ./media/cimpl-tutorial/tutorial_general_202.png
[203]: ./media/cimpl-tutorial/tutorial_general_203.png

