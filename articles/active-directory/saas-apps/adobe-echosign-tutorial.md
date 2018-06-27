---
title: 'Tutorial: Integração do Azure Active Directory com o Adobe Sign | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Adobe Sign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: jeedes
ms.openlocfilehash: 9a1a1f1a1866e5221775d583a9bafe86eef17131
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221216"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Tutorial: Integração do Azure Active Directory ao Adobe Sign

Neste tutorial, você aprende a integrar o Adobe Sign ao Azure AD (Azure Active Directory).

A integração do Adobe Sign ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Adobe Sign.
- Você pode permitir que seus usuários façam logon automaticamente no Adobe Sign (logon único) com suas contas do AD do Azure.
- Você pode gerenciar suas contas em um único local: o novo Portal do Azure.

Para obter mais detalhes sobre a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Azure AD ao Adobe Sign, você precisa:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Adobe Sign

Para testar as etapas neste tutorial, siga estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Adobe Sign da galeria.
2. Configurar e testar o logon único do Azure AD.

## <a name="add-adobe-sign-from-the-gallery"></a>Adicionar o Adobe Sign da galeria
Para configurar a integração do Adobe Sign ao Azure AD, você precisa adicionar o Adobe Sign à lista de aplicativos SaaS gerenciados por meio da galeria.

