---
title: 'Tutorial: Integração do Azure Active Directory ao PagerDuty | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o PagerDuty.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c68fdadcf115f57ba524b1b4dc8a2c800cbfaf8
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56177913"
---
# <a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Tutorial: Integração do Azure Active Directory ao PagerDuty

Neste tutorial, você aprenderá a integrar o PagerDuty ao Azure AD (Azure Active Directory).

A integração do PagerDuty ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao PagerDuty
- Você pode permitir que os usuários façam logon automaticamente no PagerDuty (logon único) com as respectivas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao PagerDuty, você precisará dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura do PagerDuty habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o PagerDuty da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-pagerduty-from-the-gallery"></a>Adicionar o PagerDuty da galeria
Para configurar a integração do PagerDuty ao Azure AD, você precisa adicionar o PagerDuty da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o PagerDuty da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **PagerDuty**, selecione **PagerDuty** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/pagerduty-tutorial/tutorial_pagerduty_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o PagerDuty, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do PagerDuty é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no PagerDuty.

No PagerDuty, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o PagerDuty, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do PagerDuty](#create-a-pagerduty-test-user)** – para ter um equivalente de Brenda Fernandes no PagerDuty que esteja vinculado à representação do usuário no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Testar o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo PagerDuty.

**Para configurar o logon único do Azure AD com o PagerDuty, realize as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **PagerDuty**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Caixa de diálogo Logon único](./media/pagerduty-tutorial/tutorial_pagerduty_samlbase.png)

1. Na seção **Domínio e URLs do PagerDuty**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do PagerDuty](./media/pagerduty-tutorial/tutorial_pagerduty_url.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<tenant-name>.pagerduty.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao cliente do PagerDuty](https://www.pagerduty.com/support/) para obter esses valores.

1. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/pagerduty-tutorial/tutorial_pagerduty_certificate.png)

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/pagerduty-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do PagerDuty**, clique em **Configurar o PagerDuty** para abrir a janela **Configurar logon**. Copie a **URL do serviço de logon único do SAML e a URL de logoff** da **seção de Referência Rápida.**

    ![Configuração do PagerDuty](./media/pagerduty-tutorial/tutorial_pagerduty_configure.png)

1. Em outra janela do navegador da Web, faça logon em seu site de empresa do Pagerduty como administrador.

1. No menu na parte superior, clique em **Configurações de Conta**.

    ![Configurações de Conta](./media/pagerduty-tutorial/ic778535.png "Configurações de Conta")

1. Clique em **Logon Único**.

    ![Logon Único](./media/pagerduty-tutorial/ic778536.png "Logon Único")

1. Na página **Habilitar Logon Único (SSO)**, execute as seguintes etapas:

    ![Habilitar logon único](./media/pagerduty-tutorial/ic778537.png "Habilitar logon único")

     a. Abra o certificado codificado em Base 64 baixado no Portal do Azure no bloco de notas, copie o conteúdo dele para a área de transferência e, depois, cole-o na caixa de texto **Certificado X.509**
  
    b. Na caixa de texto **URL de Logon**, cole a **URL do Serviço de Logon Único SAML** copiada do Portal do Azure.
  
    c. Na caixa de texto **URL de Logoff**, cole a **URL de Saída** copiada do Portal do Azure.

    d. Selecione **Permitir logon com nome de usuário/senha**.

    e. Marque a caixa de seleção **Exigir comparação de contexto de autenticação EXACT**.

    f. Clique em **Salvar Alterações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/pagerduty-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/pagerduty-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![O botão Adicionar](./media/pagerduty-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![A caixa de diálogo Usuário](./media/pagerduty-tutorial/create_aaduser_04.png) 

     a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-pagerduty-test-user"></a>Criar um usuário de teste do PagerDuty

Para permitir que os usuários do Azure AD façam logon no PagerDuty, eles devem ser provisionados no PagerDuty.  
No caso do PagerDuty, o provisionamento é uma tarefa manual.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do PagerDuty ou as APIs fornecidas pelo PagerDuty para provisionar as contas de usuário do Azure Active Directory.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **Pagerduty** .

1. No menu na parte superior, clique em **Usuários**.

1. Clique em **Adicionar Usuários**.
   
    ![Adicionar Usuários](./media/pagerduty-tutorial/ic778539.png "Adicionar Usuários")

1.  No diálogo **Convidar sua equipe**, execute as seguintes etapas:
   
    ![Convidar suas equipe](./media/pagerduty-tutorial/ic778540.png "Convidar suas equipe")

     a. Digite o **Nome e Sobrenome** do usuário, por exemplo, **Brenda Fernandes**. 
   
    b. Digite o endereço de **Email** do usuário, por exemplo, **brittasimon@contoso.com**.
   
    c. Clique em **Adicionar** e depois em **Enviar Convites**.
   
    >[!NOTE]
    >Todos os usuários adicionados receberão um convite para criar uma conta do PagerDuty.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao PagerDuty.

![Atribuir a função de usuário][200]

**Para atribuir Brenda Fernandes ao PagerDuty, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **PagerDuty**.

    ![O link do PagerDuty na lista de Aplicativos](./media/pagerduty-tutorial/tutorial_pagerduty_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco PagerDuty no Painel de Acesso, você deverá ser automaticamente conectado ao aplicativo PagerDuty.

Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/pagerduty-tutorial/tutorial_general_01.png
[2]: ./media/pagerduty-tutorial/tutorial_general_02.png
[3]: ./media/pagerduty-tutorial/tutorial_general_03.png
[4]: ./media/pagerduty-tutorial/tutorial_general_04.png

[100]: ./media/pagerduty-tutorial/tutorial_general_100.png

[200]: ./media/pagerduty-tutorial/tutorial_general_200.png
[201]: ./media/pagerduty-tutorial/tutorial_general_201.png
[202]: ./media/pagerduty-tutorial/tutorial_general_202.png
[203]: ./media/pagerduty-tutorial/tutorial_general_203.png
