<properties
	pageTitle="Tutorial: integração do Active Directory do Azure com o @Task | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Active Directory do Azure e o @Task."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/12/2015"
	ms.author="markusvi"/>


# Tutorial: integração do Active Directory do Azure com o @Task

O objetivo deste tutorial é mostrar como integrar o @Task com o AD do Azure (Active Directory do Azure). <br>A integração do @Task com o AD do Azure oferece os seguintes benefícios:

- Você pode controlar no AD do Azure quem tem acesso ao @Task
- Você pode habilitar seus usuários a fazerem logon automaticamente no @Task (logon único) com suas contas do AD do Azure
- Você pode gerenciar suas contas em um local central – o Portal do Active Directory do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos 

Para configurar a integração do AD do Azure com o @Task, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do @Task


> [AZURE.NOTE]Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do AD do Azure em um ambiente de teste. <br> O cenário descrito neste tutorial consiste em três blocos de construção principais:

1. Adicionando o @Task por meio da galeria 
2. Configurar e testar o logon único do AD do Azure


## Adicionando o @Task por meio da galeria
Para configurar a integração do @Task ao AD do Azure, você precisa adicionar o @Task por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o @Task por meio da galeria, realize as seguintes etapas:**

1. No **Portal de Gerenciamento do Azure**, no painel de navegação à esquerda, clique em **Active Directory.** <br><br> ![Active Directory][1] <br>

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior. <br><br> ![Aplicativos][2] <br>

4. Clique em **Adicionar** na parte inferior da página. <br><br> ![Aplicativos][3] <br>

5. No diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**. <br><br> ![Aplicativos][4] <br>

6. Na caixa de pesquisa, digite **@Task**. <br><br>![Aplicativos][5] <br>

7. No painel de resultados, selecione **@Task** e clique em **Concluir** para adicionar o aplicativo. <br><br>![Aplicativos][30] <br>



##  Configurar e testar o logon único do AD do Azure

O objetivo desta seção é mostrar como configurar e testar o logon único do AD do Azure com o @Task, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o AD do Azure precisa saber qual usuário do @Task é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado do @Task.<br> Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no AD do Azure como sendo o valor de **nome de usuário** no @Task.
 
Para configurar e testar o logon único do AD do Azure com o @Task, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criando um usuário de teste do @Task](#creating-a-halogen-software-test-user)** - para ter um equivalente de Brenda Fernandes no @Task que esteja vinculado à representação dela no AD do Azure.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)**: para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do AD do Azure no portal do AD do Azure e configurar o logon único em seu aplicativo do @Task.<br>

**Para configurar o logon único do AD do Azure com o @Task, realize as seguintes etapas:**

1. No portal do AD do Azure, na página de integração de aplicativos **@Task**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**. <br><br> ![Configurar o logon único][6] <br>

2. Na página **Como você deseja que os usuários façam logon no @Task**, selecione **Logon único do AD do Azure** e clique em **Avançar**. <br><br> ![Logon único do AD do Azure][7] <br>

3. Na página do diálogo **Definir Configurações de Aplicativo**, realize as seguintes etapas: <br><br>![Definir configurações de aplicativo][8] <br>
 
     a. Na caixa de texto **URL de Resposta**, digite a URL usada pelos usuários para entrar no site do @Task (por exemplo: *https://<Tenant name>.attask-ondemand.com*).

     b. Clique em **Próximo**.

4. Na página **Configurar logon único no @Task**, clique em **Baixar metadados** e salve o arquivo de metadados localmente no computador e clique em **Avançar**. <br><br>![O que é o Azure AD Connect][9] <br>



1. Faça logon no site da empresa @Task como administrador.

2. Vá para **Configuração de Logon Único**.


1. Na caixa de diálogo **Logon Único**, realize as seguintes etapas <br><br>![Configurar o logon único][23]<br>

    a. Como **Tipo**, selecione **SAML 2.0**.

    b. Selecione **ID do Provedor de Serviços**.

    c. No portal do Azure, copie a **URL de Logon Remoto** e cole-a na caixa de texto **URL do Portal de Logon**.

    d. No portal do Azure, copie a **URL do Serviço de Logout Único** e cole-a na caixa de texto **URL de Logout**.

    e. No portal do Azure, copie **Alterar a URL da Senha** e cole-a na caixa de texto **Alterar a URL da Senha**.

    e. Clique em **Salvar**.

6. No portal do AD do Azure, selecione a confirmação de configuração de logon único e clique em **Avançar**. <br><br>![O que é o Azure AD Connect][10]<br>

7. Na página **Confirmação de logon único**, clique em **Concluir**. <br><br>![O que é o Azure AD Connect][11]




### Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no portal do Azure chamado Brenda Fernandes.<br> Na lista Usuários, selecione **Brenda Fernandes**.<br><br>![Criar um usuário do AD do Azure][20]<br>

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal de Gerenciamento do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.<br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-attask-tutorial/create_aaduser_02.png) 

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.<br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-attask-tutorial/create_aaduser_03.png)
 
