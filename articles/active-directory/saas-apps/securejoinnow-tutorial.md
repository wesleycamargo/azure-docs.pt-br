---
title: 'Tutorial: Integração do Azure Active Directory ao SecureW2 JoinNow Connector | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SecureW2 JoinNow Connector.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2445b3af-f827-40de-9097-6f5c933d0f53
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2018
ms.author: jeedes
ms.openlocfilehash: 283f8c935556006a21812578d0638b72adb6eed0
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2018
ms.locfileid: "37906312"
---
# <a name="tutorial-azure-active-directory-integration-with-securew2-joinnow-connector"></a>Tutorial: Integração do Azure Active Directory ao SecureW2 JoinNow Connector

Neste tutorial, você aprenderá a integrar o SecureW2 JoinNow Connector ao Azure AD (Azure Active Directory).

A integração do SecureW2 JoinNow Connector ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao SecureW2 JoinNow Connector.
- Você pode permitir que seus usuários façam logon automaticamente no SecureW2 JoinNow Connector (logon único) com suas respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Azure AD com o SecureW2 JoinNow Connector, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do SecureW2 JoinNow Connector

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o SecureW2 JoinNow Connector da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-securew2-joinnow-connector-from-the-gallery"></a>Adicionando o SecureW2 JoinNow Connector da galeria
Para configurar a integração do SecureW2 JoinNow Connector com o Azure AD, você precisará adicionar o SecureW2 JoinNow Connector da galeria para a lista de aplicativos de SaaS gerenciados.

**Para adicionar o SecureW2 JoinNow Connector da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **SecureW2 JoinNow Connector**, selecione **SecureW2 JoinNow Connector** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![SecureW2 JoinNow Connector na lista de resultados](./media/securejoinnow-tutorial/tutorial_securejoinnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o SecureW2 JoinNow Connector com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do SecureW2 JoinNow Connector é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SecureW2 JoinNow Connector.

Para configurar e testar o logon único do Azure AD com o SecureW2 JoinNow Connector, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do SecureW2 JoinNow Connector](#create-a-securew2-joinnow-connector-test-user)** – para ter um equivalente de Brenda Fernandes no SecureW2 JoinNow Connector que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único no aplicativo SecureW2 JoinNow Connector.

**Para configurar o logon único do Azure AD com o SecureW2 JoinNow Connector, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo do **SecureW2 JoinNow Connector**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/securejoinnow-tutorial/tutorial_securejoinnow_samlbase.png)

3. Na seção **Domínio e URLs do SecureW2 JoinNow Connector**, execute as seguintes etapas:

    ![Informações de logon único de domínio e de URLs do SecureW2 JoinNow Connector](./media/securejoinnow-tutorial/tutorial_securejoinnow_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<organization-identifier>-auth.securew2.com/auth/saml/SSO`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<organization-identifier>-auth.securew2.com/auth/saml`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao cliente do SecureW2 JoinNow Connector](mailto:support@securew2.com) para obter esses valores. 

4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/securejoinnow-tutorial/tutorial_securejoinnow_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/securejoinnow-tutorial/tutorial_general_400.png)

6. Para configurar o logon único no lado do **SecureW2 JoinNow Connector**, é necessário enviar o **XML de metadados** baixado para a [equipe de suporte do SecureW2 JoinNow Connector](mailto:support@securew2.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/securejoinnow-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/securejoinnow-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/securejoinnow-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/securejoinnow-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-securew2-joinnow-connector-test-user"></a>Criar um usuário de teste do SecureW2 JoinNow Connector

Nesta seção, você cria um usuário chamado Brenda Fernandes no SecureW2 JoinNow Connector. Trabalhe com a [equipe de suporte ao cliente do SecureW2 JoinNow Connector](mailto:support@securew2.com) para adicionar os usuários na plataforma do SecureW2 JoinNow Connector. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao SecureW2 JoinNow Connector.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao SecureW2 JoinNow Connector, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **SecureW2 JoinNow Connector**.

    ![O link do SecureW2 JoinNow Connector na lista de aplicativos](./media/securejoinnow-tutorial/tutorial_securejoinnow_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

**Para testar o aplicativo, execute as seguintes etapas:** 

a. Abra o cliente SecureW2 JoinNow Connector, selecione o dispositivo apropriado na lista e clique no botão **Entrar**.

b. O navegador padrão será aberto e você será redirecionado para o portal do Azure para realizar a autenticação.

c. Após a autenticação bem-sucedida, você retornará à página de aterrissagem inicial do SecureW2 JoinNow Connector.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/securejoinnow-tutorial/tutorial_general_01.png
[2]: ./media/securejoinnow-tutorial/tutorial_general_02.png
[3]: ./media/securejoinnow-tutorial/tutorial_general_03.png
[4]: ./media/securejoinnow-tutorial/tutorial_general_04.png

[100]: ./media/securejoinnow-tutorial/tutorial_general_100.png

[200]: ./media/securejoinnow-tutorial/tutorial_general_200.png
[201]: ./media/securejoinnow-tutorial/tutorial_general_201.png
[202]: ./media/securejoinnow-tutorial/tutorial_general_202.png
[203]: ./media/securejoinnow-tutorial/tutorial_general_203.png

