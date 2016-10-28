<properties
	pageTitle="Tutorial: Integração do Active Directory do Azure com o Workrite | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Active Directory do Azure e o Workrite."
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
	ms.date="08/12/2016"
	ms.author="jeedes"/>


# Tutorial: Integração do Active Directory do Azure com o Workrite

O objetivo desse tutorial é mostrar como integrar o Workrite ao Azure AD (Azure Active Directory). A integração do Workrite ao Azure AD oferece os seguintes benefícios:


- Você pode controlar no AD do Azure quem tem acesso ao Workrite
- Você pode habilitar seus usuários a fazerem logon automaticamente no Workrite (logon único) com suas contas do AD do Azure
- Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos 

Para configurar a integração do AD do Azure ao Workrite, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Workrite com logon único habilitado


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

 
## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em três blocos de construção principais:

1. Adicionar o Workrite a partir da galeria
2. configurar e testar o logon único do AD do Azure


## Adicionar o Workrite a partir da galeria
Para configurar a integração do Workrite ao AD do Azure, você precisa adicionar o Workrite a partir da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Workrite a partir da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
 
	![Active Directory][1]

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

	![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

	![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
 
	![Aplicativos][4]

6. Na caixa de pesquisa, insira **Workrite**.

	![Aplicativos][5]

7. No painel de resultados, selecione **Workrite** e clique em **Concluir** para adicionar o aplicativo.

	![Aplicativos][500]


##  Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do AD do Azure com o Workrite, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o AD do Azure precisa saber qual usuário do Workrite é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado no Workrite. Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no AD do Azure ao valor do **Nome de Usuário** no Workrite.
 
Para configurar e testar o logon único do AD do Azure com o Workrite, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do Azure AD](#configuring-azure-ad-single-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criando um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criação de um usuário de teste do Workrite](#creating-a-halogen-software-test-user)**: para ter um equivalente de Brenda Fernandes no Workrite que esteja vinculado à representação dela no AD do Azure.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)**: para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do Azure AD no portal do Azure AD e configurar o logon único em seu aplicativo do Workrite.

**Para configurar o logon único do AD do Azure com o Workrite, execute as seguintes etapas:**

1. No Portal Clássico do Azure, na página de integração de aplicativos do **Workrite**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

	![Configurar o logon único][6]

2. Na página **Como você deseja que os usuários façam logon no Workrite**, selecione **Logon Único do AD do Azure** e clique em **Avançar**.

	![Logon único do AD do Azure][7]

3. Na página de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:
	
	![Logon único do AD do Azure][8]
 
     a. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para entrar no site do Workrite (por exemplo: *https://app.workrite.co.uk/securelogin/samlgateway.aspx?id=1a82b5aa-4dd6-4472-9721-7d0193f59e22*).

     > [AZURE.NOTE] Entre em contato com sua equipe de suporte do Workrite[support@workrite.co.uk](mailto:support@workrite.co.uk) se você não souber o valor da URL de Logon.

     b. Clique em **Avançar**.
 
4. Na página **Configurar logon único no Workrite**, execute as seguintes etapas:

	![Logon único do AD do Azure][9]

    a. Clique em Baixar certificado e salve o certificado localmente no computador.

    b. Entre em contato com a equipe de suporte do Workrite [support@workrite.co.uk](mailto:support@workrite.co.uk), forneça o certificado baixado, a **URL do Emissor** (ID da Entidade), **URL do Serviço de Logon Único**, **URL de Saída Única** e peça para configurar o SSO para seu aplicativo Workrite.

    c. Clique em **Avançar**.


6. No Portal Clássico do Azure, selecione a confirmação de configuração de logon único e clique em **Avançar**.

	![Logon único do AD do Azure][10]

7. Na página **Confirmação de logon único**, clique em **Concluir**.
 
	![Logon único do AD do Azure][11]




### Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-workrite-tutorial/create_aaduser_09.png)

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-workrite-tutorial/create_aaduser_03.png)
 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-workrite-tutorial/create_aaduser_04.png)

5. Na página do diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-workrite-tutorial/create_aaduser_05.png)

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Avançar**.

6.  Na página da caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-workrite-tutorial/create_aaduser_06.png)
 
    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**. e. Clique em **Avançar**.

7. Na página do diálogo **Obter senha temporária**, clique em **Criar**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-workrite-tutorial/create_aaduser_07.png)
 
8. Na página de caixa de diálogo **Obter senha temporária**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-workrite-tutorial/create_aaduser_08.png)
  
    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.

  
 
### Criar um usuário de teste do Workrite

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Workrite.

**Para criar um usuário chamado Brenda Fernandes no Workrite, execute as seguintes etapas:**

1. Faça logon no site da empresa workrite como administrador.

2. No painel de navegação, clique em **Administrador**.

	![Atribuir usuário][400]

3. Vá para Links Rápidos e clique em **Criar Usuário**.

	![Atribuir usuário][401]

4. No diálogo **Criar Usuário**, realize as seguintes etapas:

	![Atribuir usuário][402]

    a. Insira o **Email**, **Nome** e **Sobrenome** de um usuário válido do AD do Azure que você deseja fornecer.

    b. Selecione **Administrador Cliente** como **Escolher Função**.

    c. Clique em **Salvar**.


### Atribuição do usuário de teste do AD do Azure

O objetivo desta seção é habilitar que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Workrite.

	![Assign User][200] 

**Para atribuir Brenda Fernandes ao Workrite, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.

	![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **Workrite**.

	![Atribuir usuário][202]

1. No menu na parte superior, clique em **Usuários**.

	![Atribuir usuário][203]
1. Na lista de usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

	![Atribuir usuário][205]



### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso. Quando você clica no bloco Workrite no Painel de Acesso, deve fazer logon automaticamente no seu aplicativo Workrite.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_01.png
[500]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_05.png

[6]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_02.png
[8]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_03.png
[9]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_04.png
[10]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_07.png
[203]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_400.png
[401]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_401.png
[402]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_402.png

<!---HONumber=AcomDC_0817_2016-->