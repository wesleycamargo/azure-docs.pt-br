---
title: "Tutorial: integração do Azure Active Directory com o ADP GlobalView | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o ADP GlobalView."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: ffb6464f-714d-41a9-869a-2b7e5ae9f125
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 748704129f417d28c838e71434dfeab376ea0d8e


---
# <a name="tutorial-azure-active-directory-integration-with-adp-globalview"></a>Tutorial: Integração do Azure Active Directory ao ADP GlobalView
O objetivo deste tutorial é mostrar como integrar o ADP GlobalView ao Azure AD (Azure Active Directory).  
A integração do ADP GlobalView ao Azure AD oferece os seguintes benefícios:

* No AD do Azure, você poderá controlar quem tem acesso ao ADP GlobalView
* Você pode permitir que seus usuários façam logon automaticamente no ADP GlobalView (logon único) com suas contas do AD do Azure
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do AD do Azure com o ADP GlobalView, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura do ADP GlobalView com logon único habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste.  
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do ADP GlobalView da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-adp-globalview-from-the-gallery"></a>Adição do ADP GlobalView da galeria
Para configurar a integração do ADP GlobalView com o AD do Azure, você precisará adicionar o ADP GlobalView à sua lista de aplicativos SaaS gerenciados da galeria.

**Para adicionar o ADP GlobalView da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **ADP GlobalView**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_01.png)
7. No painel de resultados, escolha **ADP GlobalView** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_06.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do AD do Azure com o ADP GlobalView, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o AD do Azure precisa saber qual usuário do ADP GlobalView é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado no ADP GlobalView.  
Essa relação de vinculação é estabelecida por meio da atribuição do valor de **nome de usuário** no Azure AD como o valor de **Nome de Usuário** no ADP GlobalView.

Para configurar e testar o logon único do AD do Azure com o ADP GlobalView, você precisará concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste do ADP GlobalView](#creating-a-adp-globalview-test-user)** : para ter um equivalente de Brenda Fernandes no ADP GlobalView que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do AD do Azure
O objetivo desta seção é habilitar o logon único do AD do Azure no portal clássico do Azure e configurar o logon único em seu aplicativo do ADP GlobalView.

Seu aplicativo ADP GlobalView espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos de token SAML. A captura de tela a seguir mostra um exemplo disso. O nome da declaração sempre será **"PersonImmutableID"** e o valor que mapeamos para ExtensionAttribute2 que contém o EmployeeID do usuário. Aqui, o mapeamento do usuário do AD do Azure para o ADP GlobalView será feito com o EmployeeID, mas isso pode ser mapeado para um valor diferente também baseado em suas configurações do aplicativo. Portanto, trabalhe com a equipe do ADP GlobalView primeiro para usar o identificador correto de um usuário e mapear esse valor com a declaração **"PersonImmutableID"** . Você também pode mapear a declaração de Email e ID de usuário, conforme mostra na imagem.

![Configurar Logon Único](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_02.png) 

Antes de configurar a declaração SAML, você precisará entrar em contato com a equipe de suporte do ADP GlobalView e solicitar o valor do atributo de identificador exclusivo para seu locatário. Você precisa desse valor para configurar a declaração personalizada para seu aplicativo.

**Para configurar o logon único do AD do Azure com o ADP GlobalView, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração do aplicativo **ADP GlobalView**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar o logon único][6] 
2. Na página **Como você deseja que os usuários façam logon no ADP GlobalView**, selecione **Logon único do Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_03.png) 
3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_04.png) 

    a. Na caixa de texto **Identificador**, digite a URL usada para identificar o aplicativo ADP GlobalView usando um dos seguintes padrões: `https://<server name>.globalview.adp.com/federate2` ou `https://<server name>.globalview.adp.com/federate`


    b. Na caixa de texto **URL de Resposta**, digite a URL usada pelo Azure AD para enviar a resposta para o aplicativo ADP GlobalView usando um dos seguintes padrões: `https://<server name>.globalview.adp.com/federate2/sp/ACS.saml2` ou `https://<server name>.globalview.adp.com/federate/sp/ACS.saml2`

    c. Clique em **Avançar**.


1. Na página **Configurar logon único no ADP GlobalView** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_05.png) 
   
    a. Clique em **Baixar certificado**e salve o arquivo em seu computador.
   
    b. Clique em **Avançar**.
2. Para que o SSO seja configurado para seu aplicativo, entre em contato com a equipe de suporte do ADP GlobalView e forneça o seguinte: 
   
   * O **certificado** baixado
   * **ID da Entidade**
   * **URL DO SSO DE SAML**
   * **URL do Serviço de Logoff Único**

    > [AZURE.NOTE] Depois que a equipe do **ADP GlobalView** configurar a instância, obtenha o valor **RelayState** deles. Siga as etapas mencionadas abaixo para configurá-lo. Após essa configuração, você poderá testar a integração. Portanto, observe que essa configuração é importante para que a integração com o aplicativo funcione

1. Para configurar o valor RelayState no Azure AD, execute as seguintes etapas: 
   
    a. Entre no [Portal de Gerenciamento do Azure](https://portal.azure.com) como um administrador.
   
    b. No painel de navegação esquerdo, clique em **Mais Serviços**. 
   
    ![Configurar Logon Único](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_07.png)
   
    c. Na caixa de texto **Pesquisar**, digite **Azure Active Directory** e clique no link relacionado.
   
    ![Configurar Logon Único](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_08.png)
   
    d. Clique em **Aplicativos Empresariais**.
   
    ![Configurar Logon Único](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_09.png)
   
    e. Na seção **Gerenciar**, clique em **Todos os Aplicativos**.
   
    ![Configurar Logon Único](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_10.png)
   
    f. Na caixa de texto **Pesquisar**, digite **ADP eTime** e clique no link relacionado. 
   
    ![Configurar Logon Único](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_11.png)
   
    g. Na seção **Gerenciar**, clique em **Logon único**.
   
    ![Configurar Logon Único](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_12.png)
   
    h. Selecione **Mostrar configurações de URL avançadas**.
   
    ![Configurar o logon único](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_13.png)
   
    i. Na caixa de texto **Estado de Retransmissão**, digite um valor usando os seguintes padrões:
   
    `https://<server name>.globalview.adp.com/gvolution/session/<instance name>/sso` 
   
    ![Configurar o logon único](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_14.png)
   
    j. Salve as configurações.
2. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
3. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal clássico do Azure chamado Brenda Fernandes.  
Na lista de usuários, selecione **Brenda Fernandes**.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_05.png) 
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_06.png) 
   
   a. Na caixa de texto **Nome**, digite **Brenda**.  
   
   b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
   c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
   d. Na lista **Função**, selecione **Usuário**.
   
   e. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_08.png) 
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-a-adp-globalview-test-user"></a>Criar um usuário de teste do ADP GlobalView
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no ADP GlobalView. Trabalhe com a equipe de suporte do ADP GlobalView para adicionar os usuários na conta do ADP GlobalView. 

> [!NOTE]
> Se precisar criar um usuário manualmente, entre em contato com a equipe de suporte do ADP GlobalView.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao ADP GlobalView.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao ADP GlobalView, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, escolha **ADP GlobalView**.
   
    ![Configurar Logon Único](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.  
Ao clicar no bloco do ADP GlobalView no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo do ADP GlobalView.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO5-->


