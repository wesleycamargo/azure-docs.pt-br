<properties
	pageTitle="Tutorial: integração do Active Directory do Azure ao CloudPassage | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Active Directory do Azure e o CloudPassage."
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
	ms.date="07/14/2016"
	ms.author="jeedes"/>


# Tutorial: integração do Active Directory do Azure ao CloudPassage

O objetivo desse tutorial é mostrar como integrar o CloudPassage ao Azure AD (Azure Active Directory). A integração do CloudPassage ao Azure AD oferece os seguintes benefícios:

- Você pode controlar, no AD do Azure, quem tem acesso ao CloudPassage
- Você pode habilitar seus usuários a fazerem logon automaticamente no CloudPassage (logon único) com suas contas do AD do Azure
- Você pode gerenciar suas contas em um único local: o Active Directory do Azure


Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos 

Para configurar a integração do AD do Azure com o CloudPassage, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do CloudPassage com logon único habilitado


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se você não tiver um ambiente de teste do AD do Azure, você pode obter um mês de uma assinatura de avaliação gratuita do Azure [aqui](https://azure.microsoft.com/pricing/free-trial/).

 
## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando CloudPassage da Galeria
2. Configurar e testar o logon único do Azure AD


## Adicionando CloudPassage da Galeria
Para configurar a integração do CloudPassage com o AD do Azure, você precisa adicionar o CloudPassage, por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

### Para adicionar o CloudPassage por meio da galeria, execute as seguintes etapas:

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
 
	![Active Directory][1]

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

	![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

	![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

	![Aplicativos][4]

6. Na caixa de pesquisa, digite **CloudPassage**.

	![Aplicativos][5]

7. No painel de resultados, selecione **CloudPassage** e clique em **Concluir** para adicionar o aplicativo.

	![Aplicativos][6]



##  Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar logon único do AD do Azure com o CloudPassage, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o AD do Azure precisa saber qual usuário do CloudPassage é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do CloudPassage. Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no AD do Azure ao valor de **nome de usuário** no CloudPassage.
 
Para configurar e testar o logon único do AD do Azure com o CloudPassage, você precisa concluir os seguintes blocos de construção:

1. **[Configurando o Logon Único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** - para habilitar os usuários a usar esse recurso.
2. **[Criando um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criação de um usuário de teste do CloudPassage](#creating-a-halogen-software-test-user)** - para ter um equivalente de Brenda Fernandes no CloudPassage que esteja vinculado à representação dela no AD do Azure.
5. **[Atribuindo o usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure AD e configurar o logon único no aplicativo CloudPassage. Seu aplicativo CloudPassage espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizado à sua configuração de atributos de token SAML. A captura de tela a seguir mostra um exemplo disso.

![Configurar o logon único][21]

**Para configurar o logon único do AD do Azure com o CloudPassage, execute as seguintes etapas:**

1. No portal clássico do Azure AD, na página de integração do aplicativo **CloudPassage**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.

	![Configurar o logon único][7]

2. Na página **Como você deseja que os usuários façam logon no CloudPassage**, selecione **Logon único do Azure AD** e clique em **Avançar**.

	![Configurar o logon único][8]

3. Na página de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:

	![Definir configurações de aplicativo][9]
 
    a. Na caixa de texto **URL de Logon**, digite o URL usada pelos usuários para entrar no aplicativo CloudPassage (por exemplo: *https://portal.cloudpassage.com/saml/init/accountid*).

    b. Na caixa de texto **URL de Resposta**, digite a URL de AssertionConsumerService URL (por exemplo: *https://portal.cloudpassage.com/saml/consume/accountid*). Você pode obter o valor para este atributo clicando em **documentação de instalação do SSO** na seção **configurações de logon único** do seu portal CloudPassage. ![Configurar o logon único][10]

    C. Clique em **Próximo**.



4. Na página **Configurar logon único no CloudPassage**, clique em **Baixar certificado** e salve o arquivo de certificado no computador.

	![Configurar o logon único][11]

5. Em outra janela do navegador, entre em seu site de empresa CloudPassage como administrador.

6. No menu, na parte superior, clique em **Configurações** e clique em **Administração do Site**.

	![Configurar o logon único][12]

7. Clique na guia **Configurações de Autenticação**.

	![Configurar o logon único][13]


8. Na seção **Configurações de Logon Único**, realize as seguintes etapas:

	![Configurar o logon único][14]


    a. No portal clássico do Azure, na página de diálogo **Configurar logon único no CloudPassage**, copie o valor da **URL do Emissor** e cole-o na caixa de texto **URL do Emissor SAML**.

    b. No portal clássico do Azure, na página de diálogo **Configurar logon único no CloudPassage**, copie o valor de **Ponto de extremidade iniciado pelo Provedor de Serviço (SP)** e cole-o na caixa de texto **URL do ponto de extremidade SAML**.

    c. No portal clássico do Azure, na página de diálogo **Configurar logon único no CloudPassage**, copie o valor de **URL de Logout** e cole-o na caixa de texto **Página inicial de Logout**.

    d. Crie um arquivo codificado em **base-64** usando o certificado baixado.
          
    >[AZURE.TIP] Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    e. Abra seu certificado codificado em base-64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado x 509**.

    f. Clique em **Salvar**.


9. No portal clássico do Azure AD, selecione a confirmação de configuração do logon único e clique em **Avançar**.

	![Configurar o logon único][15]


10. Na página **Confirmação de logon único**, clique em **Concluir**.

	![Configurar o logon único][16]



11. No menu na parte superior, clique em **Atributos** para abrir a caixa de diálogo **Atributos Token SAML**.

	![Configurar o logon único][17]

12. Para adicionar os atributos de usuário necessários, para cada linha na tabela a seguir, execute as seguintes etapas:

	| Nome do atributo | Valor do atributo |
	| --- | --- |
	| nome | user.givenname |
	| sobrenome | user.surname |
	| email | user.mail |

 

	a. Clique em **adicionar atributo de usuário**.

	![Configurar o logon único][18]

    b. Na caixa de texto **Nome do Atributo**, digite o nome do atributo e, como **Valor do Atributo**, selecione o valor do atributo mostrado para a linha.

	![Configurar o logon único][19]
     
    c. Clique em **Concluído**.


13. Na barra de ferramentas na parte inferior, clique em **Aplicar Alterações**.

	![Configurar o logon único][20]



### Criação de um usuário de teste do AD do Azure

O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_01.png)

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_02.png)

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_03.png)
 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_04.png)

5. Na página de caixa de diálogo **Conte-nos sobre este usuário**, realize as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_05.png)

	a. Em Tipo de Usuário, selecione Novo usuário na organização.

	b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
  
	c. Clique em Avançar.

6.  Na página de caixa de diálogo **Perfil do Usuário**, realize as seguintes etapas:

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_06.png)
  
	a. Na caixa de texto **Nome**, digite **Brenda**.
  
	b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
  
	c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.
  
	d. Na lista **Função**, selecione **Usuário**.
  
	e. Clique em **Próximo**.

7. Na página de caixa de diálogo **Obter senha temporária**, clique em **criar**.

	![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_07.png)
 
8. Na página de caixa de diálogo **Obter senha temporária**, execute as seguintes etapas:

	![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_08.png)
  
	a. Anote o valor da **Nova Senha**.
  
	b. Clique em **Concluído**.


  
 
### Criar um usuário de teste CloudPassage

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no CloudPassage.

#### Para criar um usuário chamado Brenda Fernandes no CloudPassage, execute as seguintes etapas:

1.	Faça logon em seu site de empresa do **CloudPassage** como administrador.

2.	Na barra de ferramentas, na parte superior, clique em **Configurações** e clique em **Administração do Site**.

	![Criar um usuário de teste CloudPassage][22]

3.	Clique na guia **Usuários** e clique em **Adicionar Novo Usuário**.

	![Criar um usuário de teste CloudPassage][23]
	
4.	Na seção **Adicionar Novo Usuário**, realize as seguintes etapas:

	![Criar um usuário de teste CloudPassage][24]

    a. Na caixa de texto **Nome**, digite Brenda.

    b. Na caixa de texto **Sobrenome**, digite Fernandes.

    c. Nas caixas de texto **Nome de usuário**, **E-mail** e **Digite novamente o e-mail**, digite o nome de usuário da Brenda no AD do Azure.

    d. Como **Tipo de acesso**, selecione **Habilitar acesso do Portal de Halo**.

    e. Clique em **Adicionar**.










### Atribuição do usuário de teste do AD do Azure

O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao CloudPassage.

![Atribuir usuário][30]

**Para atribuir Brenda Fernandes ao CloudPassage, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.

	![Atribuir usuário][26]

2. Na lista de aplicativos, selecione **CloudPassage**.

	![Atribuir usuário][27]

1. No menu na parte superior, clique em **Usuários**.

	![Atribuir usuário][25]

1. Na lista de usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**.

	![Atribuir usuário][29]



### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso. Quando você clica no bloco CloudPassage no Painel de Acesso, você deve fazer logon automaticamente no seu aplicativo CloudPassage.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_01.png
[6]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_02.png
[7]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_05.png
[8]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_03.png
[9]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_04.png
[10]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_05.png
[11]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_06.png
[12]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_10.png
[16]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_11.png
[17]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_10.png
[18]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_11.png
[19]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_12.png
[20]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_14.png
[21]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_14.png
[22]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_17.png
[25]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_15.png
[26]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_12.png
[27]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_13.png
[28]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[29]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_16.png
[30]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_17.png

<!---HONumber=AcomDC_0720_2016-->