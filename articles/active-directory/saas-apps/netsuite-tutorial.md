---
title: 'Tutorial: integração do Azure Active Directory com o NetSuite | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o NetSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: jeedes
ms.openlocfilehash: 511fdcf587d16a59ff2bb11dfc55504b2218a569
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431396"
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Tutorial: integração do Azure Active Directory com o NetSuite

Neste tutorial, você aprende a integrar o NetSuite ao Azure AD (Azure Active Directory).

A integração do NetSuite com o Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao NetSuite
- Você pode habilitar seus usuários a fazerem logon automaticamente no NetSuite (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o NetSuite, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do NetSuite habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o NetSuite da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-netsuite-from-the-gallery"></a>Adicionar o NetSuite da galeria
Para configurar a integração do NetSuite com o Azure AD, é necessário adicionar o NetSuite da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o NetSuite da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]

1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **NetSuite**, selecione **NetSuite** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![NetSuite na lista de resultados](./media/netsuite-tutorial/tutorial_netsuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configura e testa o logon único do Azure AD com o NetSuite com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do NetSuite é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do NetSuite.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** no NetSuite.

Para configurar e testar o logon único do Azure AD com o NetSuite, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criar um usuário de teste do NetSuite](#creating-a-netsuite-test-user)** – para ter um equivalente de Brenda Fernandes no NetSuite que esteja vinculado à representação do usuário do Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo NetSuite.

**Para configurar o logon único do Azure AD com o NetSuite, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **NetSuite**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Configurar o logon único](./media/NetSuite-tutorial/tutorial_NetSuite_samlbase.png)

1. Na seção **Domínio e URLs do NetSuite**, execute as seguintes etapas:

    ![Configurar o logon único](./media/NetSuite-tutorial/tutorial_NetSuite_url.png)

    Na caixa de texto **URL de resposta** , digite uma URL no seguinte padrão:

    `https://<tenant-name>.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.NetSuite.com/saml2/acs`

    `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs`
    
    > [!NOTE]
    > Esses não são valores reais. Atualize esses valores com a URL de Resposta real. Contate a [Equipe de suporte do NetSuite](http://www.NetSuite.com/portal/services/support.shtml) para obter esses valores.

1. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo XML em seu computador.

    ![Configurar o logon único](./media/NetSuite-tutorial/tutorial_NetSuite_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/NetSuite-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do NetSuite**, clique em **Configurar o NetSuite** para abrir a janela **Configurar logon**. Copie a **URL de serviço de logon único SAML** da **seção de Referência Rápida.**

    ![Configurar o logon único](./media/NetSuite-tutorial/tutorial_NetSuite_configure.png)

1. Abra uma nova guia no navegador e entre no site do NetSuite de sua empresa como administrador.

1. Na barra de ferramentas na parte superior da página, clique em **Configuração**, navegue até **Empresa** e clique em **Habilitar Recursos**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-setupsaml.png)

1. Na barra de ferramentas no meio da página, clique em **SuiteCloud**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-suitecloud.png)

1. Na seção **Gerenciar Autenticação**, selecione **LOGON ÚNICO DO SAML** para habilitar a opção de LOGON ÚNICO DO DAML no NetSuite.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-ticksaml.png)

1. Na barra de ferramentas na parte superior da página, clique em **Configuração**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-setup.png)

1. Na lista **TAREFAS DE INSTALAÇÃO**, selecione **Integração**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-integration.png)

1. Na seção **GERENCIAR AUTENTICAÇÃO**, clique em **Logon Único do SAML**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-saml.png)

1. Na página de **Configuração do SAML**, na seção **Configuração do NetSuite**, execute as seguintes etapas:

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Selecione **MÉTODO DE AUTENTICAÇÃO PRIMÁRIA**.

    b. Para o campo rotulado como **METADADOS DO PROVEDOR DE IDENTIDADE DO SAMLV2**, selecione **CARREGAR ARQUIVO DE METADADOS DO IDP**. Em seguida, clique em **Procurar** para carregar o arquivo de metadados baixado no portal do Azure.

    c. Clique em **Enviar**.

