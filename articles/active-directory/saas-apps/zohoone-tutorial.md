---
title: 'Tutorial: Integração do Active Directory do Azure ao Zoho One | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Zoho One.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.openlocfilehash: 9f1db693ac75d7ab4b9776d60f63a0ca7524f760
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39050177"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Tutorial: Integração do Active Directory do Azure ao Zoho One

Neste tutorial, você aprenderá como integrar o Zoho One ao Azure AD (Azure Active Directory).

A integração do Zoho One ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Zoho One.
- Você pode permitir que seus usuários façam logon automaticamente no Zoho One (Logon Único) com suas contas do AD do Azure.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Zoho One, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada do Zoho One com logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Zoho One da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-zoho-one-from-the-gallery"></a>Adicionando Zoho One da galeria
Para configurar a integração do Zoho One ao Azure AD, você precisa adicionar o Zoho One da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar Zoho One da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Zoho One**, selecione **Zoho One** no painel de resultados e clique no botão **Add** para adicionar o aplicativo.

    ![Zoho One na lista de resultados](./media/zohoone-tutorial/tutorial_zohoone_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com Zoho One com base em um usuário de teste chamado"Britta Simon".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Zoho One é um usuário do Azure AD. Em outras palavras, um relacionamento de link entre um usuário do Azure AD e o usuário relacionado no Zoho One precisa ser estabelecido.

Para configurar e testar o logon único do Azure AD com o Zoho One, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[ Crie um usuário de teste do Zoho One ](#create-a-zoho-one-test-user)** - para ter um equivalente de Brenda Fernandes no Zoho One que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único em seu aplicativo Zoho One.

**Para configurar o logon único do Azure AD com o Zoho One, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Zoho One**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/zohoone-tutorial/tutorial_zohoone_samlbase.png)

3. Na seção **Zoho One Domain and URLs**, execute as seguintes etapas se você deseja configurar o aplicativo no modo **IDP** iniciado:

    ![Zoho One Domain e informações de logon único de URLs](./media/zohoone-tutorial/tutorial_zohoone_url.png)

    a. Na caixa de texto **Identificador (ID da entidade)**, digite uma URL: `one.zoho.com`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    c. Marque **Mostrar configurações de URL avançadas**.

    d. Na caixa de texto **Estado de retransmissão**, digite uma URL: `https://one.zoho.com`

4. Se você deseja configurar o aplicativo no modo **SP** iniciado, execute o seguinte passo:

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com`
     
    > [!NOTE] 
    > Os valores da **URL de Resposta** e **URL de Entrada** anteriores não são reais. Você atualizará o valor com a URL de Resposta real e a URL de Entrada explicado no tutorial posteriormente. 

5. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/zohoone-tutorial/tutorial_zohoone_certificate.png) 

6. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/zohoone-tutorial/tutorial_general_400.png)
    
7. Na seção **Configuração do Zoho One** clique em **Configurar Zoho One** para abrir a janela **Configurar logon**. Copie a **URL do serviço de logon único do SAML e a URL de logoff** da **seção de Referência Rápida.**

    ![Configuração do Zoho One](./media/zohoone-tutorial/tutorial_zohoone_configure.png) 

8. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa Zoho One como administrador.

9. Na guia **Organização**, clique em **Configuração** em **Autenticação SAML**.

    ![Org. Zoho One](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

10. Na página Pop-up, execute as seguintes etapas:

    ![Assinatura Zoho One](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. Na caixa de texto **URL de Entrada** cole o valor da **URL do Serviço de Logon Único do SAML**, copiado do portal do Azure.

    b. Na caixa de texto **URL de Entrada** cole o valor da **URL de Entrada**, copiado do portal do Azure.

    c. Clique em **Procurar** para carregar o **Certificado (Base64)** que você baixou do portal do Azure.

    d. Clique em **Salvar**.

11. Depois de salvar a configuração da Autenticação SAML, copie o valor **SAML-Identfier** e use esse valor na **URL de Resposta** no portal do Azure, na seção **Domínio e URLs do Zoho One**.

    ![SAML do Zoho One](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

12. Acesse a guia **Domínios** e, em seguida, clique em **Adicionar Domínio**.

    ![Domínio do Zoho One](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

13. Na página**Adicionar Domínio**, execute as seguintes etapas:

    ![Adicionar domínio do Zoho One](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. Na caixa de texto **Nome de Domínio**, digite um domínio como **contoso.com**.

    b. Clique em **Adicionar**.

    >[!Note]
    >Depois de adicionar o domínio, siga [estas](https://www.zoho.com/one/help/admin-guide/domain-verification.html) etapas para verificar o domínio. Depois que o domínio for verificado, use o nome do domínio na **URL de Entrada** na seção **Domínio e URLs do Zoho One** no portal do Azure.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/zohoone-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/zohoone-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/zohoone-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/zohoone-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-zoho-one-test-user"></a>Criar um usuário de teste do Zoho One

Para permitir que os usuários do Azure AD façam logon no Zoho One, eles devem ser provisionados no Zoho One. No Zoho One, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon no Zoho One como Administrador de Segurança.

2. Na guia **Usuários**, clique em **logotipo do usuário**.

    ![Usuário do Zoho One](./media/zohoone-tutorial/tutorial_zohoone_users.png)

3. Na página **Adicionar Usuário**, realize as seguintes etapas:

    ![Adicionar usuário do Zoho One](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. Na caixa de texto **Nome**, insira o nome do usuário como **Brenda Fernandes**.
    
    b. Na caixa de texto **Endereço de Email**, insira o email do usuário como **brittasimon@contoso.com**.

    >[!Note]
    >Selecione o domínio verificado na lista de domínios.

    c. Clique em **Adicionar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Zoho One.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Zoho One, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **Zoho One**.

    ![O link do Zoho One na lista de Aplicativos](./media/zohoone-tutorial/tutorial_zohoone_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco Zoho One no Painel de Acesso, deverá conectar automaticamente no seu aplicativo Zoho One.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zohoone-tutorial/tutorial_general_01.png
[2]: ./media/zohoone-tutorial/tutorial_general_02.png
[3]: ./media/zohoone-tutorial/tutorial_general_03.png
[4]: ./media/zohoone-tutorial/tutorial_general_04.png

[100]: ./media/zohoone-tutorial/tutorial_general_100.png

[200]: ./media/zohoone-tutorial/tutorial_general_200.png
[201]: ./media/zohoone-tutorial/tutorial_general_201.png
[202]: ./media/zohoone-tutorial/tutorial_general_202.png
[203]: ./media/zohoone-tutorial/tutorial_general_203.png

