<properties
	pageTitle="Tutorial: Integração do Active Directory do Azure com o Soonr Workplace | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Active Directory do Azure e o Soonr Workplace."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="jeedes"/>


# Tutorial: Integração do Active Directory do Azure com o Soonr Workplace

O objetivo desse tutorial é mostrar como integrar o Soonr Workplace ao Azure AD (Azure Active Directory). A integração do Soonr Workplace ao Azure AD oferece os seguintes benefícios:

- No AD do Azure, você pode controlar quem tem acesso ao Soonr Workplace
- Você pode habilitar o logon automático de seus usuários no Soonr Workplace (Logon único) com as contas do AD do Azure
- Gerenciar suas contas em um único local: o Portal clássico do Azure


Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos

Para configurar a integração do AD do Azure ao Soonr Workplace, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Soonr Workplace com logon único habilitado


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Como adicionar o Soonr Workplace a partir da Galeria
2. Configurar e testar o logon único do AD do Azure


## Como adicionar o Soonr Workplace a partir da Galeria
Para configurar a integração do Soonr Workplace ao AD do Azure, você precisa adicionar o Soonr Workplace da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Soonr Workplace da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 

	![Active Directory][1]

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

	![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

	![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
 
	![Aplicativos][4]

6. Na caixa de pesquisa, digite **Soonr Workplace**.
 
	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_01.png)

7. No painel de resultados, selecione **Soonr Workplace** e clique em **Concluir** para adicionar o aplicativo.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_02.png)

##  Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar logon único do AD do Azure com o Soonr Workplace, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o AD do Azure precisa saber qual usuário do Soonr Workplace é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado do Soonr Workplace. Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no AD do Azure ao valor do **Nome de Usuário** no Soonr Workplace.


Para configurar e testar o logon único do AD do Azure com o Soonr Workplace, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criação de um usuário de teste do Soonr Workplace](#creating-a-soonr-workplace-test-user)** - para ter um equivalente de Brenda Fernandes no Soonr Workplace que esteja vinculado à representação dela no AD do Azure.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)**: para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do AD do Azure no portal clássico do Azure e configurar o logon único em seu aplicativo do Soonr Workplace.



**Para configurar o logon único do AD do Azure com o Soonr Workplace, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração do aplicativo **Soonr Workplace**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

	![Configurar o logon único][6]

2. Na página **Como você deseja que os usuários façam logon no Soonr Workplace**, selecione **Logon Único do AD do Azure** e clique em **Avançar**.

	![Configurar o logon único](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_03.png)

3. Na página do diálogo **Definir Configurações do Aplicativo**, realize as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_04.png)


    a. Na caixa de texto URL de Entrada, digite a URL usada pelos usuários para fazer logon em seu aplicativo Soonr Workplace usando o seguinte padrão: **"https://<nome-servidor>.soonr.com/singlesignon/saml/SSO"**.

    b. Clique em **Próximo**.

4. Na página **Configurar logon único no Soonr Workplace**, execute as seguintes etapas:

	![Configurar o logon único](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_05.png)

    a. Clique em **Baixar metadados** e salve o arquivo no computador.

    b. Clique em **Próximo**.


5. Para configurar o SSO para seu aplicativo, entre em contato com sua equipe de suporte do Soonr Workplace e envie o arquivo de metadados baixado anexado ao email. Forneça também a URL de Emissor, a URL de SSO de SAML e a URL de Saída para que elas possam ser configuradas para integração com o SSO.


6. No portal clássico do Azure, selecione a confirmação da configuração de logon único e, em seguida, clique em **Avançar**.

	![Logon único do AD do Azure][10]

7. Na página **Confirmação de logon único**, clique em **Concluir**.
  
	![Logon único do AD do Azure][11]



### Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-soonr-tutorial/create_aaduser_09.png)

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-soonr-tutorial/create_aaduser_03.png)

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-soonr-tutorial/create_aaduser_04.png)

5. Na página do diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-soonr-tutorial/create_aaduser_05.png)

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na página da caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-soonr-tutorial/create_aaduser_06.png)

    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Próximo**.

7. Na página do diálogo **Obter senha temporária**, clique em **Criar**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-soonr-tutorial/create_aaduser_07.png)

8. Na página da caixa de diálogo **Obter senha temporária**, execute as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-soonr-tutorial/create_aaduser_08.png)

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.



### Criar um usuário de teste do Soonr Workplace

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Soonr Workplace. Trabalhe com a equipe de suporte do Soonr Workplace para adicionar os usuários à conta do Soonr Workplace.


> [AZURE.NOTE] Se precisar criar um usuário manualmente, entre em contato com a equipe de suporte do Soonr Workplace.


### Atribuição do usuário de teste do AD do Azure

O objetivo desta seção é habilitar Brenda Fernandes a usar o logon único do Azure, concedendo a ela acesso ao Soonr Workplace.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao Soonr Workplace, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.

	![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **Soonr Workplace**.

	![Configurar o logon único](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_50.png)

1. No menu na parte superior, clique em **Usuários**.

	![Atribuir usuário][203]

1. Na lista de usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

	![Atribuir usuário][205]



### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso. Quando você clica no bloco Soonr Workplace no Painel de Acesso, você deve ser conectado automaticamente ao seu aplicativo Soonr Workplace.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0518_2016-->