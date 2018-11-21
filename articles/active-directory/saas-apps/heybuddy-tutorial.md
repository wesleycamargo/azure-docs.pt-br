---
title: 'Tutorial: Integração do Azure Active Directory com HeyBuddy | Microsoft Docs'
description: Saiba como configurar logon único entre o Azure Active Directory e o HeyBuddy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d51b5af6-018e-4678-9a3f-b70438394f67
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2018
ms.author: jeedes
ms.openlocfilehash: 3ef345d866de16b856d3c8b56fa086b83b74ae0f
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51289543"
---
# <a name="tutorial-azure-active-directory-integration-with-heybuddy"></a>Tutorial: Integração do Azure Active Directory com HeyBuddy

Neste tutorial, você aprenderá como integrar o HeyBuddy ao Azure AD (Azure Active Directory).

A integração do HeyBuddy ao Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao HeyBuddy.
- Você pode permitir que seus usuários façam logon automaticamente no HeyBuddy (Logon único) com as contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o HeyBuddy, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do HeyBuddy

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando HeyBuddy da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-heybuddy-from-the-gallery"></a>Adicionando HeyBuddy da galeria

Para configurar a integração do HeyBuddy ao Azure AD, você precisará adicionar o HeyBuddy da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o HeyBuddy da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **HeyBuddy**, selecione **HeyBuddy** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![HeyBuddy na lista de resultados](./media/heybuddy-tutorial/tutorial_heybuddy_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o HeyBuddy, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do HeyBuddy é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no HeyBuddy.

Para configurar e testar o logon único do Azure AD com o HeyBuddy, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do HeyBuddy](#creating-a-heybuddy-test-user)** – para ter um equivalente de Brenda Fernandes no HeyBuddy que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único no aplicativo HeyBuddy.

**Para configurar o logon único do Azure AD com o HeyBuddy, execute as etapas a seguir:**

1. No portal do Azure, na página de integração do aplicativo **HeyBuddy**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](common/tutorial_general_301.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Configurar o logon único](common/editconfigure.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do HeyBuddy](./media/heybuddy-tutorial/tutorial_heybuddy_url.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://api.heybuddy.com/auth/<ENTITY ID>`

    b. Na caixa de texto **Identificador (ID da Entidade)**, digite uma URL usando o seguinte padrão: `YourCompanyInstanceofHeyBuddy`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais (ID de entidade). O valor `Entity ID` na URL de Entrada é gerado automaticamente para cada organização. Contate a [equipe de suporte do Cliente HeyBuddy](mailto:support@heybuddy.com) para obter esses valores.

5. O aplicativo HeyBuddy espera as declarações SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos na seção **Atributos e reivindicações do usuário** na página de integração de aplicativos. Na página **Configurar logon único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **User Attributes & Claims**.

    ![image](./media/heybuddy-tutorial/tutorial_heybuddy_attribute.png)

    >[!NOTE]
    >Consulte este [link](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) sobre como instalar e configurar as funções para o aplicativo.

6. Na seção **Reivindicações do usuário** da caixa de diálogo **Atributos e reivindicações do usuário**, configure o atributo do token SAML conforme mostrado na imagem acima e execute as seguintes etapas:
    
    | NOME  | Atributo de Origem  |
    | --------------- | --------------- |
    | Funções               | user.assignedroles |

     a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](./common/new_save_attribute.png)

    ![image](./common/new_attribute_details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o valor de **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Salvar**.

7. Na página **Certificado de assinatura SAML**, na seção **Certificado de assinatura SAML**, clique no **ícone** de cópia para copiar  **URL de metadados da federação de aplicativo**  e cole-o no bloco de notas.

    ![O link de download do Certificado](./media/heybuddy-tutorial/tutorial_heybuddy_certificate.png) 

8. Para configurar o logon único no lado do **HeyBuddy**, é necessário enviar a **URL de metadados de federação do aplicativo** copiada para a [equipe de suporte do HeyBuddy](mailto:support@heybuddy.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Criar um usuário do AD do Azure][100]

2. Selecione **Novo usuário** na parte superior da tela.

    ![Criação de um usuário de teste do AD do Azure](common/create_aaduser_01.png) 

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![Criação de um usuário de teste do AD do Azure](common/create_aaduser_02.png)

     a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No **nome de usuário** , digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.

### <a name="creating-a-heybuddy-test-user"></a>Criando um usuário de teste do HeyBuddy

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no HeyBuddy. O HeyBuddy dá suporte para provisionamento just-in-time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o HeyBuddy, se ainda não existir.
>[!Note]
>Se for necessário criar um usuário manualmente, contate a  [equipe de suporte do HeyBuddy](mailto:support@heybuddy.com).

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que o Brenda Fernandes use o logon único do Azure, concedendo acesso ao HeyBuddy.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, escolha **HeyBuddy**.

    ![Configurar o logon único](./media/heybuddy-tutorial/tutorial_heybuddy_app.png)

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco HeyBuddy no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo HeyBuddy.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
