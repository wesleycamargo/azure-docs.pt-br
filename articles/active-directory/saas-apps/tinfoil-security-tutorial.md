---
title: 'Tutorial: integração do Azure Active Directory com o TINFOIL SECURITY | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o TINFOIL SECURITY.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ed0912e8d4705b6cbefa9eb8f7a712a37d8b0be
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56183168"
---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Tutorial: integração do Azure Active Directory com o TINFOIL SECURITY

Neste tutorial, você aprenderá como integrar o TINFOIL SECURITY ao Azure AD (Azure Active Directory).

A integração do TINFOIL SECURITY com o Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao TINFOIL SECURITY
- É possível permitir que seus usuários façam logon automaticamente no TINFOIL SECURITY (logon único) com as contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o TINFOIL SECURITY, serão necessários os seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura do TINFOIL SECURITY habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar TINFOIL SECURITY da galeria
1. Configurar e testar logon único do Azure AD

## <a name="add-tinfoil-security-from-the-gallery"></a>Adicionar TINFOIL SECURITY da galeria
Para configurar a integração do TINFOIL SECURITY com o Azure AD, é necessário adicionar o TINFOIL SECURITY da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o TINFOIL SECURITY da galeria, siga as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **TINFOIL SECURITY**, selecione **TINFOIL SECURITY** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![TINFOIL SECURITY da galeria](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
Nesta seção, você configurará e testará o logon único do Azure AD com o TINFOIL SECURITY com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do TINFOIL SECURITY é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no TINFOIL SECURITY.

No TINFOIL SECURITY, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o TINFOIL SECURITY, é necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do TINFOIL SECURITY](#create-a-tinfoil-security-test-user)** – para ter um equivalente de Brenda Fernandes no TINFOIL SECURITY que esteja vinculado à representação de usuário no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Testar o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único em seu aplicativo TINFOIL SECURITY.

**Para configurar o logon único do Azure AD com o TINFOIL SECURITY, siga as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **TINFOIL SECURITY**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Logon baseado em SAML](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_samlbase.png)

1. Na seção **URLs e Domínio do TINFOIL SECURITY**, o usuário não precisa seguir nenhuma etapa, uma vez que o aplicativo já está integrado ao Azure.

    ![Configurar o logon único](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_url.png)


1. Na seção **Certificado de Autenticação SAML**, copie o valor da **IMPRESSÃO DIGITAL**.

    ![Seção Certificado de Autenticação SAML](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_certificate.png) 

1. Para adicionar os mapeamentos de atributo necessários, execute as seguintes etapas:
    
    ![Atributos](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_attribute1.png "Atributos")
    
    | Nome do atributo    |   Valor do atributo |
    | ------------------- | -------------------- |
    | accountid | UXXXXXXXXXXXXX |
    
     a. Clique em **adicionar atributo de usuário**.
    
    ![ADICIONAR atributo](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_attribute.png "Atributos")
    
    ![ADICIONAR atributo](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_addatt.png "Atributos")
    
    b. Na caixa de texto **Nome do Atributo**, digite **accountid**.
    
    c. Na caixa de texto **Valor do atributo**, cole o valor da ID da conta que você obterá posteriormente no tutorial.
    
    d. Clique em **OK**.    

1. Clique no botão **Salvar** .

    ![Botão Salvar](./media/tinfoil-security-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do TINFOIL SECURITY**, clique em **Configurar o TINFOIL SECURITY** para abrir a janela **Configurar logon**. Copie a **URL de serviço de logon único SAML** da **seção de Referência Rápida.**

    ![Configuração do TINFOIL SECURITY](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_configure.png) 

1. Em uma janela diferente do navegador da Web, faça logon no site da empresa TINFOIL SECURITY como administrador.

1. Na barra de ferramentas na parte superior, clique em **Minha Conta**.
   
    ![Painel](./media/tinfoil-security-tutorial/ic798971.png "Painel")

1. Clique em **Segurança**.
   
    ![Segurança](./media/tinfoil-security-tutorial/ic798972.png "Segurança")

1. Na página de configuração **Logon Único** , realize as seguintes etapas:
   
    ![Logon Único](./media/tinfoil-security-tutorial/ic798973.png "Logon Único")
   
     a. Selecione **Habilitar SAML**.
   
    b. Clique em **Configuração Manual**.
   
    c. Na caixa de texto **URL da Postagem SAML**, cole o valor da **URL do Serviço de Logon Único SAML** copiada do Portal do Azure
   
    d. Na caixa de texto **Impressão Digital do Certificado SAML**, cole o valor de **Impressão Digital** copiado da seção **Certificado de Autenticação SAML**.
  
    e. Copie o valor da **ID da Sua Conta** e cole-o na caixa de texto **Valor do Atributo** na seção **Adicionar Atributo** no Portal do Azure.
   
    f. Clique em **Salvar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre o recurso de documentação inserida aqui: [Documentação inserida do Microsoft Azure Active Directory]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/tinfoil-security-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Usuários e grupos -> Todos os usuários ](./media/tinfoil-security-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Usuário](./media/tinfoil-security-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/tinfoil-security-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-tinfoil-security-test-user"></a>Criar um usuário de teste do TINFOIL SECURITY

Para permitir que os usuários do Azure AD façam logon no TINFOIL SECURITY, eles devem ser provisionados no TINFOIL SECURITY. No caso do TINFOIL SECURITY, o provisionamento é uma tarefa manual.

**Para provisionar um usuário, siga as seguintes etapas:**

1. Se o usuário fizer parte de uma conta Enterprise, será necessário [contatar a equipe de suporte do TINFOIL SECURITY](https://www.tinfoilsecurity.com/contact) para que a conta de usuário seja criada.

1. Se o usuário for um usuário de SaaS do TINFOIL SECURITY regular, ele poderá adicionar um colaborador a qualquer um dos sites do usuário. Isso dispara um processo para enviar um convite ao email especificado para criar uma nova conta de usuário do TINFOIL SECURITY.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação de conta de usuário do TINFOIL SECURITY ou APIs fornecidas pelo TINFOIL SECURITY para provisionar as contas de usuário do Azure AD.
> 
> 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure, concedendo acesso ao TINFOIL SECURITY.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao TINFOIL SECURITY, siga as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **TINFOIL SECURITY**.

    ![selecione TINFOIL SECURITY](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco TINFOIL SECURITY no Painel de Acesso, você deverá entrar automaticamente no seu aplicativo TINFOIL SECURITY. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tinfoil-security-tutorial/tutorial_general_01.png
[2]: ./media/tinfoil-security-tutorial/tutorial_general_02.png
[3]: ./media/tinfoil-security-tutorial/tutorial_general_03.png
[4]: ./media/tinfoil-security-tutorial/tutorial_general_04.png

[100]: ./media/tinfoil-security-tutorial/tutorial_general_100.png

[200]: ./media/tinfoil-security-tutorial/tutorial_general_200.png
[201]: ./media/tinfoil-security-tutorial/tutorial_general_201.png
[202]: ./media/tinfoil-security-tutorial/tutorial_general_202.png
[203]: ./media/tinfoil-security-tutorial/tutorial_general_203.png

