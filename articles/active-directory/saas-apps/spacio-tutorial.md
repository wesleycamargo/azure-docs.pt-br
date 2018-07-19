---
title: 'Tutorial: Integração do Azure Active Directory ao Spacio | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Spacio.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 9df8d199-b955-483c-aa4e-cabad1a0b9d6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: b341e3cf07a4193c4958910b4d124417f3decf1d
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041179"
---
# <a name="tutorial-azure-active-directory-integration-with-spacio"></a>Tutorial: Integração do Microsoft Azure Active Directory ao Spacio

Neste tutorial, você aprenderá a integrar o Spacio ao Azure AD (Azure Active Directory).

A integração do Spacio ao Microsoft Azure Active Directory oferece os seguintes benefícios:

- Você pode controlar no Microsoft Azure Active Directory quem terá acesso ao Spacio.
- Você pode permitir que seus usuários façam logon automaticamente em Spacio (logon único) com suas contas do Microsoft Azure Active Directory.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure Active Directory ao Spacio, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Spacio

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Spacio da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-spacio-from-the-gallery"></a>Adicionando o Spacio da galeria
Para configurar a integração do Spacio ao Microsoft Azure Active Directory, você precisará adicionar o Spacio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Spacio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Spacio**, selecione **Spacio** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Spacio na lista de resultados](./media/spacio-tutorial/tutorial_spacio_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure Active Directory com o Spacio, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Microsoft Azure Active Directory precisa saber qual usuário do Spacio é equivalente a um usuário do Microsoft Azure Active Directory. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Microsoft Azure Active Directory e o usuário relacionado do Spacio.

Para configurar e testar o logon único do Microsoft Azure Active Directory com o Spacio, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Spacio](#create-a-spacio-test-user)** – para ter um equivalente de Brenda Fernandes no Spacio que esteja vinculado à representação de usuário no Microsoft Azure Active Directory.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Microsoft Azure Active Directory no Portal do Azure e configurará o logon único no aplicativo Spacio.

**Para configurar o logon único do Microsoft Azure Active Directory com o Spacio, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Spacio**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/spacio-tutorial/tutorial_spacio_samlbase.png)

3. Na seção **URLs e Domínio do Spacio**, execute as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do Spacio](./media/spacio-tutorial/tutorial_spacio_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://sso.spac.io/<brokerageID>`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://sso.spac.io/<brokerageID>`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do Spacio](mailto:support@spac.io) para obter esses valores. 

4. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/spacio-tutorial/tutorial_general_400.png)

5. Na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar a **URL de metadados de federação do aplicativo** e cole-a no bloco de notas. 

    ![O link de download do Certificado](./media/spacio-tutorial/tutorial_spacio_certificate.png)

6. Para configurar o logon único no lado do **Spacio**, é necessário enviar a **URL de metadados de federação do aplicativo** para a [equipe de suporte do Spacio](mailto:support@spac.io). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/spacio-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/spacio-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/spacio-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/spacio-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-spacio-test-user"></a>Criar um usuário de teste do Spacio

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Spacio. Trabalhe com a [equipe de suporte do Spacio](mailto:support@spac.io) para adicionar os usuários na plataforma do Spacio. Os usuários devem ser criados e ativados antes de você usar o logon único

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Spacio.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Spacio, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **Spacio**.

    ![O link do Spacio na lista Aplicativos](./media/spacio-tutorial/tutorial_spacio_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco Spacio no Painel de Acesso, deverá ser conectado automaticamente ao seu aplicativo Spacio.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/spacio-tutorial/tutorial_general_01.png
[2]: ./media/spacio-tutorial/tutorial_general_02.png
[3]: ./media/spacio-tutorial/tutorial_general_03.png
[4]: ./media/spacio-tutorial/tutorial_general_04.png

[100]: ./media/spacio-tutorial/tutorial_general_100.png

[200]: ./media/spacio-tutorial/tutorial_general_200.png
[201]: ./media/spacio-tutorial/tutorial_general_201.png
[202]: ./media/spacio-tutorial/tutorial_general_202.png
[203]: ./media/spacio-tutorial/tutorial_general_203.png

