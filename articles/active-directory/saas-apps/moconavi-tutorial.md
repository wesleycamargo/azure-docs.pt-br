---
title: 'Tutorial: Integração do Azure Active Directory com moconavi | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o moconavi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e1916224-e1c2-426f-b233-0a2518fa41db
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76d1621834c5096743f6d37dad94754ec8b259a4
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56196239"
---
# <a name="tutorial-azure-active-directory-integration-with-moconavi"></a>Tutorial: Integração do Azure Active Directory com moconavi

Neste tutorial, você aprenderá a integrar o moconavi ao Azure AD (Azure Active Directory).

A integração do moconavi ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao moconavi.
- Você pode permitir que seus usuários façam logon automaticamente no moconavi (logon único) com as contas do Azure AD deles.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o moconavi, você precisará dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura habilitada para logon único do moconavi

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.
 O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o moconavi da Galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-moconavi-from-the-gallery"></a>Adicionando o moconavi da Galeria
Para configurar a integração do moconavi ao Azure AD, você precisará adicionar o moconavi da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o moconavi da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **moconavi**, selecione **moconavi** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![moconavi na lista de resultados](./media/moconavi-tutorial/tutorial_moconavi_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o moconavi, com base em um usuário de teste chamado “Britta Simon”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do moconavi é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no moconavi.

Para configurar e testar o logon único do Azure AD com o moconavi, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do moconavi](#create-a-moconavi-test-user)** – para ter um equivalente de Britta Simon no moconavi que esteja vinculado à representação de usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único no aplicativo moconavi.

**Para configurar o logon único do Azure AD com o moconavi, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **moconavi**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Caixa de diálogo Logon único](./media/moconavi-tutorial/tutorial_moconavi_samlbase.png)

3. Na seção **Domínio e URLs do moconavi**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do moconavi](./media/moconavi-tutorial/tutorial_moconavi_url.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<yourserverurl>/moconavi-saml2/saml/login`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<yourserverurl>/moconavi-saml2`

    C. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<yourserverurl>/moconavi-saml2/saml/SSO`

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte ao cliente do moconavi](mailto:support@recomot.co.jp) para obter esses valores.

4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/moconavi-tutorial/tutorial_moconavi_certificate.png)

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/moconavi-tutorial/tutorial_general_400.png)

6. Para configurar o logon único no lado do **moconavi**, é necessário enviar o **XML de Metadados** baixado para a [equipe de suporte do moconavi](mailto:support@recomot.co.jp). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/moconavi-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/moconavi-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/moconavi-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/moconavi-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.

### <a name="create-a-moconavi-test-user"></a>Criar um usuário de teste do moconavi

Nesta seção, você criará uma usuária chamada Britta Simon no moconavi. Trabalhe com a  [equipe de suporte do moconavi](mailto:support@recomot.co.jp)  para adicionar os usuários na plataforma do moconavi. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Britta Simon use o logon único do Azure concedendo a ela acesso ao moconavi.

![Atribuir a função de usuário][200]

**Para atribuir Britta Simon ao moconavi, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, escolha **moconavi**.

    ![O link do moconavi na lista Aplicativos](./media/moconavi-tutorial/tutorial_moconavi_app.png)

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.

### <a name="test-single-sign-on"></a>Testar logon único

1. Instale o moconavi da Microsoft Store.

2. Inicie o moconavi.

3. Clique no botão **Configuração de conexão**.

    ![Teste do logon único](./media/moconavi-tutorial/testing1.png)

4. Digite `https://mcs-admin.moconavi.biz/gateway` na caixa de texto **Conectar-se à URL** e clique no botão **Concluído**.

    ![Teste do logon único](./media/moconavi-tutorial/testing2.png)

5. Na captura de tela a seguir, execute as seguintes etapas:

    ![Teste do logon único](./media/moconavi-tutorial/testing3.png)

     a. Digite **Chave de Autenticação de Entrada**:`azureAD` na caixa de texto **Chave de Autenticação de Entrada**.

    b. Digite **ID de Usuário de Entrada**: `your ad account` na caixa de texto **ID de Usuário de Entrada**.

    c. Clique em **LOGON**.

6. Insira sua senha do Azure AD n caixa de texto **Senha** e clique no botão **Logon**.

    ![Teste do logon único](./media/moconavi-tutorial/testing4.png)

7. A autenticação do Azure AD tem êxito quando o menu é exibido.

    ![Teste do logon único](./media/moconavi-tutorial/testing5.png)

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/moconavi-tutorial/tutorial_general_01.png
[2]: ./media/moconavi-tutorial/tutorial_general_02.png
[3]: ./media/moconavi-tutorial/tutorial_general_03.png
[4]: ./media/moconavi-tutorial/tutorial_general_04.png

[100]: ./media/moconavi-tutorial/tutorial_general_100.png

[200]: ./media/moconavi-tutorial/tutorial_general_200.png
[201]: ./media/moconavi-tutorial/tutorial_general_201.png
[202]: ./media/moconavi-tutorial/tutorial_general_202.png
[203]: ./media/moconavi-tutorial/tutorial_general_203.png

