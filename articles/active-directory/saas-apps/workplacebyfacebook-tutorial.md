---
title: 'Tutorial: Integração do Azure Active Directory com o Workplace by Facebook| Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Workplace by Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: jeedes
ms.openlocfilehash: 1f83dd64c7f6773ddb8956e6ebbc37b8c55aacec
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423864"
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Tutorial: Integração do Azure Active Directory com o Workplace by Facebook

Neste tutorial, você aprenderá a integrar o Workplace by Facebook ao Azure AD (Azure Active Directory).

A integração do Workplace by Facebook ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso ao Workplace by Facebook
- Você pode habilitar o logon automático de seus usuários no Workplace by Facebook (Logon único) com as contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Workplace by Facebook, são necessários os seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Workplace by Facebook habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

> [!NOTE]
> O Facebook tem dois produtos, o Workplace Standard (gratuito) e o Workplace Premium (pago). Qualquer locatário do Workplace Premium pode configurar a integração de SCIM e SSO sem outras implicações para o custo ou as licenças necessárias. O SSO e o SCIM não estão disponíveis nas instâncias do Workplace Standard.

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Workplace by Facebook da Galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Adicionar o Workplace by Facebook da Galeria
Para configurar a integração do Workplace by Facebook ao Azure AD, você precisa adicionar o Workplace by Facebook da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Workplace by Facebook da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **Workplace by Facebook**.

    ![Criação de um usuário de teste do AD do Azure](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_search.png)

1. No painel de resultados, selecione **Workplace by Facebook** e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Workplace by Facebook, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Workplace by Facebook é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Workplace by Facebook.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** no Workplace by Facebook.

Para configurar e testar o logon único do Azure AD com o Workplace by Facebook, é necessário concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Configurando a frequência de reautenticação](#configuring-reauthentication-frequency)** – para configurar o Workplace para solicitar uma verificação SAML.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criação de um usuário de teste do Workplace by Facebook](#creating-a-workplace-by-facebook-test-user)** – para ter um equivalente de Brenda Fernandes no Workplace by Facebook que esteja vinculado à representação de usuário no Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo Workplace by Facebook.

**Para configurar o logon único do Azure AD com o Workplace by Facebook, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Workplace by Facebook**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_samlbase.png)

1. Na seção **Domínio e URLs do Workplace by Facebook**, realize as seguintes etapas:

    ![Configurar o logon único](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<instancename>.facebook.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://www.facebook.com/company/<instanceID>`

    > [!NOTE] 
    > Esses não são os valores reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Consulte a página Autenticação do Workplace Company Dashboard a fim de obter os valores corretos para a comunidade do Workplace. 

1. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar o logon único](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/workplacebyfacebook-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do Workplace by Facebook**, clique em **Configurar o Workplace by Facebook** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configurar o logon único](./media/workplacebyfacebook-tutorial/config.png) 

1. Em outra janela do navegador da Web, faça logon em seu site de empresa do Workplace by Facebook como administrador.
  
   > [!NOTE] 
   > Como parte do processo de autenticação SAML, o Workplace poderá utilizar cadeias de consulta de até 2,5 quilobytes para passar parâmetros para o Azure AD.

1. No **Painel da Empresa**, acesse a guia **Autenticação**.

1. Em **Autenticação SAML**, selecione **Somente SSO** na lista suspensa.

1. Insira os valores copiados na seção **Configuração do Workplace by Facebook** do Portal do Azure nos campos correspondentes:

    *   Na caixa de texto **URL de SAML**, cole o valor da **URL do Serviço de Logon Único** copiado do Portal do Azure.
    *   Na caixa de texto **URL do Emissor SAML**, cole o valor da **ID da Entidade SAML** copiado do Portal do Azure.
    *   Em **Redirecionamento de Logoff SAML** (Opcional), cole o valor da **URL de Saída** copiado do Portal do Azure.
    *   Abra o **Certificado codificado em Base64** no bloco de notas baixado do Portal do Azure, copie o conteúdo dele para a área de transferência e, depois, cole-o na caixa de texto **Certificado SAML**.

1. Talvez seja necessário inserir uma URL do Público-Alvo, uma URL do Destinatário e uma URL do ACS (Serviço do Consumidor de Declaração) listadas na seção **Configuração do SAML**.

1. Role até o final da seção e clique no botão **Testar SSO**. Isso resultará na exibição de uma janela pop-up com a página de logon do Azure AD apresentada. Insira suas credenciais como de costume para se autenticar. 

    **Solução de problemas:** verifique se o endereço de email retornado do Azure AD é o mesmo da conta do Workplace com a qual você está conectado.

1. Depois que o teste for concluído com êxito, role até a parte inferior da página e clique no botão **Salvar**.

1. Agora, todos os usuários que usam o Workplace verão a página de logon do Azure AD para autenticação.

1. **Redirecionamento de Logoff SAML (opcional)** - 

    Você pode optar por configurar uma URL de Logoff SAML, que pode ser usada para apontar para a página de logoff do Azure AD. Quando essa configuração for habilitada e configurada, o usuário não será mais direcionado para a página de logoff do Workplace. Em vez disso, o usuário será redirecionado para a URL que foi adicionada à configuração Redirecionamento de Logoff SAML.

### <a name="configuring-reauthentication-frequency"></a>Configurando a frequência de reautenticação

É possível configurar o Workplace para solicitar uma verificação SAML todos os dias, a cada três dias, toda semana, a cada duas semanas, todos os meses ou nunca.

> [!NOTE] 
>O valor mínimo para a verificação SAML em aplicativos móveis é definido como uma semana.

Você também pode forçar uma redefinição SAML para todos os usuários usando o botão Exigir autenticação SAML para todos os usuários agora.


### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/workplacebyfacebook-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/workplacebyfacebook-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/workplacebyfacebook-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/workplacebyfacebook-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-workplace-by-facebook-test-user"></a>Criação de um usuário de teste do Workplace by Facebook

Nesta seção, um usuário chamado Brenda Fernandes será criado no Workplace by Facebook. O Workplace by Facebook dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão.

Não há nenhuma ação para você nesta seção. Se um usuário ainda não existir no Workplace by Facebook, um novo será criado quando você tentar acessar o Workplace by Facebook.

>[!Note]
>Se você precisar criar um usuário manualmente, contate a [equipe de suporte ao Cliente do Workplace by Facebook](https://workplace.fb.com/faq/)

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Workplace by Facebook.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Workplace by Facebook, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **Workplace by Facebook**.

    ![Configurar o logon único](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Configurar Provisionamento de Usuário](workplacebyfacebook-provisioning-tutorial.md)



<!--Image references-->

[1]: ./media/workplacebyfacebook-tutorial/tutorial_general_01.png
[2]: ./media/workplacebyfacebook-tutorial/tutorial_general_02.png
[3]: ./media/workplacebyfacebook-tutorial/tutorial_general_03.png
[4]: ./media/workplacebyfacebook-tutorial/tutorial_general_04.png

[100]: ./media/workplacebyfacebook-tutorial/tutorial_general_100.png

[200]: ./media/workplacebyfacebook-tutorial/tutorial_general_200.png
[201]: ./media/workplacebyfacebook-tutorial/tutorial_general_201.png
[202]: ./media/workplacebyfacebook-tutorial/tutorial_general_202.png
[203]: ./media/workplacebyfacebook-tutorial/tutorial_general_203.png
