---
title: 'Tutorial: Integração do Azure Active Directory com Zoho | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e Zoho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 9874e1f3-ade5-42e7-a700-e08b3731236a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: jeedes
ms.openlocfilehash: 03950d983f6ed119ae6cf7a7391418804bb20c76
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39446257"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Tutorial: Integração do Azure Active Directory com Zoho

Neste tutorial, você aprenderá a integrar o Zoho ao Azure AD (Azure Active Directory).

A integração do Zoho com o Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem terá acesso ao Zoho.
- Você pode habilitar seus usuários para conexão automática no Zoho (Logon Único) com suas contas Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Zoho, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Zoho

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar Zoho da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-zoho-from-the-gallery"></a>Adicionar Zoho da galeria
Para configurar a integração do Zoho com o Azure AD, você precisará adicionar o Zoho à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Zoho da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **Zoho**, selecione **Zoho** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Zoho na lista de resultados](./media/zoho-mail-tutorial/tutorial_zoho_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Zoho, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Zoho é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado no Zoho.

NoZoho, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vinculação.

Para configurar e testar o logon único do Azure AD com o Zoho, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do Zoho](#create-a-zoho-test-user)** – para ter um equivalente de Brenda Fernandes no Zoho que esteja vinculado à representação de usuário do Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no seu aplicativo Zoho.

**Para configurar o logon único do Azure AD com o Zoho, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Zoho**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/zoho-mail-tutorial/tutorial_zoho_samlbase.png)

1. Na seção **URLs e Domínio do Zoho**, execute as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do Zoho](./media/zoho-mail-tutorial/tutorial_zoho_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<company name>.zohomail.com`

    > [!NOTE] 
    > Esse valor não é real. Atualize esse valor com a URL de Logon real. Entre em contato com a [equipe de suporte ao cliente do Zoho](https://www.zoho.com/mail/contact.html) para obter esse valor. 
 
1. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/zoho-mail-tutorial/tutorial_zoho_certificate.png) 

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/zoho-mail-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do Zoho** clique em **Configurar Zoho** para abrir a janela **Configurar logon**. Copie a **URL de Saída, a URL de Alteração de Senha e a URL do Serviço de Logon Único SAML** da **seção Referência Rápida.**

    ![Configuração do Zoho](./media/zoho-mail-tutorial/tutorial_zoho_configure.png) 

1. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa Zoho Mail como administrador.

1. Vá para o **Painel de Controle**.
   
    ![Painel de controle](./media/zoho-mail-tutorial/ic789607.png "Painel de controle")

1. Clique na guia **Autenticação SAML** .
   
    ![Autenticação SAML](./media/zoho-mail-tutorial/ic789608.png "Autenticação SAML")

1. Na seção **Detalhes da Autenticação SAML** , realize as seguintes etapas:
   
    ![Detalhes de Autenticação SAML](./media/zoho-mail-tutorial/ic789609.png "Detalhes de Autenticação SAML")
   
    a. Na caixa de texto **URL de Logon**, cole a **URL do Serviço de Logon Único SAML** copiada do portal do Azure.
   
    b. Na caixa de texto **URL de Logoff**, cole a **URL de Saída** copiada do portal do Azure.
   
    c. Na caixa de texto **Alterar URL de Senha**, cole **Alterar URL da Senha** copiada do portal do Azure.
       
    d. Abra o certificado codificado em BASE-64 baixado no portal do Azure no bloco de notas, copie o conteúdo dele para a área de transferência e, depois, cole-o na caixa de texto **PublicKey**.
   
    e. Para **Algoritmo**, selecione **RSA**.
   
    f. Clique em **OK**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/zoho-mail-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/zoho-mail-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/zoho-mail-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/zoho-mail-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-zoho-test-user"></a>Criar um usuário de teste do Zoho

Para permitir que os usuários do AD do Azure façam logon no Zoho Mail, eles devem ser provisionados no Zoho Mail. No caso do Zoho Mail, o provisionamento é uma tarefa manual.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Zoho Mail ou APIs fornecidas pelo Zoho Mail para provisionar as contas de usuário do AAD.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para provisionar uma conta de usuário, execute as seguintes etapas:

1. Faça logon em seu site de empresa do **Zoho Mail** como administrador.

1. Vá para **Painel de Controle \> Emails e Documentos**.

1. Vá para **Detalhes do Usuário \> Adicionar Usuário**.
   
    ![Adicionar Usuário](./media/zoho-mail-tutorial/ic789611.png "Adicionar Usuário")

1. No diálogo **Adicionar usuários** , realize as seguintes etapas:
   
    ![Adicionar Usuário](./media/zoho-mail-tutorial/ic789612.png "Adicionar Usuário")
   
    a. Na caixa de texto **Nome**, digite o Nome do usuário, como **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite o Sobrenome do usuário, como **Fernandes**.

    c. Na caixa de texto **ID de Email** digite a identificação do email do usuário, como **brittasimon@contoso.com**.

    d. Na caixa de texto **Senha** digite a senha do usuário.
   
    e. Clique em **OK**.  
      
    > [!NOTE]
    > O titular da conta do Active Directory do Azure receberá um email com um link para confirmar a conta antes que ela se torne ativa.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Zoho.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Zoho, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, escolha **Zoho**.

    ![O link do Zoho na lista de Aplicativos](./media/zoho-mail-tutorial/tutorial_zoho_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco Zoho no Painel de Acesso, deverá conectar automaticamente no seu aplicativo Zoho.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zoho-mail-tutorial/tutorial_general_01.png
[2]: ./media/zoho-mail-tutorial/tutorial_general_02.png
[3]: ./media/zoho-mail-tutorial/tutorial_general_03.png
[4]: ./media/zoho-mail-tutorial/tutorial_general_04.png

[100]: ./media/zoho-mail-tutorial/tutorial_general_100.png

[200]: ./media/zoho-mail-tutorial/tutorial_general_200.png
[201]: ./media/zoho-mail-tutorial/tutorial_general_201.png
[202]: ./media/zoho-mail-tutorial/tutorial_general_202.png
[203]: ./media/zoho-mail-tutorial/tutorial_general_203.png

