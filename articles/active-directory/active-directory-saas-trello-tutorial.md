---
title: 'Tutorial: integração do Azure Active Directory ao Trello | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Trello.
services: active-directory
documentationcenter: ''
author: jeevansd
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: jeedes

---
# <a name="tutorial:-azure-active-directory-integration-with-trello"></a>Tutorial: integração do Azure Active Directory ao Trello
Neste tutorial, você aprenderá a integrar o Trello ao Azure AD (Azure Active Directory).

A integração do Trello ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Trello
* Você pode permitir que usuários façam logon automaticamente no Trello (Logon Único) com as respectivas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao Trello, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada para logon único do **Trello**

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Trello da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-trello-from-the-gallery"></a>Adicionando o Trello da galeria
Para configurar a integração do Trello ao Azure AD, você precisará adicionar o Trello da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Trello da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Trello**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-trello-tutorial/tutorial_trello_01.png)
7. No painel de resultados, selecione **Trello** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-trello-tutorial/tutorial_trello_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Trello, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Trello é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Trello.
Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD como sendo o valor de **Nome de Usuário** no Trello. Para configurar e testar o logon único do Azure AD com o Trello, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do Trello](#creating-a-the-funding-portal-test-user)** – Para ter um equivalente de Brenda Fernandes no Trello que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD
O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure e configurar o logon único em seu aplicativo Trello.

O aplicativo Trello espera que as asserções SAML contenham atributos específicos. Configure as atribuições a seguir para o aplicativo. Você pode gerenciar os valores dos atributos na guia **"Atributos"** do aplicativo. A captura de tela a seguir mostra um exemplo disso.

![Configurar Logon Único](./media/active-directory-saas-trello-tutorial/tutorial_trello_03.png) 

**Para configurar o logon único do Azure AD com o Trello, execute as seguintes etapas:**

1. No Portal clássico do Azure, na página de integração do aplicativo **Trello**, no menu superior, clique em **Atributos**.
   
    ![Configurar Logon Único][5]
2. Na caixa de diálogo **Atributos de token SAML** , para cada linha mostrada na tabela a seguir, execute as seguintes etapas:

      | Nome do atributo | Valor do atributo |
      | --- | --- |    
      | User.Email | user.mail |
      | User.FirstName | user.givenname |
      | User.LastName | user.surname |

    a. Clique em **adicionar atributo de usuário** para abrir a caixa de diálogo **Adicionar Atributo de Usuário**.

    ![Configurar Logon Único](./media/active-directory-saas-trello-tutorial/tutorial_trello_05.png)

    b. Na caixa de texto **Nome do Atributo** , digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor do Atributo** , selecione o valor mostrado para essa linha.

    d. Clique em **Concluído**. Clique em **Aplicar Alterações** na parte inferior da página.

1. No menu na parte superior, clique em **Início Rápido**.
   
    ![Configurar Logon Único][6]
2. No portal clássico, na página de integração de aplicativos do **Trello**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar o logon único][7] 
3. Na página **Como você deseja que os usuários façam logon no Trello**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-trello-tutorial/tutorial_trello_06.png)
4. Na página de diálogo **Definir Configurações de Aplicativo**, se você quer configurar o aplicativo em **modo iniciado pelo IDP**, execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-trello-tutorial/tutorial_trello_07.png)

    a. Na caixa de texto URL de Resposta, digite uma URL no seguinte padrão: `https://trello.com/auth/saml/consume/<enterprise>`.

    b. Clique em **Próximo**.

> [!NOTE]
> Você deve obter o campo de dados dinâmico **\<empresa\>** do Trello. Se você não tiver o valor do campo de dados dinâmico, entre em contato com equipe de suporte do Trello em <mailto:support@trello.com> a fim de obtê-lo para sua empresa.
> 
> 

1. Se quiser configurar o aplicativo no **modo iniciado pelo SP**, na página de diálogo **Definir Configurações do Aplicativo**, clique em **"Mostrar configurações avançadas (opcional)"**, insira a **URL de Logon**:
   
    ![Configurar o logon único](./media/active-directory-saas-trello-tutorial/tutorial_trello_08.png)
   
    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://trello.com/auth/saml/consume/<enterprise>`
   
    b. Clique em **Próximo**
2. Na página **Configurar logon único no Trello**, clique em **Baixar certificado** e salve o arquivo no computador.
   
    ![Configurar Logon Único](./media/active-directory-saas-trello-tutorial/tutorial_trello_09.png)
3. Para obter o SSO configurado para o seu aplicativo, vá para a página [Configuração de SSO empresarial do Trello](https://trello.com/sso-configuration) a fim de enviar a URL de Logon Único para a equipe do Trello e anexar o certificado baixado.
4. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
5. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-trello-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-trello-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-trello-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-trello-tutorial/create_aaduser_05.png) 
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-trello-tutorial/create_aaduser_06.png) 
   
   a. Na caixa de texto **Nome**, digite **Brenda**.  
   
   b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
   c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
   d. Na lista **Função**, selecione **Usuário**.
   
   e. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-trello-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-trello-tutorial/create_aaduser_08.png) 
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-a-trello-test-user"></a>Criando um usuário de teste do Trello
Nesta seção, você cria um usuário chamado Brenda Fernandes no Trello. Nesta seção, você cria um usuário chamado Brenda Fernandes no Trello. O Trello dá suporte a provisionamento just-in-time e uma nova conta é criada na primeira vez em que você entra pelo Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Trello.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Trello, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione **Trello**.
   
    ![Configurar Logon Único](./media/active-directory-saas-trello-tutorial/tutorial_trello_10.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista Todos os Usuários, escolha **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Trello no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Trello.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-trello-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-trello-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-trello-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-trello-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-trello-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-trello-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-trello-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-trello-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-trello-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-trello-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-trello-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-trello-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-trello-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-trello-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-trello-tutorial/tutorial_general_205.png



<!--HONumber=Oct16_HO2-->


