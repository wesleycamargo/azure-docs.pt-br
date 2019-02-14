---
title: 'Tutorial: Integração do Azure Active Directory ao FreshGrade | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o FreshGrade.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 1055bba6-f4df-462e-bc9b-1ad5ada0f638
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f1b14fb88d72e1cb816498dbccf7b0e595f1637
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56203847"
---
# <a name="tutorial-azure-active-directory-integration-with-freshgrade"></a>Tutorial: Integração do Azure Active Directory ao FreshGrade

Neste tutorial, você aprenderá a integrar o FreshGrade ao Azure Active Directory (AD do Azure).

A integração do FreshGrade ao AD do Azure oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao FreshGrade
- É possível permitir que os usuários se conectem automaticamente ao FreshGrade (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do AD do Azure ao FreshGrade, você precisará dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura habilitada para logon único do FreshGrade

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar FreshGrade da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-freshgrade-from-the-gallery"></a>Adicionar FreshGrade da galeria
Para configurar a integração do FreshGrade com o AD do Azure, você precisará adicionar o FreshGrade à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o FreshGrade por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **FreshGrade**.

    ![Criação de um usuário de teste do AD do Azure](./media/freshgrade-tutorial/tutorial_freshgrade_search.png)

1. No painel de resultados, selecione **FreshGrade** e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/freshgrade-tutorial/tutorial_freshgrade_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do AD do Azure com o FreshGrade, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o AD do Azure precisa saber qual usuário do FreshGrade é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado do FreshGradey.

No FreshGrade, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do AD do Azure com o FreshGrade, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criando um usuário de teste do FreshGrade](#creating-a-freshgrade-test-user)** – para ter um equivalente de Brenda Fernandes no FreshGrade que esteja vinculado à representação de usuário do Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo FreshGrade.

**Para configurar o logon único do AD do Azure com o FreshGrade, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **FreshGrade**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Configurar o logon único](./media/freshgrade-tutorial/tutorial_freshgrade_samlbase.png)

1. Na seção **Domínio e URLs do FreshGrade**, realize as seguintes etapas:

    ![Configurar o logon único](./media/freshgrade-tutorial/tutorial_freshgrade_url.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL usando os seguintes padrões:
      | |
      |--|
      | `https://<subdomain>.freshgrade.com/login` |
      | `https://<subdomain>.onboarding.freshgrade.com/login` |

    b. Na caixa de texto **Identificador**, digite uma URL usando os seguintes padrões:
      | |
      |--|
      | `https://login.onboarding.freshgrade.com:443/saml/metadata/alias/<instancename>` |
      | `https://login.freshgrade.com:443/saml/metadata/alias/<instancename>` |

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do FreshGrade](mailTo:support@freshgrade.com) para obter esses valores.

1. Na seção **Certificado de Autenticação SAML** , clique no botão Copiar para copiar a **URL de Metadados de Federação do Aplicativo** e cole-a no bloco de notas.
    
    ![Configurar o logon único](./media/freshgrade-tutorial/tutorial_metadataurl.png)
     
1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/freshgrade-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do FreshGrade**, clique em **Configurar o FreshGrade** para abrir a janela **Configurar logon**. Copie a **URL de serviço de logon único SAML** da **seção de Referência Rápida.**

    ![Configurar o logon único](./media/freshgrade-tutorial/tutorial_freshgrade_configure.png)

1. Para configurar o logon único no **FreshGrade**, é necessário enviar a **URL de Metadados de Federação de Aplicativos** e a **URL do Serviço de Logon Único do SAML** para a [Equipe de suporte do FreshGrade](mailTo:support@freshgrade.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/freshgrade-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/freshgrade-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/freshgrade-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/freshgrade-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-freshgrade-test-user"></a>Criação de um usuário de teste FreshGrade

Nesta seção, você criará uma usuária chamado Brenda Fernandes no FreshGrade. Trabalhe com a [equipe de suporte do FreshGrade](mailTo:support@freshgrade.com) para adicionar os usuários à plataforma FreshGrade.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao FreshGrade.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao FreshGrade, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, escolha **FreshGrade**.

    ![Configurar o logon único](./media/freshgrade-tutorial/tutorial_freshgrade_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do FreshGrade no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo do FreshGrade.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/freshgrade-tutorial/tutorial_general_01.png
[2]: ./media/freshgrade-tutorial/tutorial_general_02.png
[3]: ./media/freshgrade-tutorial/tutorial_general_03.png
[4]: ./media/freshgrade-tutorial/tutorial_general_04.png

[100]: ./media/freshgrade-tutorial/tutorial_general_100.png

[200]: ./media/freshgrade-tutorial/tutorial_general_200.png
[201]: ./media/freshgrade-tutorial/tutorial_general_201.png
[202]: ./media/freshgrade-tutorial/tutorial_general_202.png
[203]: ./media/freshgrade-tutorial/tutorial_general_203.png

