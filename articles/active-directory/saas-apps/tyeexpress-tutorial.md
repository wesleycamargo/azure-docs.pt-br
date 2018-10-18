---
title: 'Tutorial: integração do Azure Active Directory ao T&E Express | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o T&E Express.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: B42374E5-2559-4309-8EF2-820BEE7EBB0C
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: jeedes
ms.openlocfilehash: ff4d634fb7f6f8057e5f370a694e46ca5e0d772d
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114066"
---
# <a name="tutorial-azure-active-directory-integration-with-te-express"></a>Tutorial: integração do Azure Active Directory com o T&E Express

Neste tutorial, você aprenderá a integrar o T&E Express ao Azure AD (Azure Active Directory).

A integração do T&E Express com o Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao T&E Express
- Você pode permitir que os usuários façam logon automaticamente no T&E Express (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local - o portal de Gerenciamento do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o T&E Express, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do T&E Express

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o T&E Express da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-te-express-from-the-gallery"></a>Adicionando o T&E Express da galeria
Para configurar a integração do T&E Express com o Azure AD, você precisa adicionar o T&E Express por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o T&E Express da galeria, execute as seguintes etapas:**

1. No **[Portal de Gerenciamento do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **T&E Express**.

    ![Criação de um usuário de teste do AD do Azure](./media/tyeexpress-tutorial/tutorial_tyeexpress_search.png)

1. No painel de resultados, selecione **T&E Express** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/tyeexpress-tutorial/tutorial_tyeexpress_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o T&E Express, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do T&E Express é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do T&E Express.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD ao valor do **Nome de Usuário** no T&E Express.

Para configurar e testar o logon único do Azure AD com o T&E Express, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criando um usuário de teste do T&E Express](#creating-a-te-express-test-user)**: para ter um equivalente a Brenda Fernandes no T&E Express que esteja vinculado à sua representação no Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal de Gerenciamento do Azure e configura o logon único em seu aplicativo do T&E Express.

**Para configurar o logon único do Azure AD com o T&E Express, execute as seguintes etapas:**

1. No Portal de Gerenciamento do Azure, na página de integração de aplicativo do **T&E Express**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, como **Modo**, selecione **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/tyeexpress-tutorial/tutorial_tyeexpress_samlbase.png)

1. Na seção **URLs e Domínio do T&E Express**, execute as seguintes etapas:

    ![Configurar o logon único](./media/tyeexpress-tutorial/tutorial_tyeexpress_url.png)

    a. Na caixa de texto **Identificador**, digite o valor como `https://<domain>.tyeexpress.com`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<domain>.tyeexpress.com/authorize/samlConsume.aspx`

    > [!NOTE] 
    > Observe que esses não são os valores reais. Você precisa atualizar esses valores com o Identificador e a URL de Resposta reais. Aqui, sugerimos que você use o valor exclusivo de cadeia de caracteres no Identificador. Entre em contato com a [Equipe de suporte do T&E Express](http://www.tyeexpress.com/contacto.aspx) para obter esses valores.

1. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo XML em seu computador.

    ![Configurar o logon único](./media/tyeexpress-tutorial/tutorial_tyeexpress_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/tyeexpress-tutorial/tutorial_general_400.png)

1. Para configurar o logon único usando o **T&E Express**, faça logon no aplicativo do T&E Express sem logon único com SAML usando credenciais de administrador.

1. Na guia **Administrador**, clique em **Domínio SAML** para abrir a página de configurações de SAML.

    ![Configurar o logon único](./media/tyeexpress-tutorial/tye-SAML.png)

1. Altere a opção **Ativar** de **Não** para **Sim**. Na caixa de texto **Metadados do Provedor de Identidade**, cole o XML dos metadados que você baixou do portal do Azure.

    ![Configurar o logon único](./media/tyeexpress-tutorial/tyeAdmin.png)

1. Clique no botão **Salvar** para salvar as configurações.  


### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal de Gerenciamento do Azure chamado Britta Simon.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/tyeexpress-tutorial/create_aaduser_01.png) 

1. Vá para **usuários e grupos** e clique em **todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/tyeexpress-tutorial/create_aaduser_02.png) 

1. Na parte superior da caixa de diálogo clique **adicionar** para abrir o **usuário** caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/tyeexpress-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/tyeexpress-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-te-express-test-user"></a>Criar um usuário de teste do T&E Express

A fim de permitir que os usuários do Azure AD façam logon no T&E Express, eles devem ser provisionados no T&E Express.  
No caso do T&E Express, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Faça logon no site da empresa do T&E Express como administrador.

1. Na guia Administrador, clique em Usuários para abrir a página mestra de Usuários.

    ![Adicionar Funcionário](./media/tyeexpress-tutorial/tye-adminusers.png)

1. Na página inicial, clique em **+** para adicionar os usuários.

    ![Adicionar Funcionário](./media/tyeexpress-tutorial/tye-usershome.png)

1. Insira todos os detalhes obrigatórios conforme solicitado no formulário e clique no botão Salvar para salvar os detalhes.

    ![Adicionar Funcionário](./media/tyeexpress-tutorial/tye-usersadd.png)

    ![Adicionar Funcionário](./media/tyeexpress-tutorial/tye-userssave.png)


### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao T&E Express.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao T&E Express, execute as seguintes etapas:**

1. No Portal de Gerenciamento do Azure, abra a exibição de aplicativos e, em seguida, navegue até o modo de exibição de diretório e vá para **Aplicativos empresariais**, depois clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **T&E Express**.

    ![Configurar o logon único](./media/tyeexpress-tutorial/tutorial_tyeexpress_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando clicar no bloco T&E Express no Painel de Acesso, você deve ser conectado automaticamente ao seu aplicativo T&E Express.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tyeexpress-tutorial/tutorial_general_01.png
[2]: ./media/tyeexpress-tutorial/tutorial_general_02.png
[3]: ./media/tyeexpress-tutorial/tutorial_general_03.png
[4]: ./media/tyeexpress-tutorial/tutorial_general_04.png

[100]: ./media/tyeexpress-tutorial/tutorial_general_100.png

[200]: ./media/tyeexpress-tutorial/tutorial_general_200.png
[201]: ./media/tyeexpress-tutorial/tutorial_general_201.png
[202]: ./media/tyeexpress-tutorial/tutorial_general_202.png
[203]: ./media/tyeexpress-tutorial/tutorial_general_203.png

