---
title: 'Tutorial: integração do Azure Active Directory com o LinkedIn Elevate | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o LinkedIn Elevate.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 8b11b5e3e420577590e95c6839673f54c52d078b
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49116169"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>Tutorial: integração do Azure Active Directory com o LinkedIn Elevate

Neste tutorial, você aprenderá a integrar o LinkedIn Elevate ao Azure AD (Azure Active Directory).

A integração do LinkedIn Elevate com o Azure AD oferece os seguintes benefícios:

- É possível controlar, no Azure AD, quem tem acesso ao LinkedIn Elevate
- É possível permitir que seus usuários façam logon automaticamente no LinkedIn Elevate (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local - o portal de Gerenciamento do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o LinkedIn Elevate, são necessários os seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do LinkedIn Elevate habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o LinkedIn da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Adicionando o LinkedIn da galeria
Para configurar a integração do LinkedIn Elevate ao Azure AD, é necessário adicionar o LinkedIn Elevate por meio da galeria à lista de aplicativos SaaS gerenciados.

**Para adicionar o LinkedIn Elevate da galeria, siga as etapas abaixo:**

1. No **[Portal de Gerenciamento do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]

1. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **LinkedIn Elevate**. No painel de resultados, clique em **LinkedIn Elevate** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/linkedinelevate-tutorial/tutorial-linkedinElevate_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o LinkedIn Elevate, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do LinkedIn Elevate é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no LinkedIn Elevate.

Essa relação de vínculo é estabelecida por meio da atribuição do valor de **nome de usuário** no Azure AD como o valor de **Nome de Usuário** no LinkedIn Elevate.

Para configurar e testar o logon único do Azure AD com o LinkedIn Elevate, é necessário concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criando um usuário de teste do LinkedIn Elevate](#creating-a-linkedin-elevate-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal de Gerenciamento do Azure e configurará o logon único em seu aplicativo LinkedIn Elevate.

**Para configurar o logon único do Azure AD com o LinkedIn Elevate, siga as etapas abaixo:**

1. No portal de Gerenciamento do Azure, na página de integração de aplicativos do **LinkedIn Elevate**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, como **Modo**, selecione **Logon baseado em SAML** para habilitar o logon único.

    ![Configurar o logon único](./media/linkedinelevate-tutorial/tutorial-linkedin_01.png)

1. Em uma janela diferente do navegador da Web, faça logon em seu locatário do LinkedIn Elevate como um administrador.

1. Em **Centro de Contas**, clique em **Configurações Globais** em **Configurações**. Além disso, selecione **Elevar – Elevar teste do AAD** na lista suspensa.

    ![Configurar o logon único](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Clique em **OU Clique aqui para carregar e copiar campos individuais do formulário** e copie a **ID da entidade** e **URL ACS (acesso do consumidor de declaração)**

    ![Configurar o logon único](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

1. No Portal do Azure, em **Domínio e URLs do LinkedIn Elevate**, siga as etapas abaixo para configurar o SSO no modo **iniciado pelo IdP**

    ![Configurar o logon único](./media/linkedinelevate-tutorial/tutorial_linkedin_signon_01.png)

    a. Na caixa de texto **Identificador**, insira a **ID da Entidade** copiada do Portal do LinkedIn 

    b. Na caixa de texto **URL de resposta**, insira a **URL ACS (acesso do consumidor de declaração)** copiada do Portal do LinkedIn

1. Se você desejar configurar o SSO em **Iniciado pelo SP**, clique na opção Mostrar configurações avançadas de URL na seção de configuração e configure o logon na URL com o seguinte padrão:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>` 

    ![Configurar o logon único](./media/linkedinelevate-tutorial/tutorial_linkedin_signon_02.png) 

1. Seu aplicativo LinkedIn Elevate espera as declarações SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra um exemplo disso. O valor padrão do **identificador de usuário** é **user.userprincipalname**, mas o LinkedIn Elevate espera que isso seja mapeado com o endereço de email do usuário. Para que você possa usar o atributo **user. mail** na lista ou usar o valor do atributo apropriado com base na configuração da sua organização.

    ![Configurar o logon único](./media/linkedinelevate-tutorial/updateusermail.png)

1. Na seção **Atributos de Usuário**, clique em **Exibir e editar todos os outros atributos de usuário** e defina os atributos. É necessário adicionar outra declaração denominada **departamento** e o valor deve ser mapeado para **user.department**.

    | Nome do atributo | Valor do atributo |
    | --- | --- |
    | department| user.department |

      ![Criação de um usuário de teste do AD do Azure](./media/linkedinelevate-tutorial/userattribute.png)

      a. Clique em Adicionar atributo para abrir a página de detalhes do atributo. Adicione o atributo department conforme mostrado abaixo.

      ![Criação de um usuário de teste do AD do Azure](./media/linkedinelevate-tutorial/adduserattribute.png)

      b. Clique em **Ok** para salvar o atributo.

      c. Altere o nome do atributo **emailaddress** para **email**.

1. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo XML em seu computador.

    ![Configurar o logon único](./media/linkedinelevate-tutorial/tutorial-linkedinElevate_certificate.png) 

1. Clique em **Salvar**.

    ![Configurar o logon único](./media/linkedinelevate-tutorial/tutorial_general_400.png)

1. Vá para a seção **Configurações de administração do LinkedIn**. Carregue o arquivo XML que você acabou de baixar no portal do Azure clicando na opção Carregar arquivo XML.

    ![Configurar o logon único](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Clique em **Ativar** para habilitar o SSO. O status do SSO será alterado de **Não conectado** para **Conectado**

    ![Configurar o logon único](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal de Gerenciamento do Azure chamado Britta Simon.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/linkedinelevate-tutorial/create_aaduser_01.png) 

1. Vá para **usuários e grupos** e clique em **todos os usuários** para exibir a lista de usuários.

    ![Criação de um usuário de teste do AD do Azure](./media/linkedinelevate-tutorial/create_aaduser_02.png) 

1. Na parte superior da caixa de diálogo clique **adicionar** para abrir o **usuário** caixa de diálogo.

    ![Criação de um usuário de teste do AD do Azure](./media/linkedinelevate-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:

    ![Criação de um usuário de teste do AD do Azure](./media/linkedinelevate-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.

### <a name="creating-a-linkedin-elevate-test-user"></a>Criando um usuário de teste do LinkedIn Elevate

O aplicativo LinkedIn Elevate dá suporte ao provisionamento de usuário just in time e, após a autenticação, os usuários serão criados automaticamente no aplicativo. Na página de configurações de administração no portal do LinkedIn Elevate, coloque a opção **Atribuir licenças automaticamente** na posição de ativar o provisionamento just in time, o que também atribuirá uma licença ao usuário. O LinkedIn Elevate também dá suporte ao provisionamento automático do usuário. É possível encontrar [aqui](linkedinelevate-provisioning-tutorial.md) detalhes sobre como configurar o provisionamento automático do usuário.

   ![Criação de um usuário de teste do AD do Azure](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao LinkedIn Elevate.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao LinkedIn Elevate, siga as etapas abaixo:**

1. No Portal de Gerenciamento do Azure, abra a exibição de aplicativos e, em seguida, navegue até o modo de exibição de diretório e vá para **Aplicativos empresariais**, depois clique em **Todos os aplicativos**.

    ![Atribuir usuário][201]

1. Na lista de aplicativos, selecione **LinkedIn Elevate**.

    ![Configurar o logon único](./media/linkedinelevate-tutorial/tutorial-linkedinElevate_0001.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco LinkedIn Elevate no Painel de Acesso, você deve obter a página de logon do Azure e, após o logon bem-sucedido, você deve entrar em seu aplicativo LinkedIn Elevate.

## <a name="additional-resources"></a>Recursos adicionais

* [Tutorial: Configurar o LinkedIn Elevate para o provisionamento automático de usuário com o Azure Active Directory](linkedinelevate-provisioning-tutorial.md)
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Configurar Provisionamento de Usuário](linkedinelevate-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/linkedinElevate-tutorial/tutorial_general_01.png
[2]: ./media/linkedinElevate-tutorial/tutorial_general_02.png
[3]: ./media/linkedinElevate-tutorial/tutorial_general_03.png
[4]: ./media/linkedinElevate-tutorial/tutorial_general_04.png

[100]: ./media/linkedinElevate-tutorial/tutorial_general_100.png

[200]: ./media/linkedinElevate-tutorial/tutorial_general_200.png
[201]: ./media/linkedinElevate-tutorial/tutorial_general_201.png
[202]: ./media/linkedinElevate-tutorial/tutorial_general_202.png
[203]: ./media/linkedinElevate-tutorial/tutorial_general_203.png