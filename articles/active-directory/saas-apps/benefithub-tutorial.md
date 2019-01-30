---
title: 'Tutorial: Integração do Azure Active Directory com o BenefitHub | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o BenefitHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 4069fe32-a452-463f-973e-7aa0baa4c2fa
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: jeedes
ms.openlocfilehash: ee0e91cfc106cd065c233e3bba09ca2ceba04bb1
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54815745"
---
# <a name="tutorial-azure-active-directory-integration-with-benefithub"></a>Tutorial: Integração do Azure Active Directory com o BenefitHub

Neste tutorial, você aprende a integrar o BenefitHub ao Azure AD (Azure Active Directory).

A integração do BenefitHub ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao BenefitHub
- É possível permitir que os usuários se conectem automaticamente ao BenefitHub (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao BenefitHub, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do BenefitHub

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o BenefitHub por meio da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-benefithub-from-the-gallery"></a>Adicionando o BenefitHub por meio da galeria
Para configurar a integração do BenefitHub ao Azure AD, você precisa adicionar o BenefitHub à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o BenefitHub por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **BenefitHub**.

    ![Criação de um usuário de teste do AD do Azure](./media/benefithub-tutorial/tutorial_benefithub_search.png)

1. No painel de resultados, selecione **BenefitHub** e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/benefithub-tutorial/tutorial_benefithub_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configura e testa o logon único do Azure AD com o BenefitHub, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do BenefitHub é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do BenefitHub.

No BenefitHub, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o BenefitHub, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criando um usuário de teste do BenefitHub](#creating-a-benefithub-test-user)** – para ter um equivalente de Brenda Fernandes no BenefitHub que esteja vinculado à representação de usuário do Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo BenefitHub.

**Para configurar o logon único do Azure AD com o BenefitHub, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo do **BenefitHub**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/benefithub-tutorial/tutorial_benefithub_samlbase.png)

1. Na seção **Domínio e URLs do BenefitHub**, realize as seguintes etapas:

    ![Configurar o logon único](./media/benefithub-tutorial/tutorial_benefithub_url1.png)
  
     a. Na caixa de texto **Identificador**, digite: `urn:benefithub:passport`
    
    b. Na caixa de texto **URL de Resposta**, digite: `https://passport.benefithub.info/saml/post/ac`

1. O aplicativo BenefitHub espera as declarações do SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizados para a configuração de atributos do token SAML. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção "**Atributos de Usuário**" na página de integração do aplicativo. 

    ![Configurar o logon único](./media/benefithub-tutorial/tutorial_benefithub_attribute.png)

1. Na seção **Atributos de Usuário** da caixa de diálogo **Logon único**, configure o atributo do token SAML, conforme mostrado na imagem anterior e realize as seguintes etapas:
    
    | Nome do atributo | Valor do atributo |
    | ------------------- | -------------------- |    
    | organizationid | < organizationid > |

    > [!NOTE]
    > Esse valor de atributo não é real. Atualize esse valor com a organizationid real. Contate a [equipe de suporte do BenefitHub](https://www.benefithub.com/Home/ContactUs) para obter a organizationid real.
    
     a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/benefithub-tutorial/tutorial_attribute_04.png)

    ![Configurar o logon único](./media/benefithub-tutorial/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.
    
    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.
    
    d. Clique em **OK**.

    > [!NOTE] 
    > Antes de configurar a declaração SAML, você precisa contatar o [suporte do BenefitHub](https://www.benefithub.com/Home/ContactUs) e solicitar o valor do atributo de identificador exclusivo para seu locatário. Você precisa desse valor para configurar a declaração personalizada para seu aplicativo.

1. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![Configurar o logon único](./media/benefithub-tutorial/tutorial_benefithub_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/benefithub-tutorial/tutorial_general_400.png)

1. Para configurar o logon único no lado do **BenefitHub**, é necessário enviar o **XML de Metadados** baixado para a [equipe de suporte do BenefitHub](https://www.benefithub.com/Home/ContactUs). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre o recurso de documentação inserida aqui: [Documentação inserida do Microsoft Azure Active Directory]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/benefithub-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/benefithub-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/benefithub-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/benefithub-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-benefithub-test-user"></a>Criando um usuário de teste do BenefitHub

Nesta seção, você cria um usuário chamado Brenda Fernandes no BenefitHub. Trabalhe com a [equipe de suporte do BenefitHub](https://www.benefithub.com/Home/ContactUs) para adicionar os usuários à plataforma do BenefitHub. Os usuários devem ser criados e ativados antes de usar o logon único. 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao BenefitHub.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao BenefitHub, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **BenefitHub**.

    ![Configurar o logon único](./media/benefithub-tutorial/tutorial_benefithub_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco BenefitHub no Painel de Acesso, deverá ser automaticamente conectado ao aplicativo BenefitHub.
Para obter mais informações sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/benefithub-tutorial/tutorial_general_01.png
[2]: ./media/benefithub-tutorial/tutorial_general_02.png
[3]: ./media/benefithub-tutorial/tutorial_general_03.png
[4]: ./media/benefithub-tutorial/tutorial_general_04.png

[100]: ./media/benefithub-tutorial/tutorial_general_100.png

[200]: ./media/benefithub-tutorial/tutorial_general_200.png
[201]: ./media/benefithub-tutorial/tutorial_general_201.png
[202]: ./media/benefithub-tutorial/tutorial_general_202.png
[203]: ./media/benefithub-tutorial/tutorial_general_203.png