4. Para abrir o diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**. <br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-attask-tutorial/create_aaduser_04.png)

5. Na página do diálogo **Conte-nos sobre este usuário**, realize as seguintes etapas: <br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-attask-tutorial/create_aaduser_05.png)

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome de Usuário, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na página do diálogo **Perfil do Usuário**, realize as seguintes etapas: <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-attask-tutorial/create_aaduser_06.png) <br>
 
    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**. e. Clique em **Próximo**.

7. Na página do diálogo **Obter senha temporária**, clique em **criar**. <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-attask-tutorial/create_aaduser_07.png) <br>
 
8. Na página do diálogo **Obter senha temporária**, realize as seguintes etapas: <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-attask-tutorial/create_aaduser_08.png) <br>
  
    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.

  
 
### Criando um usuário de teste do @Task

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no @Task.


**Para criar um usuário chamado Brenda Fernandes no @Task, realize as seguintes etapas:**

1. Faça logon no site da empresa @Task como administrador.

2. No menu na parte superior, clique em **Pessoas**.

3. Clique em **Nova Pessoa**.

4. No diálogo Nova Pessoa, realize as seguintes etapas: <br><br>![Criar um usuário de teste do @Task][21] <br>

    a. Na caixa de texto **Nome**, digite “Brenda”.

    b. Na caixa de texto **Sobrenome**, digite “Fernandes”.

    c. Na caixa de texto **Endereço de Email**, digite o endereço de email de Brenda Fernandes no Active Directory do Azure.

    d. Clique em **Adicionar Pessoa**.




### Atribuição do usuário de teste do AD do Azure

O objetivo desta seção é habilitar Brenda Fernandes a usar o logon único do Azure, concedendo a ela acesso ao @Task. <br><br>![Atribuir usuário][200] <br>

**Para atribuir Brenda Fernandes ao @Task, realize as seguintes etapas:**

1. No portal do Azure, para abrir a exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior. <br><br>![Atribuir usuário][201] <br>

2. Na lista de aplicativos, selecione **@Task**. <br><br>![Atribuir usuário][202] <br>

1. No menu na parte superior, clique em **Usuários**. <br><br>![Atribuir usuário][203] <br>

1. Na lista Usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**. <br><br>![Atribuir usuário][205]



### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do AD do Azure usando o Painel de Acesso.<br> Quando você clica no bloco @Task no Painel de Acesso, você deve ser conectado automaticamente ao seu aplicativo @Task.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-attask-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-attask-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-attask-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-attask-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_01.png
[30]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_02.png


[6]: ./media/active-directory-saas-attask-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_03.png
[8]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_04.png
[9]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_05.png
[10]: ./media/active-directory-saas-attask-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-attask-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-attask-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_08.png


[23]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_06.png

[200]: ./media/active-directory-saas-attask-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-attask-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_09.png
[203]: ./media/active-directory-saas-attask-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-attask-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-attask-tutorial/tutorial_general_205.png

<!---HONumber=Oct15_HO4-->