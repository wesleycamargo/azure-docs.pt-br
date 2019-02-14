---
title: 'Tutorial: Integração do Azure Active Directory com o Hosted Graphite | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Hosted Graphite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: a1ac4d7f-d079-4f3c-b6da-0f520d427ceb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce0ae55fdc43704670f1d74e52b55db91a20cceb
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56179239"
---
# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Tutorial: Integração do Azure Active Directory com o Hosted Graphite

Neste tutorial, você aprenderá a integrar o Hosted Graphite ao Azure AD (Azure Active Directory).

A integração do Hosted Graphite ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Hosted Graphite
- Você pode permitir que seus usuários façam logon automaticamente no Hosted Graphite (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Hosted Graphite, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura do Hosted Graphite habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Hosted Graphite por meio da Galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-hosted-graphite-from-the-gallery"></a>Adição do Hosted Graphite por meio da Galeria
Para configurar a integração do Hosted Graphite ao Azure AD, você precisa adicionar o Hosted Graphite da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Hosted Graphite por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **Hosted Graphite**.

    ![Criação de um usuário de teste do AD do Azure](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_search.png)

1. No painel de resultados, selecione **Hosted Graphite** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Hosted Graphite com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Hosted Graphite é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Hosted Graphite.

No Hosted Graphite, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Hosted Graphite, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criação de um usuário de teste no Hosted Graphite](#creating-a-hosted-graphite-test-user)**: para ter um equivalente de Brenda Fernandes no Hosted Graphite que esteja vinculado à representação do usuário no Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único no aplicativo Hosted Graphite.

**Para configurar o logon único do Azure AD com o Hosted Graphite, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **Hosted Graphite**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_samlbase.png)

1. Na seção **Domínio e URLs do Hosted Graphite**, se você quiser configurar o aplicativo em **Modo iniciado pelo IDP**, execute as seguintes etapas:

    ![Configurar o logon único](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_url.png)

     a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://www.hostedgraphite.com/metadata/<user id>`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://www.hostedgraphite.com/complete/saml/<user id>`

1. Na seção **Domínio e URLs do Hosted Graphite**, se você quiser configurar o aplicativo em **Modo iniciado pelo SP**, execute as seguintes etapas:
   
    ![Configurar o logon único](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_10.png)
  
     a. Clique na opção **Mostrar URL configurações avançadas**

    b. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://www.hostedgraphite.com/login/saml/<user id>/`   

    > [!NOTE] 
    > Observe que esses não são os valores reais. Você precisa atualizar esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Para obter esses valores, você pode ir para Acessar-> Configuração do SAML no lado do Aplicativo ou entrar em contato com a [equipe de suporte do Hosted Graphite](mailto:help@hostedgraphite.com).
    >
 
1. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![Configurar o logon único](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/hostedgraphite-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do Hosted Graphite**, clique em **Configurar Hosted Graphite** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único do SAML** da **seção de Referência Rápida.**

    ![Configurar o logon único](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_configure.png) 

1. Faça logon no seu locatário do Hosted Graphite como administrador.

1. Acesse a **página de configuração do SAML** na barra lateral (**Acesso -> Configuração do SAML**).
   
    ![Configurar o logon único no lado do aplicativo](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)

1. Confirmar que esses URIs correspondem à configuração feita na seção **Domínio e URLs do Hosted Graphite** do portal do Azure.
   
    ![Configurar o logon único no lado do aplicativo](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)

1. Nas caixas de texto **ID da Entidade ou do Emissor** e **URL de Logon SSO**, cole os valores da **ID da Entidade do SAML** e da **URL de Serviço de Logon Único do SAML** copiados do portal do Azure. 
   
    ![Configurar o logon único no lado do aplicativo](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)
   

1. Selecione "**Somente leitura**" como a **Função de usuário padrão**.
    
    ![Configurar o logon único no lado do aplicativo](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)

1. Abra seu certificado codificado em base 64 no bloco de notas baixado do portal do Azure, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado X.509**.
    
    ![Configurar o logon único no lado do aplicativo](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)

1. Clique no botão **Salvar** .

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre o recurso de documentação inserida aqui: [Documentação inserida do Microsoft Azure Active Directory]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/hostedgraphite-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/hostedgraphite-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/hostedgraphite-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/hostedgraphite-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-hosted-graphite-test-user"></a>Criação de um usuário de teste no Hosted Graphite

O objetivo desta seção é criar uma usuária chamada Brenda Fernandes no Hosted Graphite. O Hosted Graphite dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão.

Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o Hosted Graphite, caso ele ainda não exista.

>[!NOTE]
>Se precisar criar um usuário manualmente, entre em contato com a equipe de suporte do Hosted Graphite via <mailto:help@hostedgraphite.com>. 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você habilita Brenda Fernandes a usar o logon único do Azure concedendo acesso ao Hosted Graphite.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Hosted Graphite, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **Hosted Graphite**.

    ![Configurar o logon único](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

O objetivo desta seção é testar sua configuração de SSO do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Hosted Graphite no Painel de Acesso, você deverá ser conectado automaticamente no seu aplicativo Hosted Graphite.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/hostedgraphite-tutorial/tutorial_general_01.png
[2]: ./media/hostedgraphite-tutorial/tutorial_general_02.png
[3]: ./media/hostedgraphite-tutorial/tutorial_general_03.png
[4]: ./media/hostedgraphite-tutorial/tutorial_general_04.png

[100]: ./media/hostedgraphite-tutorial/tutorial_general_100.png

[200]: ./media/hostedgraphite-tutorial/tutorial_general_200.png
[201]: ./media/hostedgraphite-tutorial/tutorial_general_201.png
[202]: ./media/hostedgraphite-tutorial/tutorial_general_202.png
[203]: ./media/hostedgraphite-tutorial/tutorial_general_203.png

