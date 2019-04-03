---
title: 'Tutorial: Integração do Azure Active Directory com o Workpath | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Workpath.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 320b0daf-14be-4813-b59b-25a6a5070690
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e8b334998983684d50c4faddceb03a0f30fd257
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878155"
---
# <a name="tutorial-azure-active-directory-integration-with-workpath"></a>Tutorial: Integração do Azure Active Directory com o Workpath

Neste tutorial, você aprenderá a integrar o Workpath ao Azure AD (Azure Active Directory).

A integração do Workpath ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Workpath
- Você pode permitir que seus usuários façam logon automaticamente no Workpath (logon único) com as contas do Azure AD deles
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Workpath, você precisará dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura do Workpath com logon único habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Workpath a partir da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-workpath-from-the-gallery"></a>Adicionar o Workpath a partir da galeria
Para configurar a integração do Workpath ao Azure AD, você precisará adicionar o Workpath da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Workpath da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **Workpath**.

    ![Criação de um usuário de teste do AD do Azure](./media/workpath-tutorial/tutorial_workpath_search.png)

1. No painel de resultados, selecione **Workpath** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/workpath-tutorial/tutorial_workpath_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Workpath, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Workpath é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Workpath.

No Workpath, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Workpath, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criação de um usuário de teste do Workpath](#creating-a-workpath-test-user)** – para ter um equivalente de Brenda Fernandes no Workpath que esteja vinculado à representação do usuário no Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal do Azure e configura o logon único no aplicativo Workpath.

**Para configurar o logon único do Azure AD com o Workpath, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Workpath**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/workpath-tutorial/tutorial_workpath_samlbase.png)

1. Na seção **Domínio e URLs do Workpath**, se você quiser configurar o aplicativo em modo iniciado pelo **IDP**, execute as seguintes etapas:

    ![Configurar o logon único](./media/workpath-tutorial/tutorial_workpath_url.png)

     a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://api.workpath.com/v1/saml/metadata/<instancename>`

    b. Na caixa de texto **URL de resposta** , digite uma URL no seguinte padrão: `https://api.workpath.com/v1/saml/assert/<instancename>`

1. Marque **Mostrar configurações de URL avançadas**. Se desejar configurar o aplicativo no modo iniciado pelo **SP**, realize as seguintes etapas:

    ![Configurar o logon único](./media/workpath-tutorial/tutorial_workpath_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<subdomain>.workpath.com/`

    > [!NOTE] 
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte do Workpath](https://help.workpath.com) para obter esses valores.

1. O aplicativo Workpath espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção "**Atributos de Usuário**" na página de integração do aplicativo. A captura de tela a seguir mostra um exemplo dessa configuração. 

    ![Configurar o logon único](./media/workpath-tutorial/tutorial_workpath_attributes.png)
    
1. Na seção **Atributos do Usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML como mostra a imagem e execute as etapas a seguir:
    
    | Nome do atributo | Valor do atributo |
    | ------------------- | -------------------- |    
    | first_name | user.givenname |
    | last_name | user.surname |
    
     a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/workpath-tutorial/tutorial_attribute_04.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    ![Configurar o logon único](./media/workpath-tutorial/tutorial_attribute_05.png)

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Deixe a caixa de texto **Namespace** em branco.
    
    e. Clique em **OK**.
    

1. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![Configurar o logon único](./media/workpath-tutorial/tutorial_workpath_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/workpath-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do Workpath**, clique em **Configurar Workpath** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configurar o logon único](./media/workpath-tutorial/tutorial_workpath_configure.png) 

1. Para configurar o logon único no lado do **Workpath**, é necessário enviar o **XML de Metadados** baixado, a **URL de Saída, a ID da Entidade e a URL do Serviço de Logon Único SAML** para a [equipe de suporte do Workpath](https://help.workpath.com). 

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre o recurso de documentação inserida aqui: [Documentação inserida do Microsoft Azure Active Directory]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/workpath-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/workpath-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/workpath-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/workpath-tutorial/create_aaduser_04.png) 

     a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-workpath-test-user"></a>Criar um usuário de teste do Workpath

O Workpath dá suporte ao provisionamento de usuário just in time. Após a autenticação, os usuários são criados no aplicativo automaticamente. 


### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Workpath.

![Atribuir usuário][200] 

**Para atribuir Britta Simon ao Workpath, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, escolha **Workpath**.

    ![Configurar o logon único](./media/workpath-tutorial/tutorial_workpath_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco Workpath no Painel de Acesso, deverá ser automaticamente conectado ao seu aplicativo Workpath.
Para obter mais informações sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/workpath-tutorial/tutorial_general_01.png
[2]: ./media/workpath-tutorial/tutorial_general_02.png
[3]: ./media/workpath-tutorial/tutorial_general_03.png
[4]: ./media/workpath-tutorial/tutorial_general_04.png

[100]: ./media/workpath-tutorial/tutorial_general_100.png

[200]: ./media/workpath-tutorial/tutorial_general_200.png
[201]: ./media/workpath-tutorial/tutorial_general_201.png
[202]: ./media/workpath-tutorial/tutorial_general_202.png
[203]: ./media/workpath-tutorial/tutorial_general_203.png

