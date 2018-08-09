---
title: 'Tutorial: integração do Azure Active Directory ao TimeOffManager | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o TimeOffManager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3685912f-d5aa-4730-ab58-35a088fc1cc3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: e7f0f6bb778dedeea61b74b5ca0c2edbadd5279b
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39430280"
---
# <a name="tutorial-azure-active-directory-integration-with-timeoffmanager"></a>Tutorial: Integração do Active Directory do Azure ao TimeOffManager

Neste tutorial, você aprenderá a integrar o TimeOffManager ao Azure AD (Azure Active Directory).

Integrar o TimeOffManager ao Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao TimeOffManager
- Você pode permitir que seus usuários façam logon automaticamente no TimeOffManager (logon único) com as respectivas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao TimeOffManager, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do TimeOffManager

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o TimeOffManager da galeria
1. Configurar e testar logon único do Azure AD

## <a name="add-timeoffmanager-from-the-gallery"></a>Adicionar o TimeOffManager da galeria
Para configurar a integração do TimeOffManager ao Azure AD, você precisará adicionar o TimeOffManager da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o TimeOffManager da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **TimeOffManager**, selecione **TimeOffManager** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Adicionar da galeria](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
Nesta seção, você configurará e testará o logon único do Azure AD com o TimeOffManager, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do TimeOffManager é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no TimeOffManager.

No TimeOffManager, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o TimeOffManager, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do TimeOffManager](#create-a-timeoffmanager-test-user)** – para ter um equivalente de Brenda Fernandes no TimeOffManager que esteja vinculado à representação de usuário do Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Testar o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal do Azure e configura o logon único no aplicativo TimeOffManager.

**Para configurar o logon único do Azure AD com o TimeOffManager, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração do aplicativo **TimeOffManager**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Logon baseado em SAML](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_samlbase.png)

1. Na seção **Domínio e URLs do TimeOffManager**, siga as etapas a seguir:

     ![Seção Domínio e URLs do TimeOffManager](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_url.png)

    Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company_id=<companyid>`

    > [!NOTE] 
    > Esse valor não é real. Atualize esse valor com a URL de Resposta real. Você pode obter esse valor de **página Configurações de logon único** que é explicada posteriormente no tutorial ou entrar em contato com a [equipe de suporte do TimeOffManager](https://www.purelyhr.com/contact-us).
 
1. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Seção Certificado de Autenticação SAML](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_certificate.png) 

1. O objetivo desta seção é descrever como permitir que os usuários se autentiquem no TimeOffManger com sua conta do Azure AD usando federação baseada em protocolo SAML.
    
    Seu aplicativo TimeOffManger espera as declarações do SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra um exemplo disso.

    ![Atributos de Token SAML](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_attrb.png "Atributos de Token SAML")
    
    | Nome do atributo | Valor do atributo |
    | --- | --- |
    | Firstname |User.givenname |
    | Sobrenome |User.surname |
    | Email |User.mail |
    
    a.  Para cada linha de dados na tabela acima, clique em **adicionar atributo do usuário**.
    
    ![Atributos de Token SAML](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_addattrb.png "Atributos de Token SAML")
    
    ![Atributos de Token SAML](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_addattrb1.png "Atributos de Token SAML")
    
    b.  Na caixa de texto **Nome do Atributo** , digite o nome do atributo mostrado para essa linha.
    
    c.  Na caixa de texto **Valor do Atributo**, selecione o valor do atributo mostrado para essa linha.
    
    d.  Clique em **OK**.
    
1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/timeoffmanager-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do TimeOffManager**, clique em **Configurar TimeOffManager** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Seção de configuração do TimeOffManager](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_configure.png) 

1. Em outra janela do navegador da Web, faça logon em seu site de empresa TimeOffManager como um administrador.

1. Vá para **Conta \> Opções da Conta \> Configurações de Logon Único**.
   
   ![Configurações de Logon Único](./media/timeoffmanager-tutorial/ic795917.png "Configurações de Logon Único")
1. Na seção **Configurações de Logon Único** , realize as seguintes etapas:
   
   ![Configurações de Logon Único](./media/timeoffmanager-tutorial/ic795918.png "Configurações de Logon Único")
   
   a. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole todo o Certificado na caixa de texto **Certificado X.509** .
   
   b. Na caixa de texto **Emissor IdP**, cole o valor da **ID de Entidade do SAML** que você copiou do Portal do Azure.
   
   c. Na caixa de texto **URL do Ponto de Extremidade IdP**, cole o valor da **URL de Serviço de Logon Único do SAML** que você copiou do Portal do Azure.
   
   d. Para **Impor SAML**, selecione **Não**.
   
   e. Para **Criação Automática de Usuários**, selecione **Sim**.
   
   f. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Saída** copiado do portal do Azure.
   
   g. Clique em **Salvar Alterações**.

1. Na página **Configurações de logon único**, copie o valor de **URL do Serviço do Consumidor de Declaração** e cole-o na caixa de texto **URL de Resposta** na seção **Domínio e URLs do TimeOffManager** no Portal do Azure. 

      ![Configurações de Logon Único](./media/timeoffmanager-tutorial/ic795915.png "Configurações de Logon Único")

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/timeoffmanager-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Usuários e grupos --> Todos os usuários](./media/timeoffmanager-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Botão Adicionar](./media/timeoffmanager-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Página da caixa de diálogo do usuário](./media/timeoffmanager-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-timeoffmanager-test-user"></a>Criar um usuário de teste do TimeOffManager

Para permitir que os usuários do AD do Azure façam logon no TimeOffManager, eles deverão ser provisionados no TimeOffManager.  

O TimeOffManager dá suporte ao provisionamento de usuário just in time. Não há nenhum item de ação para você.  

Os usuários são adicionados automaticamente durante o primeiro logon usando o logon único.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do TimeOffManager ou as APIs fornecidas pelo TimeOffManager para provisionar as contas de usuário do Azure AD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao TimeOffManager.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao TimeOffManager, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **TimeOffManager**.

    ![TimeOffManager na lista de aplicativos](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco de TimeOffManager no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo TimeOffManager. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/timeoffmanager-tutorial/tutorial_general_01.png
[2]: ./media/timeoffmanager-tutorial/tutorial_general_02.png
[3]: ./media/timeoffmanager-tutorial/tutorial_general_03.png
[4]: ./media/timeoffmanager-tutorial/tutorial_general_04.png

[100]: ./media/timeoffmanager-tutorial/tutorial_general_100.png

[200]: ./media/timeoffmanager-tutorial/tutorial_general_200.png
[201]: ./media/timeoffmanager-tutorial/tutorial_general_201.png
[202]: ./media/timeoffmanager-tutorial/tutorial_general_202.png
[203]: ./media/timeoffmanager-tutorial/tutorial_general_203.png

