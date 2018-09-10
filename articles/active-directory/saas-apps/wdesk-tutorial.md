---
title: 'Tutorial: Integração do Azure Active Directory ao Wdesk | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Wdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: 9ff4d38240bf44cdb3112730b6f6962feed09a6a
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444429"
---
# <a name="tutorial-azure-active-directory-integration-with-wdesk"></a>Tutorial: Integração do Azure Active Directory ao Wdesk

Neste tutorial, você aprenderá a integrar o Wdesk ao Azure AD (Azure Active Directory).

A integração do Wdesk ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Wdesk
- Você pode permitir que seus usuários entrem automaticamente no Wdesk (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte. [O que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Wdesk, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Wdesk habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Wdesk da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-wdesk-from-the-gallery"></a>Adicionar o Wdesk da galeria
Para configurar a integração do Wdesk ao Azure AD, você precisará adicionar o Wdesk a partir da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Wdesk por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **Wdesk**.

    ![Criação de um usuário de teste do AD do Azure](./media/wdesk-tutorial/tutorial_wdesk_search.png)

1. No painel de resultados, selecione **Wdesk** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/wdesk-tutorial/tutorial_wdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Wdesk, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Wdesk é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Wdesk.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** ao Azure AD como sendo o valor de **Nome de usuário** no Wdesk.

Para configurar e testar o logon único do Azure AD com o Wdesk, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criar um usuário de teste do Wdesk](#creating-a-wdesk-test-user)** – para ter um equivalente de Brenda Fernandes no Wdesk que esteja vinculado à representação do usuário no Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal do Azure e configura o logon único no aplicativo Wdesk.

**Para configurar o logon único do Azure AD com o Wdesk, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Wdesk**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/wdesk-tutorial/tutorial_wdesk_samlbase.png)

1. Na seção **Domínio e URLs do Wdesk**, se você quiser configurar o aplicativo no modo iniciado pelo **IDP**, execute as seguintes etapas:

    ![Configurar o logon único](./media/wdesk-tutorial/tutorial_wdesk_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

1. Marque **Mostrar configurações de URL avançadas**. Se quiser configurar o aplicativo no modo iniciado pelo **SP**, realize as seguintes etapas:

    ![Configurar o logon único](./media/wdesk-tutorial/tutorial_wdesk_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`
     
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Você pode obter esses valores no portal do WDesk ao configurar o SSO. 
  
1. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![Configurar o logon único](./media/wdesk-tutorial/tutorial_wdesk_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/wdesk-tutorial/tutorial_general_400.png)
    
1. Em uma janela diferente do navegador da Web, faça logon no Wdesk como Administrador de segurança.

1. No canto inferior esquerdo, clique em **Administrador** e escolha **Conta de Administrador**:
 
     ![Configurar o logon único](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

1. Na Administração do Wdesk, navegue até **Segurança**, **SAML** > **Configurações de SAML**:

    ![Configurar o logon único](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

1. Em **Configurações Gerais**, marque **Habilitar Logon Único do SAML**:

    ![Configurar o logon único](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

1. Em **Detalhes do Provedor de Serviço**, execute as seguintes etapas:

    ![Configurar o logon único](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Copie a **URL de Logon** e cole-a na caixa de texto **Url de Logon** no Portal do Azure.
   
      b. Copie a **URL de Metadados** e cole-a na caixa de texto **Identificador** no Portal do Azure.
       
      c. Copie a **URL de Logon** e cole-a na caixa de texto **Url de Resposta** no Portal do Azure.
   
      d. Clique em **Salvar** no Portal do Azure para salvar as alterações.      

1. Clique em **Definir Configurações do IdP** para abrir a caixa de diálogo **Editar Configurações do IdP**. Clique em **Escolher Arquivo** para localizar o arquivo **Metadata.xml** salvo no Portal do Azure, depois carregue-o.
    
    ![Configurar o logon único](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
1. Clique em **Salvar alterações**.

    ![Configurar o logon único](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/wdesk-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/wdesk-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/wdesk-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/wdesk-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-wdesk-test-user"></a>Criar um usuário de teste do Wdesk

Para permitir que os usuários do Azure AD façam logon no Wdesk, eles devem ser provisionados no Wdesk. No Wdesk, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon no Wdesk como Administrador de Segurança.
1. Navegue até **Administrador** > **Conta de Administrador**.

     ![Configurar o logon único](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

1. Clique em **Membros**, sob **Pessoas**.

1. Agora clique em **Adicionar Membro** para abrir a caixa de diálogo **Adicionar Membro**. 
   
    ![Criação de um usuário de teste do AD do Azure](./media/wdesk-tutorial/createuser1.png)  

1. Na caixa de texto **Usuário**, insira o nome do usuário, como **brittasimon@contoso.com** e clique no botão **Continuar**.

    ![Criação de um usuário de teste do AD do Azure](./media/wdesk-tutorial/createuser3.png)

1.  Insira os detalhes, conforme mostrado abaixo:
  
    ![Criação de um usuário de teste do AD do Azure](./media/wdesk-tutorial/createuser4.png)
 
    a. Na caixa de texto **Email**, insira o email do usuário como **brittasimon@contoso.com**.

    b. Na caixa de texto **Nome**, insira o nome do usuário como **Brenda**.

    c. Na caixa de texto **Sobrenome**, insira o nome do usuário como **Fernandes**.

1. Clique no botão **Salvar Membro**.  

    ![Criação de um usuário de teste do AD do Azure](./media/wdesk-tutorial/createuser5.png)

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Wdesk.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Wdesk, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, escolha **Wdesk**.

    ![Configurar o logon único](./media/wdesk-tutorial/tutorial_wdesk_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Wdesk no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Wdesk.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/wdesk-tutorial/tutorial_general_01.png
[2]: ./media/wdesk-tutorial/tutorial_general_02.png
[3]: ./media/wdesk-tutorial/tutorial_general_03.png
[4]: ./media/wdesk-tutorial/tutorial_general_04.png

[100]: ./media/wdesk-tutorial/tutorial_general_100.png

[200]: ./media/wdesk-tutorial/tutorial_general_200.png
[201]: ./media/wdesk-tutorial/tutorial_general_201.png
[202]: ./media/wdesk-tutorial/tutorial_general_202.png
[203]: ./media/wdesk-tutorial/tutorial_general_203.png

