---
title: 'Tutorial: integração do Azure Active Directory ao Zscaler Three | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Zscaler Three.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2018
ms.author: jeedes
ms.openlocfilehash: 0ef8fc2ea8b006d49dd54d638183a58bf78a5797
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2018
ms.locfileid: "49311620"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-three"></a>Tutorial: integração do Azure Active Directory ao Zscaler Three

Neste tutorial, você aprenderá como integrar o Zscaler Three ao Azure AD (Azure Active Directory).

A integração do Zscaler Three com o Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Zscaler Three
- É possível permitir que seus usuários entrem automaticamente no Zscaler Three (Logon Único) com as contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Zscaler Three, são necessários os seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Zscaler Three habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, será possível obter uma versão de avaliação de um mês aqui: [Oferta de avaliação](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Zscaler Três da Galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-zscaler-three-from-the-gallery"></a>Adicionando Zscaler Três da Galeria

Para configurar a integração do Zscaler Three ao Azure AD, é necessário adicionar o Zscaler Three da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Zscaler Three da galeria, siga as etapas abaixo:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![Active Directory][1]

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![APLICATIVOS][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![APLICATIVOS][3]

4. No painel de resultados, selecione **Zscaler Three** e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/zscaler-three-tutorial/tutorial_zscalerthree_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure

Nesta seção, você configurará e testará o logon único do Azure AD com o Zscaler Three com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Zscaler Three é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Zscaler Three.

Para configurar e testar o logon único do Azure AD com o Zscaler Three, é necessário concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Definir configurações de proxy](#configuring-proxy-settings)** – para definir as configurações de proxy no Internet Explorer
3. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criação de um usuário de teste do Zscaler Three](#creating-a-zscaler-three-test-user)** – para ter um equivalente de Brenda Fernandes no Zscaler Three que esteja vinculado à representação de usuário no Azure AD.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
6. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único em seu aplicativo Zscaler Three.

**Para configurar o logon único do Azure AD com o Zscaler Three, siga as etapas abaixo:**

1. No Portal do Azure, na página de integração de aplicativos do **Zscaler Three**, clique em **Logon único**.

    ![Configurar o logon único][4]

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Configurar o logon único](./media/zscaler-three-tutorial/tutorial_general_301.png)

3. Se você precisar alterar para o modo **SAML** de qualquer outro modo, clique em **Alterar modo de logon único** na parte superior da tela.

    ![Configurar o logon único](./media/zscaler-three-tutorial/tutorial_general_300.png)

4. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Configurar o logon único](./media/zscaler-three-tutorial/tutorial_general_302.png)

5. Na seção **Configuração básica de SAML**, execute as etapas a seguir:

    ![Configurar o logon único](./media/zscaler-three-tutorial/tutorial_zscalerthree_url.png)

    Na caixa de texto URL de Resposta, insira a URL: `https://login.zscalerthree.net/sfc_sso`

    > [!NOTE]
    > Você precisa atualizar esse valor com a URL de Entrada real. Contate a [equipe de suporte ao cliente do Zscaler Three](https://www.zscaler.com/company/contact) para obter esses valores.

6. Na seção **Certificado de Autenticação SAML**, clique em **Download** para baixar o **Certificado (Base64)** e, em seguida, salve o arquivo de certificado no computador.

    ![Configurar o logon único](./media/zscaler-three-tutorial/tutorial_zscalerthree_certificate.png)

8. Na seção **Configurar Zscaler Three**, copie a **URL de logon**.

    ![Configurar o logon único](./media/zscaler-three-tutorial/tutorial_zscalerthree_configure.png)

9. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa Zscaler Three como administrador.

10. No menu na parte superior, clique em **Administração**.

    ![Administração](./media/zscaler-three-tutorial/ic800206.png "Administração")

9. Em **Gerenciar Administradores e Funções**, clique em **Gerenciar Usuários e Autenticação**.

    ![Gerenciar usuários e autenticação](./media/zscaler-three-tutorial/ic800207.png "Gerenciar usuários e autenticação")

10. Na seção **Escolher Opções de Autenticação para a sua Organização** , realize as seguintes etapas:

    ![Autenticação](./media/zscaler-three-tutorial/ic800208.png "Autenticação")

    a. Selecione **Autenticar usando o Logon Único do SAML**.

    b. Clique em **Configurar Parâmetros de Logon Único do SAML**.

11. Na página da caixa de diálogo **Configurar Parâmetros de Logon Único do SAML**, execute as seguintes etapas e, em seguida, clique em **Concluído**

    ![Logon Único](./media/zscaler-three-tutorial/ic800209.png "Logon Único")

    a. Cole o valor **URL de Logon** copiado do Portal do Azure na caixa de texto **URL do Portal SAML ao qual os usuários são enviados para autenticação**.

    b. Na caixa de texto **Atributo que contém o Nome de Logon**, digite **NameI**.

    c. Para carregar seu certificado baixado, clique em **Zscaler pem**.

    d. Selecione **Habilitar Provisionamento Automático do SAML**.

12. Na página de caixa de diálogo **Configurar Autenticação de Usuário** , execute as seguintes etapas:

    ![Administração](./media/zscaler-three-tutorial/ic800210.png "Administração")

    a. Clique em **Salvar**.

    b. Clique em **Ativar Agora**.

## <a name="configuring-proxy-settings"></a>Definindo as configurações de proxy

### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para definir as configurações de proxy no Internet Explorer

1. Inicie o **Internet Explorer**.

2. Selecione **Opções da Internet** no menu **Ferramentas** para abrir a caixa de diálogo **Opções da Internet**.

     ![Opções da Internet](./media/zscaler-three-tutorial/ic769492.png "Opções da Internet")

3. Clique na guia **Conexões** .
  
     ![Conexões](./media/zscaler-three-tutorial/ic769493.png "Conexões")

4. Clique em **Configurações da LAN** para abrir a caixa de diálogo **Configurações da LAN**.

5. Na seção Servidor de proxy, execute as seguintes etapas:

    ![Servidor proxy](./media/zscaler-three-tutorial/ic769494.png "Servidor proxy")

    a. Selecione **Usar um servidor proxy para LAN**.

    b. Na caixa de texto endereço, digite **gateway.zscalerthree.net**.

    c. Na caixa de texto Porta, digite **80**.

    d. Selecione **Ignorar servidor proxy para endereços locais**.

    e. Clique em **OK** para fechar a caixa de diálogo **Configurações da Rede Local (LAN)**.

6. Clique em **OK** para fechar a caixa de diálogo **Opções da Internet**.

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Criar um usuário do AD do Azure][100]

