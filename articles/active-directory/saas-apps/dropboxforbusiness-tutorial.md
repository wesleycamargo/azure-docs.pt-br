---
title: 'Tutorial: integração do Azure Active Directory do ao Dropbox for Business | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Dropbox for Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2018
ms.author: jeedes
ms.openlocfilehash: eadf6724891d348c2ea3654bcf19ef0d74078049
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2018
ms.locfileid: "42144778"
---
# <a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Tutorial: integração do Active Directory do Azure ao Dropbox for Business

Neste tutorial, você aprende a integrar o Dropbox for Business ao Azure AD (Azure Active Directory).

A integração do Dropbox for Business ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Dropbox for Business.
- É possível permitir que os usuários se conectem automaticamente ao Dropbox for Business (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Dropbox for Business, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Dropbox for Business

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Dropbox for Business por meio da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Adicionando o Dropbox for Business por meio da galeria

Para configurar a integração do Dropbox for Business ao Azure AD, é necessário adicionar o Dropbox for Business à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Dropbox for Business por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Dropbox for Business**, selecione **Dropbox for Business** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Dropbox for Business na lista de resultados](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Dropbox for Business, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Dropbox for Business é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Dropbox for Business.

No Dropbox for Business, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Dropbox for Business, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Crie um usuário de teste do Dropbox for Business](#create-a-dropbox-for-business-test-user)** – para ter um equivalente de Brenda Fernandes no Dropbox for Business que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Dropbox for Business.

**Para configurar o logon único do Azure AD com o Dropbox for Business, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Dropbox for Business**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_samlbase.png)

3. Na seção **Domínio e URLs do Dropbox for Business**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Dropbox for Business](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_url1.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://www.dropbox.com/sso/<id>`

    b. Na caixa de texto **Identificador**, digite um valor: `Dropbox`

    > [!NOTE]
    > O valor da URL de entrada anterior não é um valor real. Você atualizará o valor com a URL de entrada real, que é explicada no tutorial posteriormente.

4. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/dropboxforbusiness-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Dropbox for Business** do portal do Azure, clique em **Configurar o Dropbox for Business** para abrir a janela **Configurar logon**. Copie a **URL de serviço de logon único SAML** da **seção de Referência Rápida.**

    ![Configuração do Dropbox for Business](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_configure.png) 

7. Para configurar o login único no **Dropbox for Business**, acesse o locatário do Dropbox for Business e faça login no Dropbox for business inquilino.

    ![Configurar logon único](./media/dropboxforbusiness-tutorial/ic769509.png "Configurar logon único")

8. Clique na guia **ícone do usuário** e selecione **configurações**.

    ![Configurar logon único](./media/dropboxforbusiness-tutorial/configure1.png "Configurar logon único")

9. No painel de navegação à esquerda, clique em **console de administração**.

    ![Configurar logon único](./media/dropboxforbusiness-tutorial/configure2.png "Configurar logon único")

10. Sobre o **console de administração**, clique em **configurações** no painel de navegação à esquerda.

    ![Configurar logon único](./media/dropboxforbusiness-tutorial/configure3.png "Configurar logon único")

11. Selecione a opção **Conexão única** na seção **Autenticação**.

    ![Configurar logon único](./media/dropboxforbusiness-tutorial/configure4.png "Configurar logon único")

12. Na seção **Conexão única**, execute as seguintes etapas:  

    ![Configurar logon único](./media/dropboxforbusiness-tutorial/configure5.png "Configurar logon único")

    a. Selecione **necessária** como uma opção no menu suspenso para o **logon único**.

    b. Clique em **Adicionar URL de login** e, na caixa de texto **URL de login do provedor de identidade**, cole o valor do **URL do Serviço de Logon Único SAML** que você copiou no portal do Azure e selecione **Concluído**.

    ![Configurar logon único](./media/dropboxforbusiness-tutorial/configure6.png "Configurar logon único")

    c. Clique em **Carregar certificado** e, em seguida, navegue até o **arquivo de certificado codificado em Base64** que você baixou do portal do Azure.

    d. Clique em **Copiar link** e cole o valor copiado na caixa de texto **URL de login** da seção **Dropbox for Business Domain e URLs** no portal do Azure.

    e. Clique em **Salvar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/dropboxforbusiness-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/dropboxforbusiness-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/dropboxforbusiness-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/dropboxforbusiness-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-dropbox-for-business-test-user"></a>Criar um usuário de teste do Dropbox for Business

Nesta seção, um usuário chamado Brenda Fernandes é criado no Dropbox for Business. O Dropbox for Business dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão.

Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Dropbox for Business, um novo será criado quando você tentar acessar o Dropbox for Business.

>[!Note]
>Se você precisar criar um usuário manualmente, contate a [equipe de suporte ao Cliente do Dropbox for Business](https://www.dropbox.com/business/contact) 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Dropbox for Business.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Dropbox for Business, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **Dropbox for Business**.

    ![O link do Dropbox for Business na lista de aplicativos](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco do Dropbox for Business no Painel de Acesso, deverá ver a página de logon do aplicativo Dropbox for Business.
 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/dropboxforbusiness-tutorial/tutorial_general_01.png
[2]: ./media/dropboxforbusiness-tutorial/tutorial_general_02.png
[3]: ./media/dropboxforbusiness-tutorial/tutorial_general_03.png
[4]: ./media/dropboxforbusiness-tutorial/tutorial_general_04.png

[100]: ./media/dropboxforbusiness-tutorial/tutorial_general_100.png

[200]: ./media/dropboxforbusiness-tutorial/tutorial_general_200.png
[201]: ./media/dropboxforbusiness-tutorial/tutorial_general_201.png
[202]: ./media/dropboxforbusiness-tutorial/tutorial_general_202.png
[203]: ./media/dropboxforbusiness-tutorial/tutorial_general_203.png

