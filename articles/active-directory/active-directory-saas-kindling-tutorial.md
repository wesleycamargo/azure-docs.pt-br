<properties
	pageTitle="Tutorial: integração do Active Directory do Azure com o Kindling | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Active Directory do Azure e o Kindling."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="prasannas"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="jeedes"/>


# Tutorial: integração do Active Directory do Azure com o Kindling

O objetivo deste tutorial é mostrar como integrar o Kindling com o Active Directory do Azure (AD do Azure).<br>A integração do Kindling com o AD do Azure oferece os seguintes benefícios:

- Você pode controlar no AD do Azure quem tem acesso ao Kindling 
- Você pode permitir que seus usuários façam logon automaticamente no Kindling (logon único) com suas contas do AD do Azure
- Você pode gerenciar suas contas em um local central – o Portal do Active Directory do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos 

Para configurar a integração do AD do Azure ao Kindling, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada do Kindlling com logo único


> [AZURE.NOTE]Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. <br> O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Kindling da galeria 
2. Configurar e testar o logon único do AD do Azure


## Adicionando o Kindling da galeria
Para configurar a integração do Kindling ao AD do Azure, você precisará adicionar o Kidling da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Kidling da galeria, execute as seguintes etapas:**

1. No **Portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir o modo de exibição de aplicativo, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.<br><br> ![Aplicativos][2]<br>
4. Clique em **Adicionar** na parte inferior da página.<br><br> ![Aplicativos][3]<br>
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.<br><br> ![Aplicativos][4]<br>
6. Na caixa de pesquisa, digite **Kindling**.<br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_01.png)<br>
7. No painel de resultados, selecione **Kindling** e clique em **Concluir** para adicionar o aplicativo.<br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_02.png)<br>

##  Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do AD do Azure com o Kindling com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o AD do Azure precisa saber qual usuário do Kindling é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado do Kindling.<br> Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no AD do Azure ao valor do **Nome de Usuário** no Kindling.
 
Para configurar e testar o logon único do AD do Azur com o Kindling, conclua os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criar um usuário de teste Kindling](#creating-a-kindling-test-user)** - para ter um equivalente de Brenda Fernandes no Kindling vinculado à representação dela do AD do Azure.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)**: para permitir que Brenda Fernandes use o logon único do AD do Azure.
6. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do AD do Azure no portal do AD do Azure e configurar o logon único em seu aplicativo Kindling. Como parte deste procedimento, é necessário criar um arquivo de certificado codificado em base 64. Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

Para configurar o logon único para o Kindling, você precisa de um domínio registrado. Se não tiver um domínio registrado, entre em contato com a equipe de suporte do Kindling pelo email [support@flatterfiles.com](mailto:support@flatterfiles.com).



**Para configurar o logon único do AD do Azure AD com o Kindling, execute as seguintes etapas:**

1. No portal do AD do Azure, na página de integração do aplicativo **Kindling**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**. <br><br> ![Configurar o logon único][6] <br>

2. Na página **Como você deseja que os usuários façam logon no Kindling**, selecione **Logon único do AD do Azure** e clique em **Próximo**.<br><br>![Configurar o logon único](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_03.png)<br>

3. Na página de caixa de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:<br><br>![Configurar o logon único](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_04.png) <br>


    a. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para entrar no aplicativo Kindling (por exemplo: **https://fabrikam.Kindling.com/*)).

    b. Entre em contato com a equipe de suporte do Kindling para obter o valor do **emissor** e **URL de resposta**.
 
    c. Clique em **Próximo**.
 
 
4. Em **Configurar logon único no Kindling**, execute as etapas a seguir: <br><br>![Configurar o logon único](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_05.png) <br>

    a. Clique em **Baixar certificado** e, em seguida, salve o arquivo em seu computador.

    b. Entre em contato com a equipe de suporte do layout Kindling e peça-lhe para configurar o SSO em seu aplicativo. Você precisará fornecer a equipe de suporte com o certificado baixado, o valor do **emissor URL** que é mapeado para a **ID de entidade** do Kindling, a **URL de serviço de logon único** que mapeia para Kindling **SSO URL de logon** e **única URL de serviço de saída** que mapeia para Kindling **SSO sair da URL**.

    c. Clique em **Próximo**.

6. No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**. <br><br>![Logon único do AD do Azure][10]<br>

7. Na página **Confirmação de logon único**, clique em **Concluir**. <br><br>![Logon único do AD do Azure][11]




### Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no portal do Azure chamado Brenda Fernandes.<br> Na lista Usuários, selecione **Brenda Fernandes**.<br><br>![Criar um usuário do AD do Azure][20]<br>

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal de Gerenciamento do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kindling-tutorial/create_aaduser_09.png) <br> 

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**. <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kindling-tutorial/create_aaduser_03.png) <br>
 
4. Para abrir o diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**. <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kindling-tutorial/create_aaduser_04.png) <br>

5. Na página do diálogo **Conte-nos sobre este usuário**, realize as seguintes etapas: <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kindling-tutorial/create_aaduser_05.png) <br>

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome de Usuário, digite **Brenda Fernandes**.

    c. Clique em **Próximo**.

6.  Na página da caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas: <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kindling-tutorial/create_aaduser_06.png) <br>
 
    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**. e. Clique em **Próximo**.

7. Na página do diálogo **Obter senha temporária**, clique em **criar**. <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kindling-tutorial/create_aaduser_07.png) <br>
 
8. Na página do diálogo **Obter senha temporária**, realize as seguintes etapas: <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kindling-tutorial/create_aaduser_08.png) <br>
  
    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.

  
 
### Criar um usuário de teste Kindling

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Kindling. O Kindling dá suporte ao provisionamento just-in-time. Você já habilitou isso em [Configurar o logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on).

Não há itens de ação para você nesta seção.




### Atribuição do usuário de teste do AD do Azure

O objetivo desta seção é habilitar Brenda Fernandes a usar o logon único do Azure concedendo a ela acesso ao Kindling. <br><br>![Atribuir usuário][200] <br>

**Para atribuir Brenda Fernandes ao Kindling, execute as seguintes etapas:**

1. No portal do Azure, para abrir a exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior. <br><br>![Atribuir usuário][201] <br>

2. Na lista de aplicativos, selecione **Kindling**. <br><br>![Configurar o logon único](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_50.png) <br>

1. No menu na parte superior, clique em **Usuários**. <br><br>![Atribuir usuário][203] <br>

1. Na Lista de usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**. <br><br>![Atribuir usuário][205]



### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do AD do Azure usando o painel de acesso.<br> Quando você clica no bloco Kindling no Painel de Acesso, deve fazer logon automaticamente no seu aplicativo Kindling.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_1203_2015-->