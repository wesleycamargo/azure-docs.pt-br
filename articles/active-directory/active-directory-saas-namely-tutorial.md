<properties
	pageTitle="Tutorial: Integração do Active Directory do Azure ao Namely | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Active Directory do Azure e o Namely."
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


# Tutorial: Integração do Active Directory do Azure com o Namely

O objetivo deste tutorial é mostrar como integrar o Namely ao Active Directory do Azure (AD do Azure). <br>A integração do Namely ao AD do Azure oferece os seguintes benefícios:

- No AD do Azure, você pode controlar quem tem acesso ao Namely 
- Você pode permitir que seus usuários façam logon automaticamente no Namely (Logon único) com as contas do AD do Azure
- Você pode gerenciar suas contas em um local central – o Portal do Active Directory do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos 

Para configurar a integração do AD do Azure ao Namely, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Namely habilitada para logon único


> [AZURE.NOTE]Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. <br> O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Namely da galeria 
2. Configurar e testar o logon único do AD do Azure


## Adicionando o Namely da galeria
Para configurar a integração do Namely ao AD do Azure, você precisa adicionar o do Namely da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o do Namely da galeria, execute as seguintes etapas:**

1. No **Portal de Gerenciamento do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a exibição dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu superior.<br><br> ![Aplicativos][2]<br>
4. Clique em **Adicionar** na parte inferior da página.<br><br> ![Aplicativos][3]<br>
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.<br><br> ![Aplicativos][4]<br>
6. Na caixa de pesquisa, digite **Namely**.<br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-namely-tutorial/tutorial_namely_01.png)<br>
7. No painel de resultados, selecione **Namely** e clique em **Concluir** para adicionar o aplicativo. <br><br>

##  Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do AD do Azure com o Namely, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o AD do Azure precisa saber qual usuário do Namely é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado do Namely.<br> Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no AD do Azure como sendo o valor de **Nome de Usuário** no Namely.
 
Para configurar e testar o logon único do AD do Azure com o Namely, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** - para permitir que seus usuários usem esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criando um usuário de teste do Namely](##creating-a-namely-test-user)** - para ter um equivalente de Brenda Fernandes no Namely que esteja vinculado à representação dela no AD do Azure.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** - para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do AD do Azure no portal do AD do Azure e configurar o logon único em seu aplicativo Namely.




**Para configurar o logon único do AD do Azure com o Namely, execute as seguintes etapas:**

1. No portal do AD do Azure, na página de integração do aplicativo **Namely**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**. <br><br> ![Configurar o logon único][6] <br>

2. Na página **Como você deseja que os usuários façam logon no Namely**, selecione **Logon único do AD do Azure** e clique em **Avançar**.<br><br>![Configurar o logon único](./media/active-directory-saas-namely-tutorial/tutorial_namely_03.png)<br>

3. Na página da caixa de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas: <br><br>![Configurar o logon único](./media/active-directory-saas-namely-tutorial/tutorial_namely_04.png) <br>

    a. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para entrar no aplicativo Namely (por exemplo: **https://fabrikam.Namely.com/*)).

    b. Clique em **Próximo**.
 
 
4. Na página **Configurar logon único no Namely**, execute as seguintes etapas: <br><br>![Configurar o logon único](./media/active-directory-saas-namely-tutorial/tutorial_namely_05.png) <br>

    a. Clique em **Baixar certificado** e salve o arquivo em seu computador.

    b. Clique em **Próximo**.


1. Em outra janela do navegador, entre em seu site de empresa do Namely como administrador.

1. Na barra de ferramentas na parte superior, clique em **Empresa**. <br><br>![Configurar o logon único](./media/active-directory-saas-namely-tutorial/tutorial_namely_06.png)<br>

1. Clique na guia **Configurações**. <br><br>![Configurar o logon único](./media/active-directory-saas-namely-tutorial/tutorial_namely_07.png)<br>


1. Clique em **SAML**. <br><br>![Configurar o logon único](./media/active-directory-saas-namely-tutorial/tutorial_namely_08.png)<br>


1. Na página **Configurações de SAML**, execute as seguintes etapas:<br><br>![Configurar o logon único](./media/active-directory-saas-namely-tutorial/tutorial_namely_09.png)<br>

    a. Clique em **Habilitar SAML**.

    b. No Portal do Azure, na página do diálogo **Configurar logon único no Namely**, copie o valor da **URL do Serviço de Logon Único** e cole-o na caixa de texto **Url DDO do provedor de identidade**.

    c. Abra o certificado baixado no Bloco de Notas, copie o conteúdo e cole-o na caixa de texto **Certificado do provedor de identidade**.

    d. Clique em **Salvar**.


6. No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**. <br><br>![Logon único do AD do Azure][10]<br>

7. Na página **Confirmação de logon único**, clique em **Concluir**. <br><br>![Logon único do AD do Azure][11]




### Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar no portal do Azure um usuário de teste chamado Brenda Fernandes.<br> Na lista Usuários, selecione **Brenda Fernandes**.<br><br>![Criar um usuário do AD do Azure][20]<br>

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal de Gerenciamento do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-namely-tutorial/create_aaduser_09.png) <br> 

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**. <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-namely-tutorial/create_aaduser_03.png) <br>
 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**. <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-namely-tutorial/create_aaduser_04.png) <br>

