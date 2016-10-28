<properties
	pageTitle="Tutorial: integração do Active Directory do Azure com o SilkRoad Life Suite | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Active Directory do Azure e o SilkRoad Life Suite."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/19/2016"
	ms.author="jeedes"/>


# Tutorial: integração do Active Directory do Azure com o SilkRoad Life Suite

O objetivo desse tutorial é mostrar como integrar o SilkRoad Life Suite ao Azure AD (Azure Active Directory). A integração do SilkRoad Life Suite ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no AD do Azure quem tem acesso ao SilkRoad Life Suite
- Você pode habilitar seus usuários a fazer logon automaticamente no SilkRoad Life Suite (logon único) com suas contas do AD do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos 

Para configurar a integração do AD do Azure com o SilkRoad Life Suite, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do SilkRoad Life Suite


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

 
## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o SilkRoad Life Suite por meio da galeria
2. Configurar e testar o logon único do AD do Azure


## Adicionando o SilkRoad Life Suite por meio da galeria
Para configurar a integração do SilkRoad Life Suite com o AD do Azure, você precisa adicionar o SilkRoad Life Suite por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SilkRoad Life Suite por meio da galeria, realize as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Active Directory][1]

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

	![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

	![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

	![Aplicativos][4]

6. Na caixa de pesquisa, digite **SilkRoad Life Suite**.

	![Aplicativos][5]

7. No painel de resultados, selecione **SilkRoad Life Suite** e clique em **Concluir** para adicionar o aplicativo.

	![Aplicativos][50]


##  Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do AD do Azure com o SilkRoad Life Suite, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o AD do Azure precisa saber qual usuário do SilkRoad Life Suite é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado no SilkRoad Life Suite. Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no AD do Azure como o valor de **Nome de usuário** no SilkRoad Life Suite.
 
Para configurar e testar o logon único do AD do Azure com o SilkRoad Life Suite, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criando um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criando um usuário de teste do SilkRoad Life Suite](#creating-a-silkroad-life-suite-test-user)** - para ter um equivalente de Brenda Fernandes no SilkRoad Life Suite que esteja vinculado à representação dela no AD do Azure.
5. **[Atribuindo o usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do Azure AD no Portal clássico do Azure e configurar o logon único em seu aplicativo SilkRoad Life Suite.

**Para configurar o logon único do AD do Azure com o SilkRoad Life Suite, realize as seguintes etapas:**

5. Faça logon no site da empresa SilkRoad como administrador.


    > [AZURE.NOTE] Para obter acesso ao aplicativo de Autenticação do SilkRoad Life Suite para configurar a federação com o AD do Microsoft Azure, entre em contato com o Suporte da SilkRoad ou com seu representante de Serviços da SilkRoad.


6. Vá para **Provedor de Serviços** e clique em **Detalhes de Federação**.

	![Logon único do AD do Azure][10]


1. Clique em **Baixar Metadados de Federação** e salve o arquivo de metadados no computador.

	![Logon único do AD do Azure][11]

3. No Portal clássico do Azure, na página de integração do aplicativo **SilkRoad Life Suite**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

	![Configurar o logon único][6]

2. Na página **Como você deseja que os usuários façam logon no SilkRoad Life Suite**, selecione **Logon Único do Azure AD** e clique em **Avançar**.

	![Logon único do AD do Azure][7]

3. Na página de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:

	![Logon único do AD do Azure][8]
 
    a. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para entrar em seu site do SilkRoad Life Suite (por exemplo: *https://defcompanytest-test-redcarpet.silkroad-eng.com/Authentication/*).

    b. Abra o arquivo de metadados do **Silkroad** baixado.

    c. Localize a marca **AssertionConsumerService** e copie o atributo **Location**.

	![Logon único do AD do Azure][21]
   
    d. Cole o valor na caixa de texto **URL de Resposta**.
 
    e. Clique em **Avançar**.
 
4. Na página **Configurar logon único no SilkRoad Life Suite**, execute as seguintes etapas:

	![Logon único do AD do Azure][9]

    a. Clique em Baixar certificado e salve o certificado localmente no computador.

    b. Clique em **Avançar**.




1. Em seu aplicativo **SilkRoad**, clique em **Fontes de Autenticação**.

	![Logon único do AD do Azure][12]



1. Clique em **Adicionar Fonte de Autenticação**.

	![Logon único do AD do Azure][13]



1. Na seção **Adicionar Fonte de Autenticação**, execute as seguintes etapas:

	![Logon único do AD do Azure][14]

    a. Em **Opção 2 - Arquivo de Metadados**, clique em **Procurar** para carregar o arquivo de metadados baixado.

    b. Clique em **Criar Provedor de Identidade usando Dados de Arquivo**.



1. Na seção **Fontes de Autenticação**, clique em **Editar**.

	![Logon único do AD do Azure][15]


1. No diálogo **Editar Fonte de Autenticação**, execute as seguintes etapas:

	![Logon único do AD do Azure][16]

    a. Para **Habilitado**, selecione **Sim**.

    b. Na caixa de texto **Descrição do IdP**, digite uma descrição para a sua configuração (por exemplo: *SSO do Azure AD*).

    c. Na caixa de texto **Nome do IdP**, digite um nome específico para a sua configuração (por exemplo: *Azure SP*).

    d. Clique em **Salvar**.


6. Desabilite todas as outras fontes de autenticação.

	![Logon único do AD do Azure][17]

7. No Portal clássico do Azure, na página **Confirmação de logon único**, clique em **Avançar**.

	![Logon único do AD do Azure][18]

1. Na página **Confirmação de logon único**, clique em **Concluir**.

	![Logon único do AD do Azure][19]


### Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_09.png)

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_03.png)
 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_04.png)

5. Na página do diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_05.png)

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Avançar**.

6.  Na página da caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_06.png)
 
    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**. e. Clique em **Avançar**.

7. Na página do diálogo **Obter senha temporária**, clique em **Criar**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_07.png)
 
8. Na página de caixa de diálogo **Obter senha temporária**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_08.png)
  
    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.

  
 
### Criando um usuário de teste do SilkRoad Life Suite

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no SilkRoad Life Suite. Brenda deve ter uma ID de SSO (às vezes chamada um *AuthParam*) que corresponde ao **emailaddress** de Brenda no Azure AD.

**Para criar um usuário chamado Brenda Fernandes no SilkRoad Life Suite, realize as seguintes etapas:**

1. Peça à sua equipe de suporte do SilkRoad Life Suite para criar um usuário que tenha o atributo **ID de SSO** com o mesmo valor que **emailaddress** de Brenda Fernandes no Azure AD.



### Atribuição do usuário de teste do AD do Azure

O objetivo desta seção é habilitar Brenda Fernandes a usar o logon único do Azure, concedendo a ela o acesso ao SilkRoad Life Suite.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao SilkRoad Life Suite, realize as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior.

	![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **SilkRoad Life Suite**.

	![Atribuir usuário][202]

1. No menu na parte superior, clique em **Usuários**.

	![Atribuir usuário][203]

1. Na lista de usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

	![Atribuir usuário][205]



### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso. Quando clica no bloco SilkRoad Life Suite no Painel de Acesso, você deve ser conectado automaticamente ao seu aplicativo SilkRoad Life Suite.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_01.png
[50]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_02.png

[6]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_03.png
[8]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_04.png
[9]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_05.png
[10]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_13.png
[18]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_06.png
[19]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_15.png


[200]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_14.png
[203]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0921_2016-->