---
title: 'Tutorial: Integração do Active Directory do Azure ao ajuste de músicas | Microsoft Docs'
description: Aprenda como configurar o logon único entre o Azure Active Directory e o estabelecimento de músicas.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6f86a8a2-4bd0-40cc-b1b4-752fce123328
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: jeedes
ms.openlocfilehash: 07533670a188c7f50a2848e76ed9d83a96c54cf7
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36211975"
---
# <a name="tutorial-azure-active-directory-integration-with-settling-music"></a>Tutorial: Integração do Active Directory do Azure com o ajuste de músicas

Neste tutorial, você aprenderá a integrar a organização de músicas com o Azure AD (Azure Active Directory).

A integração da música com o Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso à música Estabelecer.
- Você pode permitir que seus usuários façam logon automaticamente para estabelecer a música (Logon único) com suas contas do AD do Azure.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Azure AD com a instalação de músicas, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Assinatura habilitada de assinatura única de música do Settling

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando música da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-settling-music-from-the-gallery"></a>Adicionando música da galeria
Para configurar a integração da instalação de músicas no Azure AD, você precisa adicionar a opção Estabelecer músicas da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar músicas fixas da galeria, siga as etapas abaixo:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Settling music**, selecione **Settling music** no painel de resultados e clique no botão **Add** para adicionar o aplicativo.

    ![Estabelecendo música na lista de resultados](./media/settlingmusic-tutorial/tutorial_settlingmusic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o estabelecimento de músicas com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber o que o usuário de contrapartida em Estabelecer músicas é para um usuário no Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do AD do Azure e o usuário relacionado em Estabelecer músicas.

Para configurar e testar o logon único do Azure AD com a instalação de músicas, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[ Crie um usuário de teste de música Estabelecedora ](#create-a-settling-music-test-user)** - para ter uma contraparte de Brenda Fernandes na organização de músicas vinculadas à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo Settling music.

**Para configurar o logon único do Azure AD com o Settling music, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Settling music**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/settlingmusic-tutorial/tutorial_settlingmusic_samlbase.png)

3. Na seção **URLs e Domínio do Settling music**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Settling music](./media/settlingmusic-tutorial/tutorial_settlingmusic_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.rakurakuseisan.jp/<USERACCOUNT>/`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.rakurakuseisan.jp/<USERACCOUNT>/`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte do Cliente Settling music](https://rakurakuseisan.jp/) para obter esses valores. 
 
4. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/settlingmusic-tutorial/tutorial_settlingmusic_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/settlingmusic-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Settling music**, clique em **Configurar Settling music** para abrir a janela **Configurar Logon**. Copie a **URL do serviço de logon único do SAML e a URL de logoff** da **seção de Referência Rápida.**

    ![Configuração do Settling music](./media/settlingmusic-tutorial/tutorial_settlingmusic_configure.png) 

7. Em uma janela diferente do navegador da Web, faça logon no Settling music como Administrador de segurança.

8. Na parte superior da página, clique na guia **gerenciamento**.

    ![Settling music etapa 1](./media/settlingmusic-tutorial/tutorial_settlingmusic_step1.png)

9. Clique na guia **Configuração do sistema**.

    ![Settling music etapa 2](./media/settlingmusic-tutorial/tutorial_settlingmusic_step2.png)

10. Alterne para a guia **Segurança**.

    ![Settling music etapa 3](./media/settlingmusic-tutorial/tutorial_settlingmusic_step3.png)

11. Na seção de **Configurações de logon único**, execute as seguintes etapas:

    ![Settling music etapa 5](./media/settlingmusic-tutorial/tutorial_settlingmusic_step4.png)

    a. Clique **para habilitar**.

    b. Na caixa de texto **URL de Logon do provedor de ID**, cole o valor da **URL do Serviço de Logon Único SAML** copiado no Portal do Azure.

    c. Na caixa de texto **URL de Logoff do provedor de ID**, cole o valor da **URL de Saída** copiado do Portal do Azure.

    d. Clique em **Escolher Arquivo** para carregar o **Certificado (Base64)** que você baixou do Portal do Azure.

    e. Clique no botão **Salvar** .

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/settlingmusic-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/settlingmusic-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/settlingmusic-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/settlingmusic-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-settling-music-test-user"></a>Criar um usuário de teste do Settling music

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Settling music. Trabalhe com a [equipe de suporte ao Cliente do Settling music](https://rakurakuseisan.jp/) para adicionar os usuários à plataforma Settling music. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Settling music.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Settling music, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **Settling music**.

    ![O link do Settling music na lista de Aplicativos](./media/settlingmusic-tutorial/tutorial_settlingmusic_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Settling music no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Settling music.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/settlingmusic-tutorial/tutorial_general_01.png
[2]: ./media/settlingmusic-tutorial/tutorial_general_02.png
[3]: ./media/settlingmusic-tutorial/tutorial_general_03.png
[4]: ./media/settlingmusic-tutorial/tutorial_general_04.png

[100]: ./media/settlingmusic-tutorial/tutorial_general_100.png

[200]: ./media/settlingmusic-tutorial/tutorial_general_200.png
[201]: ./media/settlingmusic-tutorial/tutorial_general_201.png
[202]: ./media/settlingmusic-tutorial/tutorial_general_202.png
[203]: ./media/settlingmusic-tutorial/tutorial_general_203.png

