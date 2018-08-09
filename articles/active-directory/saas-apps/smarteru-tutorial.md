---
title: 'Tutorial: integração do Azure Active Directory com o SmarterU | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SmarterU.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 95fe3212-d052-4ac8-87eb-ac5305227e85
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 12c7a2751b980ef7951be9043a62dbb1ec50a63d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439754"
---
# <a name="tutorial-azure-active-directory-integration-with-smarteru"></a>Tutorial: integração do Azure Active Directory com o SmarterU

Neste tutorial, você aprenderá a integrar o SmarterU ao Azure AD (Azure Active Directory).

A integração do SmarterU ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao SmarterU
- Você pode permitir que seus usuários façam logon automaticamente no SmarterU (logon único) com as contas do Azure AD deles
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o SmarterU, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do SmarterU

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o SmarterU da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-smarteru-from-the-gallery"></a>Adicionar o SmarterU da galeria
Para configurar a integração do SmarterU ao Azure AD, você precisará adicionar o SmarterU da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SmarterU na galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **SmarterU**.

    ![Criação de um usuário de teste do AD do Azure](./media/smarteru-tutorial/tutorial_smarteru_search.png)

1. No painel de resultados, selecione **SmarterU** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/smarteru-tutorial/tutorial_smarteru_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o SmarterU, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do SmarterU é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no SmarterU.

No SmarterU, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o SmarterU, você precisará concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criação de um usuário de teste do SmarterU](#creating-a-smarteru-test-user)** – para ter um equivalente de Brenda Fernandes no SmarterU que esteja vinculado à representação do usuário no Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo SmarterU.

**Para configurar o logon único do Azure AD com o SmarterU, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **SmarterU**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/smarteru-tutorial/tutorial_smarteru_samlbase.png)

1. Na seção **URLs e Domínio do SmarterU**, execute as seguintes etapas: 

    ![Configurar o logon único](./media/smarteru-tutorial/tutorial_smarteru_url.png)

    Na caixa de texto **Identificador**, digite a URL: `https://www.smarteru.com/`

1. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![Configurar o logon único](./media/smarteru-tutorial/tutorial_smarteru_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/smarteru-tutorial/tutorial_general_400.png)

1. Em outra janela do navegador da Web, faça logon em seu site de empresa do SmarterU como um administrador.

1. Na barra de ferramentas na parte superior, clique em **Configurações da Conta**.
   
    ![Configurações da Conta](./media/smarteru-tutorial/accountsettings.png)

1. Na página de configuração da conta, execute as seguintes etapas: 
   
    ![Autoridade Externa](./media/smarteru-tutorial/externalauthorizationconfiguration.png) 
 
      a. Selecione **Habilitar Autorização Externa**.
  
      b. Na seção **Controle de Logon Principal**, selecione a guia **SmarterU**.
  
      c. Na seção **Logon do Usuário Padrão**, selecione a guia **SmarterU**.
  
      d. Selecione **Habilitar SAML**.
  
      e. Copie o conteúdo do arquivo de metadados baixado e cole-o na caixa de texto **Metadados de IdP**.
      
      f. Selecione um **Atributo/Declaração do Identificador**.
  
      g. Clique em **Salvar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/smarteru-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/smarteru-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/smarteru-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/smarteru-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-smarteru-test-user"></a>Criar um usuário de teste do SmarterU

Para permitir que os usuários do Azure AD façam logon no SmarterU, eles deverão ser provisionados no SmarterU.

No caso do SmarterU, o provisionamento será uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **SmarterU** .

1. Vá para **Usuários**.

1. Na seção do usuário, execute as seguintes etapas:
   
    ![Novo Usuário](./media/smarteru-tutorial/adduser.png)  

    a. Clique em **+Usuário**.
    
    b. Digite os valores de atributo relacionados da conta de usuário do Azure AD nas seguintes caixas de texto: **Email Principal**, **ID do Funcionário**, **Senha**, **Verificar Senha**, **Nome Dado**, **Sobrenome**.
    
    c. Clique em **Ativo**. 
    
    d. Clique em **Salvar**.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do SmarterU ou as APIs fornecidas pelo SmarterU para provisionar as contas de usuário do AAD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao SmarterU.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao SmarterU, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **SmarterU**.

    ![Configurar o logon único](./media/smarteru-tutorial/tutorial_smarteru_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.
 
Ao clicar no bloco do SmarterU no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo SmarterU.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/smarteru-tutorial/tutorial_general_01.png
[2]: ./media/smarteru-tutorial/tutorial_general_02.png
[3]: ./media/smarteru-tutorial/tutorial_general_03.png
[4]: ./media/smarteru-tutorial/tutorial_general_04.png

[100]: ./media/smarteru-tutorial/tutorial_general_100.png

[200]: ./media/smarteru-tutorial/tutorial_general_200.png
[201]: ./media/smarteru-tutorial/tutorial_general_201.png
[202]: ./media/smarteru-tutorial/tutorial_general_202.png
[203]: ./media/smarteru-tutorial/tutorial_general_203.png

