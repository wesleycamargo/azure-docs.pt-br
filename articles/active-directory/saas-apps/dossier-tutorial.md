---
title: 'Tutorial: Integração do Azure Active Directory ao Dossier | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o Dossier.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7a5fec92-9c01-4ced-99b2-a10e28fc028e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2018
ms.author: jeedes
ms.openlocfilehash: 932a832d4717a788f2d9adfd98ce1ba0c4ca07a1
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39507507"
---
# <a name="tutorial-azure-active-directory-integration-with-dossier"></a>Tutorial: integração do Azure Active Directory com o Dossier

Neste tutorial, você aprende a integrar o Dossier ao Microsoft Azure Active Directory.

A integração do Dossier ao Microsoft Azure Active Directory oferece os seguintes benefícios:

- Você pode controlar no Microsoft Azure Active Directory quem terá acesso ao Dossier.
- Você pode permitir que usuários façam logon automaticamente no Dossier (logon único) com as respectivas contas do Microsoft Azure Active Directory.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure Active Directory com o Dossier, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada do Dossier para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Dossier da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-dossier-from-the-gallery"></a>Adicionando o Dossier da galeria

Para configurar a integração do Dossier ao Microsoft Azure Active Directory, você precisará adicionar o Dossier da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Dossier da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Dossier**, selecione **Dossier** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Dossier na lista de resultados](./media/dossier-tutorial/tutorial_dossier_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure Active Directory com o Dossier, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Dossier é equivalente a um usuário do Microsoft Azure Active Directory. Em outras palavras, é necessário estabelecer uma relação de vínculo o Microsoft Azure Active Directory e o usuário relacionado no Dossier precisa ser estabelecido.

Para configurar e testar o logon único do Microsoft Azure Active Directory com o Dossier, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Dossier](#create-a-dossier-test-user)** – para ter um equivalente de Brenda Fernandes no Dossier que esteja vinculado à representação de usuário do Microsoft Azure Active Directory.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Microsoft Azure Active Directory no portal do Azure e configurará o logon único no aplicativo portal Dossier.

**Para configurar o logon único do Microsoft Azure Active Directory com o Dossier, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Dossier**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Caixa de diálogo Logon único](./media/dossier-tutorial/tutorial_dossier_samlbase.png)

3. Na seção **URLs e Domínio do Dossier**, execute as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do Dossier](./media/dossier-tutorial/tutorial_dossier_url1.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão:
    
    | | |
    |-|-|
    | `https://<SUBDOMAIN>.dossiersystems.com/azuresso/account/SignIn`|
    | `https://dossier.<CLIENTDOMAINNAME>/azuresso/account/SignIn`|
    | |

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `Dossier/<CLIENTNAME>`

    > [!NOTE]
    > Para o valor do identificador deve ser no formato de `Dossier/<CLIENTNAME>` ou qualquer usuário personalizadas de valor.

    c. Na caixa de texto **URL de resposta** , digite uma URL no seguinte padrão:
    | | |
    |-|-|
    |  `https://<SUBDOMAIN>.dossiersystems.com/azuresso`|
    | `https://dossier.<CLIENTDOMAINNAME>/azuresso`|
    | |

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Contate a [equipe de suporte ao Cliente do Dossier](mailto:support@intellimedia.ca) para obter esses valores.

4. Na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar a **URL de metadados de federação do aplicativo** e cole-a no Bloco de Notas.

    ![O link de download do Certificado](./media/dossier-tutorial/tutorial_dossier_certificate.png) 

6. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/dossier-tutorial/tutorial_general_400.png)

7. Para configurar o logon único no lado do **Dossier**, é necessário enviar a **URL de metadados de Federação do Aplicativo** para a [equipe de suporte do Dossier](mailto:support@intellimedia.ca). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/dossier-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/dossier-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/dossier-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/dossier-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.

### <a name="create-a-dossier-test-user"></a>Criar um usuário de teste do Dossier

Nesta seção, você criará uma usuária chamado Brenda Fernandes no Dossier. Trabalhe com a [equipe de suporte do Dossier](mailto:support@intellimedia.ca) para adicionar os usuários na plataforma do Dossier. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Dossier.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Dossier, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **Dossier**.

    ![O link do v na lista de Aplicativos](./media/dossier-tutorial/tutorial_dossier_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar na peça do Dossier no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo do Dossier.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/dossier-tutorial/tutorial_general_01.png
[2]: ./media/dossier-tutorial/tutorial_general_02.png
[3]: ./media/dossier-tutorial/tutorial_general_03.png
[4]: ./media/dossier-tutorial/tutorial_general_04.png

[100]: ./media/dossier-tutorial/tutorial_general_100.png

[200]: ./media/dossier-tutorial/tutorial_general_200.png
[201]: ./media/dossier-tutorial/tutorial_general_201.png
[202]: ./media/dossier-tutorial/tutorial_general_202.png
[203]: ./media/dossier-tutorial/tutorial_general_203.png

