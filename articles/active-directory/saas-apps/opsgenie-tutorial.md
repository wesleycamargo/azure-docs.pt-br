---
title: 'Tutorial: Integração do Azure Active Directory ao OpsGenie | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o OpsGenie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9858fc38698ae2c5bd272a3494bcf02bce2d8e9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56194590"
---
# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>Tutorial: Integração do Azure Active Directory ao OpsGenie

Neste tutorial, você aprenderá a integrar o OpsGenie ao Azure AD (Azure Active Directory).

A integração do OpsGenie ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao OpsGenie.
- Você pode permitir que seus usuários façam logon automaticamente no OpsGenie (logon único) com as contas do Azure AD deles
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao OpsGenie, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura habilitada para logon único do OpsGenie

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do OpsGenie da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-opsgenie-from-the-gallery"></a>Adição do OpsGenie da galeria
Para configurar a integração do OpsGenie ao Azure AD, você precisa adicionar o OpsGenie a partir da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o OpsGenie a partir da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **OpsGenie**.

    ![Criação de um usuário de teste do AD do Azure](./media/opsgenie-tutorial/tutorial_opsgenie_search.png)

1. No painel de resultados, selecione **OpsGenie** e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/opsgenie-tutorial/tutorial_opsgenie_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o OpsGenie, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do OpsGenie é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no OpsGenie.

No OpsGenie, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o OpsGenie, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criação de um usuário de teste para OpsGenie](#creating-a-opsgenie-test-user)** – para ter um equivalente de Brenda Fernandes no OpsGenie que esteja vinculado à representação desse usuário no Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal do Azure e configura o logon único no seu aplicativo OpsGenie.

**Para configurar o logon único do Azure AD com o OpsGenie, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **OpsGenie**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/opsgenie-tutorial/tutorial_opsgenie_samlbase.png)

1. Na seção **URLs e Domínio do OpsGenie**, execute as seguintes etapas:

    ![Configurar o logon único](./media/opsgenie-tutorial/tutorial_opsgenie_url.png)

    Na caixa de texto **URL de Logon**, digite a URL: `https://app.opsgenie.com/auth/login`

1. Na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar a **URL de metadados de federação do aplicativo** e cole-a no bloco de notas.

    ![O link de download do Certificado](./media/opsgenie-tutorial/tutorial_opsgenie_certificate.png)

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/opsgenie-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do OpsGenie**, clique em **Configurar OpsGenie** para abrir a janela **Configurar logon**. Copie a **URL do Serviço de Logon Único SAML** da seção de Referência Rápida.

    ![Configurar o logon único](./media/opsgenie-tutorial/tutorial_opsgenie_configure.png)

1. Abra outra instância do navegador e, em seguida, faça logon no OpsGenie como administrador.

1. Clique em **Configurações** e na guia **Logon único**.
   
    ![Logon único do OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_06.png)

1. Para habilitar o SSO, selecione **Habilitado**.
   
    ![Configurações do OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_07.png) 

1. Na seção **Provedor**, clique na guia **Azure Active Directory**.
   
    ![Configurações do OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_08.png) 

1. Na página de diálogo Azure Active Directory, execute as seguintes etapas:
   
    ![Configurações do OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_09.png)
    
     a. Na caixa de texto **do Ponto de Extremidade SML 2.0**, cole o valor**a URL do Serviço de Logon único**que você copiou do portal do Azure.
    
    b. Na caixa de texto **URL de Metadados:**, cole o valor da **URL de metadados de federação do aplicativo** que você copiou do Portal do Azure.
    
    c. Clique em **Salvar Alterações**.

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/opsgenie-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/opsgenie-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/opsgenie-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/opsgenie-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-opsgenie-test-user"></a>Criação de um usuário de teste para OpsGenie

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no OpsGenie. 

1. Em uma janela de navegador da Web, faça logon em seu locatário do OpsGenie como administrador.

1. Navegue até a lista Usuários clicando em **Usuário** no painel esquerdo.
   
   ![Configurações do OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_10.png) 

1. Clique em **Adicionar Usuário**.

1. No diálogo **Adicionar Usuário** , realize as seguintes etapas:
   
   ![Configurações do OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_11.png)
   
    a. Na caixa de texto **Email**, digite o endereço de email de Brenda Fernandes endereçado no Azure Active Directory.
   
   b. Na caixa de texto **Nome Completo**, digite **Brenda Fernandes**.
   
   c. Clique em **Salvar**. 

>[!NOTE]
>Brenda receberá um email com instruções para configurar o perfil dela.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você concederá a Brenda Fernandes acesso ao OpsGenie para que ela fique habilitada a usar o logon único do Azure.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao OpsGenie, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **OpsGenie**.

    ![Configurar o logon único](./media/opsgenie-tutorial/tutorial_opsgenie_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

O objetivo desta seção é testar sua configuração de SSO do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do OpsGenie no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo do OpsGenie.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/opsgenie-tutorial/tutorial_general_01.png
[2]: ./media/opsgenie-tutorial/tutorial_general_02.png
[3]: ./media/opsgenie-tutorial/tutorial_general_03.png
[4]: ./media/opsgenie-tutorial/tutorial_general_04.png

[100]: ./media/opsgenie-tutorial/tutorial_general_100.png

[200]: ./media/opsgenie-tutorial/tutorial_general_200.png
[201]: ./media/opsgenie-tutorial/tutorial_general_201.png
[202]: ./media/opsgenie-tutorial/tutorial_general_202.png
[203]: ./media/opsgenie-tutorial/tutorial_general_203.png