5. Na página da caixa de diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas: <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-namely-tutorial/create_aaduser_05.png) <br>

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na caixa de texto **Nome de Usuário**, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na página da caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas: <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-namely-tutorial/create_aaduser_06.png) <br>
 
    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**. e. Clique em **Próximo**.

7. Na página da caixa de diálogo **Obter senha temporária**, clique em **criar**. <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-namely-tutorial/create_aaduser_07.png) <br>
 
8. Na página da caixa de diálogo **Obter senha temporária**, execute as seguintes etapas: <br><br>![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-namely-tutorial/create_aaduser_08.png) <br>
  
    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.

  
 
### Criando um usuário de teste do Namely

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Namely.

**Para criar um usuário chamado Brenda Fernandes no Namely, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do Namely como administrador.

1. Na barra de ferramentas na parte superior, clique em **Pessoas**.<br><br>![Configurar o logon único](./media/active-directory-saas-namely-tutorial/tutorial_namely_10.png)<br>

1. Clique na guia **Diretório**. <br><br>![Configurar o logon único](./media/active-directory-saas-namely-tutorial/tutorial_namely_11.png) <br>

1. Clique em **Adicionar Nova Pessoa**.



1. Na caixa de diálogo **Adicionar Nova Pessoa**, execute as seguintes etapas:

    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Email**, digite o endereço de email de Brenda Fernandes no Portal do Azure.

    d. Clique em **Salvar**.





### Atribuição do usuário de teste do AD do Azure

O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Namely <br><br>![Atribuir usuário][200] <br>

**Para atribuir Brenda Fernandes ao Namely, execute as seguintes etapas:**

1. No portal do Azure, para abrir a exibição dos aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior. <br><br>![Atribuir usuário][201] <br>

2. Na lista de aplicativos, selecione **Namely**. <br><br>![Configurar o logon único](./media/active-directory-saas-namely-tutorial/tutorial_namely_50.png) <br>

1. No menu na parte superior, clique em **Usuários**. <br><br>![Atribuir usuário][203] <br>

1. Na lista Usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**. <br><br>![Atribuir usuário][205]



### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do AD do Azure usando o Painel de Acesso.<br> Quando você clicar no bloco Namely no Painel de Acesso, deverá ser automaticamente conectado ao seu aplicativo Namely.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-namely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-namely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-namely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-namely-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-namely-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-namely-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-namely-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-namely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-namely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-namely-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-namely-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-namely-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-namely-tutorial/tutorial_general_205.png

<!---HONumber=Nov15_HO2-->