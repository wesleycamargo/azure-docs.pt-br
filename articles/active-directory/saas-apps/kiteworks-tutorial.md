---
title: 'Tutorial: integração do Azure Active Directory ao Kiteworks | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o Kiteworks.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f7984aaf-ab1f-4a85-9646-a9523f5275d9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jeedes
ms.openlocfilehash: 88531ee1a98eefdf3ee85b4308150f10417858d0
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420216"
---
# <a name="tutorial-azure-active-directory-integration-with-kiteworks"></a>Tutorial: Integração do Active Directory do Azure com o Kiteworks

Neste tutorial, você aprenderá como integrar o Kiteworks ao Azure AD (Azure Active Directory).

A integração do Kiteworks ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no AD do Azure quem tem acesso ao Kiteworks
- Você pode permitir que seus usuários façam logon automaticamente no Kiteworks (logon único) com suas contas do AD do Azure
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do AD do Azure ao Kiteworks, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Kiteworks

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Kiteworks da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-kiteworks-from-the-gallery"></a>Adicionando o Kiteworks da galeria
Para configurar a integração do Kiteworks ao AD do Azure, você precisará adicionar o Kiteworks da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Kiteworks da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **Kiteworks**.

    ![Criação de um usuário de teste do AD do Azure](./media/kiteworks-tutorial/tutorial_kiteworks_search.png)

1. No painel de resultados, selecione **Kiteworks** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/kiteworks-tutorial/tutorial_kiteworks_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Kiteworks, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Kiteworks é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Kiteworks.

No Kiteworks, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do AD do Azure com o Kiteworks, você precisará concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criação de um usuário de teste do Kiteworks](#creating-a-kiteworks-test-user)** – para ter um equivalente de Brenda Fernandes no Kiteworks que esteja vinculado à representação do usuário no Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Kiteworks.

**Para configurar o logon único do AD do Azure com o Kiteworks, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **Kiteworks**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/kiteworks-tutorial/tutorial_kiteworks_samlbase.png)

1. Na seção **Domínio e URLs do Kiteworks**, execute as seguintes etapas:

    ![Configurar o logon único](./media/kiteworks-tutorial/tutorial_kiteworks_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<subdomain>.kiteworks.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<subdomain>.kiteworks.com/sp/module.php/saml/sp/saml2-acs.php/sp-sso`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte do cliente Kiteworks](http://accellion.com/support) para obter esses valores. 
 
1. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar o logon único](./media/kiteworks-tutorial/tutorial_kiteworks_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/kiteworks-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do Kiteworks**, clique em **Configurar Kiteworks** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configurar o logon único](./media/kiteworks-tutorial/tutorial_kiteworks_configure.png) 

1. Faça logon no site da sua empresa do Kiteworks como um administrador.

1. Na barra de ferramentas na parte superior, clique em **Configurações**.
   
    ![Configurar o logon único](./media/kiteworks-tutorial/tutorial_kiteworks_06.png) 

1. Na seção **Autenticação e Autorização**, clique em **Configuração do SSO**. 
   
    ![Configurar o logon único](./media/kiteworks-tutorial/tutorial_kiteworks_07.png)
 
1. Na página Instalação do SSO, execute as seguintes etapas:
   
    ![Configurar o logon único](./media/kiteworks-tutorial/tutorial_kiteworks_09.png)   

    a. Selecione **Autenticar via SSO**.

    b. Selecione **Iniciar AuthnRequest**.

    c. Na caixa de texto **ID da Entidade do IDP**, cole o valor da **ID da Entidade do SAML** copiado no portal do Azure. 

    d. Cole o valor da **URL de Serviço de Logon Único do SAML** que você colou do portal do Azure em **URL de Serviço de Logon Único**.

    e. Cole o valor da **URL de Serviço de Saída Única** que você colou do portal do Azure na caixa de texto **URL de Serviço de Logon Único**.

    f. Abra seu certificado baixado no Bloco de Notas, copie o conteúdo e cole-o na caixa de texto **Certificado de Chave Pública RSA** .
 
    g. Clique em **Salvar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/kiteworks-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/kiteworks-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/kiteworks-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/kiteworks-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-kiteworks-test-user"></a>Criar um usuário de teste do Kiteworks

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Kiteworks.

O Kiteworks oferece suporte ao provisionamento Just-In-Time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o Kiteworks se ele ainda não existir.

>[!NOTE]
>Se você precisar criar um usuário manualmente, entre em contato com a [equipe de suporte do Kiteworks](http://accellion.com/support).
 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Kiteworks.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Kiteworks, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **Kiteworks**.

    ![Configurar o logon único](./media/kiteworks-tutorial/tutorial_kiteworks_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

O objetivo desta seção é testar sua configuração de SSO do Azure AD usando o Painel de Acesso.  

Quando você clica no bloco Kiteworks no Painel de Acesso, deve fazer logon automaticamente no seu aplicativo Kiteworks.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kiteworks-tutorial/tutorial_general_01.png
[2]: ./media/kiteworks-tutorial/tutorial_general_02.png
[3]: ./media/kiteworks-tutorial/tutorial_general_03.png
[4]: ./media/kiteworks-tutorial/tutorial_general_04.png

[100]: ./media/kiteworks-tutorial/tutorial_general_100.png

[200]: ./media/kiteworks-tutorial/tutorial_general_200.png
[201]: ./media/kiteworks-tutorial/tutorial_general_201.png
[202]: ./media/kiteworks-tutorial/tutorial_general_202.png
[203]: ./media/kiteworks-tutorial/tutorial_general_203.png

