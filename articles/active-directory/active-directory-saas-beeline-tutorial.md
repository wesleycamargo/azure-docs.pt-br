---
title: "Tutorial: Integração do Azure Active Directory ao Beeline | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Beeline."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 0726859d-1dac-44a0-810b-da56d89039ee
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 49a8d812924a421d131497b9f275525202f94a43
ms.openlocfilehash: ef40a9ddd33434299e9a603751fa75ef6d61742b


---
# <a name="tutorial-azure-active-directory-integration-with-beeline"></a>Tutorial: Integração do Azure Active Directory ao Beeline
Neste tutorial, você aprenderá a integrar o Beeline ao Azure AD (Azure Active Directory).

A integração do Beeline ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Beeline
* Você pode permitir que os usuários façam logon automaticamente no Beeline (Logon Único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao Beeline, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada para logon único do Beeline

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Beeline da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-beeline-from-the-gallery"></a>Adição do Beeline da galeria
Para configurar a integração do Beeline ao Azure AD, você precisará adicionar o Beeline à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Beeline por meio da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Beeline**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_01.png)
7. No painel de resultados, selecione **Beeline** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_06.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Beeline, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Beeline é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Beeline.
Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD como o valor de **Nome de usuário** no Beeline.

Para configurar e testar o logon único do Azure AD com o Beeline, você precisará concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do Beeline](#creating-an-beeline-test-user)** : para ter um equivalente de Brenda Fernandes no Beeline que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD
Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o logon único em seu aplicativo Beeline.

O aplicativo Beeline espera que as declarações SAML estejam em um formato específico. Trabalhe com a equipe do Beeline primeiro para verificar o identificador de usuário correto que será mapeado para o aplicativo. Também siga as diretrizes da equipe do Beeline sobre o atributo que deseja usar para mapeamento. A Microsoft recomenda usar o atributo **"NameIdentifier"** como identificador de usuário. Você pode gerenciar o valor desse atributo na guia **"Atributo"** do aplicativo. A captura de tela a seguir mostra um exemplo disso. Aqui mapeamos a declaração do nameidentifier com o atributo **userprincipalname** , que fornece uma ID de usuário exclusiva, que será enviada para o aplicativo Beeline em cada resposta SAML com êxito.

![Configurar Logon Único](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_07.png) 

**Para configurar o logon único do Azure AD com o Beeline, execute as seguintes etapas:**

1. No portal clássico, na página de integração de aplicativos do **Beeline**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
     ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários façam logon no Beeline**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_03.png) 
3. Na página do diálogo **Definir Configurações do Aplicativo** , realize as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_04.png) 

    a. Na caixa de texto **Identificador**, digite a URL usada pelos usuários para entrar em seu aplicativo Beeline usando o seguinte padrão: `https://projects.beeline.net/<instance name>`

    b. Em URL de Resposta, digite a URL no seguinte padrão: `https://projects.beeline.net/<instance name>/SSO_External.ashx` ou `https://projects.beeline.net/<company name>/SSO_External.ashx`


1. Na página **Configure single sign-on at Beeline (Configurar logon único no Beeline)** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_05.png) 
   
    a. Clique em **Baixar metadados**e salve o arquivo no computador.
   
    b. Clique em **Avançar**.
2. Para configurar o SSO para seu aplicativo, entre em contato com a equipe de suporte do Beeline e ela auxiliará na configuração do SSO. Observe que você precisa enviar o email e anexar o arquivo de metadados baixado e também fornecer a ID da entidade e a URL de serviço de saída única.
3. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
4. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-beeline-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-beeline-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-beeline-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-beeline-tutorial/create_aaduser_05.png) 
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-beeline-tutorial/create_aaduser_06.png) 
   
    a. Na caixa de texto **Nome**, digite **Brenda**.  
   
    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
    d. Na lista **Função**, selecione **Usuário**.
   
    e. Clique em **Próximo**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-beeline-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-beeline-tutorial/create_aaduser_08.png) 
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-an-beeline-test-user"></a>Criação de um usuário de teste do Beeline
Nesta seção, você criará um usuário chamado Brenda Fernandes no Beeline. O aplicativo Beeline precisa que todos os usuários sejam provisionados no aplicativo antes de fazer o Logon Único. Portanto, trabalhe com o associado de atendimento ao cliente do Beeline para provisionar todos esses usuários no aplicativo. 

> [!NOTE]
> Se você precisar criar um usuário ou um lote de usuários manualmente, entre em contato com a equipe de suporte do Beeline.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Beeline.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Beeline, execute as seguintes etapas:**

1. No portal clássico, para abrir a exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, escolha **Beeline**.
   
    ![Configurar Logon Único](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.
Ao clicar no bloco Beeline no Painel de Acesso, você deverá ser conectado automaticamente a seu aplicativo Beeline.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO5-->


