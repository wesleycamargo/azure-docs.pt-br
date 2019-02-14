---
title: 'Tutorial: Integração do Azure Active Directory ao MaxxPoint | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o MaxxPoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 15ba026e-96fc-4ae8-b135-0169da810e99
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23801a796473d7985c17ffcf8a9f1350c1b0e8e9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56187756"
---
# <a name="tutorial-azure-active-directory-integration-with-maxxpoint"></a>Tutorial: Integração do Azure Active Directory ao MaxxPoint

Neste tutorial, você aprenderá a integrar o MaxxPoint ao Azure AD (Azure Active Directory).

A integração do MaxxPoint ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao MaxxPoint
- Você pode permitir que seus usuários façam logon automaticamente no MaxxPoint (logon único) com as contas do Azure AD deles
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao MaxxPoint, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura habilitada para logon único do MaxxPoint

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o MaxxPoint da galeria
1. configurar e testar o logon único do AD do Azure


## <a name="adding-maxxpoint-from-the-gallery"></a>Adicionar o MaxxPoint da galeria
Para configurar a integração do MaxxPoint ao Azure AD, você precisará adicionar o MaxxPoint à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o MaxxPoint da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **MaxxPoint**.

    ![Criação de um usuário de teste do AD do Azure](./media/maxxpoint-tutorial/tutorial_maxxpoint_001.png)

1. No painel de resultados, selecione **MaxxPoint** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/maxxpoint-tutorial/tutorial_maxxpoint_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você vai configurar e testar o logon único do Azure AD com o MaxxPoint com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do MaxxPoint é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do MaxxPoint.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD como sendo o valor de **Nome de Usuário** no MaxxPoint.

Para configurar e testar o logon único do Azure AD com o MaxxPoint, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criar um usuário de teste do MaxxPoint](#creating-a-maxxpoint-test-user)** — para ter um equivalente de Brenda Fernandes no MaxxPoint que esteja vinculado à representação dela no Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal do Azure e configura o logon único em seu aplicativo MaxxPoint.

**Para configurar o logon único do Azure AD com o MaxxPoint, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **MaxxPoint**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/maxxpoint-tutorial/tutorial_general_300.png)

1. Na seção **Domínio e URLs do MaxxPoint**, se você quiser configurar o aplicativo no **Modo iniciado por IDP**, não é necessário executar qualquer etapa.

    ![Configurar o logon único](./media/maxxpoint-tutorial/tutorial_maxxpoint_02.png)
    
1. Na seção **Domínio e URLs do MaxxPoint**, se você quiser configurar o aplicativo no **Modo iniciado por SP**, não é necessário executar qualquer etapa:
    
    ![Configurar o logon único](./media/maxxpoint-tutorial/tutorial_maxxpoint_03.png)

     a. Clique na opção **Mostrar configurações avançadas de URL**

    b. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://maxxpoint.westipc.com/default/sso/login/entity/<customer-id>-azure`

    > [!NOTE] 
    > Observe que esse não é o valor real. Você precisa atualizar esse valor com a URL de Entrada real. Ligue para a equipe do MaxxPoint **888-728-0950** para obter esse valor.

1. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![Configurar o logon único](./media/maxxpoint-tutorial/tutorial_maxxpoint_06.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/maxxpoint-tutorial/tutorial_general_400.png)

1. Para obter o SSO configurado para seu aplicativo, ligue para a equipe de suporte do MaxxPoint no número **888-728-0950** e ela irá ajudá-lo sobre como disponibilizar o arquivo **XML de Metadados** baixado. 

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, simplesmente clique na guia **Logon Único** e acesse a documentação inserida através da seção **Configuração** na parte inferior. Saiba mais sobre o recurso de documentação inserida aqui: [Documentação inserida do Microsoft Azure Active Directory]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/maxxpoint-tutorial/create_aaduser_01.png) 

1. Vá para **usuários e grupos** e clique em **todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/maxxpoint-tutorial/create_aaduser_02.png) 

1. Na parte superior da caixa de diálogo clique **adicionar** para abrir o **usuário** caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/maxxpoint-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/maxxpoint-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**. 

### <a name="creating-a-maxxpoint-test-user"></a>Criar um usuário de teste do MaxxPoint

Nesta seção, você criará uma usuária chamada Brenda Fernandes no MaxxPoint. Ligue para a equipe de suporte do MaxxPoint no número **888-728-0950** para adicionar os usuários no aplicativo MaxxPoint.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao MaxxPoint.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao MaxxPoint, execute as seguintes etapas:**

1. No portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **MaxxPoint**.

    ![Configurar o logon único](./media/maxxpoint-tutorial/tutorial_maxxpoint_50.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do MaxxPoint no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo MaxxPoint.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/maxxpoint-tutorial/tutorial_general_01.png
[2]: ./media/maxxpoint-tutorial/tutorial_general_02.png
[3]: ./media/maxxpoint-tutorial/tutorial_general_03.png
[4]: ./media/maxxpoint-tutorial/tutorial_general_04.png

[100]: ./media/maxxpoint-tutorial/tutorial_general_100.png

[200]: ./media/maxxpoint-tutorial/tutorial_general_200.png
[201]: ./media/maxxpoint-tutorial/tutorial_general_201.png
[202]: ./media/maxxpoint-tutorial/tutorial_general_202.png
[203]: ./media/maxxpoint-tutorial/tutorial_general_203.png
