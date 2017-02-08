---
title: "Tutorial: Integração do Azure Active Directory com o Allocadia | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Allocadia."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: c415fc55-6dc1-49f2-a8a2-2fc6e3790d65
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 69473976eb0a9b6a0e9aa929581c0a7cf0c8d188
ms.openlocfilehash: e9d59c593c3637d72611516fd8a6b03f3f5f917b


---
# <a name="tutorial-azure-active-directory-integration-with-allocadia"></a>Tutorial: Integração do Azure Active Directory ao Allocadia
Neste tutorial, você aprenderá a integrar o Allocadia ao Azure AD (Azure Active Directory).

A integração do Allocadia ao Azure AD oferece os seguintes benefícios:

* É possível controlar no Azure AD quem tem acesso ao Allocadia
* Você pode permitir que seus usuários façam logon automaticamente no Allocadia (logon único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao Allocadia, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada para logon único do Allocadia

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Allocadia da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-allocadia-from-the-gallery"></a>Adicionar o Allocadia da galeria
Para configurar a integração do Allocadia ao Azure AD, você precisará adicionar o Allocadia da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Allocadia da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **Allocadia**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_01.png)

7. No painel de resultados, selecione **Allocadia**, em seguida, clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_06.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Allocadia, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Allocadia é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Allocadia.
Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Username** no Allocadia.

Para configurar e testar o logon único do Azure AD com o Allocadia, você precisará concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação um usuário de teste do Allocadia](#creating-an-allocadia-test-user)** : para ter um equivalente de Brenda Fernandes no Allocadia que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do AD do Azure
Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o logon único em seu aplicativo Allocadia.

O aplicativo Allocadia espera que as declarações SAML estejam em um formato específico. Configure as seguintes declarações para o aplicativo. Você pode gerenciar o valor dos atributos na guia **"Atributo"** do aplicativo. A captura de tela a seguir mostra um exemplo disso. 

![Configurar o logon único](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_07.png) 

**Para configurar o logon único do Azure AD com o Hightail, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração de aplicativos **Allocadia**, no menu na parte superior, clique em **Atributos**.
   
    ![Configurar Logon Único](./media/active-directory-saas-allocadia-tutorial/tutorial_general_80.png) 

2. Na caixa de diálogo **Atributos de token SAML** , para cada linha mostrada na tabela a seguir, execute as seguintes etapas:
   
    | Nome do atributo | Valor do atributo |
    | --- | --- |
    | nome |user.givenname |
    | sobrenome |user.surname |
    | email |user.mail |

    a. Clique em **adicionar atributo de usuário** para abrir a caixa de diálogo **Adicionar Atributo de Usuário**.

    ![Configurar Logon Único](./media/active-directory-saas-allocadia-tutorial/tutorial_general_81.png) 

    b. Na caixa de texto **Nome do Atributo** , digite o nome do atributo mostrado nessa linha.

    c. Na lista **Valor do Atributo** , selecione o valor do atributo mostrado nessa linha.

    d. Clique em **Concluído**.    


1. No menu na parte superior, clique em **Início Rápido**.
   
    ![Configurar Logon Único](./media/active-directory-saas-allocadia-tutorial/tutorial_general_83.png)  

2. Na página **Como você gostaria que os usuários fizessem logon no Allocadia**, selecione **Logon Único do Azure AD**, em seguida, clique em **Próximo**.
   
    ![Configurar Logon Único](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_03.png) 

3. Na página do diálogo **Definir Configurações do Aplicativo** , realize as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_04.png) 
   
    a. Na caixa IDENTIFICADOR, digite a URL no seguinte padrão: para o ambiente de teste, use a URL como **"https://na2standby.allocadia.com"** e para o ambiente de produção, use **"https://na2.allocadia.com"**
   
    b. Na URL de resposta, digite a URL no seguinte padrão: para o ambiente de teste, use o padrão de URL como  **"https://na2standby.allocadia.com/allocadia/saml/SSO"** e para o ambiente de produção, use **"https://na2.allocadia.com/allocadia/saml/SSO"**

4. Na página **Configure single sign-on at Allocadia (Configurar logon único no Allocadia)** , execute as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_05.png) 
   
    a. Clique em **Baixar metadados**e salve o arquivo no computador.
   
    b. Clique em **Avançar**.

5. Para configurar o SSO para seu aplicativo, entre em contato com a equipe de [Suporte do Allocadia](mailTo:support@allocadia.com) e ela auxiliará na configuração do SSO. Observe que você precisa enviar o email e anexar o arquivo de metadados baixado para configurar o SSO no lado do Allocadia.
   
    > [!NOTE]
    > Verifique se a equipe Allocadia definiu o valor do Identificador no ambiente de teste como **"https://na2standby.allocadia.com"** e para o ambiente de produção, deve ser: **"https://na2.allocadia.com"**
    > 
    > 

6. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]

7. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.
Na lista de usuários, selecione **Brenda Fernandes**.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-allocadia-tutorial/create_aaduser_09.png) 

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-allocadia-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-allocadia-tutorial/create_aaduser_04.png) 

5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-allocadia-tutorial/create_aaduser_05.png) 
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Próximo**.

6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-allocadia-tutorial/create_aaduser_06.png) 
   
    a. Na caixa de texto **Nome**, digite **Brenda**.  
   
    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
    d. Na lista **Função**, selecione **Usuário**.
   
    e. Clique em **Próximo**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-allocadia-tutorial/create_aaduser_07.png) 

8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-allocadia-tutorial/create_aaduser_08.png) 
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-an-allocadia-test-user"></a>Criação um usuário de teste do Allocadia
Nesta seção, você criará um usuário chamado Brenda Fernandes no Allocadia. O aplicativo Allocadia dá suporte ao provisionamento de usuário just in time. Se você tiver configurado as declarações conforme mencionado acima na seção **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** , ele provisionará os usuários no aplicativo. 

> [!NOTE]
> Se você precisar criar um usuário ou um lote de usuários manualmente, entre em contato com a equipe de suporte do Allocadia.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Allocadia.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Allocadia, execute as seguintes etapas:**

1. No portal clássico, para abrir a exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Allocadia**.
   
    ![Configurar o logon único](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_50.png) 

3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 

4. Na lista de usuários, selecione **Brenda Fernandes**.

5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.
Ao clicar no bloco Allocadia no Painel de Acesso, você deverá ser conectado automaticamente a seu aplicativo Allocadia.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO5-->


