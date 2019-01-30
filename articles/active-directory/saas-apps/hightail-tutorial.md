---
title: 'Tutorial: integração do Azure Active Directory com o Hightail | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Hightail.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: e15206ac-74b0-46e4-9329-892c7d242ec0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2018
ms.author: jeedes
ms.openlocfilehash: 59342aa95e50b29e58035892967be6d0407aae91
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54812957"
---
# <a name="tutorial-azure-active-directory-integration-with-hightail"></a>Tutorial: integração do Azure Active Directory com o Hightail

Neste tutorial, você aprenderá a integrar o Hightail ao Azure AD (Azure Active Directory).

A integração do Hightail ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Hightail
- Você pode habilitar os usuários a entrar automaticamente no Hightail (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Hightail, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Hightail

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Hightail a partir da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-hightail-from-the-gallery"></a>Adição do Hightail a partir da galeria
Para configurar a integração do Hightail ao Azure AD, você precisará adicionar o Hightail da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Hightail da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **Hightail**.

    ![Criação de um usuário de teste do AD do Azure](./media/hightail-tutorial/tutorial_hightail_search.png)

1. No painel de resultados, selecione **Hightail** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/hightail-tutorial/tutorial_hightail_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com Hightail com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Hightail é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Hightail.

No Hightail, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Hightail, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Como criar de um usuário de teste do Hightail](#creating-a-hightail-test-user)** – para ter um equivalente de Brenda Fernandes no Hightail que esteja vinculado de usuário no Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Hightail.

**Para configurar o logon único do Azure AD com o Hightail, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **Hightail**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Configurar o logon único](./media/hightail-tutorial/tutorial_hightail_samlbase.png)

1. Na seção **Domínio e URLs do Hightail**, execute as seguintes etapas se quiser configurar o aplicativo no modo iniciado pelo **IDP**:

    ![Configurar o logon único](./media/hightail-tutorial/tutorial_hightail_url.png)

    Na caixa de texto **URL de Resposta**, digite a URL como: `https://www.hightail.com/samlLogin?phi_action=app/samlLogin&subAction=handleSamlResponse`

    > [!NOTE]
    > O valor de URL de Resposta não é real. Você atualizará o valor da URL de Resposta com a URL de Resposta real, que é explicada no tutorial posteriormente.

1. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Configurar o logon único](./media/hightail-tutorial/tutorial_hightail_url1.png)

    Na caixa de texto **URL de Logon**, digite a URL como: `https://www.hightail.com/loginSSO`

1. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar o logon único](./media/hightail-tutorial/tutorial_hightail_certificate.png) 

1. O aplicativo Hightail espera que as declarações SAML estejam em um formato específico. Configure as seguintes declarações para o aplicativo. Gerencie os valores desses atributos na guia **"Atributo"** do aplicativo. A captura de tela a seguir mostra um exemplo disso. 

    ![Configurar o logon único](./media/hightail-tutorial/tutorial_hightail_attribute.png) 

1. Na seção **Atributos do Usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML como mostra a imagem e execute as etapas a seguir:
    
    | Nome do atributo | Valor do atributo |
    | ------------------- | -------------------- |
    | Nome | user.givenname |
    | Sobrenome | user.surname |
    | Email | user.mail |    
    | UserIdentity | user.mail |
    
     a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/hightail-tutorial/tutorial_officespace_04.png)

    ![Configurar o logon único](./media/hightail-tutorial/tutorial_officespace_05.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Deixe o **Namespace** em branco.

    e. Clique em **OK**.

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/hightail-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do Hightail**, clique em **Configurar Hightail** para abrir a janela **Configurar logon**. Copie a **URL de serviço de logon único SAML** da **seção de Referência Rápida.**

    ![Configurar o logon único](./media/hightail-tutorial/tutorial_hightail_configure.png)

    >[!NOTE]
    >Antes de configurar o logon único no aplicativo Hightail, coloque seu domínio de email na lista de permissões com a equipe do Hightail para que todos os usuários que usam esse domínio possam utilizar a funcionalidade de Logon Único.

1. Em outra janela do navegador, abra o portal de administração do **Hightail**.

1. Clique no **ícone de Usuário** no canto superior direito da página. 

    ![Configurar o logon único](./media/hightail-tutorial/configure1.png)

1. Clique na guia **Exibir Console de Administrador**.

    ![Configurar o logon único](./media/hightail-tutorial/configure2.png)

1. No menu superior, clique na guia **SAML** e execute estas etapas:

    ![Configurar o logon único](./media/hightail-tutorial/configure3.png)

     a. Na caixa de texto **URL de Logon**, cole o valor da **URL do Serviço de Logon Único SAML** copiado do portal do Azure.

    b. Abra o Certificado codificado em base-64 no bloco de notas baixado do portal do Azure, copie o conteúdo dele para a área de transferência e, depois, cole-o na caixa de texto **Certificado SAML**.

    c. Copie em **COPIAR** para copiar a URL do consumidor do SAML para a sua instância e cole-a na caixa de texto **URL de Resposta** na seção **Domínio e URLs do Hightail** no portal do Azure.

    d. Clique em **Salvar Configurações**.

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/hightail-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/hightail-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/hightail-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/hightail-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-hightail-test-user"></a>Criação de um usuário de teste do Hightail

O objetivo desta seção é criar uma usuária chamada Brenda Fernandes no Hightail. 

Não há itens de ação para você nesta seção. O Hightail dá suporte ao provisionamento de usuários just-in-time com base nas declarações personalizadas. Se você tiver configurado as declarações personalizadas, como mostrado na seção **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-sign-on)** acima, um usuário será criado automaticamente no aplicativo, caso ainda não exista. 

>[!NOTE]
>Se precisar criar um usuário manualmente, você precisará entrar em contato com a [equipe de suporte do Hightail](mailto:support@hightail.com). 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Hightail.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Hightail, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **Hightail**.

    ![Configurar o logon único](./media/hightail-tutorial/tutorial_hightail_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.

### <a name="testing-single-sign-on"></a>Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Hightail no Painel de Acesso, você deverá ser conectado automaticamente a seu aplicativo Hightail.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/hightail-tutorial/tutorial_general_01.png
[2]: ./media/hightail-tutorial/tutorial_general_02.png
[3]: ./media/hightail-tutorial/tutorial_general_03.png
[4]: ./media/hightail-tutorial/tutorial_general_04.png

[100]: ./media/hightail-tutorial/tutorial_general_100.png

[200]: ./media/hightail-tutorial/tutorial_general_200.png
[201]: ./media/hightail-tutorial/tutorial_general_201.png
[202]: ./media/hightail-tutorial/tutorial_general_202.png
[203]: ./media/hightail-tutorial/tutorial_general_203.png

