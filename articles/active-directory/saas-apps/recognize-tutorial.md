---
title: 'Tutorial: Integração do Azure Active Directory ao Recognize | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Recognize.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: e47b8d6490ddc9620574c6834378471c8c02bac7
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55190047"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Tutorial: Integração do Azure Active Directory ao Recognize

Neste tutorial, você aprende a integrar o Recognize ao Azure AD (Azure Active Directory).

A integração do Recognize ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Recognize
- Você pode permitir que os usuários façam logon automaticamente no Recognize (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Recognize, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Recognize

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, é possível obter uma avaliação por um mês aqui: [Oferta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Recognize da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-recognize-from-the-gallery"></a>Adicionando o Recognize da galeria
Para configurar a integração do Recognize ao Azure AD, você precisará adicionar o Recognize da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Recognize da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **Recognize**.

    ![Criação de um usuário de teste do AD do Azure](./media/recognize-tutorial/tutorial_recognize_search.png)

1. No painel de resultados, selecione **Recognize** e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/recognize-tutorial/tutorial_recognize_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configura e testa o logon único do Azure AD com o Recognize, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Recognize é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Recognize.

No Recognize, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Recognize, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criando um usuário de teste do Recognize](#creating-a-recognize-test-user)** – para ter um equivalente de Brenda Fernandes no Recognize que esteja vinculado à representação de usuário do Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Recognize.

**Para configurar o logon único do Azure AD com o Recognize, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Recognize**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/recognize-tutorial/tutorial_recognize_samlbase.png)

1. Na seção **Domínio e URLs do Recognize**, realize as seguintes etapas:

    ![Configurar o logon único](./media/recognize-tutorial/tutorial_recognize_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://recognizeapp.com/<your-domain>/saml/sso`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://recognizeapp.com/<your-domain>`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com a [equipe de suporte ao cliente do Recognize](mailto:support@recognizeapp.com) para obter a URL de Logon. Você pode obter o valor do Identificador abrindo a URL de Metadados do Provedor de Serviços na seção Configurações de SSO, explicada mais à frente no tutorial. . 
 
1. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar o logon único](./media/recognize-tutorial/tutorial_recognize_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/recognize-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do Recognize**, clique em **Configurar o Recognize** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configurar o logon único](./media/recognize-tutorial/tutorial_recognize_configure.png) 

1. Em uma janela diferente do navegador da Web, faça logon em seu locatário do Recognize como um administrador.

1. No canto superior direito, clique em **Menu**. Vá para **Administrador da Empresa**.
   
    ![Configurar o logon único no lado do aplicativo](./media/recognize-tutorial/tutorial_recognize_000.png)

1. No painel de navegação esquerdo, clique em **Configurações**.
   
    ![Configurar o logon único no lado do aplicativo](./media/recognize-tutorial/tutorial_recognize_001.png)

1. Realize as seguintes etapas na seção **Configurações do SSO**.
   
    ![Configurar o logon único no lado do aplicativo](./media/recognize-tutorial/tutorial_recognize_002.png)
    
     a. Em **Habilitar SSO**, selecione **ATIVADO**.

    b. Na caixa de texto **ID da Entidade IDP**, cole o valor da **ID da Entidade SAML** copiado do portal do Azure.
    
    c. Na caixa de texto **URL de destino do SSO**, cole o valor da **URL do Serviço de Logon Único SAML** copiado do portal do Azure.
    
    d. Na caixa de texto **URL de destino do SLO**, cole o valor da **URL de Saída** copiado do portal do Azure. 
    
    e. Abra o arquivo de **Certificado (Base64)** baixado no bloco de notas, copie o conteúdo dele para a área de transferência e, depois, cole-o na caixa de texto **Certificado**.
    
    f. Clique no botão **Salvar configurações**. 

1. Ao lado da seção **Configurações de SSO**, copie a URL em **URL de metadados do provedor de serviço**.
   
    ![Configurar o logon único no lado do aplicativo](./media/recognize-tutorial/tutorial_recognize_003.png)

1. Abra o **link da URL de metadados** em um navegador em branco para baixar o documento de metadados. Em seguida, copie o value(entityID) de EntityDescriptor do arquivo e cole-o na caixa de texto  **Identificador** na **seção Domínio e URLs do Recognize** do portal do Azure.
    
    ![Configurar o logon único no lado do aplicativo](./media/recognize-tutorial/tutorial_recognize_004.png)

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre o recurso de documentação inserida aqui: [Documentação inserida do Microsoft Azure Active Directory]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/recognize-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/recognize-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/recognize-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/recognize-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-recognize-test-user"></a>Criando um usuário de teste do Recognize

Para permitir que os usuários do Azure AD façam logon no Recognize, eles deverão ser provisionados no Recognize. No caso do Recognize, o provisionamento é uma tarefa manual.

Este aplicativo não dá suporte ao provisionamento de SCIM, mas tem uma sincronização de usuário alternativa que provisiona usuários. 

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa Recognize como um administrador.

1. No canto superior direito, clique em **Menu**. Vá para **Administrador da Empresa**.

1. No painel de navegação esquerdo, clique em **Configurações**.

1. Execute as seguintes etapas na seção **Sincronização de usuário**.
   
   ![Novo Usuário](./media/recognize-tutorial/tutorial_recognize_005.png "Novo Usuário")
   
    a. Em **Sincronização habilitada**, selecione **ATIVADO**.
   
   b. Em **Escolher o provedor de sincronização**, selecione **Microsoft / Office 365**.
   
   c. Clique em **Executar Sincronização de Usuário**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Recognize.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Recognize, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, escolha **Recognize**.

    ![Configurar o logon único](./media/recognize-tutorial/tutorial_recognize_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Recognize no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo do Recognize. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/recognize-tutorial/tutorial_general_01.png
[2]: ./media/recognize-tutorial/tutorial_general_02.png
[3]: ./media/recognize-tutorial/tutorial_general_03.png
[4]: ./media/recognize-tutorial/tutorial_general_04.png

[100]: ./media/recognize-tutorial/tutorial_general_100.png

[200]: ./media/recognize-tutorial/tutorial_general_200.png
[201]: ./media/recognize-tutorial/tutorial_general_201.png
[202]: ./media/recognize-tutorial/tutorial_general_202.png
[203]: ./media/recognize-tutorial/tutorial_general_203.png