1. No Azure AD, clique na caixa de seleção **Exibir e editar todos os outros atributos de usuário** e adicione um atributo.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-attributes.png)

1. Para o campo **Nome do Atributo**, digite `account`. Para o campo **Valor do Atributo** , digite a ID de conta do NetSuite. Esse valor é constante e muda com a conta. Instruções sobre como localizar a ID de conta estão abaixo:

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-add-attribute.png)

    a. No NetSuite, clique em **Configuração**, navegue até **Empresa** e clique em **Informações da Empresa** no menu de navegação superior.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-com.png)

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-account-id.png)

    b. Na página **Informações da Empresa**, na coluna à direita, copie a **ID DA CONTA**.

    c. Cole a **ID da Conta** que você copiou da conta do NetSuite no campo **Valor do Atributo** no Azure AD. 

1. Antes que os usuários possam realizar logon único no NetSuite, eles devem primeiro ter as permissões apropriadas no NetSuite. Siga as instruções abaixo para atribuir essas permissões.

    a. No menu de navegação superior, clique em **Configuração**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-setup.png)

    b. No menu de navegação à esquerda, selecione **Usuários/Funções** e, em seguida, clique em **Gerenciar Funções**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Clique em **Nova Função**.

    d. Digite um **Nome** para sua nova função.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-new-role.png)

    e. Clique em **Salvar**.

    f. No menu na parte superior, clique em **Permissões**. Em seguida, clique em **Instalação**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-sso.png)

    g. Selecione **Logon Único do SAML** e, em seguida, clique em **Adicionar**.

    h. Clique em **Salvar**.

    i. No menu de navegação superior, clique em **Instalação** e depois em **Gerenciador de Instalação**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-setup.png)

    j. No menu de navegação à esquerda, selecione **Usuários/Funções** e, em seguida, clique em **Gerenciar Usuários**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Selecione um usuário de teste. Depois, clique em **Editar** e, em seguida, navegue até a guia **Acesso**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-edit-user.png)

    l. Na caixa de diálogo Funções, atribua a função correta que você criou.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-add-role.png)

    m. Clique em **Salvar**.
 
### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/NetSuite-tutorial/create_aaduser_01.png) 

1.  Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/NetSuite-tutorial/create_aaduser_02.png) 

1. Na parte superior da caixa de diálogo, clique em **Adicionar** para abrir a caixa de diálogo **Usuário**.
 
    ![Criação de um usuário de teste do AD do Azure](./media/NetSuite-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/NetSuite-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**. 

### <a name="creating-a-netsuite-test-user"></a>Criando um usuário de teste do NetSuite

Nesta seção, um usuário chamado Brenda Fernandes é criado no NetSuite. O NetSuite dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão.
Não há itens de ação para você nesta seção. Se um usuário ainda não existir no NetSuite, um novo usuário será criado quando você tentar acessar o NetSuite.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao NetSuite.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao NetSuite, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **NetSuite**.

    ![Configurar o logon único](./media/NetSuite-tutorial/tutorial_NetSuite_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Para testar as configurações de logon único, abra o Painel de Acesso em [https://myapps.microsoft.com](https://myapps.microsoft.com/), entre na conta de teste e clique em **NetSuite**.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Configurar Provisionamento de Usuário](NetSuite-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/NetSuite-tutorial/tutorial_general_01.png
[2]: ./media/NetSuite-tutorial/tutorial_general_02.png
[3]: ./media/NetSuite-tutorial/tutorial_general_03.png
[4]: ./media/NetSuite-tutorial/tutorial_general_04.png

[100]: ./media/NetSuite-tutorial/tutorial_general_100.png

[200]: ./media/NetSuite-tutorial/tutorial_general_200.png
[201]: ./media/NetSuite-tutorial/tutorial_general_201.png
[202]: ./media/NetSuite-tutorial/tutorial_general_202.png
[203]: ./media/NetSuite-tutorial/tutorial_general_203.png