1. No [Portal do Azure](https://portal.azure.com), no painel esquerdo, selecione o ícone do **Azure Active Directory**. 

    ![Captura de tela do ícone do Active Directory do Azure][1]

2. Navegue até **Aplicativos empresariais** > **Todos os aplicativos**.

    ![Captura de tela dos menus do Active Directory do Azure, com os aplicativos Enterprise e Todos os aplicativos realçados][2]
    
3. Para adicionar um novo aplicativo, selecione **Novo aplicativo** na parte superior da caixa de diálogo.

    ![Captura de tela da nova opção de aplicativo na parte superior da caixa de diálogo][3]

4. Na caixa de pesquisa, digite **Adobe Sign**.

    ![Captura de tela da caixa de pesquisa](./media/adobe-echosign-tutorial/tutorial_adobesign_search.png)

5. No painel de resultados, selecione **Adobe Sign** e, em seguida, selecione **Add**.

    ![Captura de tela do painel de resultados](./media/adobe-echosign-tutorial/tutorial_adobesign_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
Nesta seção, você configura e testa o logon único do Azure AD com o Adobe Sign, com base em um usuário de teste chamado "Britta Simon."

Para que o logon único funcione, o Azure AD precisa reconhecer um relacionamento vinculado entre um usuário do AD do Azure e o usuário relacionado no Adobe Sign.

Para estabelecer o relacionamento vinculado, no Adobe Sign, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário**.

Para configurar e testar o logon único do Azure AD com o Adobe Sign, conclua os seguintes blocos de construção:

1. [Configurar o logon único do Azure AD](#configuring-azure-ad-single-sign-on) para habilitar seus usuários a usar esse recurso.
2. [Criar um usuário de teste do Azure AD](#creating-an-azure-ad-test-user) para testar o logon único do Azure AD com Brenda Fernandes.
3. [ Crie um usuário de teste do Adobe Sign ](#creating-an-adobe-sign-test-user) para ter um equivalente de Brenda Fernandes no Adobe Sign que esteja vinculado à representação de usuário do Azure AD.
4. [Atribuir o usuário de teste do Azure AD](#assigning-the-azure-ad-test-user) para permitir que Brenda Fernandes use o logon único do Azure AD.
5. [ Teste o logon único ](#testing-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único no seu aplicativo Adobe Sign.

1. No portal do Azure, na página de integração do aplicativo **Adobe Sign**, selecione **Logon único**.

    ![Captura de tela da página de integração do aplicativo Adobe Sign, com Single sign-on destacado][4]

2. Na caixa de diálogo **Single sign-on**, para o **Mode**, selecione **Logon com base em SAML** para ativar o logon único.

    ![Captura de tela da caixa de diálogo Logon único, com a caixa Modo realçada](./media/adobe-echosign-tutorial/tutorial_adobesign_samlbase.png)

3. Na seção **Adobe Sign Domain e URLs**, execute as seguintes etapas:

    ![Captura de tela da seção Adobe Sign Domain e URLs](./media/adobe-echosign-tutorial/tutorial_adobesign_url.png)

    a. Na caixa de texto **URL de Entrada**, digite uma URL que usa o seguinte padrão: `https://<companyname>.echosign.com/`

    b. Na caixa de texto **Identificador**, digite uma URL que usa o seguinte padrão: `https://<companyname>.echosign.com`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do Adobe Sign](https://helpx.adobe.com/in/contact/support.html) para obter esses valores.

4. Na seção **Certificado de Autenticação do SAML**, selecione **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![Captura de tela da seção Certificado de Assinatura SAML](./media/adobe-echosign-tutorial/tutorial_adobesign_certificate.png) 

5. Clique em **Salvar**.

    ![Captura de tela do botão Salvar](./media/adobe-echosign-tutorial/tutorial_general_400.png)

6. Na seção **Configuração da assinatura do Adobe**, selecione **Configurar o Adobe Sign** para abrir a janela **Configurar logon**. Copie a **URL de Saída**, a **ID da Entidade SAML** e a **URL do Serviço de Logon Único SAML** da seção **Referência Rápida**.

    ![Captura de tela da seção Configuração de sinal da Adobe, com Configurar sinal da Adobe destacado](./media/adobe-echosign-tutorial/tutorial_adobesign_configure.png)

7. Antes da configuração, entre em contato com a [ equipe de suporte do Adobe Sign Client ](https://helpx.adobe.com/in/contact/support.html) para colocar seu domínio na lista de permissões do Adobe Sign. Veja como adicionar o domínio:

    a. A equipe de suporte do [ Adobe Sign Client ](https://helpx.adobe.com/in/contact/support.html) envia um token gerado aleatoriamente. Para o seu domínio, o token será como o seguinte: **adobe-sign-verification = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Publique o token de verificação em um registro de texto DNS e notifique a [ equipe de suporte do Adobe Sign Client ](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > Isso pode levar alguns dias ou mais. Observe que os atrasos de propagação de DNS significam que um valor publicado no DNS pode não estar visível por uma hora ou mais. Seu administrador de TI deve ter conhecimento sobre como publicar esse token em um registro de texto DNS.
    
    c. Quando você notifica a [ equipe de suporte do Adobe Sign Client ](https://helpx.adobe.com/in/contact/support.html) por meio do ticket de suporte, depois que o token é publicado, ele valida o domínio e o adiciona à sua conta.
    
    d. Geralmente, aqui está como publicar o token em um registro DNS:

    * Entre na sua conta de domínio
    * Encontrar a página para atualizar o registro DNS. Esta página pode ser chamada de Gerenciamento de DNS, Gerenciamento do Servidor de Nomes ou Configurações Avançadas.
    * Encontrar os registros TXT para o domínio.
    * Adicione um registro TXT com o valor total do token fornecido pela Adobe.
    * Salve suas alterações.

8. Em uma janela diferente do navegador da Web, entre no site da empresa Adobe Sign como administrador.

9. No menu SAML, selecione **Configurações da conta**  >  **Configurações SAML**.
   
    ![ Captura de tela da página Configurações de SAML do Adobe Sign ] (./media/adobe-echosign-tutorial/ic789520.png " Conta ")

10. Na seção **Configuração do SAML**, execute as seguintes etapas:
  
    ![ Captura de tela das configurações do SAML ] (./media/adobe-echosign-tutorial/ic789521.png " Configurações do SAML ")
   
    a. Em **Modo SAML**, selecione **SAML Obrigatório**.
   
    b. Selecione **Permitir Administradores da Conta Echosign para efetuar login usando suas Credenciais Echosign**.
   
    c. Em **Criação de usuário**, selecione **Adicionar automaticamente usuários autenticados por SAML**.

    d. Cole **ID da entidade SAML**, que você copiou do portal do Azure na caixa de texto **ID da entidade / URL do emissor**.
    
    e. Cole **o URL do Serviço de Logon Único SAML**, que você copiou do portal do Azure na caixa de texto **Login URL / Ponto de Extremidade SSO**.
   
    f. Cole **URL de saída**, copiado do portal do Azure na caixa de texto **URL de logoff / ponto de extremidade de SLO**.

    g. Abra o arquivo **Certificate (Base64)** baixado no Bloco de Notas. Copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Certificado IdP**.

    h. Selecione **Salvar alterações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste, chamado Britta Simon, no portal do Azure.

![Captura de tela do nome de usuário de teste no portal do Azure][100]

1. No **Portal do Azure**, no painel esquerdo, selecione o ícone do **Azure Active Directory**.

    ![Captura de tela do ícone do Azure AD](./media/adobe-echosign-tutorial/create_aaduser_01.png) 

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e selecione **Todos os usuários**.
    
    ![Captura de tela dos menus do Azure AD, com Usuários e grupos e Todos os usuários realçados](./media/adobe-echosign-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, selecione **Adicionar**.
 
    ![Captura de tela da caixa de diálogo superior de Todos os usuários, com a opção Adicionar realçada](./media/adobe-echosign-tutorial/create_aaduser_03.png) 

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:
 
    ![Captura de tela da caixa de diálogo do usuário](./media/adobe-echosign-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **BrendaFernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o endereço de e-mail de BrittaSimon.

    c. Selecione **Show Password** e anote o valor da **Password**.

    d. Selecione **Criar**.
 
### <a name="create-an-adobe-sign-test-user"></a>Crie um usuário de teste do Adobe Sign

Para permitir que os usuários do Azure AD entrem no Adobe Sign, eles devem ser provisionados no Adobe Sign. Esta é uma tarefa manual.

>[!NOTE]
>Você pode usar qualquer outra ferramenta ou API de criação de conta de usuário do Adobe Sign fornecida pelo Adobe Sign para aprovisionar contas de usuários do Azure AD. 

1. Faça login no **site da empresa do Adobe Sign** como administrador.

2. No menu no topo, selecione **Conta**. Em seguida, no painel esquerdo, selecione **Usuários e grupos**  >  **Crie um novo usuário**.
   
    ![ Captura de tela do site da empresa Adobe Sign, com Conta, Usuários e Grupos e Criar um novo usuário realçado ] (./media/adobe-echosign-tutorial/ic789524.png " Conta ")
   
3. Na seção **Criar Novo Usuário** , realize as seguintes etapas:
   
    ![ Captura de tela da seção Criar novo usuário ] (./media/adobe-echosign-tutorial/ic789525.png " Criar usuário ")
   
    a. Digite **Endereço de e-mail**, **Nome** e **Sobrenome** de uma conta válida do AD do Azure que você deseja provisionar nas caixas de texto relacionadas.
   
    b. Selecione **Criar usuário**.

>[!NOTE]
>O titular da conta do Active Directory do Azure recebe um e-mail que inclui um link para confirmar a conta antes que ela se torne ativa. 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure, concedendo acesso ao Adobe Sign.

![Captura de tela do logon único do portal do Azure][200] 

1. No Portal do Azure, abra o modo de exibição de aplicativos. Em seguida, navegue até a visualização de diretório, acesse **Aplicativos corporativos** e selecione **Todos os aplicativos**.

    ![Captura de tela da exibição de aplicativos do portal do Azure, com aplicativos Enterprise e Todos os aplicativos realçados][201] 

2. Na lista de aplicativos, selecione **Adobe Sign**.

    ![Captura de tela da lista de aplicativos, com o Adobe Sign destacado](./media/adobe-echosign-tutorial/tutorial_adobesign_app.png) 

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![Captura de tela do menu, com Usuários e grupos destacados][202] 

4. Selecione **Adicionar**. Em seguida, na seção **Adicionar designação**, selecione **Usuários e grupos**.

    ![Captura de tela da página Usuários e grupos e da seção Adicionar atribuição][203]

5. Na caixa de diálogo **Usuários e grupos**, na lista de usuários, selecione **Britta Simon**.

6. Na caixa de diálogo **Usuários e grupos**, clique em **Selecione**.

7. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Quando você seleciona o bloco Sinal da Adobe no Painel de Acesso, deve ser conectado automaticamente ao seu aplicativo Adobe Sign. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/adobe-echosign-tutorial/tutorial_general_01.png
[2]: ./media/adobe-echosign-tutorial/tutorial_general_02.png
[3]: ./media/adobe-echosign-tutorial/tutorial_general_03.png
[4]: ./media/adobe-echosign-tutorial/tutorial_general_04.png

[100]: ./media/adobe-echosign-tutorial/tutorial_general_100.png

[200]: ./media/adobe-echosign-tutorial/tutorial_general_200.png
[201]: ./media/adobe-echosign-tutorial/tutorial_general_201.png
[202]: ./media/adobe-echosign-tutorial/tutorial_general_202.png
[203]: ./media/adobe-echosign-tutorial/tutorial_general_203.png
