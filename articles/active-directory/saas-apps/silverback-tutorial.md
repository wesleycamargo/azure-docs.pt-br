---
title: 'Tutorial: Integração do Azure Active Directory com o Silverback | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e Silverback.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32cfc96f-2137-49ff-818b-67feadcd73b7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5614c061586c39e44f04f3542285e55e07f14d9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56172698"
---
# <a name="tutorial-azure-active-directory-integration-with-silverback"></a>Tutorial: Integração do Azure Active Directory com o Silverback

Neste tutorial, você aprenderá a integrar o Silverback ao Microsoft Azure Active Directory.

A integração Silverback com o Microsoft Azure Active Directory oferece os seguintes benefícios:

- Você pode controlar no Microsoft Azure Active Directory quem tem acesso ao Silverback.
- Você pode habilitar seus usuários a obter logon automaticamente para Silverback (logon único) com suas contas do Microsoft Azure Active Directory.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure Active Directory ao Silverback, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura ativa do Silverback

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Silverback da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-silverback-from-the-gallery"></a>Adicionar o Silverback da galeria
Para configurar a integração do Silverback ao Microsoft Azure Active Directory você precisará adicionar o Silverback da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Silverback por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Silverback**, selecione **Silverback** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Silverback na lista de resultados](./media/silverback-tutorial/tutorial_silverback_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você vai configurar e testar o logon único do Microsoft Azure Active Directory com o Silverback, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Silverback é equivalente a um usuário do Microsoft Azure Active Directory. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Microsoft Azure Active Directory e o usuário relacionado no Silverback.

Para configurar e testar o logon único do Microsoft Azure Active Directory com o Silverback, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar de um usuário de teste do Silverback](#create-a-silverback-test-user)** : para ter um equivalente de Brenda Fernandes no Silverback que esteja vinculado à representação de usuário no Microsoft Azure Active Directory.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Microsoft Azure Active Directory no portal do Azure e configurará o logon único no aplicativo Silverback.

**Para configurar o logon único do Microsoft Azure Active Directory com o Silverback, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Silverback**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/silverback-tutorial/tutorial_silverback_samlbase.png)

3. Na seção **Domínio e URLs do Silverback**, execute as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do Silverback](./media/silverback-tutorial/tutorial_silverback_url.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<YOURSILVERBACKURL>.com/ssp`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `<YOURSILVERBACKURL>.com`

    c. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<YOURSILVERBACKURL>.com/sts/authorize/login`

    > [!NOTE] 
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Contate a [equipe de suporte do Cliente Silverback](mailto:helpdesk@matrix42.com) para obter esses valores. 

4. Na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar a **URL de metadados de federação do aplicativo** e cole-a no bloco de notas.

    ![O link de download do Certificado](./media/silverback-tutorial/tutorial_silverback_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/silverback-tutorial/tutorial_general_400.png)

6.  Faça logon em seu servidor Silverback como administrador e execute as seguintes etapas:

     a.  Navegue até **Admin** > **provedor de autenticação**.

    b. Na página **Configurações do Provedor de Autenticação**, execute as etapas a seguir:

    ![O administrador ](./media/silverback-tutorial/tutorial_silverback_admin.png)

    c.  Clique em **Importar da URL**.
    
    d.  Cole a URL de metadados copiados e clique em **OK**.
    
    e.  Confirme com **OK** e em seguida, os valores serão preenchidos automaticamente.
    
    f.  Habilitar **Mostrar na página de logon**.
    
    g.  Habilitar **Criação Dinâmica de Usuárioa** se você quiser adicionar automaticamente por usuários do Microsoft Azure Active Directory autorizado (opcional).
    
    h.  Criar um **título** para o botão sobre o Portal de autoatendimento.

    i.  Carregar uma **ícone** clicando em **Escolher arquivo**.
    
    j.  Selecione o plano de fundo **cor** do botão.
    
    k.  Clique em **Salvar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/silverback-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/silverback-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/silverback-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/silverback-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-silverback-test-user"></a>Criar um usuário de teste Silverback

Para habilitar os usuários do Microsoft Azure Active Directory no Silverback, devem ser provisionados no Silverback. No Silverback, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon no servidor de processo como administrador.

2. Navegue até  **Usuários** e **adicione um novo usuário do dispositivo**.

3. Na página **Básico**, execute as seguintes etapas:

    ![O usuário ](./media/silverback-tutorial/tutorial_silverback_user.png)

     a. Na caixa de texto **Nome de usuário**, digite o nome do usuário, como **Brenda**.

    b. Na caixa de texto **Nome**, insira o nome do usuário como **Brenda**.

    c. Na caixa de texto **Sobrenome**, insira o nome do usuário como **Fernandes**.

    d. Na caixa de texto **Endereço de Email**, insira o email do usuário como **Brittasimon@contoso.com**.

    e. Na caixa de texto **Senha**, insira sua senha.
    
    f. Na caixa de texto **Confirmar senha**, digite sua senha novamente e confirme.

    g. Clique em **Salvar**.

>[!NOTE]
>Se você não quiser criar manualmente para cada usuário habilitar a **Criação Dinâmica de Usuário** caixa de seleção em **Admin** > **Provedor de Autenticação**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Silverback.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Silverback, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **Silverback**.

    ![O link do Silverback na lista de Aplicativos](./media/silverback-tutorial/tutorial_silverback_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar na peça Silverback no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Silverback.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/silverback-tutorial/tutorial_general_01.png
[2]: ./media/silverback-tutorial/tutorial_general_02.png
[3]: ./media/silverback-tutorial/tutorial_general_03.png
[4]: ./media/silverback-tutorial/tutorial_general_04.png

[100]: ./media/silverback-tutorial/tutorial_general_100.png

[200]: ./media/silverback-tutorial/tutorial_general_200.png
[201]: ./media/silverback-tutorial/tutorial_general_201.png
[202]: ./media/silverback-tutorial/tutorial_general_202.png
[203]: ./media/silverback-tutorial/tutorial_general_203.png

