---
title: 'Tutorial: Integração do Azure Active Directory ao Trisotech Digital Enterprise Server | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Trisotech Digital Enterprise Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6d54d20c-eca1-4fa6-b56a-4c3ed0593db0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: e3f90da177f46246d1a7453796b7b9a9c93f4a21
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55193158"
---
# <a name="tutorial-azure-active-directory-integration-with-trisotech-digital-enterprise-server"></a>Tutorial: Integração do Azure Active Directory ao Trisotech Digital Enterprise Server

Neste tutorial, você aprenderá a integrar o Trisotech Digital Enterprise Server com o Microsoft Azure AD (Azure Active Directory).

A integração do Trisotech Digital Enterprise Server com o Microsoft Azure AD oferece os seguintes benefícios:

- Você pode controlar no Microsoft Azure AD quem tem acesso ao Trisotech Digital Enterprise Server.
- Você pode habilitar os usuários a entrar automaticamente no Trisotech Digital Enterprise Server (Logon Único) com suas contas do Microsoft Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure AD com o Trisotech Digital Enterprise Server, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada de logon único do Trisotech Digital Enterprise Server

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Trisotech Digital Enterprise Server da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-trisotech-digital-enterprise-server-from-the-gallery"></a>Adicionar o Trisotech Digital Enterprise Server da galeria
Para configurar a integração do Trisotech Digital Enterprise Server ao Microsoft Azure AD, você precisa adicionar o Trisotech Digital Enterprise Server da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Trisotech Digital Enterprise Server por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **Trisotech Digital Enterprise Server**, selecione **Trisotech Digital Enterprise Server** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Trisotech Digital Enterprise Server na lista de resultados](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure AD com o Trisotech Digital Enterprise Server, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Microsoft Azure AD precisa saber qual usuário do Trisotech Digital Enterprise Server é equivalente a um usuário do Microsoft Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Microsoft Azure AD e o usuário relacionado do Trisotech Digital Enterprise Server.

Para configurar e testar o logon único do Microsoft Azure AD com o Trisotech Digital Enterprise Server, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar de um usuário de teste do Trisotech Digital Enterprise Server](#create-a-trisotech-digital-enterprise-server-test-user)**: para ter um equivalente de Brenda Fernandes no Trisotech Digital Enterprise Server que esteja vinculado à representação do usuário no Microsoft Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Microsoft Azure AD no Portal do Azure e configura o logon único no aplicativo Trisotech Digital Enterprise Server.

**Para configurar o logon único do Microsoft Azure AD com o Trisotech Digital Enterprise Server, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração do aplicativo do **Trisotech Digital Enterprise Server**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_samlbase.png)

1. Na seção **Domínio e URLs do Trisotech Digital Enterprise Server**, realize as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do Trisotech Digital Enterprise Server](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_url.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.trisotech.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<companyname>.trisotech.com`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte do Trisotech Digital Enterprise Server](mailto:support@trisotech.com) para obter esses valores.

1. Na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar a **URL de metadados de federação do aplicativo** e cole-a no bloco de notas. 

    ![O link de download do Certificado](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_certificate.png)

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_400.png)

1. Em uma janela diferente do navegador da Web, faça logon no site da empresa na configuração do Trisotech Digital Enterprise Server como um administrador.

1. Clique no **Menu ícone** e, em seguida, selecione **Administração**.

    ![Configurar o logon único](./media/trisotechdigitalenterpriseserver-tutorial/user1.png)

1. Selecione **Provedor de Usuário**.

    ![Configurar o logon único](./media/trisotechdigitalenterpriseserver-tutorial/user2.png)

1. Na seção**Configurações do Provedor de Usuário**, execute as seguintes etapas:

    ![Configurar o logon único](./media/trisotechdigitalenterpriseserver-tutorial/user3.png)

     a. Selecione **SAML 2 (Secured Assertion Markup Language 2)** da lista suspensa no **Método de Autenticação**.

    b. Na caixa de texto **URL de Metadados**, cole o valor da **URL de metadados de federação do aplicativo** que você copiou do Portal do Azure.

    c. Na caixa de texto da **ID do Aplicativo**, insira a URL usando o padrão a seguir: `https://<companyname>.trisotech.com`.

    d. Clique em **Salvar**

    e. Inserir o nome de domínio na caixa de texto **Domínios Permitidos (vazio significa todos)**, ele atribui automaticamente licenças para usuários que correspondem aos domínios permitidos

    f. Clique em **Salvar**

 ### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-trisotech-digital-enterprise-server-test-user"></a>Criar um usuário de teste do Trisotech Digital Enterprise Server

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Trisotech Digital Enterprise Server. O Trisotech Digital Enterprise Server dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o Trisotech Digital Enterprise Server, caso ele ainda não exista.
>[!Note]
>Caso precise criar um usuário manualmente, contate a  [equipe de suporte do Trisotech Digital Enterprise Server](mailto:support@trisotech.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Trisotech Digital Enterprise Server.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Trisotech Digital Enterprise Server, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **Trisotech Digital Enterprise Server**.

    ![O link do Trisotech Digital Enterprise Server na lista de aplicativos](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Trisotech Digital Enterprise Server no Painel de Acesso, você deverá ser conectado automaticamente a seu aplicativo Trisotech Digital Enterprise Server.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_01.png
[2]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_02.png
[3]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_03.png
[4]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_04.png

[100]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_100.png

[200]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_200.png
[201]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_201.png
[202]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_202.png
[203]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_203.png