2. Selecione **Novo usuário** na parte superior da tela.

    ![Criação de um usuário de teste do AD do Azure](./media/zscaler-three-tutorial/create_aaduser_01.png) 

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![Criação de um usuário de teste do AD do Azure](./media/zscaler-three-tutorial/create_aaduser_02.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="creating-a-zscaler-three-test-user"></a>Como criar um usuário de teste do Zscaler Three

Para permitir que os usuários do Azure AD façam logon no Zscaler Three, eles devem ser provisionados no Zscaler Three. No caso do Zscaler Three, o provisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento de usuários, execute as seguintes etapas:

1. Faça logon no seu locatário do **Zscaler Three**.

2. Clique em **Administração**.

    ![Administração](./media/zscaler-three-tutorial/ic781035.png "Administração")

3. Clique em **Gerenciamento de Usuários**.

     ![Adicionar](./media/zscaler-three-tutorial/ic781036.png "Adicionar")

4. Na guia **Usuários**, clique em **Adicionar**.

    ![Adicionar](./media/zscaler-three-tutorial/ic781037.png "Adicionar")

5. Na seção Adicionar Usuário, execute as seguintes etapas:

    ![Adicionar Usuário](./media/zscaler-three-tutorial/ic781038.png "Adicionar Usuário")

    a. Digite **UserID**, **Nome de Exibição do Usuário**, **Senha**, **Confirmar Senha** e, em seguida, selecione **Grupos** e o **Departamento** de uma conta válida do Azure AD que você deseja provisionar.

    b. Clique em **Salvar**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação de conta de usuário do Zscaler Three ou APIs fornecidas pelo Zscaler Three para provisionar as contas de usuário do Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Zscaler Three.

1. No portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **Zscaler Three**.

    ![Configurar o logon único](./media/zscaler-three-tutorial/tutorial_zscalerthree_app.png)

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar usuário** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Zscaler Three no Painel de Acesso, você deverá entrar automaticamente no aplicativo Zscaler Three.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zscaler-three-tutorial/tutorial_general_01.png
[2]: ./media/zscaler-three-tutorial/tutorial_general_02.png
[3]: ./media/zscaler-three-tutorial/tutorial_general_03.png
[4]: ./media/zscaler-three-tutorial/tutorial_general_04.png

[100]: ./media/zscaler-three-tutorial/tutorial_general_100.png

[200]: ./media/zscaler-three-tutorial/tutorial_general_200.png
[201]: ./media/zscaler-three-tutorial/tutorial_general_201.png
[202]: ./media/zscaler-three-tutorial/tutorial_general_202.png
[203]: ./media/zscaler-three-tutorial/tutorial_general_203.png