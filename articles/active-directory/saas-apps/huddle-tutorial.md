---
title: 'Tutorial: Integração do Azure Active Directory ao Huddle | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Huddle.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 8389ba4c-f5f8-4ede-b2f4-32eae844ceb0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.openlocfilehash: 3ec78c7dc3ac3ce9747a09218e1e35068c60b894
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55187718"
---
# <a name="tutorial-azure-active-directory-integration-with-huddle"></a>Tutorial: Integração do Azure Active Directory ao Huddle

Neste tutorial, você aprenderá a integrar o Huddle ao Azure AD (Azure Active Directory).

A integração do Huddle ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem terá acesso ao Huddle
- Você pode permitir que usuários façam logon automaticamente no Huddle (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Huddle, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Huddle

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Como adicionar o Huddle por meio da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-huddle-from-the-gallery"></a>Como adicionar o Huddle por meio da galeria

Para configurar a integração do Huddle ao Azure AD, você precisará adicionar o Huddle por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Huddle por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

4. Na caixa de pesquisa, digite **Huddle**. Selecione **Huddle** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/huddle-tutorial/tutorial_huddle_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure

Nesta seção, você configurará e testará o logon único do Azure AD com o Huddle, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Huddle é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Huddle.

Para configurar e testar o logon único do Azure AD com o Huddle, você precisará concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste do Huddle](#creating-a-huddle-test-user)**: para ter um equivalente de Brenda Fernandes no Huddle que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal do Azure e configura o logon único no aplicativo Huddle.

**Para configurar o logon único do Azure AD com o Huddle, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Huddle**, clique em **Logon único**.

    ![Configurar o logon único][4]

2. Clique em **Alterar modo de logon único** na parte superior da tela para selecionar o modo **SAML**.

    ![Configurar o logon único](./media/huddle-tutorial/tutorial_general_300.png)

3. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](./media/huddle-tutorial/tutorial_general_301.png)

4. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Configurar o logon único](./media/huddle-tutorial/tutorial_general_302.png)

5. Na seção **Configuração Básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo iniciado por **IDP**:

    > [!NOTE]
    > Sua instância do huddle será detectada automaticamente no domínio que você inserir abaixo.

    ![Informações de logon único de Domínio e URLs do Huddle](./media/huddle-tutorial/tutorial_huddle_url.png)

     a. Na caixa de texto **Identificador**, digite uma URL:

    | | |
    |--|--|
    | `https://login.huddle.net`|
    | `https://login.huddle.com`|
    | |

    b. Na caixa de texto **URL de Resposta**, digite uma URL:

    | | |
    |--|--|
    | `https://login.huddle.net/saml/browser-sso`|
    | `https://login.huddle.com/saml/browser-sso`|
    | `https://login.huddle.com/saml/idp-initiated-sso`|
    | |

    c. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único de Domínio e URLs do Huddle](./media/huddle-tutorial/tutorial_huddle_url1.png)

    Na caixa de texto **URL de logon**, digite qualquer uma da URL usando o seguinte padrão:

    | | |
    |--|--|
    | `https://<customsubdomain>.huddle.com`|
    | `https://us.huddle.com`|
    | |

    > [!NOTE]
    > O valor da URL de logon não é real. Atualize esse valor com a URL de Logon real. Para obter esse valor, entre em contato com a [equipe de suporte do cliente Huddle](https://huddle.zendesk.com).

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o certificado apropriado de acordo com seus requisitos e salve-o no computador.

    ![Configurar o logon único](./media/huddle-tutorial/tutorial_huddle_certificate.png)

7. Na seção **Configurar Huddle**, copie a URL apropriada, de acordo com suas necessidades.

    ![Configurar o logon único](./media/huddle-tutorial/tutorial_huddle_configure.png)

8. Para configurar o logon único no lado do **Huddle**, é necessário enviar o certificado que você baixou e as URLs que você copiou da seção **Configurar** **Huddle** do portal do Azure para a [equipe de suporte do cliente Huddle](https://huddle.zendesk.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

    >[!NOTE]
    > O logon único precisa ser habilitado pela equipe de suporte do Huddle. Assim que a configuração for concluída, você receberá uma notificação.

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Criar um usuário do AD do Azure][100]

2. Selecione **Novo usuário** na parte superior da tela.

    ![Criação de um usuário de teste do AD do Azure](./media/huddle-tutorial/create_aaduser_01.png) 

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![Criação de um usuário de teste do AD do Azure](./media/huddle-tutorial/create_aaduser_02.png)

     a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.

### <a name="creating-a-huddle-test-user"></a>Criação de um usuário de teste do Huddle

Para permitir que os usuários do Azure AD façam logon no Huddle, eles deverão ser provisionados no Huddle. No caso do Huddle, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **Huddle** como administrador.

2. Clique em **Workspace**.

3. Clique em **Pessoas \> Convidar Pessoas**.

    ![Pessoas](./media/huddle-tutorial/IC787838.png "Pessoas")

4. Na seção **Criar novo convite** , realize as seguintes etapas:
  
    ![Novo Convite](./media/huddle-tutorial/IC787839.png "Novo Convite")
  
     a. Na lista **Escolha uma equipe para convidar pessoas para participar**, selecione **equipe**.

    b. Insira o **Endereço de Email** de uma conta do Azure AD válida que você deseja provisionar na caixa de texto **Inserir endereço de email para pessoas que você gostaria de convidar**.

    c. Clique em **Convidar**.

    >[!NOTE]
    > O titular da conta do Azure AD receberá um email com um link de confirmação de conta para que ela se torne ativa.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do Huddle ou as APIs fornecidas pelo Huddle para provisionar as contas de usuário do Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure ao conceder acesso ao Huddle.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **Huddle**.

    ![Configurar o logon único](./media/huddle-tutorial/tutorial_huddle_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Huddle no Painel de Acesso, você deverá ser conectado automaticamente à página de logon do aplicativo de Huddle.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/huddle-tutorial/tutorial_general_01.png
[2]: ./media/huddle-tutorial/tutorial_general_02.png
[3]: ./media/huddle-tutorial/tutorial_general_03.png
[4]: ./media/huddle-tutorial/tutorial_general_04.png
[100]: ./media/huddle-tutorial/tutorial_general_100.png
[200]: ./media/huddle-tutorial/tutorial_general_200.png
[201]: ./media/huddle-tutorial/tutorial_general_201.png
[202]: ./media/huddle-tutorial/tutorial_general_202.png
[203]: ./media/huddle-tutorial/tutorial_general_203.png
