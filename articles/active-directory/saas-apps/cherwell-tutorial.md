---
title: 'Tutorial: integração do Azure Active Directory ao Cherwell | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Cherwell.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: ad891f99-179e-4487-834d-35f3bc01c1ec
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/03/2017
ms.author: jeedes
ms.openlocfilehash: 9643a65eb2618a6b9ed5834024a1f29450c54af6
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421224"
---
# <a name="tutorial-azure-active-directory-integration-with-cherwell"></a>Tutorial: Integração do Active Directory do Azure ao Cherwell

Neste tutorial, você aprende a integrar o Cherwell ao Azure AD (Azure Active Directory).

A integração do Cherwell ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Cherwell
- É possível permitir que os usuários se conectem automaticamente ao Cherwell (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Cherwell, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Cherwell

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Cherwell por meio da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-cherwell-from-the-gallery"></a>Adicionando o Cherwell por meio da galeria
Para configurar a integração do Cherwell ao Azure AD, você precisa adicionar o Cherwell à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Cherwell por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **Cherwell**.

    ![Criação de um usuário de teste do AD do Azure](./media/cherwell-tutorial/tutorial_cherwell_search.png)

1. No painel de resultados, selecione **Cherwell** e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/cherwell-tutorial/tutorial_cherwell_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configura e testa o logon único do Azure AD com o Cherwell, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Cherwell é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Cherwell.

No Cherwell, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Cherwell, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criando um usuário de teste do Cherwell](#creating-a-cherwell-test-user)** – para ter um equivalente de Brenda Fernandes no Cherwell que esteja vinculado à representação de usuário do Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Cherwell.

**Para configurar o logon único do Azure AD com o Cherwell, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo do **Cherwell**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/cherwell-tutorial/tutorial_cherwell_samlbase.png)

1. Na seção **Domínio e URLs do Cherwell**, realize as seguintes etapas:

    ![Configurar o logon único](./media/cherwell-tutorial/tutorial_cherwell_url.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.cherwellondemand.com/cherwellclient`

    > [!NOTE] 
    > Esse valor não é real. Atualize esse valor com a URL de Logon real. Contate a [equipe de suporte do Cherwell](https://csm.cherwell.com/contact) para obter esse valor.
 
1. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar o logon único](./media/cherwell-tutorial/tutorial_cherwell_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/cherwell-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do Cherwell**, clique em **Configurar o Cherwell** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção Referência Rápida.**

    ![Configurar o logon único](./media/cherwell-tutorial/tutorial_cherwell_configure.png) 

1. Para configurar o logon único no lado do **Cherwell**, é necessário enviar o **Certificado (Base64)** baixado, a **URL do Serviço de Logon Único SAML** e a **ID da Entidade SAML** para a [equipe de suporte do Cherwell](https://csm.cherwell.com/contact). 

    >[!NOTE]
    >A equipe de suporte do Cherwell precisa fazer a configuração real do SSO. Você receberá uma notificação quando o SSO tiver sido habilitado para sua assinatura.
    > 
    
> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/cherwell-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/cherwell-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/cherwell-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/cherwell-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-cherwell-test-user"></a>Criando um usuário de teste do Cherwell

Para permitir que os usuários do Azure AD façam logon no Cherwell, eles devem ser provisionados no Cherwell.

No caso do Cherwell, as contas de usuário precisam ser criadas pela [equipe de suporte do Cherwell](https://csm.cherwell.com/contact).

>[!NOTE]
>Você pode usar qualquer outra ferramenta de criação da conta de usuário do Cherwell ou as APIs fornecidas pelo Cherwell para provisionar as contas de usuário do Active Directory do Azure.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Cherwell.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Cherwell, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **Cherwell**.

    ![Configurar o logon único](./media/cherwell-tutorial/tutorial_cherwell_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/cherwell-tutorial/tutorial_general_01.png
[2]: ./media/cherwell-tutorial/tutorial_general_02.png
[3]: ./media/cherwell-tutorial/tutorial_general_03.png
[4]: ./media/cherwell-tutorial/tutorial_general_04.png

[100]: ./media/cherwell-tutorial/tutorial_general_100.png

[200]: ./media/cherwell-tutorial/tutorial_general_200.png
[201]: ./media/cherwell-tutorial/tutorial_general_201.png
[202]: ./media/cherwell-tutorial/tutorial_general_202.png
[203]: ./media/cherwell-tutorial/tutorial_general_203.png

