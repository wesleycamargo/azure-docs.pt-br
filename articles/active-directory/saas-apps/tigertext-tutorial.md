---
title: 'Tutorial: Integração do Azure Active Directory ao TigerText Secure Messenger | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o TigerText Secure Messenger.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 03f1e128-5bcb-4e49-b6a3-fe22eedc6d5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20bb003fbb8c6e7c1592618a8742f9b88882e358
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56184135"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>Tutorial: Integração do Azure Active Directory ao TigerText Secure Messenger

Neste tutorial, você aprenderá a integrar o TigerText Secure Messenger ao Azure AD (Azure Active Directory).

A integração do TigerText Secure Messenger ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao TigerText Secure Messenger
- Você pode permitir que os usuários façam logon automaticamente no TigerText Secure Messenger (Logon Único) com as respectivas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao TigerText Secure Messenger, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura do TigerText Secure Messenger habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o TigerText Secure Messenger da galeria
1. Configurar e testar logon único do Azure AD

## <a name="add-tigertext-secure-messenger-from-the-gallery"></a>Adicionar o TigerText Secure Messenger da galeria
Para configurar a integração do TigerText Secure Messenger ao Azure AD, você precisa adicionar o TigerText Secure Messenger da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o TigerText Secure Messenger da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **TigerText Secure Messenger**, selecione **TigerText Secure Messenger** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Adicionar da galeria](./media/tigertext-tutorial/tutorial_tigertext_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
Nesta seção, você vai configurar e testar o logon único do Azure AD com o TigerText Secure Messenger, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do TigerText Secure Messenger é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado do TigerText Secure Messenger.

No TigerText Secure Messenger, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o TigerText Secure Messenger, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do TigerText Secure Messenger](#create-a-tigertext-secure-messenger-test-user)** – para ter um equivalente de Brenda Fernandes no TigerText Secure Messenger vinculado à representação do usuário no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Testar o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo TigerText Secure Messenger.

**Para configurar o logon único do Azure AD com o TigerText Secure Messenger, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **TigerText Secure Messenger**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Logon único baseado em SAML](./media/tigertext-tutorial/tutorial_tigertext_samlbase.png)

1. Na seção **Domínio e URLs do TigerText Secure Messenger**, realize as seguintes etapas:

    ![Seção Domínio e URLs do TigerText Secure Messenger](./media/tigertext-tutorial/tutorial_tigertext_url.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL como: `https://home.tigertext.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://saml-lb.tigertext.me/v1/organization/<instance Id>`

    > [!NOTE] 
    > Esse valor não é real. Atualize esse valor com o Identificador real. Contate a [equipe de suporte ao cliente do TigerText Secure Messenger](mailTo:prosupport@tigertext.com) para obter esse valor. 
 
1. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![Seção Certificado de Autenticação SAML](./media/tigertext-tutorial/tutorial_tigertext_certificate.png) 

1. Clique no botão **Salvar** .

    ![Botão Salvar](./media/tigertext-tutorial/tutorial_general_400.png)

1. Para que o logon único seja configurado para o aplicativo, entre em contato com a [equipe de suporte do TigerText Secure Messenger](mailTo:prosupport@tigertext.com) e forneça os **metadados baixados**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre o recurso de documentação inserida aqui: [Documentação inserida do Microsoft Azure Active Directory]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/tigertext-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Usuários e grupos->Todos os usuários](./media/tigertext-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Botão Adicionar](./media/tigertext-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Diálogo do usuário](./media/tigertext-tutorial/create_aaduser_04.png) 

     a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-tigertext-secure-messenger-test-user"></a>Criar um usuário de teste do TigerText Secure Messenger

Nesta seção, você criará um usuário chamado Brenda Fernandes no TigerText. Contate a [equipe de suporte ao cliente do TigerText Secure Messenger](mailTo:prosupport@tigertext.com) para adicionar os usuários na plataforma TigerText.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao TigerText Secure Messenger.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao TigerText Secure Messenger, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **TigerText Secure Messenger**.

    ![TigerText Secure Messenger na lista de aplicativos](./media/tigertext-tutorial/tutorial_tigertext_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando clicar no bloco TigerText no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo TigerText. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tigertext-tutorial/tutorial_general_01.png
[2]: ./media/tigertext-tutorial/tutorial_general_02.png
[3]: ./media/tigertext-tutorial/tutorial_general_03.png
[4]: ./media/tigertext-tutorial/tutorial_general_04.png

[100]: ./media/tigertext-tutorial/tutorial_general_100.png

[200]: ./media/tigertext-tutorial/tutorial_general_200.png
[201]: ./media/tigertext-tutorial/tutorial_general_201.png
[202]: ./media/tigertext-tutorial/tutorial_general_202.png
[203]: ./media/tigertext-tutorial/tutorial_general_203.png

