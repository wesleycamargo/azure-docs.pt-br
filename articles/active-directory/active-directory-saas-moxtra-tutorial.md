<properties
	pageTitle="Tutorial: integração do Active Directory do Azure ao Moxtra | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Active Directory do Azure e o Moxtra."
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


# Tutorial: integração do Active Directory do Azure ao Moxtra

O objetivo deste tutorial é mostrar como integrar o Moxtra ao Azure AD (Active Directory do Azure).<br>A integração do Moxtra ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso ao Moxtra 
- Você pode permitir que os usuários façam logon automaticamente no Moxtra (Logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central – o Portal do Active Directory do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos 

Para configurar a integração do Azure AD ao Moxtra, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Moxtra


> [AZURE.NOTE]Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. <br>
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Moxtra da galeria 
2. Configurar e testar o logon único do AD do Azure


## Adicionando Moxtra da galeria
Para configurar a integração do Moxtra ao Azure AD, você precisará adicionar o Moxtra da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Moxtra a partir da galeria, realize as seguintes etapas:**

1. No **Portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Active Directory**.
<br><br> ![Active Directory][1]<br>

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir o modo de exibição de aplicativo, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
<br><br> ![Aplicativos][2]<br>
4. Clique em **Adicionar** na parte inferior da página.
<br><br> ![Aplicativos][3]<br>
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
<br><br> ![Aplicativos][4]<br>
6. Na caixa de pesquisa, digite **Moxtra**.
<br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_01.png)<br>
7. No painel de resultados, selecione **Moxtra** e clique em **Concluir** para adicionar o aplicativo. 
<br><br>

##  Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do Azure AD com o Moxtra, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Moxtra é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Moxtra.<br> Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD como sendo o valor de **Nome de Usuário** no Moxtra.
 
Para configurar e testar o logon único do Azure AD com o Moxtra, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criando um usuário de teste do Moxtra](#creating-a-moxtra-test-user)** - para ter um equivalente de Brenda Fernandes no Moxtra que esteja vinculado à representação dela no Azure AD.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)**: para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do Azure AD no portal do Azure AD e configurar o logon único em seu aplicativo Moxtra.

Seu aplicativo Moxtra espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra um exemplo disso.
<br><br>![Configurar o logon único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_09.png)<br>



**Para configurar o logon único do Azure AD com o Moxtra, realize as seguintes etapas:**

1. No portal do AD do Azure, na página de integração do aplicativo **Moxtra**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
<br><br> ![Configurar o logon único][6] <br>

2. Na página **Como você deseja que os usuários façam logon no Moxtra**, selecione **Logon único do Azure AD** e clique em **Próximo**.
<br><br>![Configurar o logon único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_03.png)<br>

3. Na página de caixa de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:
<br><br>![Configurar o logon único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_04.png)<br>

    a. Na caixa de texto **URL de logon**, digite a seguinte URL: **https://www.moxtra.com/service/#login**.

    b. Clique em **Próximo**.
 
 
4. Na página **Configurar logon único no Moxtra**, execute as seguintes etapas:
<br><br>![Configurar o logon único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_05.png) <br>

    a. Clique em **Baixar certificado** e salve o arquivo no computador.

    b. Clique em **Próximo**.


1. Em outra janela do navegador, entre em seu site de empresa do Moxtra como administrador.

1. Na barra de ferramentas à esquerda, clique em **Console do Administrador > Logon Único SAML** e em **Novo**.
<br><br>![Configurar o logon único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_06.png) <br>


1. Na página **Instalação do SAML**, realize as seguintes etapas:
<br><br>![Configurar o logon único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_08.png)<br>

    a. Na caixa de texto **Nome**, digite um nome para a sua configuração (por exemplo: *SAML*).

    b. No portal do Azure, na página do diálogo **Configurar logon único no Moxtra**, copie o valor de **ID de Entidade** e cole-o na caixa de texto **ID de Entidade do IdP**.

    c. No portal do Azure, na página do diálogo **Configurar logon único no Moxtra**, copie o valor de **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon**.

    d. Na caixa de texto **AuthnContextClassRef**, digite **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**.

    e. No portal do Azure, na página do diálogo **Configurar logon único no Moxtra**, copie o valor de **Formato do Identificador de Nome** e cole-o na caixa de texto **Formato da ID de Nome**.

    f. Abra seu certificado baixado no Bloco de Notas, copie o conteúdo e cole-o na caixa de texto **Certificado**.

    g. Na caixa de texto de domínio de email SAML email, digite seu domínio de email SAML.
    > [AZURE.NOTE]Para ver as etapas de verificação do domínio, clique no “**i**” abaixo.


    h. Clique em **Atualizar**.


6. No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**.
<br><br>![Logon único do AD do Azure][10]<br>

7. Na página **Confirmação de logon único**, clique em **Concluir**.
<br><br>![Logon único do AD do Azure][11]

1. Para adicionar mapeamentos de atributo personalizados para sua configuração de atributos de token saml, na parte superior do menu, clique em **Atributos** para abrir o diálogo **Atributos de Token SAML**.
<br><br>![Configurar o logon único](./media/active-directory-saas-moxtra-tutorial/tutorial_general_80.png) <br>



1. Para cada linha de dados na tabela abaixo, realize as seguintes etapas:

    | Nome do atributo | Valor do atributo |
    | ---            | ---             |
    | nome | givenname |
    | sobrenome | sobrenome |
    | idpid | *<\o valor de **ID da Entidade** do diálogo **Configurar logon único no Moxtra** no portal do Azure \>* |

 
    a. Clique em adicionar atributo de usuário <br><br>![Configurar o logon único](./media/active-directory-saas-moxtra-tutorial/tutorial_general_81.png) <br>

    b. No diálogo **Adicionar Atributo de Usuário**, digite o nome e o valor do atributo mostrado para aquela linha na tabela. <br><br>![Configurar o logon único](./media/active-directory-saas-moxtra-tutorial/tutorial_general_82.png) <br>

    c. Clique em **Concluído**.



1. Clique em **Aplicar alterações**.
<br><br>![Configurar o logon único](./media/active-directory-saas-moxtra-tutorial/tutorial_general_84.png)<br>








### Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no portal do Azure chamado Brenda Fernandes.<br>
Na lista Usuários, selecione **Brenda Fernandes**.<br><br>![Criar um usuário do AD do Azure][20]<br>

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal de Gerenciamento do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
<br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moxtra-tutorial/create_aaduser_09.png) <br> 

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
<br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moxtra-tutorial/create_aaduser_03.png) <br>
 
