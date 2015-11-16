<properties
	pageTitle="Tutorial: Integração do Active Directory do Azure com o Kiteworks | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Active Directory do Azure e o Kiteworks."
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
	ms.date="11/05/2015"
	ms.author="markusvi"/>


# Tutorial: Integração do Active Directory do Azure com o Kiteworks

O objetivo deste tutorial é mostrar a você como integrar o Kiteworks ao Active Directory do Azure (AD do Azure).<br>A integração do Kiteworks ao AD do Azure oferece os seguintes benefícios:

- Você pode controlar no AD do Azure quem tem acesso ao Kiteworks 
- Você pode permitir que seus usuários façam logon automaticamente no Kiteworks (logon único) com suas contas do AD do Azure
- Você pode gerenciar suas contas em um local central – o Portal do Active Directory do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos 

Para configurar a integração do AD do Azure ao Kiteworks, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Kiteworks


> [AZURE.NOTE]Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. <br> O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Kiteworks da galeria 
2. Configurar e testar o logon único do AD do Azure


## Adicionando o Kiteworks da galeria
Para configurar a integração do Kiteworks ao AD do Azure, você precisará adicionar o Kiteworks da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Kiteworks da galeria, execute as seguintes etapas:**

1. No **Portal de Gerenciamento do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a exibição dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu superior.<br><br> ![Aplicativos][2]<br>
4. Clique em **Adicionar** na parte inferior da página.<br><br> ![Aplicativos][3]<br>
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.<br><br> ![Aplicativos][4]<br>
6. Na caixa de pesquisa, digite **Kiteworks**.<br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_01.png)<br>
7. No painel de resultados, selecione **Kiteworks** e clique em **Concluir** para adicionar o aplicativo. <br><br> ![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_02.png)<br>

##  Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar logon único do AD do Azure com o Kiteworks com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o AD do Azure precisa saber qual usuário do Kiteworks é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado do Kiteworks.<br> Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no AD do Azure ao valor do **Nome de Usuário** no Kiteworks.
 
Para configurar e testar o logon único do AD do Azure com o Kiteworks, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criação de um usuário de teste do Kiteworks](#creating-a-kiteworks-test-user)**: para ter um equivalente de Brenda Fernandes no Kiteworks que esteja vinculado à representação dela no AD do Azure.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)**: para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do AD do Azure no portal do AD do Azure e configurar o logon único em seu aplicativo Kiteworks. Como parte deste procedimento, é necessário criar um arquivo de certificado codificado em base 64. Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

Para configurar o logon único para o Kiteworks, você precisa de um domínio registrado. Se não tiver um domínio registrado, entre em contato com a equipe de suporte do Kiteworks pelo email [support@flatterfiles.com](mailto:support@flatterfiles.com).



**Para configurar o logon único do AD do Azure com o Kiteworks, execute as seguintes etapas:**

1. No portal do AD do Azure, na página de integração do aplicativo **Kiteworks**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**. <br><br> ![Configurar o logon único][6] <br>

2. Na página **Como você deseja que os usuários façam logon no Kiteworks**, selecione **Logon único do AD do Azure** e clique em **Avançar**. <br><br> ![Configurar o logon único](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_03.png) <br>

3. Na página da caixa de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas: <br><br>![Configurar o logon único](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_04.png) <br>


    a. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para entrar no aplicativo Kiteworks (por exemplo: **https://fabrikam.kiteworks.com/*)).

    b. Clique em **Próximo**.
 
 
4. Na página **Configurar logon único no Kiteworks**, realize as seguintes etapas: <br><br>![Configurar o logon único](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_05.png) <br>

    a. Clique em **Baixar certificado** e salve o arquivo em seu computador.

    b. Clique em **Próximo**.


1. Faça logon no site da sua empresa do Kiteworks como um administrador.

1. Na barra de ferramentas na parte superior, clique em **Configurações**. <br><br> ![Configurar o logon único](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_06.png) <br>


1. Na seção **Autenticação e Autorização** seção, clique em **Configuração do SSO**. <br><br> ![Configurar o logon único](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_07.png) <br>


1. Na página Instalação do SSO, execute as seguintes etapas: <br><br>![Configurar o logon único](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_09.png) <br>

    a. Selecione **Autenticar via SSO**.

    b. Selecione **Iniciar AuthnRequest**.

    c. No portal do Azure, na página do diálogo **Configurar logon único no Kiteworks**, copie o valor da **ID de Entidade** e cole-o na caixa de texto **ID de Entidade do IDP**.

    d. No portal do Azure, na página do diálogo **Configurar logon único no Kiteworks**, copie o valor da **URL do Serviço de Logon Único** e cole-o na caixa de texto **URL do Serviço de Logon Único**.

    e. No portal do Azure, na página do diálogo **Configurar logon único no Kiteworks**, copie o valor da **URL do Serviço de Logout Único** e cole-o na caixa de texto **URL do Serviço de Logout Único**.

    f. Abra seu certificado baixado no Bloco de Notas, copie o conteúdo e cole-o na caixa de texto **Certificado de Chave Pública RSA**.

    g. Clique em **Salvar**.


6. No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**. <br><br>![Logon único do AD do Azure][10]<br>

7. Na página **Confirmação de logon único**, clique em **Concluir**. <br><br>![Logon único do AD do Azure][11]




### Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar no portal do Azure um usuário de teste chamado Brenda Fernandes.<br> Na lista Usuários, selecione **Brenda Fernandes**.<br><br>![Criar um usuário do AD do Azure][20]<br>

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal de Gerenciamento do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_09.png) <br> 

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**. <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_03.png) <br>
 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**. <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_04.png) <br>

5. Na página da caixa de diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas: <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_05.png) <br>

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na caixa de texto **Nome de Usuário**, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na página da caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas: <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_06.png) <br>
 
    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**. e. Clique em **Próximo**.

7. Na página da caixa de diálogo **Obter senha temporária**, clique em **criar**. <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_07.png) <br>
 
8. Na página da caixa de diálogo **Obter senha temporária**, execute as seguintes etapas: <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kiteworks-tutorial/create_aaduser_08.png) <br>
  
    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.

  
 
### Criar um usuário de teste do Kiteworks

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Kiteworks. O Kiteworks oferece suporte ao provisionamento Just-In-Time, que é habilitado por padrão.

Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o Kitewors se ele ainda não existir.

> [AZURE.NOTE]Se você precisar criar um usuário manualmente, entre em contato com a equipe de suporte do Kiteworks.


### Atribuição do usuário de teste do AD do Azure

O objetivo desta seção é habilitar Brenda Fernandes a usar o logon único do Azure, concedendo a ela acesso ao Kiteworks. <br><br>![Atribuir usuário][200] <br>

**Para atribuir Brenda Fernandes ao Kiteworks, execute as seguintes etapas:**

1. No portal do Azure, para abrir a exibição dos aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior. <br><br>![Atribuir usuário][201] <br>

2. Na lista de aplicativos, selecione **Kiteworks**. <br><br>![Configurar o logon único](./media/active-directory-saas-kiteworks-tutorial/tutorial_kiteworks_50.png) <br>

1. No menu na parte superior, clique em **Usuários**. <br><br>![Atribuir usuário][203] <br>

1. Na lista Usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**. <br><br>![Atribuir usuário][205]



### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do AD do Azure usando o Painel de Acesso.<br> Quando você clica no bloco Kiteworks no Painel de Acesso, deve fazer logon automaticamente no seu aplicativo Kiteworks.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-kiteworks-tutorial/tutorial_general_205.png

<!---HONumber=Nov15_HO2-->