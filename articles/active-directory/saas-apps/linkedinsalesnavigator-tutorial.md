---
title: 'Tutorial: Integração do Azure Active Directory ao LinkedIn Sales Navigator | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o LinkedInSalesNavigator.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 7a9fa8f3-d611-4ffe-8d50-04e9586b24da
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63e3e98c2c3dc8f99e733174c86965304fe483ce
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56181993"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-sales-navigator"></a>Tutorial: Integração do Azure Active Directory ao LinkedIn Sales Navigator

Neste tutorial, você aprenderá a integrar o LinkedIn Sales Navigator ao Azure AD (Azure Active Directory).

A integração do LinkedIn Sales Navigator com o Azure AD oferece os seguintes benefícios:

- É possível controlar, no Azure AD, quem tem acesso ao LinkedIn Sales Navigator
- É possível permitir que seus usuários façam logon automaticamente no LinkedIn Sales Navigator (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, procure [O que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o LinkedIn Sales Navigator, serão necessários os seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura do LinkedIn Sales Navigator com logon único habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Evite usar o ambiente de produção a menos que necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar LinkedIn Sales Navigator da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>Adicionar LinkedIn Sales Navigator da galeria
Para configurar a integração do LinkedIn Sales Navigator com o Azure AD, é necessário adicionar o LinkedIn Sales Navigator da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o LinkedIn Sales Navigator da galeria, siga as etapas abaixo:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **LinkedIn Sales Navigator**.

    ![Criação de um usuário de teste do AD do Azure](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_search.png)

1. No painel de resultados, selecione **LinkedIn Sales Navigator** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o LinkedIn Sales Navigator, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do LinkedIn Sales Navigator é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do LinkedIn Sales Navigator.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** no LinkedIn Sales Navigator.

Para configurar e testar o logon único do Azure AD com o LinkedIn Sales Navigator, é necessário concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criar um usuário de teste do LinkedIn Sales Navigator](#creating-a-linkedin-sales-navigator-test-user)** – para ter um equivalente de Brenda Fernandes no LinkedIn Sales Navigator que esteja vinculado à representação do usuário no Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único em seu aplicativo LinkedIn Sales Navigator.

**Para configurar o logon único do Azure AD com o LinkedIn Sales Navigator, siga as etapas abaixo:**

1. No Portal do Azure, na página de integração de aplicativos do **LinkedIn Sales Navigator**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, em **Modo**, selecione **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_samlbase.png)

1. Em uma janela diferente do navegador da Web, faça logon em seu site do **LinkedIn Sales Navigator** como administrador.

1. Em **Centro de Contas**, clique em **Configurações Globais** em **Configurações**. Além disso, selecione **Sales Navigator** na lista suspensa.

    ![Configurar o logon único](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

1. Clique em **OU clique aqui para carregar e copiar campos individuais do formulário** e copie a **ID da Entidade** e a **URL ACS (acesso do consumidor de declaração)**.

    ![Configurar o logon único](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

1. No portal do Azure, em **Domínio e URLs do LinkedIn Sales Navigator**, siga as etapas abaixo se você desejar configurar o aplicativo em modo iniciado por **IDP**.

    ![Configurar o logon único](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_url1.png)

     a. Na caixa de texto **Identificador**, insira a **ID da Entidade** copiada do Portal do LinkedIn 

    b. Na caixa de texto **URL de Resposta**, insira a **URL ACS (acesso do consumidor de declaração)** copiada do Portal do LinkedIn

1. Marque **Mostrar configurações avançadas de URL** se quiser configurar o aplicativo no modo iniciado em **SP**.

    ![Configurar o logon único](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_url2.png)

    Na caixa de texto **URL de Logon**, digite o valor usando o seguinte padrão: `https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

1. Seu aplicativo **LinkedIn Sales Navigator** espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributos personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra um exemplo. O valor padrão do **Identificador de Usuário** é **user.userprincipalname**, mas o LinkedIn Sales Navigator espera que isso seja mapeado com o endereço de email do usuário. Você pode usar o atributo **user.mail** na lista ou usar o valor do atributo apropriado com base na configuração da sua organização. 

    ![Configurar o logon único](./media/linkedinsalesnavigator-tutorial/updateusermail.png)
    
1. Na seção **Atributos de Usuário**, clique em **Exibir e editar todos os outros atributos de usuário** e defina os atributos. O usuário precisa adicionar quatro declarações denominadas **email**, **departamento**, **nome** e **sobrenome** e o valor deve ser mapeado com **user.mail**, **user.department**, **user.givenname** e **user.surname** respectivamente

    | Nome do atributo | Valor do atributo |
    | --- | --- |    
    | email| user.mail |
    | department| user.department |
    | nome| user.givenname |
    | sobrenome| user.surname |
    
    ![Criação de um usuário de teste do AD do Azure](./media/linkedinsalesnavigator-tutorial/userattribute.png)
    
     a. Clique em **Adicionar Atributo** para abrir a caixa de diálogo do atributo.
    
    ![Criação de um usuário de teste do AD do Azure](./media/linkedinsalesnavigator-tutorial/tutorial_attribute_04.png)
    
    ![Criação de um usuário de teste do AD do Azure](./media/linkedinsalesnavigator-tutorial/tutorial_attribute_05.png)
   
    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.
    
    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.
    
    d. Clique em **Ok**

1. Realize as seguintes etapas no atributo **name**-

     a. Clique no atributo para abrir a janela **Editar Atributo**.

    ![Configurar o logon único](./media/linkedinsalesnavigator-tutorial/url_update.png)

    b. Exclua o valor da URL do **namespace**.
    
    c. Clique em **OK** para salvar a configuração.

1. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo XML em seu computador.

    ![Configurar o logon único](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/linkedinsalesnavigator-tutorial/tutorial_general_400.png)

1. Vá para a seção **Configurações de administração do LinkedIn**. Clique em **Carregar arquivo XML** para carregar o arquivo XML de metadados que você baixou do Portal do Azure.

    ![Configurar o logon único](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

1. Clique em **Ativar** para habilitar o SSO. O status do SSO é alterado de **Não Conectado** para **Conectado**

    ![Configurar o logon único](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)


> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre o recurso de documentação inserida aqui: [Documentação inserida do Microsoft Azure Active Directory]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/linkedinsalesnavigator-tutorial/create_aaduser_01.png) 

1. Clicar em **Usuários e grupos** e **Todos os usuários**.
    
    ![Criação de um usuário de teste do AD do Azure](./media/linkedinsalesnavigator-tutorial/create_aaduser_02.png) 

1. Na parte superior da caixa de diálogo clique **adicionar** para abrir o **usuário** caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/linkedinsalesnavigator-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/linkedinsalesnavigator-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-linkedin-sales-navigator-test-user"></a>Criar um usuário de teste do LinkedIn Sales Navigator

O aplicativo LinkedIn Sales Navigator dá suporte ao provisionamento de usuário JIT (Just-In-Time) e, após a autenticação, os usuários são automaticamente criados no aplicativo. Ative **atribuir licenças automaticamente** para atribuir uma licença ao usuário.
   
   ![Criação de um usuário de teste do AD do Azure](./media/linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, ao conceder acesso ao LinkedIn Sales Navigator a Brenda Fernandes, você permitirá que ela use o logon único do Azure.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao LinkedIn Sales Navigator, siga as etapas abaixo:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **LinkedIn Sales Navigator**.

    ![Configurar o logon único](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco LinkedIn Sales Navigator no painel de acesso, você deverá ser redirecionado à página organizacional em que você precisa fornecer os detalhes da sua conta pessoal do LinkedIn. Ele vincula a sua conta pessoal à sua conta de negócios do LinkedIn. Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_01.png
[2]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_02.png
[3]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_03.png
[4]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_04.png

[100]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_100.png

[200]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_200.png
[201]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_201.png
[202]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_202.png
[203]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_203.png

