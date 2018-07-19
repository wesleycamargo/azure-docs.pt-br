---
title: 'Tutorial: Integração do Azure Active Directory com o FiscalNote | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o FiscalNote.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 55274f26-be7e-4514-964c-7186ecb55c4a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 1eb11b52a89e25603939af9ec42d922d6e7ac0df
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39047402"
---
# <a name="tutorial-azure-active-directory-integration-with-fiscalnote"></a>Tutorial: Integração do Azure Active Directory com o FiscalNote

Neste tutorial, você aprenderá a integrar o FiscalNote ao Azure AD (Azure Active Directory).

A integração do FiscalNote ao AD do Azure oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao FiscalNote.
- Você pode habilitar seus usuários a fazerem logon automaticamente no FiscalNote (logon único) com suas contas do AD do Azure.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o FiscalNote, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do FiscalNote habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do FiscalNote da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-fiscalnote-from-the-gallery"></a>Adição do FiscalNote da galeria
Para configurar a integração do FiscalNote ao Azure AD, você precisará adicionar o FiscalNote da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o FiscalNote a partir da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **FiscalNote**, selecione **FiscalNote** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![FiscalNote na lista de resultados](./media/fiscalnote-tutorial/tutorial_fiscalnote_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você vai configurar e testar o logon único do Azure AD com o FiscalNote, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do FiscalNote é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado no FiscalNote.

Para configurar e testar o logon único do AD do Azure com o FiscalNote, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar de um usuário de teste do FiscalNote](#create-a-fiscalnote-test-user)** – para ter um equivalente de Brenda Fernandes no FiscalNote vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo portal FiscalNote.

**Para configurar o logon único do AD do Azure com o FiscalNote, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **FiscalNote**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Caixa de diálogo Logon único](./media/fiscalnote-tutorial/tutorial_fiscalnote_samlbase.png)

3. Na seção **URLs e Domínio do FiscalNote**, execute as seguintes etapas:

    ![Informações de logon único de URLs e Domínio do FiscalNote](./media/fiscalnote-tutorial/tutorial_fiscalnote_url.png)
    
    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<InstanceName>.fiscalnote.com/login?client=<ClientID>&redirect_uri=https://app.fiscalnote.com/saml-login.html&audience=https://api.fiscalnote.com/&connection=<CONNECTION_NAME>&response_type=id_token%20token`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `urn:auth0:fiscalnote:<CONNECTIONNAME>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte do Cliente FiscalNote](mailto:support@fiscalnote.com) para obter esses valores.

4. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Bruto)** e, em seguida, salve o arquivo de certificado no computador.

    ![O link de download do Certificado](./media/fiscalnote-tutorial/tutorial_fiscalnote_certificate.png)

5. Seu aplicativo FiscalNote espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos de token SAML. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção "**Atributos de Usuário**" na página de integração do aplicativo.

    ![Configurar o logon único](./media/fiscalnote-tutorial/tutorial_attribute.png)

6. Na seção **Atributos de Usuário** da caixa de diálogo **Logon único**, configure o atributo do token SAML, conforme mostrado na imagem anterior e realize as seguintes etapas:
           
    | Nome do atributo | Valor do atributo |
    | ---------------| ----------------|
    | Nome | user.userprincipalname|
    | givenName| user.givenname|
    | familyName| user.surname|
    | email| user.mail|
    
    a. Remova os atributos existentes e adicione novos atributos. Clique em **Adicionar Atributo** para abrir a caixa de diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/fiscalnote-tutorial/tutorial_attribute_04.png)
    
    ![Configurar o logon único](./media/fiscalnote-tutorial/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Deixe o namespace em branco.
    
    e. Clique em **OK**.

7. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/fiscalnote-tutorial/tutorial_general_400.png)

8. Na seção **Configuração do FiscalNote**, clique em **Configurar o FiscalNote** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do FiscalNote](./media/fiscalnote-tutorial/tutorial_fiscalnote_configure.png) 

9. Para configurar o logon único no lado do **FiscalNote**, é necessário enviar o **Certificado (Bruto) baixado, a URL de Saída, ID da Entidade SAML e URL do Serviço de Logon Único SAML**para a [equipe de suporte do FiscalNote](mailto:support@fiscalnote.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/fiscalnote-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/fiscalnote-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/fiscalnote-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/fiscalnote-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-fiscalnote-test-user"></a>Criar um usuário de teste do FiscalNote

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no FiscalNote. O FiscalNote dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o FiscalNote, caso ele ainda não exista.
>[!Note]
>Se você precisar criar um usuário manualmente, contate a [equipe de suporte do FiscalNote](mailto:support@fiscalnote.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao FiscalNote.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao FiscalNote, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **FiscalNote**.

    ![O link do FiscalNote na lista de Aplicativos](./media/fiscalnote-tutorial/tutorial_fiscalnote_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco FiscalNote no Painel de Acesso, deve fazer logon automaticamente no seu aplicativo FiscalNote.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/fiscalnote-tutorial/tutorial_general_01.png
[2]: ./media/fiscalnote-tutorial/tutorial_general_02.png
[3]: ./media/fiscalnote-tutorial/tutorial_general_03.png
[4]: ./media/fiscalnote-tutorial/tutorial_general_04.png

[100]: ./media/fiscalnote-tutorial/tutorial_general_100.png

[200]: ./media/fiscalnote-tutorial/tutorial_general_200.png
[201]: ./media/fiscalnote-tutorial/tutorial_general_201.png
[202]: ./media/fiscalnote-tutorial/tutorial_general_202.png
[203]: ./media/fiscalnote-tutorial/tutorial_general_203.png