4. Para abrir o diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
<br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moxtra-tutorial/create_aaduser_04.png) <br>

5. Na página do diálogo **Conte-nos sobre este usuário**, realize as seguintes etapas:
<br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moxtra-tutorial/create_aaduser_05.png) <br>

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome de Usuário, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na página da caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas:
<br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moxtra-tutorial/create_aaduser_06.png) <br>
 
    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**. e. Clique em **Próximo**.

7. Na página do diálogo **Obter senha temporária**, clique em **criar**.
<br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moxtra-tutorial/create_aaduser_07.png) <br>
 
8. Na página do diálogo **Obter senha temporária**, realize as seguintes etapas:
<br><br>![Criação de um usuário de teste do Azure AD](./media/active-directory-saas-moxtra-tutorial/create_aaduser_08.png) <br>
  
    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.

  
 
### Criar um usuário de teste Moxtra

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Moxtra.

**Para criar um usuário chamado Brenda Fernandes no Moxtra, realize as seguintes etapas:**

1. Faça logon em seu site de empresa do Moxtra como administrador.

1. Na barra de ferramentas à esquerda, clique em **Console de Administração > Gerenciamento de Usuário** e em **Adicionar Usuário**.
<br><br>![Configurar o logon único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_10.png) <br>



1. No diálogo **Adicionar Usuário**, realize as seguintes etapas:

    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Email**, digite o endereço de email de Brenda Fernandes no portal do Azure.

    d. Na caixa de texto **Divisão**, digite **Dev**.

    e. Na caixa de texto **Departamento**, digite **TI**.

    f. Selecione **Administrador**.

    g. Clique em **Adicionar**.





### Atribuição do usuário de teste do AD do Azure

O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Moxtra.
<br><br>![Atribuir usuário][200] <br>

**Para atribuir Brenda Fernandes ao Moxtra, realize as seguintes etapas:**

1. No portal do Azure, para abrir a exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior.
<br><br>![Atribuir usuário][201] <br>

2. Na lista de aplicativos, selecione **Moxtra**.
<br><br>![Configurar o logon único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_50.png) <br>

1. No menu na parte superior, clique em **Usuários**.
<br><br>![Atribuir usuário][203] <br>

1. Na lista Usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
<br><br>![Atribuir usuário][205]



### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do AD do Azure usando o Painel de Acesso.<br>
Ao clicar no bloco Moxtra no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Moxtra.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_205.png

<!----HONumber=AcomDC_1203_2015-->