<properties
	pageTitle="Tutorial: integração do Active Directory do Azure com o 23 Video | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Active Directory do Azure e o 23 Video."
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
	ms.date="08/05/2016"
	ms.author="jeedes"/>


# Tutorial: integração do Active Directory do Azure com o 23 Video

O objetivo deste tutorial é mostrar como integrar o 23 Video ao Azure AD (Azure Active Directory). A integração do 23 Video ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no AD do Azure quem tem acesso ao 23 Video
- Você pode habilitar seus usuários a fazerem logon automaticamente no 23 Video (logon único) com suas contas do AD do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos 

Para configurar a integração do AD do Azure com o 23 Video, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do 23 Video


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

 
## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o 23 Video por meio da galeria
2. configurar e testar o logon único do AD do Azure


## Adicionando o 23 Video por meio da galeria
Para configurar a integração do 23 Video ao AD do Azure, você precisa adicionar o 23 Video por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o 23 Video por meio da galeria, realize as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Active Directory][1]

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

	![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

	![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

	![Aplicativos][4]

6. Na caixa de pesquisa, digite **23 Video**.

	![Aplicativos][5]

7. No painel de resultados, selecione **23 Video** e clique em **Concluir** para adicionar o aplicativo.

	![Aplicativos][25]

##  configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do AD do Azure com o 23 Video, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o AD do Azure precisa saber qual usuário do 23 Video é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do 23 Video. Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no AD do Azure como o valor de **Nome de usuário** no 23 Video.
 
Para configurar e testar o logon único do AD do Azure com o 23 Video, você precisa concluir os seguintes blocos de construção:

1. **[Configurando o logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criando um usuário de teste do 23 Video](#creating-a-23-video-test-user)** - para ter um equivalente de Brenda Fernandes no 23 Video que esteja vinculado à representação dela no AD do Azure.
5. **[Atribuindo o usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do Azure AD no Portal Clássico do Azure e configurar o logon único em seu aplicativo 23 Video.

**Para configurar o logon único do AD do Azure com o 23 Video, realize as seguintes etapas:**

1. No Portal Clássico do Azure, na página de integração de aplicativos do **23 Video**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

	![Configurar o logon único][6]

2. Na página **Como você deseja que os usuários façam logon no 23 Video**, selecione **Logon único do Azure AD** e clique em **Avançar**.

	![Logon único do AD do Azure][7]

3. Na página de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:

	![Logon único do AD do Azure][8]
 
     a. Na caixa de texto **URL de Resposta**, digite a URL usada pelos usuários para entrar no site do 23 Video (por exemplo: *https://britta-simon.23Video.com/saml/login*).

     > [AZURE.NOTE] A integração do Active Directory com o SAML 2.0 está disponível para todos os usuários do 23 Video. Entre em contato com o Suporte em [support@23company.com](mailto:support@23company.com) se precisar dos metadados relacionados.

     b. Clique em **Avançar**.
 
4. Na página **Configurar logon único no 23 Video**, execute as seguintes etapas:

	![Logon único do AD do Azure][9]

    a. Clique em Baixar certificado e salve o certificado localmente no computador.

    b. Entre em contato com a equipe de suporte do 23 Video [support@23company.com](mailto:support@23company.com), forneça a eles seu certificado baixado, a **URL do Emissor**, a **URL de Serviço de Logon Único** e a **URL de Saída Única** e peça que eles configurem o SSO para seu aplicativo 23 Video.

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

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-23video-tutorial/create_aaduser_09.png)

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-23video-tutorial/create_aaduser_03.png)
 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-23video-tutorial/create_aaduser_04.png)

5. Na página de diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-23video-tutorial/create_aaduser_05.png)

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na página de diálogo **Perfil do Usuário**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-23video-tutorial/create_aaduser_06.png)
 
    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**. e. Clique em **Avançar**.

7. Na página de caixa de diálogo **Obter senha temporária**, clique em **criar**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-23video-tutorial/create_aaduser_07.png)
 
8. Na página de caixa de diálogo **Obter senha temporária**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-23video-tutorial/create_aaduser_08.png)
  
    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.

  
 
### Criando um usuário de teste do 23 Video

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no 23 Video.

**Para criar um usuário chamado Brenda Fernandes no 23 Video, realize as seguintes etapas:**

1. Faça logon no site da empresa 23 Video como administrador.

1. Vá para **Configurações**.


2. Na seção **Usuários**, clique em **Configurar**.

	![Atribuir usuário][400]

3. Clique em **Adicionar um novo usuário**.

	![Atribuir usuário][401]

4. Na seção **Convidar alguém para participar deste site**, execute as seguintes etapas:

	![Atribuir usuário][402]

    a. Na caixa de texto **Endereços de email**, digite o endereço de email de Brenda Fernandes no AD do Azure.

    b. Clique em **Adicionar o usuário**.


### Atribuição do usuário de teste do AD do Azure

O objetivo desta seção é habilitar Brenda Fernandes a usar o logon único do Azure, concedendo a ela o acesso ao 23 Video.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao 23 Video, realize as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.

	![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **23 Video**.

	![Atribuir usuário][202]

1. No menu na parte superior, clique em **Usuários**.

	![Atribuir usuário][203]

1. Na lista de usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

	![Atribuir usuário][205]



### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso. Quando você clica no bloco 23 Video no Painel de Acesso, você deve ser conectado automaticamente ao seu aplicativo 23 Video.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->
[1]: ./media/active-directory-saas-23video-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-23video-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-23video-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-23video-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_01.png
[25]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_02.png

[6]: ./media/active-directory-saas-23video-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_03.png
[8]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_04.png
[9]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_05.png
[10]: ./media/active-directory-saas-23video-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-23video-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-23video-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-23video-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-23video-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_07.png
[203]: ./media/active-directory-saas-23video-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-23video-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-23video-tutorial/tutorial_general_205.png

[400]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_10.png
[401]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_11.png
[402]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_12.png

<!---HONumber=AcomDC_0810_2016-->